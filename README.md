# linux-kernel-experiment

sys/module/workqueue/parameters
把 lockup 調小 10sec
把 intensive 調大 預設10000us -> 15sec
wq_cpu_intensive_thresh_us

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


要故意製造 lockup 需要 intensive = 15s, lockup = 7s
看起來四種組合卡或不卡都會 lockup 在這種config 下，畢竟 intensive 大

塞同個wq 會 inactive 但可能還是會卡到別條 wq lockup 因為同一條 wq max_active 預設 1
總結不同條 workqueue 只要超過intensive 不會卡 