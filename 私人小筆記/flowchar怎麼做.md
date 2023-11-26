```flowchart
start=>start: 開始
loginInfo=>inputoutput: 登錄數據
verifyLogin=>subroutine: 登錄驗證
isSuccess=>condition: 驗證成功？
respondSuccess=>operation: 響應成功
responseFailure=>operation: 響應失敗
end=>end: 結束

start->loginInfo->verifyLogin->isSuccess
isSuccess(yes)->respondSuccess->end
isSuccess(no)->responseFailure->end

start@>loginInfo({"stroke":"Red"})@>verifyLogin({"stroke":"Red"})@>isSuccess({"stroke":"Red"})@>respondSuccess({"stroke":"Red"})@>end({"stroke":"Red","stroke-width":6,"arrow-end":"classic-wide-long"})
```
