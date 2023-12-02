# References

> [Git 面試題 - Git 教學 | 高見龍 (gitbook.tw)](https://gitbook.tw/interview)
> 
> 上面這個還不錯 ，該看。
> 
> [《為你自己學 Git》 閱讀筆記. 人生不能重來，但 GIT 可以 （Git 篇） | by Leo Kao | Medium](https://medium.com/@leokao0726/%E7%82%BA%E4%BD%A0%E8%87%AA%E5%B7%B1%E5%AD%B8-git-%E9%96%B1%E8%AE%80%E7%AD%86%E8%A8%98-f76e4026dbce) 

## GitBook

> [Git 面試題 - Git 教學 | 高見龍 (gitbook.tw)](https://gitbook.tw/interview) 
> 
> 有很多有用的 例如

### 問：如何把好幾個 Commit 合併成同一個？

- `互動式的rebase`  >  git rebase -i

- `git reflog`  :  顯示歷史紀錄 跟log不同 !

- ```batch
  PS C:\CodeSForGit\2023WebFullStack> git reflog
  6ceb3f6 (HEAD -> master, origin/master) HEAD@{0}: commit (amend): Ch3-section37
  1002aa9 HEAD@{1}: commit (amend): Ch3-section37
  805692d HEAD@{2}: checkout: moving from 805692d1a3b3d6358215f81e549721599498732e to master
  805692d HEAD@{3}: checkout: moving from master to 805692d1a3b3d6358215f81e549721599498732e
  805692d HEAD@{4}: commit: Ch3-section37
  74948d9 HEAD@{5}: commit: Ch3-section36
  e0a188c HEAD@{6}: commit: Ch3-section35
  6a842cf HEAD@{7}: commit: Ch3-section34
  d87f1fc (origin/CH0-CH2-HTML, CH0-CH2-HTML) HEAD@{8}: commit: CH2_結束，完成到33section
  65696f3 HEAD@{9}: commit: CH2_中途上傳，內容包含到27section，預計明天會完成CH2和更多
  718dc1f HEAD@{10}: commit (initial): first commit
  PS C:\CodeSForGit\2023WebFullStack> 
  ```

- 預計要把 `805692d` 跟 `6ceb3f6` 合併成一個歷史紀錄就好

- 🔥<font style="color:lightgreen">type :wq Enter to save changes and exit vim.</font>🔥輸入`:` ，後續就可以輸入文字 `:wq` 之後`enter` 就可以離開了。

- ```batch
  noop
  pick 65696f3 CH2_中途上傳，內容包含到27section，預計明天會完成CH2和更多
  pick d87f1fc CH2_結束，完成到33section
  pick 6a842cf Ch3-section34
  pick e0a188c Ch3-section35
  pick 74948d9 Ch3-section36
  pick 6ceb3f6 Ch3-section37
  ```
  
  🔥<font style="color:lightgreen">這邊跟我預想不同耶</font> 🔥。所以`log`為主，reflog則是為輔，安全機制 ...?
  
  之前使用 amend 就已經把資料改成同一個 
  
  `reflog` 🔥 <font style="color:lightgreen">保留默認時間 90天。 </font> 🔥

## Git 清理歷史紀錄

- ```batch
  git reset --hard $commit_id
  // 上面可以強制退版
  
  git reflog expire --expire --all
  git gc --prune=now
  ```

- /pruːn/  刪減

- 不用管我這沒關係。

## (HEAD detached from 6ceb3f6)

- 不小心忘了切換回來master 就commit 

- ```batch
  PS C:\CodeSForGit\2023WebFullStack> git branch -a
  * (HEAD detached from 6ceb3f6)
    CH0-CH2-HTML
    master
    remotes/origin/CH0-CH2-HTML
    remotes/origin/master
  PS C:\CodeSForGit\2023WebFullStack> git checkout master
  Warning: you are leaving 1 commit behind, not connected to
  any of your branches:
  
    fd49d62 Ch3-section38
  
  If you want to keep it by creating a new branch, this may be a good time
  to do so with:
  
   git branch <new-branch-name> fd49d62
  
  Switched to branch 'master'
  Your branch is up to date with 'origin/master'.
  PS C:\CodeSForGit\2023WebFullStack> 
  ```

- 使用 merge 合併

- ```batch
  
  ```

## 丟棄工作區的更改 ( 還沒git add . )

- ```batch
  git checkout -- path/to/yourdir_foldername
  ```

## 如果已經staged ( git add . )

- ```batch
  git reset Head path/to/yourdir_foldername
  ```
  
  上述指令 能把staged 的對象 unstaged 
  
  可能還需要 checkout

## Commit 附加標籤

- ```batch
  git commit -a -m "Commit message" -m "Additional description"
  ```

- GPT說的，要試試看才知道 @ @🔥 <mark>上面這個最好別用</mark> 🔥

- 這樣會將兩個訊息合併到一起成為提交的訊息。第一個 `-m` 後的內容是主要的提交訊息，第二個 `-m` 後的內容則是附加的描述。 這不是tag @@

- ```batch
  git add .
  git commit -m "直接上傳囉"
  git tag <tag_name> 就可以  之後再push
  git push 
  ```

## 後續附加標籤 tag

- ```batch
  git tag -a <tag_name> -m "Tag message" <commit_hash>
  ```

## 創建分支並推送過去遠倉

- ```batch
  git branch Chapter8  左邊是章節8的意思  (因為我要依照章節做切換)
  git push origin Chapter8 這樣就能推送上去
  ```
