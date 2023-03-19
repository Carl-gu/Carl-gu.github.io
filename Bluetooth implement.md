Settings
1. based on zephyr DIS project
2. prj.conf add these configurations
	CONFIG_BT=y
	CONFIG_BT_HCI=y
	CONFIG_BT_CTLR=y
	CONFIG_BT_LL_SW_SPLIT=y
3. use command to build project
4. build in network core



hci_driver_open has been registered by hci_driver_init, and hci_driver_init will be called in system init process,
because it has been registered by SYS_INIT() in hci_driver.c
hci_driver_open -> ll_init(ull.c) -> lll_init(lll.c)

ll_init -> ticker_init()


controller_cmd_handle(in hci.c) 	case BT_OCF(BT_HCI_OP_LE_SET_ADV_ENABLE): -> 
	le_set_adv_enable -> ll_adv_enable(hci.c)
	
ll_adv_enable is implemented in ull_adv.c 
I think ll_adv_enable finally call function ticker_start(), it's a core.

ticker_start -> ticker_start_ext -> instance->sched_cb
instance->sched_cb = hal_ticker_instance0_sched(ticker.c)


Following the routine above, ticker is very important. Let's research how zephyr init ticker and how it works.
ll_init() will deliver hal_ticker_\*\*\*\*\*\*\*\*\* interface to function ticker_init()
hal_ticker_instance0_sched() will frequently call function mayfly_enqueue(), so next we need to know what does mayfly module do.

callback rtc0_nrf5_isr has been implemented in lll_init()
rtc0_nrf5_isr will frequently call mayfly_run(), and mayfly_run() will call m->fp(m->param)
value m will be assigned by function memq_peek(), and this queue named mft\[\]\[\]


next we need to find out where zephyr init rtc0:
	zephyr init rtc in nrf_rtc_timer.c, and use SYS_INIT to done this task.
	
	
some comments in code will mention "Bluetooth Specification"


radio_pkt_tx_set(),  this function is very important, if stack want to send packet in air, it must set NRF_RADIO->PACKETPTR
radio_tx_enable(), this function is also very important, because if we want to send packet in air, we must enable RADIO.
radio_isr_set(), is very important as well.
radio_freq_chan_set() <- lll_chan_set() <- chan_prepare()
