# 死結

四個條件滿足 4 score 就發生DEAD LOCK 

- `no preemption` /ˌpriːˈemp.ʃən/
  
  - 搶在...之前行動或說話
  
  - 如果沒有辦法系統強制某人退出就+1 score 

- `hold and wait`
  
  - 持有資源且等待其他資源才願意放手

- `mutual exclusion`
  
  - 互斥 只能由某人取得該資源
  
  - 有點類似HTML radio  name="resource"的感覺

- `circular waiting`
  
  - 互相持有對方需要的資源因此互相等待
