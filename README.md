# STM32 Buzzer Frequency Control  
Drive a buzzer at different frequencies using STM32, external interrupts, and TIM2 in toggle mode.

This project demonstrates generating square waves with TIM2 to drive a buzzer.  
Two push buttons connected via EXTI interrupts allow switching the buzzer frequency between **2 kHz** and **4 kHz** by adjusting the ARR (Auto-Reload Register) value.

---

## Features
- **Square Wave Generation:** TIM2 configured in Output Compare Toggle mode.  
- **Buzzer Drive:** Buzzer connected to TIM2 channel output pin.  
- **External Interrupts (EXTI):**  
  - Button 1 (EXTI0) → sets buzzer frequency to 2 kHz.  
  - Button 2 (EXTI1) → sets buzzer frequency to 4 kHz.  
- **Dynamic ARR Update:** Frequency is changed at runtime using `__HAL_TIM_SetAutoreload()`.  
- **Simple Hardware Control:** No blocking delays, buzzer frequency switches immediately when buttons are pressed.  

---

## Hardware Setup
- **MCU:** STM32F103C8T6 (Blue Pill)  
- **Buzzer:** Connected to TIM2 Channel 1 output pin  
- **Buttons:**  
  - BTN1 → GPIOB Pin (EXTI0, Pull-up, Falling edge)  
  - BTN2 → GPIOB Pin (EXTI1, Pull-up, Falling edge)  

---

## How It Works

### Timer Configuration
- **Timer:** TIM2  
- **Prescaler:** `8 - 1`  
- **ARR Values:**  
  - `250 - 1` → generates 2 kHz square wave  
  - `125 - 1` → generates 4 kHz square wave  
- **Mode:** Output Compare (Toggle)  

### External Interrupts
- Configured on **falling edge** with pull-up resistors.  
- **HAL_GPIO_EXTI_Callback()** checks which button was pressed and updates ARR accordingly:  

```c
void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
    if(GPIO_Pin == BTN1_Pin)
    {
        __HAL_TIM_SetAutoreload(&htim2, 250);   // 2 kHz
    }
    else if(GPIO_Pin == BTN2_Pin)
    {
        __HAL_TIM_SetAutoreload(&htim2, 125);   // 4 kHz
    }
}
```

---

# Execution Flow
1. TIM2 starts generating a square wave on Channel 1.
2. The buzzer plays sound at the set frequency.
3. When a button is pressed, an EXTI interrupt is triggered.
4. Callback updates TIM2 ARR → frequency changes dynamically.

---

Notes
- The buzzer frequency depends on system clock and timer settings; ensure correct prescaler and ARR values.
- Buttons are configured with pull-up resistors; wiring must connect button to GND when pressed.
- This method can be extended to more buttons and frequencies by adding conditions in the EXTI callback.
- Toggle mode is a simple way to generate square waves for buzzers and basic audio signals.

---

Monitoring
- The buzzer will beep at 2 kHz by default.
- Press BTN1 → buzzer switches to 2 kHz.
- Press BTN2 → buzzer switches to 4 kHz.
