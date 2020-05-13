---
title: Vimの临时配置&Linux对拍
date: 2020-01-20 16:37:56
categories: 学习笔记
tags:
    - Linux
    - Vim
keywords: linux
description:
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.4/vimdlspzlinuxdp.jpg
mathjax: false
---

快要WC了~~虽然可能去不了~~，研究一下noi-linux的基操。

玩的虚拟机，不过用的不是noi-linux，而是其基底ubuntu。要是我因为这俩的差异打铁了那就。。。<span class="spoiler">好像打铁了也没啥</span>

<!--more-->

---

# Vim

作为windows下的Vimer当然是要用Vim了。

终端下打开Vim的配置文件：

``` shell
vim ~/.vimrc
```

我在windows下的Vim有一堆插件，配置文件加起来有三四百行。。。

精简了一下：

```
set nu! "行号
set cul "高亮当前行
set tabstop=4 "tab宽度
set shiftwidth=4 "缩进
set smartindent "智能缩进
set mouse=a "允许鼠标操作
colorscheme ron "主题
"括号补全
inoremap { {}<esc>i<cr><esc>x%a
inoremap ( ()<esc>i
inoremap [ []<esc>i
inoremap ) <c-r>=ClosePair(')')<cr>
inoremap ] <c-r>=ClosePair(']')<cr>
func! ClosePair(char)
	if getline('.')[col('.')-1] == a:char
		return "\<Right>" "记住是双引号！
	else
		return a:char
	endif
endfunction
"光标移动优化
nnoremap j gj
nnoremap k gk
nnoremap gj j
nnoremap gk k
"编译运行
map <F9> :call Compile()<cr>
map <F10> :!./%<<cr>
func! Compile()
	exec "w"
	exec "!g++ % -o %< -std=c++11 -O2"
endfuntion
```

至于设置字体，不知道为啥`set guifont`用不了，到时候看看吧，不行就直接改终端的字体。

---

# Linux对拍

本来想学点Linux基础命令的，发现除了对拍没啥有用的。。。

和windows一样直接用`system`命令，比较文件用`diff`，运行程序要加上`./`。没有找到终端里的输入输出只能在程序里`freopen`。

有一个坑点，`Ctrl+C`会中止正在运行的程序，大概率会中止非对拍程序，所以停不下来，只能关掉终端。。。

``` cpp
while(1){
	system("./file");
	int s=clock();
	system("./my");
	s=clock()-s;
	system("./right");
	if(system("diff juruo.out dalao.out")){
		system("gedit file.in");
		return 0;
	}
	else printf("%d ms\n",s);
}
```