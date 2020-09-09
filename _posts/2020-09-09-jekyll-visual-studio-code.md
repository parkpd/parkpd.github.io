---
title: "Jekyll + GitHub 블로깅. 4. Visual Studio Code 로 편집하기"
date: 2020-09-09 08:40:18
categories: JekyllInstall
tags: [jekyll, Visual Studio Code]
---
[Visual Studio Code](https://code.visualstudio.com) 에 GitHub 로컬 폴더를 끌어다 놓으면 markdown 파일을 편집하고, preview 를 미리 확인할 수 있다.(참고 : [Visual Studio Code 에서 마크다운 사용하기](https://blog.weirdx.io/post/28723))  
왼쪽 Source Control 탭에서는 modified 파일들을 GitHub 에 commit, push 까지 할 수 있다. GitHub push 는 File -> Preferences -> Keyboard Shortcuts -> Git: Push 에 새로운 키를 할당하면 편리하다. [Using Version Control in VS Code](https://code.visualstudio.com/docs/editor/versioncontrol)   

# 플러그인
* [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)
* [Markdown Theme Kit](https://marketplace.visualstudio.com/items?itemName=ms-vscode.Theme-MarkdownKit)
* [Markdown TOC](https://marketplace.visualstudio.com/items?itemName=AlanWalk.markdown-toc)
* [Markdown Shortcuts](https://marketplace.visualstudio.com/items?itemName=mdickin.markdown-shortcuts)
 * [Liquid](https://marketplace.visualstudio.com/items?itemName=sissel.shopify-liquid)

 # 사용 안 하는 플러그인
 * [Auto-Open Markdown Preview](https://code.visualstudio.com/docs/languages/markdown) : 하다보면 preview 창이 안 나올 때가 있다. 이럴 때는 Ctrl + Shift + V 를 해도 preview 창이 보이지 않고 Visual Studio Code 를 다시 실행해야 preview 창이 보인다. Auto-Open Markdown Preview 를 uninstall 했더니 Ctrl + Shift + V 가 정상동작한다.