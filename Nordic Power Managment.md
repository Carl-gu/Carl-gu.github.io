Firstly building up your hardware, it's important. We need to exclude some factors of hardware current leakage.
https://infocenter.nordicsemi.com/topic/ug_nrf5340_dk/UG/dk/hw_measure_current.html

This page gives us important imformation and a very useful demo project(blog_lowpower.zip), because it can give you a good feedback. Get this project in the end of this page.
https://devzone.nordicsemi.com/nordic/nordic-blog/b/blog/posts/optimizing-power-on-nrf53-designs

Here is my opinion, I think it's important for us who are not familiar with a new plateform to get a pure and simple feature project to make more functions in this new plateform. So next, I will start with a simple project to impelement Low power feature.
Then, the following is my process.

1. Cutting SB40, referring [this](https://infocenter.nordicsemi.com/topic/ug_nrf5340_dk/UG/dk/hw_measure_current.html)
2. Starting with NCS V2.1.2, create a new project which using ble_peripheral(\v2.1.2\nrf\samples\bluetooth\peripheral_uart) project as a demo project.
3. Change or modify the following Configurations in prj.conf(\v2.1.2\nrf\samples\bluetooth\peripheral_uart)
CONFIG_UART_INTERRUPT_DRIVEN=y
CONFIG_UART_0_INTERRUPT_DRIVEN=n
CONFIG_UART_0_ASYNC=y
CONFIG_BT_NUS_UART_ASYNC_ADAPTER=y

CONFIG_LOG=n
CONFIG_USE_SEGGER_RTT=n
CONFIG_LOG_BACKEND_RTT=n
CONFIG_LOG_BACKEND_UART=n

CONFIG_PM=y
CONFIG_PM_DEVICE=y
CONFIG_UART_0_NRF_ASYNC_LOW_POWER=y

4. Create file(nrf5340dk_nrf5340_cpunet.conf) to folder (\v2.1.2\nrf\samples\bluetooth\peripheral_uart\child_image\hci_rpmsg\boards) and with Configurations below.
CONFIG_IPC_SERVICE=y
CONFIG_MBOX=y

CONFIG_HEAP_MEM_POOL_SIZE=8192

CONFIG_MAIN_STACK_SIZE=512
CONFIG_SYSTEM_WORKQUEUE_STACK_SIZE=512

CONFIG_BT=y
CONFIG_BT_HCI_RAW=y
CONFIG_BT_HCI_RAW_RESERVE=1
CONFIG_BT_MAX_CONN=16

CONFIG_BT_CTLR_ASSERT_HANDLER=y

# Workaround: Unable to allocate command buffer when using K_NO_WAIT since
# Host number of completed commands does not follow normal flow control.
CONFIG_BT_BUF_CMD_TX_COUNT=10

CONFIG_SERIAL=n
CONFIG_LOG=n

CONFIG_CONSOLE=n
CONFIG_UART_CONSOLE=n

5. Finally, we need to modify some codes in main.c to close uart driver to decrease power comsumption

Add two functions
#include <zephyr/pm/device.h>

static bool uart_active = false;
static void uart_enable(void)
{
	uart_active = true;
	struct uart_data_t *buf;

	pm_device_action_run(uart,PM_DEVICE_ACTION_RESUME);
	buf = k_malloc(sizeof(*buf));
	if (buf)
	{
		buf->len = 0;
	}
	else
	{
		LOG_WRN("Not able to allocate UART receive buffer");
		k_work_reschedule(&uart_work, UART_WAIT_FOR_BUF_DELAY);
		return;
	}

	uart_rx_enable(uart, buf->data, sizeof(buf->data), UART_WAIT_FOR_RX);
}

static void uart_disable(void)
{
	uart_active = false;
	uart_rx_disable(uart);
	pm_device_action_run(uart,PM_DEVICE_ACTION_SUSPEND);
}

modify in function uart_cb()
	case UART_RX_DISABLED:
		LOG_DBG("UART_RX_DISABLED");
		disable_req = false;

		if(uart_active)
		{
			buf = k_malloc(sizeof(*buf));
			if (buf) {
				buf->len = 0;
			} else {
				LOG_WRN("Not able to allocate UART receive buffer");
				k_work_reschedule(&uart_work, UART_WAIT_FOR_BUF_DELAY);
				return;
			}

			uart_rx_enable(uart, buf->data, sizeof(buf->data),
					UART_WAIT_FOR_RX);
		}
		break;
		
modify in function uart_work_handler()

	struct uart_data_t *buf;
	if(uart_active)
	{
		buf = k_malloc(sizeof(*buf));
		if (buf) {
			buf->len = 0;
		} else {
			LOG_WRN("Not able to allocate UART receive buffer");
			k_work_reschedule(&uart_work, UART_WAIT_FOR_BUF_DELAY);
			return;
		}

		uart_rx_enable(uart, buf->data, sizeof(buf->data), UART_WAIT_FOR_RX);
	}
	

modify in function uart_init()
	err = uart_tx(uart, tx->data, tx->len, SYS_FOREVER_MS);
	if (err) {
		LOG_ERR("Cannot display welcome message (err: %d)", err);
		return err;
	}

	// return uart_rx_enable(uart, rx->data, sizeof(rx->data), 50);
	return err;
	
add one line in function connected()
	char addr[BT_ADDR_LE_STR_LEN];

	if (err) {
		LOG_ERR("Connection failed (err %u)", err);
		return;
	}

	bt_addr_le_to_str(bt_conn_get_dst(conn), addr, sizeof(addr));
	LOG_INF("Connected %s", addr);

	current_conn = bt_conn_ref(conn);

	dk_set_led_on(CON_STATUS_LED);
+++	uart_enable();


add one line in function disconnected()
	char addr[BT_ADDR_LE_STR_LEN];

	bt_addr_le_to_str(bt_conn_get_dst(conn), addr, sizeof(addr));

	LOG_INF("Disconnected: %s (reason %u)", addr, reason);

	if (auth_conn) {
		bt_conn_unref(auth_conn);
		auth_conn = NULL;
	}

	if (current_conn) {
		bt_conn_unref(current_conn);
		current_conn = NULL;
		dk_set_led_off(CON_STATUS_LED);
+++		uart_disable();
	}


change advertising interval to 2s in function main()
	err = bt_le_adv_start(BT_LE_ADV_PARAM(BT_LE_ADV_OPT_CONNECTABLE, \
				       0x00a0*20, \
				       0x00f0*20, NULL), ad, ARRAY_SIZE(ad), sd,
			      ARRAY_SIZE(sd));
				  
				  
				  
You can also see my .diff file. (Nordic Power Managment - optimize power consumption patch.diff)


6. When you finish those steps above, we can get idle currunt 3.1uA ~ 4.2uA, because of different hardware has different performance.