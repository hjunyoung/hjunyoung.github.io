---
layout: single
title: 'GitHub flow'
author_profile: true
date: 2023-10-16 00:00:00 +0900
categories: git
published: true
toc: true
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

# 0. Branch 관리가 필요한 이유

처음 여러 사람들과 github으로 같이 미니 프로젝트를 만들 때, branch에 대해 잘 이해하지 못해서 main branch 하나에서 작업을 했는데, 불편한 점이 한두개가 아니었다.

1. 다른 기능들에 대한 commit들이 중구난방으로 main branch에 올라와서 관리하기가 어렵고, 각각의 기능의 진행 상황 파악도 어렵다.
2. commit이 중구난방이라서 문제가 생겼을 때 문제있는 commit을 찾고 revert하기도 어렵다.
3. 동일한 파일을 수정한 경우 commit 할 때마다 conflict이 발생한다.

<br>

다른 여러가지 문제가 있겠지만, 이런 문제들을 해결하고 프로젝트를 더 효율적으로 관리하고자 branch를 사용한다.

Branch를 나눠서 작업을 하게 되면, 위에서 나온 문제를 해결할 수 있다.

1. Branch를 나눠서 작업하면 다른 branch와 독립적으로 작업을 할 수 있다. 따라서 다른 branch에서 같은 파일이나 line을 수정해도 merge하지 전까진 conflict이 나지 않는다.
2. Branch에서 혼자 작업하는 경우에 한해서, 그 branch의 commit들에 local에서 하던 rebase나 수정이 가능하다(자세한 확인 필요)
3. 한 기능이나 bug에한 수정이 완료된 후에 merge하기 때문에 기능별 버그별로 추적하고 관리하기 편하다.

<br>
<br>

# 1. Branch는 어떻게 만들어야할까?

main에서만 작업하면 불편한 것은 경험했는데, 이제 어떻게 branch를 사용해야할까?

Branch의 효과적인 관리를 위해 사용되는 여러가지 기준(Git flow, GitHub flow, GitLab flow 등)이 있다. Git flow는 아직까지는 사용계획이 없고 복잡해서, 이번에는 내가 사용할 예정인 GitHub flow에 대해 정리하려고 한다.

> 버전관리가 필요한 앱에서는 Git flow가 적합한 반면, CI/CD로 관리되고 최신 버전만 사용하는 웹앱의 경우 Git Flow는 적합하지 않다고 한다. Release 개념이 모호한 웹앱의 경우 GitHub flow가 적합하다고 한다.

<br>
<br>

# 2. GitHub flow 순서

- Create a branch

  새로운 기능, 버그, refactoring 등 특정 작업에 대한 branch를 만든다.

  git flow와 다르게 feature branch나 develop branch가 존재하지 않으므로, 새롭게 만드는 branch의 이름은 작업하는 내용을 잘 나타낼 수 있는 이름으로 정해주어야 한다.
  e.g. `increase-test-timeout`, `add-code-of-conduct`

- Make changes

  특정 작업은 완료할 때까지 branch 위에서 수정사항을 commit한다.

- Create a pull request

  Code review와 feedback을 받기 위해 PR을 생성한다.
  commit 전 feedback이나 advice를 받기 위해 PR을 생성할 수도 있다.

- Address review comments

  Code review 후에 추가 수정한다.

- Merge PR

  PR이 승인되면 default branch에 merge한다.

- [Delete your branch](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-branches-in-your-repository/deleting-and-restoring-branches-in-a-pull-request)

  해당 branch 위에서 작업이 끝났음을 알리고, 추후에 이 오래된 branch를 사용하지 않도록 branch를 삭제해준다.

<br>

![GitHub flow](https://github.com/hjunyoung/hjunyoung.github.io/assets/50318500/c29e808e-21ca-432b-b8df-0d69f8fe6c61)

<br>

내용을 보면 GitHub에서 PR을 만들고 승인하고 merge하는 과정과 동일하다.

기능별로 하나의 branch를 만들어서 작업하고 PR만 하는 과정이므로, feature/release/develop 등 다양한 branch를 관리하는 것보다 훨씬 간단하다.

<!-- # 각각의 branch에서 어떤 merge를 해야할까? -->

<br>
<br>


# Reference

[GitHub flow](https://docs.github.com/en/get-started/quickstart/github-flow)
[GitHub Flow - The best way to use Git and GitHub](https://githubflow.github.io/)
[Git flow, GitHub flow, GitLab flow](https://ujuc.github.io/2015/12/16/git-flow-github-flow-gitlab-flow/)
[Git 브랜치 전략 (feat. Git Flow, Github Flow)](https://hudi.blog/git-branch-strategy/)
