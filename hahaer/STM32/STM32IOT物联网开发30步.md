2025/2/11日开始
-step 17
stm32中断函数中，可以调用hal_delay延时函数吗？

在 STM32 中，**中断函数**中不建议调用 `HAL_Delay()` 函数。主要原因是 `HAL_Delay()` 函数依赖于 **SysTick** 中断来实现延时，而在中断服务程序（ISR）中，SysTick 中断会被暂停，因此调用 `HAL_Delay()` 会导致死锁或者不可预知的行为。

### 原因：

1. **SysTick 中断冲突**： `HAL_Delay()` 通过 `SysTick` 定时器（通常配置为 1 毫秒触发一次中断）来实现延时。每次进入中断时，`SysTick` 中断会被暂停，导致 `HAL_Delay()` 无法正确地计算延时，可能引起程序卡死或逻辑错误。
    
2. **中断服务程序的设计原则**： 中断函数应该尽可能简短和高效，以便尽快退出并返回主程序或其他中断。如果在中断函数中加入长时间的延时操作，可能会导致中断响应迟缓，甚至丢失其他中断。
    

### 可行的替代方案：

#### 1. **使用硬件定时器**：

你可以使用 STM32 的硬件定时器（如 TIM）来代替 `HAL_Delay()`，在中断中设置一个定时器，然后在定时器的溢出中断中执行延时任务。

例如：

```c
void TIM2_IRQHandler(void)
{
    if (HAL_TIM_ReadCapturedValue(&htim2, TIM_CHANNEL_1) != 0)
    {
        // 在此处理中断延时任务
    }
}
```

#### 2. **使用软件定时器（基于计数）**：

在中断中，你可以使用一个简单的软件计数器来模拟延时。通过记录中断发生的次数，并在达到特定次数时执行操作。

```c
volatile uint32_t counter = 0;

void TIM3_IRQHandler(void)
{
    if (__HAL_TIM_GET_FLAG(&htim3, TIM_FLAG_UPDATE) != RESET)
    {
        __HAL_TIM_CLEAR_FLAG(&htim3, TIM_FLAG_UPDATE);
        counter++;
        
        if (counter >= 1000)  // 比如延时1000个计时器周期
        {
            // 执行延时后的任务
            counter = 0;  // 重置计数器
        }
    }
}
```

#### 3. **使用软件定时器库（FreeRTOS 等）**：

如果你在项目中使用 **RTOS**（如 FreeRTOS），你可以使用 RTOS 的定时器功能来管理延时。在 FreeRTOS 中，任务的延时可以通过 `vTaskDelay()` 来实现，而不会影响中断处理程序的执行。

示例代码：

```c
void my_task(void *pvParameters)
{
    while (1)
    {
        // 执行任务代码
        vTaskDelay(pdMS_TO_TICKS(100));  // 延时 100 毫秒
    }
}
```

### 总结：

- **不推荐**在 STM32 的中断服务程序中调用 `HAL_Delay()`，因为它依赖于 `SysTick` 中断，而中断服务程序会导致该中断被暂停，从而导致延时不准确或系统阻塞。
- 推荐使用 **硬件定时器**、**软件定时器**，或者在基于 **RTOS** 的应用中使用任务延时函数来替代 `HAL_Delay()`。

-step 23
SPI协议和UART协议有什么本质不同吗？


2025/2/12日结束 
总结：这部视频没有看的必要，照本宣科，不够详细，需要自己看代码看图消化，依赖视频教程的话，反而讲解的地方很少；所以看到第25集就放弃了，整部视频约等于没看。