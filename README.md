# 本地 compile & build Latex project

## requirements

### 安装 Tex Live / MacTex

- windows: https://tug.org/texlive/ -> install on Windows -> [install-tl-windows.exe](https://mirror.ctan.org/systems/texlive/tlnet/install-tl-windows.exe).

- mac: https://tug.org/mactex/ -> [MacTeX Download](https://tug.org/mactex/mactex-download.html).

- linux, wsl: 

	```bash
	# Ubuntu/Debian
	sudo apt-get update
	sudo apt-get install texlive-full
	  
	# Fedora
	sudo dnf install texlive-scheme-full
	
	```

这个东西会安装很久 (windows $\approx$ 2h, mac $\approx$ 1h, linux $\approx$ 0.5h). 内容是 latex 全家桶. 

可以用

```bash
tlmgr info scheme-full
```

来验证装的是不是 full version.

安装包含了:

- 几乎所有 LaTeX 宏包 (几千个)
- 语言支持 (中文, 日文, 韩文, 阿拉伯文等)
- 引擎: pdfTeX, XeTeX, LuaTeX, MetaPost, BibTeX, Biber
- 各种字体
- 文档, 示例, 索引文件

(除了 texlive 之外, 另一种选择是 MikTex. 但是我觉得不如安装这个完整的 Texlive. 这个免费全家桶相当于轮椅, 后续任何的配置, 只需要 Latex Workshop + 写 .vscode/settings.json 就可以直接用了. 如果用 MikTex 的话还得配置其他的东西, 比如安装 strawberry perl, 安装各种小 packages 等.)



### 安装 VSCode Latex workshop

VSCode 最好的 Latex 支持. 

不用配置文件路径, 它能找到我们的  texlive 安装的引擎和 packages

我的配置文件 settings.json: 使用 lualatex, 生成的临时文件放入 build/, 自动 compile



### 需要 make 和 python

python 就不说了. 这里说一下安装 `make` (mac 和 linux 一行指令即可. windows 稍微复杂一点)

- windows:

	下载一个包管理器 chocolatey (如果没有的话): 用 admin 身份打开 powershell, 运行

	```powershell
	Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
	```

	然后关掉窗口之后, 再次用 admin 身份打开 powershell, 运行:

	```powershell
	choco install make
	```

	验证一下已安装:

	```powershell
	make --version
	# GNU Make 4.4.1
	#Built for Windows32
	#Copyright (C) 1988-2023 Free Software Foundation, Inc.
	#License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>
	#This is free software: you are free to change and redistribute it.
	#There is NO WARRANTY, to the extent permitted by law.
	```

- mac:

	(如果没安装 brew, 安装一下)

	```bash
	/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
	```

	然后

	```bash
	brew install make
	```

- linux:

	```bash
	sudo apt update
	sudo apt install make
	```

	(Fedora / CentOS / RHEL: `apt` -> `dnf`)



## 项目结构

```
project/
├── assets/  			  # 图片资源等
├── main.tex              
├── chapters/             # 每个 chapter 的 .tex 文件
│   ├── ch01-*.tex
│   ├── ch02-*.tex
│   └── ...
├── build/                # 编译临时文件目录 (平时 autocompile 的时候我把 main.pdf 放在了这里, 其他文件都是 build 生产的临时文件)
├── scripts/              # 自动化脚本
│   ├── build_chapters.py
│   ├── generate_docs_pages.py
│   └── generate_mkdocs_config.py
├── Makefile     
└── *.pdf                 # 生成的各章节 PDF 文件 (在根目录) 
```



## basic usage

### 自动编译 `main.tex`

在 Latex Workshop Extension 中点击 View Latex PDF 放在 split editor, 我们在写任意的 .tex 文件 (`main.tex` 或者 `main.tex` 里面 input 的 chapters), 它都会自动 build.

### 逐章节编译

项目使用 `lualatex` 作为编译引擎 (它对中文支持更好).

我在 Makefile 中配置了以下这些命令. 

```bash
# 生成所有章节的独立 PDF
make # 或者 make chapters
# 生成主 PDF (包含所有章节) 
make main
# 生成所有 PDF (默认生成章节 PDF) 
make all
```

注意: `make` 生成 chapters 的独立 PDF 是用的 `scripts/build_chapters.py` 脚本, 它自动解析 `main.tex` 中所有未注释的 `\input{chapters/xxx}`.

特点: 

- 编译完成后自动清理 `build/` 目录中的临时文件
- PDF 文件输出到 `docs` (这是为了: 如果想要用 mkdocs 等工具 build github page 来放这些 pdf, 这个位置比较方便)
- `build/` 目录只保留编译过程中的临时文件 (以及一个平时 autobuild 时的 main.pdf, Latex Workshop Extension 中 view Latex PDF 看的是它)





