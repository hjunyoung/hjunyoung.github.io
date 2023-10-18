---
layout: single
title: 'Pull Request 만들기'
author_profile: true
date: 2023-10-15 23:00:00 +0900
categories: git
published: true
toc: false
toc_label: 'Table of Contents'
toc_icon: 'list-ul'
toc_sticky: true
sidebar:
  nav: 'docs'
# sidebar:
#   - title: 'Title'
#     image: http://placehold.it/350x250
#     image_alt: 'image'
#     text: 'Some text here.'
#   - title: 'Another Title'
#     text: 'More text here.'
---

> Pull Request 관련 자세한 내용은 역시 [Pull requests documentation](https://docs.github.com/en/pull-requests)을 참고하자.

# Pull Request

Github에서 자신의 소유가 아닌 프로젝트나 오픈 소스 프로젝트에 코드 기여를 할 때 Pull Request를 사용한다.

예전부터 많이 들어봤는데 오늘에서야 처음 PR을 만들어 봤다.

아직 익숙하지 않아서 나중에 모르겠으면 보려고 오늘 PR 했던 순서를 정리한다.

<br/>
<br/>

# PR 만드는 방법

## 1. 내가 기여하려는 repository fork 하기

Open Source project나 다른 사람의 repository의 경우 나에게 접근과 쓰기 권한이 없기 때문에 해당 repository를 fork 해 온다.

<br/>


## 2. fork한 repository를 local에 Clone 한다.

```zsh
git clone git@github.com:username/repository-name.git
```

<br/>

## 3. Clone한 repository에서 새로운 branch를 만들고 그 branch에서 commit을 한다.

```zsh
git checkout -b branch-name
```

나중에 PR을 요청할 때, 여기서 만든 branch를 해당 project로 merge하는 PR을 작성하게 된다.

<br/>

## 4. Commit한 branch를 나의 remote repository에 push 한다.

```zsh
git push origin branch-name
```

<br/>

## 5. fork한 내 GitHub repository에서 `Compare & pull request` 버튼을 눌러 PR을 작성한다.

<center>
<img src="https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/eba7a701-1961-47d0-a8e1-ec62d6585cb9" alt="" width=500/>
</center>

<br/>

- Title과 comment를 작성하고 `Create pull request`로 PR을 만든다.

<center>
<img src="https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/45631ead-b867-4c6e-9194-c2f9f8208b12" alt="" width=600/>
</center>

- PR의 title은 간결하게 적고, comment에서 자세하게 PR에 대해 설명하기

<br/>

## 6. Conflict이 없는 경우 해당 project 관리자가 승인 후 merge 가능하다.

<center>
<img src="https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/61e2c4f1-ac08-4c75-bf00-330cf132d0a0" alt="" width=600/>
</center>

<br/>


## 7. 코드에 문제가 있거나, Conflict이 발생한 경우 해당 project 관리자가 거절하거나 `Request changes`를 요청한다.

- PR 탭에서 해당 PR에 conflict이 존재함을 알려준다.

<center>
<img src="https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/b7fefe07-dcca-461a-a997-21a7dfd37cc6" alt="" width=600/>
</center>

<br/>

- GitHub PR 화면에서 `Review changes`를 선택한 후 `Request changes`를 선택해서 conflict을 해결하거나 문제 되는 코드를 수정하라고 요청한다.

<center>
<img src="https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/5edb8eab-66f3-4a4c-b5c3-89c6524d349a" alt="" width=600/>
</center>

<br/>

- GitHub PR 화면에서 `Changes requested` 표시가 뜬 것을 확인할 수 있다.

<center>
<img src="https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/3550796c-9674-484c-9ec7-a9d053ac523f" alt="" width=600/>
</center>

- Conflict을 해결한 경우 Merge할 수 있는 버튼이 다시 생성된다.

<br/>


## 8. repository를 fork한 뒤 해당 repository에 추가된 commit이 있는 경우

PR을 요청할 commit을 작성하는 동안 해당 open source project나 다른 project에 새로운 commit이 생긴 경우, rebase로 나의 repository를 최신상태로 sync한 뒤에 PR을 요청한다.

- 나의 GitHub repository에서 Sync fork로 최신상태로 update한다.

<center>
<img src="https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/fed03370-844a-4432-94a4-9370c43436e1" alt="" width=600/>
</center>

<br/>

- 최신 상태의 commit history를 local repository에 반영한다.

```zsh
# main branch 위에서
git pull
```

<br/>

- PR을 위해 commit하던 branch를 main branch의 최신 commit으로 rebase한다.

```zsh
# 해당 branch 위에서
git rebase main
```

<br/>

- Conflict이 있는 경우 해결 후 rebase를 마무리하고, 해당 branch를 push한다.

```zsh
git push origin branch-name -f
```
<br/>

-f 옵션이 없으면 local과 remote의 branch가 달라서 error가 발생한다.

혼자 작업한 remote repository고 rebase한 거라 -f option을 사용했다.

(📌 -f를 사용해서 불편하니까, 다른 방법이 있나 찾아보자)

- GitHub PR 화면에서 Merge할 수 있는 버튼이 다시 생성된다.

<br>


## 9. Merge가 되면 내 repository에서 branch를 삭제한다.

<br>
<br>

# Reference

[[GitHub] Pull Request(PR) 보내는 방법 : Contribution 하기](https://chanhuiseok.github.io/posts/git-3/)
