# linux-kernel-experiment

sys/module/workqueue/parameters
把 lockup 調小
把 intensive 調大 預設10000us

工作 20s
while loop version 如果 intensive 太大，會 lockup 卡到後續 work
如果 intensive 假設是 5秒，那麼五秒後，workqueue 就會令起 worker 處理後續 work，不 lockup，不管是不是同 wq。
           先 my_wq -> my_wq  會卡 沒 lockup (intensive 5s)
           先 system_wq -> system_wq 不會卡 沒 lockup  (intensive 5s)
           先 system_wq -> my_wq 不會卡 沒 lockup (intensive 5s)
           先 my_wq -> system_wq 不會卡 沒 lockup (intensive 5s)
工作 25s
my_wq 用 create_workqueue 創建
wq mdelay: 先 system_wq -> my_wq 不會卡 (intensive 5s)
           先 system_wq -> system_wq 不會卡 沒 lockup (intensive 5s)
           先 my_wq -> my_wq  會卡 沒 lockup (intensive 5s)
           先 my_wq -> system_wq  不會卡 沒 lockup (intensive 5s)