# 注: ElegantBook 使用指南

我这里用的模板是 [ElegantBook](https://github.com/ElegantLaTeX/ElegantBook), 提供了丰富的数学环境和代码块支持。

如果想使用其他模板只需要把 cls 文件换成改模板即可.

这里的 Elegantbook 是我进行过调整的, 增加了代码块等环境以及快捷的 math commands 和额外的数学符号. 这里介绍基础的使用:



### math

theorems 类环境 (彩色背景): 

```latex
\begin{definition}[optional title]
...
\end{definition}

\begin{theorem}[optional title]
...
\end{theorem}

\begin{lemma}[optional title]
...
\end{lemma}

\begin{proposition}[optional title]
...
\end{proposition}

\begin{corollary}[optional title]
...
\end{corollary}
```

examples 和 proof 等环境 (彩色字体)

```latex
\begin{example}[optional title]
...
\end{example}

\begin{exercise}[optional title]
...
\end{exercise}

\begin{problem}[optional title]
...
\end{problem}

\begin{proof}
...
\end{proof}

\begin{solution}
...
\end{solution}
```

注释类环境 (彩色字体)

```latex
\begin{note}
...
\end{note}

\begin{remark}
...
\end{remark}

\begin{assumption}
...
\end{assumption}

\begin{conclusion}
...
\end{conclusion}
```

### 代码块

Python 代码: 

```latex
\begin{python}[caption={代码标题}, label=code:label-name]
import numpy as np
def example():
    return "Hello, World!"
\end{python}
```

C++ 代码: 
```latex
\begin{cpp}[caption={代码标题}, label=code:label-name]
#include <iostream>
int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
\end{cpp}
```

终端/命令输出: 
```latex
\begin{terminal}[caption={输出标题}]
$ make chapters
\end{terminal}

% 或者使用 cmd 环境 (等价) 
\begin{cmd}[caption={输出标题}]
命令输出内容
\end{cmd}
```

纯文本: 
```latex
\begin{txt}[caption={文本标题}]
纯文本内容, 无语法高亮
\end{txt}
```

内联代码: 

```latex
使用 \pythoninline{print("Hello")} 来显示内联 Python 代码。

使用 \cppinline{std::cout} 来显示内联 C++ 代码。
```

### 图片插入

项目提供了两种图片插入方式: 

带标题和标签的图片

```latex
\insertpic[width=0.8\textwidth]{assets/image.png}{图片标题}{fig:label-name}
```

参数说明: 
- 第一个可选参数: 图片尺寸选项 (如 `width=0.8\textwidth`, `height=5cm` 等) 
- 第二个参数: 图片路径 (相对于项目根目录) 
- 第三个参数: 图片标题
- 第四个参数: 图片标签 (用于引用) 

它的原命令是:
```
    \begin{figure}[htbp]
        \centering
        \includegraphics[#1]{#2}
        \caption{#3}
        \label{#4}
    \end{figure}
```

简单居中图片
```latex
\pic[0.8]{assets/image.png}
```

参数说明: 

- 第一个可选参数: 图片宽度比例 (默认 1, 即 `\textwidth`) 
- 第二个参数: 图片路径

它的原命令是: 

```
	\begin{center}
        \includegraphics[width=#1\textwidth]{#2}
    \end{center}
```

引用图片: 
```latex
如图 \ref{fig:label-name} 所示...
```







# 注2: mkdocs 的 build 和 github pages 的 depoly

这里包括了一个构建静态网页, 并部署到 GitHub pages 的模板

流程如下:

```bash
# 先生成 docs/*.pdf (来自 chapters/*.tex)
make # or make chapters
# 再根据 docs/*.pdf 自动生成 docs/*.md + mkdocs.yml
make docs
# 部署到 gh-deploy 分支
make deploy
```

我使用的是 [mkdocs](https://www.mkdocs.org/) 作为静态网页构建器. 这里我们的自动化脚本做的事情是:

- `make docs` 根据 `make` 在 `docs` 中生成的 `*.pdf` 文件, 生成用来构建 mkdocs 配置的 `index.md` 和各个 chapters 的 markdown 文件 (只是用来放 pdf); 以及根据当前的 repository 自动生成根目录的 `mkdocs.yml` 文件, 然后自动 build
- `make depoly` 即 `mkdocs depoly`, 这里是为了 command 的 consistency.

跑完这些命令后请在 `make depoly` 后更改 GitHub Pages 配置:

- `Settings -> Pages -> Build and deployment`
- 选择 `Deploy from a branch`
- 分支选择 `gh-deploy`
- Folder 选择 `/(root)`

备注:

- `site/` 是 mkdocs 构建产物, 所以这里加入了 `.gitignore`, 不用管它
- 若 Windows 下终端暂时找不到 `make`, 重开终端即可





