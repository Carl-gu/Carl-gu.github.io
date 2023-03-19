# How IRQ registered
IRQ_CONNECT   (any way to call this MACRO)
ARCH_IRQ_CONNECT    (vx.x.x/zephyr/include/zephyr/arch/arm/aarch32/irq.h)
Z_ISR_DECLARE   (vx.x.x/zephyr/include/zephyr/sw_isr_table.h)
static Z_DECL_ALIGN(struct _isr_list) Z_GENERIC_SECTION(.intList) __used _MK_ISR_NAME(func, __COUNTER__) = {irq, flags, (void *)&func, (const void *)param}

struct _isr_list {
	/** IRQ line number */
	int32_t irq;
	/** Flags for this IRQ, see ISR_FLAG_* definitions */
	int32_t flags;
	/** ISR to call */
	void *func;
	/** Parameter for non-direct IRQs */
	const void *param;
};

static 
__aligned(__alignof(struct _isr_list))struct _isr_list 
__attribute__((section(".intList")))) 
__used 
__isr_ ## func ## _irq_ ## __LINE__
{irq, flags, (void *)&func, (const void *)param}