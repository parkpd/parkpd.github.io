---
layout: post
title: "Jekyll + GitHub 블로깅. 1. GitHub 부터 준비"
date: 2020-09-07 17:52:18 +0900
categories: jekyll install
---
Windows 기준으로 Jekyll + GitHub 블로깅을 하기 위한 준비과정을 정리한다.  
# github 계정 만들고 Git for Windows 설치
<https://git-scm.com/> 에서 git 받아서 설치한다.  
GitHub 에 계정은 만들었다고 가정한다. 로그인 한 뒤 우측 상단 + 버튼을 눌러 New repository 를 선택한다.  
user.github.io 이름으로 repository 를 만든다.(일단 이름은 이 형태만 가능하다고 생각하자.) Add a README file 도 체크한 뒤 Create repository 를 누른다. [Creating a repository for your site](https://docs.github.com/en/github/working-with-github-pages/creating-a-github-pages-site-with-jekyll#creating-a-repository-for-your-site) 참고  
(내 블로그를 기준으로) <https://github.com/parkpd/parkpd.github.io> 와 <https://parkpd.github.io> 가 접속이 잘 되는지 확인한다.

# Git 연결 및 인증
Git 을 저장할 로컬 폴더에서 git clone https://github.com/parkpd/parkpd.github.io 을 실행한다. parkpd.github.io 폴더가 추가되는지 확인한다. parkpd.github.io 폴더에서 우클릭 Git Bash Here 로 Git 콘솔에 들어간다.

(Git 인증 과정 진행 안 한 경우)  
parkpd.github.io 폴더에 파일 하나 추가 혹은 변경한 뒤
$ git add --all  
$ git commit -m "..."  
하면 *** Please tell me who you are. 가 출력된다. 이 때는 출력된 내용을 보고  
$ git config --global user.name 과  
$ git config --global user.email 을 입력해 준다.  
이제는 $ git commit -m "..." 이 성공한다.  
다음으로 $ git push 하면 GitHub Login 창이 나온다. 여기에서 username (parkpd) 와 암호를 입력하면 push 가 성공한다. 이후로는 GitHub Login 정보가 저장되므로 이 과정은 반복하지 않아도 된다.