---
title: "Jekyll + GitHub 블로깅. 3. Disqus 설치 in minima skin"
date: 2020-09-08 01:52:18
categories: jekyll install
---
# minima 스킨을 jekyll 로컬에서 확인
<https://github.com/jekyll/minima> 의 [releases](https://github.com/jekyll/minima/releases) 에서 파일을 다운로드 받는다. _includes, _layouts, _sass, assets, script 등 내 폴더에 없는 모든 파일을 복사한다. _config.yml 파일도 비교해서 없는 건 추가한다. 이렇게 하면 minima 를 gem 기반으로 사용해도 로컬에 있는 파일로 override 할 수 있다.  
참고로 bundle info minima 를 하면 minima gem 이 어느 폴더에 설치되어 있는지 확인할 수 있다.  

# disqus 댓글 추가하기
[Disqus를 이용하여 Jekyll에 댓글기능 추가하기](https://17billion.github.io/jekyll/disqus/reply/2017/06/01/jekyll_disqus.html) 을 보고 Disqus 에 가입 및 서비스를 등록한다.
admin -> General 페이지에 들어가서 Shortname 을 확인한다. 예: Your website shortname is parkpd-github-io  
_config.yml 에 다음을 추가한다.  
disqus:  
&nbsp;&nbsp;shortname: parkpd-github-io  
minima-2.5.1 에서 복사해 온 _includes\disqus_comments.html 파일을 열어 https://parkpd-github-io.disqus.com/admin/install/platforms/universalcode 에서 제공하는 코드를 덮어쓰거나 s.src = 'https://parkpd-github-io.disqus.com/embed.js'; 부분에서 unique disqus name 을 자신의 disqus unique 이름으로 변경하고 {&nbsp;%- if page.comments != false and jekyll.environment == "production" -%&nbsp;} 와 {&nbsp;%- endif -%&nbsp;} 을 제거한다.  
_layouts\post.html 에

    {%- if site.disqus.shortname -%}
      {%- include disqus_comments.html -%}
    {%- endif -%}

가 들어있는지 확인한다.

# disque 를 사용하겠다고 셋팅하기
[3. 게시물에 댓글 기능을 넣어보자](https://devinlife.com/howto%20github%20pages/blog-disqus/#3-%EA%B2%8C%EC%8B%9C%EB%AC%BC%EC%97%90-%EB%8C%93%EA%B8%80-%EA%B8%B0%EB%8A%A5%EC%9D%84-%EB%84%A3%EC%96%B4%EB%B3%B4%EC%9E%90) 를 보고 _config.yml 값에 comments: true 를 추가한다. 이러면 이게 모든 _posts 의 기본값이 된다. 각 post 의 [FrontMatter](https://jekyllrb.com/docs/front-matter/) 에서 comments: false 를 입력하면 포스팅 별로 댓글을 끌 수 있다.

# 참고
[테마 | Jekyll • 심플한, 블로그 지향적, 정적 사이트](https://jekyllrb-ko.github.io/docs/themes/)