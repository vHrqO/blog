---
title: Working directory(工作區), Staging area(暫存區), Git directory(儲存庫) | 再談 Git
date: 2025-3-23
tags:
    - Git
categories:
    - 再談 Git 
keywords:
    - Git
    - Git 教學
cover: https://images.unsplash.com/photo-1594627882045-57465104050f?q=80&w=3928&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
description:
---


## 工作區、暫存區與儲存庫

Git 大致可以分成三個區，分別是 **Working Directory（工作區）**、**Staging Area（暫存區）** 和 **Git Directory（儲存庫）**，如下圖所示。
![Working Directory, staging area, and Git directory](https://i.imgur.com/fQ394cv.png)

與其對應，在 Git 中，檔案可能處於以下狀態：
- Working Directory
    - **Tracked** : 檔案已被之前的 commit 存入儲存庫，在 Git 的管理範圍內。
        - **Unmodified**：檔案內容與最後一次 commit 相同，沒有變更。
        - **Modified**：檔案內容已更改，但尚未加入 Staging area。   
    - **Untracked** : 其他不在 Repository ，也不在 Staging Area 的檔案，不在 Git 的管理範圍內。
- Staging Area
    - **Staged**：檔案已加入 Staging area，等待 commit。
- Git Directory
    - **Committed**：commit 已被提交並存入儲存庫。

### Working Directory（工作區）
**Working Directory** 可以視為 **Git Directory（儲存庫）** 的某個版本，讓開發者能夠在此進行修改和編輯。

### Staging Area（暫存區）
**Staging Area** 用來存放將要進入下一次提交的資訊。

### Git Directory（儲存庫 Repository）
這是 Git 最核心的部分，metadata 和 database 都放這，當你從其他地方 `git clone` 時，所複製的就是這個。


## 基本 Git 流程
1. 修改 Working Directory 中的檔案。
2. `git add` - 將修改的內容加入 Staging Area。
3. `git commit` - 將 Staging Area 中的檔案提交至 Repository。


## 試著使用 git 吧!

### 初始化 Repository
```bash
git init
```
> Initializing a Repository

會在目錄中建立 `.git` 資料夾，讓 Git 開始對該目錄進行版本控制。
![git init](https://i.imgur.com/yKjfkHA.png)

### 檢查狀態
```bash
git status
```
> Checking the Status of Your Files

用於查詢當前目錄的 Git 狀態，例如哪些檔案被修改、哪些檔案在暫存區等。
![git status](https://i.imgur.com/tdSR9lB.png)

### 新增檔案
我們試著加入檔案，看看有何變化

```bash
git status
```
![add file](https://i.imgur.com/vakvH56.png)

### 把檔案加入暫存區
```bash
git add <file>
```
> Staging Modified Files

將修改的檔案加入 **Staging Area**，等待提交。
![git add](https://i.imgur.com/kAuojsI.png)
![git add , status](https://i.imgur.com/IGmk12s.png)

> 📌 Note
> 為何需要 Staging Area？
> 看起來可能多此一舉，但以下的情況只要透過 Staging Area 就能順利解決:
> - 你正在寫一個功能，但被打斷去做其他開發，想先保存未完成的 code? - `git stabash`
> - 修改了多個檔案，但只想提交部分檔案？
> - 寫到一半才發現忘了切換分支? - `git stabash` → `git checkout`
>
> 還有很多情形，只要善用 Staging Area ，就能輕鬆處理這些情況。

### 提交變更
```bash
git commit -m <message>
```
> Committing Your Changes

此指令會將 Staging Area 的內容提交至 Git Directory，形成一個新的版本。
![git commit -m <message>](https://i.imgur.com/FvUOAb6.png)

> 📌 Note
`-m` 參數後的訊息是 **提交訊息**，用來記錄這次提交的變更內容，幫助自己與其他開發者了解這次的修改。
所以不要亂寫，個人建議，寫"為甚麼"，而不是"改了甚麼"，畢竟 git 已經紀錄了，原因才是重要的部分。

> 📌 Note
`git commit` 只會提交 Staging Area 中的檔案，尚未加入的檔案仍然保持 `Modified` 狀態，可在下次提交時加入。

### 新增修改
我們試著修改剛剛加入的檔案，可以看到 git 發現了我們的修改
![modify file](https://i.imgur.com/zk6U85v.png)

接著，把修改加入暫存區
![modify file add](https://i.imgur.com/nVG37se.png)

提交變更
![commit new](https://i.imgur.com/cK2JUQ9.png)

### 檢視紀錄
最後，來看看之前 Commit 的紀錄吧，可以看到透過不同參數(這裡用`--graph`)，可以有一些不同的效果。
```bash
git log
```
![git log](https://i.imgur.com/QIaiPPe.png)

從紀錄中，我們可以看得出來：
1. Commit 的作者。
2. Commit 的時間。
3. Commit 做了什麼事。

至於 Commit 後的那串，是 hash value ，可以看做是每個 Commit 唯一的 ID 。

> 現在用的是 [SHA-1](https://en.wikipedia.org/wiki/SHA-1) ，目前已經逐漸開始往 [SHA-256](https://en.wikipedia.org/wiki/SHA-2) 遷移
> - https://stackoverflow.com/questions/28159071/why-doesnt-git-use-more-modern-sha
> - https://devclass.com/2023/08/22/git-2-42-released-sha-256-repositories-no-longer-an-experimental-curiosity/
> - https://about.gitlab.com/blog/2024/08/19/gitlab-now-supports-sha256-repositories/

這裡是個使用 gui 的好時機。
![git log , ui](https://i.imgur.com/AoWTCFu.png)


# References
- https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F
- https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository


> Photo by *Svitlana* on [Unsplash](https://unsplash.com/photos/waffle-with-sliced-strawberries-and-blueberries-on-white-ceramic-plate-NJTN-oYIIY4)

<br />


