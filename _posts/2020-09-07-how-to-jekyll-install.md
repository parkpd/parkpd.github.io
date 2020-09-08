---
title: "Jekyll + GitHub 블로깅. 2. Jekyll 설치"
date: 2020-09-07 02:00:00
categories: jekyll install
---
Windows 기준으로 Jekyll + GitHub 블로깅을 하기 위한 준비과정을 정리한다.  
# Ruby 설치
<https://rubyinstaller.org/downloads> 에서 Ruby+Devkit 2.7.1-1 (x64) 설치.  
> Ruby Installer2 for Windows 에서는 3 - MSYS2 and MINGW development toolchain 을 선택했다.  
> gem 설치가 꼬이면 Ruby 를 uninstall 해서 gem 를 리셋한 뒤에 다시 Ruby Installer2 를 설치한다.  

# [jekyll](https://jekyllrb.com) 설치 및 로컬에서 실행
Git Bash 에서 parkpd.github.io 폴더로 이동  
gem list 해보면 이미 bundler (default: 2.1.4) 가 설치되어 있다.  
$ gem install bundler 하면 gem list 했을 때 bundler (default: 2.1.4) 가 아닌 bundler (2.1.4) 으로 표시된다. 괜찮나?  
[GitHub Pages - Dependency versions](https://pages.github.com/versions) 를 보면 jekyll 3.9.0 으로 되어 있다.  
$ gem install jekyll -v 3.9.0
> $ gem install jekyll : 버전 지정하지 않으면 jekyll (4.1.1) 이 설치되는데 내가 해 봤을 때는 dependency 오류가 발생했다. gem 에 익숙하다면 해결할 수 있겠지만...  

$ jekyll -v 했을 때 오류가 발생한다면
> $ bundle update : 필요한 다른 gem 을 설치한다.  
> $ jekyll -v 했을 때 정상 실행되는지부터 확인. jekyll 3.9.0  

$ jekyll new . --force (이미 폴더가 있기 때문에 --force 옵션이 필요하다)  
> repo-name 폴더에 _config.yml, Gemfile 파일, _posts 폴더 같은 게 생성되는지 확인한다.

새로 생성된 [Gemfile](https://bundler.io/v2.1/gemfile.html) 을 열어서 \# gem "jekyll", "~> 3.9.0" 으로 주석 처리하고, gem "github-pages", group: :jekyll_plugins 으로 주석 제거한 뒤 저장한다.  
$ gem install github-pages  
$ bundle update  
이 때부터 $ jekyll -v 발생하면 그냥 $ bundle exec 를 앞에 붙여서 $ bundle exec jekyll -v 으로 실행한다.  

$ bundle exec jekyll -v 했을 때 버전 잘 보이는지 확인하고
$ bundle exec jekyll build  
$ bundle exec jekyll serve --watch  

웹브라우저로 http://localhost:4000/ 에 접속해 블로그가 잘 보이는지 확인한다. --watch 옵션으로 실행했기 때문에 변경사항이 실시간으로 반영된다.