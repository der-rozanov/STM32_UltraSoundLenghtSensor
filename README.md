Проект на STM32 NucleoF401RE для работы с датчиком HC-SR04. Программа работает следующим образом:
    
    HAL_GPIO_WritePin(GPIOA, TRIG_Pin, GPIO_PIN_SET); 
    
Триггерный пин, устанваливается в высокое положение и датчик начинает испускать ультразвук 

    HAL_TIM_SET_COUNTER(&htim3, 0);
	  while (__HAL_TIM_GET_COUNTER (&htim3) < 10);

Сбрасывается значение 3его таймера и с помощью макроса __HAL_TIM_GET_COUNTER и цикла ожидается 10мкС 

    HAL_GPIO_WritePin(GPIOA, TRIG_Pin, GPIO_PIN_RESET); 

После этого значение пина сбрасывается. 

Далее сброс триггер пина устанавливает внутренний RS триггер датчка в положение 1. Снова обнуляем таймер что бы посчиать длительность импульса. 

        while (!(HAL_GPIO_ReadPin (GPIOA, ECHO_Pin)));
	  	  	   __HAL_TIM_SET_COUNTER(&htim3, 0);

    while ((HAL_GPIO_ReadPin (GPIOA, ECHO_Pin)));
	  	  	   val2 = __HAL_TIM_GET_COUNTER (&htim3);

Когда на сенсор придет отраженная волна, на вход RS триггера придет сигнал reset, на выходе эхо пина появится 0. Это сигнал к тому что бы остановить таймер. 

    distance = (val2)* 0.034/2;
	  snprintf(trans_str, 63, "ADC %d\r\n", distance);
	  HAL_UART_Transmit(&huart2, (uint8_t*)trans_str, sizeof(trans_str), 1000);

Значение которое мы померили необходимо поделить на 2 (учет полета волны туда и обратно) и умножить на скорость звука. Так мы получаем расстояние и отправляем его в COM Port
