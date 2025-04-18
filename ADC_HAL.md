# STM32HAL

## ADC STM32

Режимы: Независимый и парный
Каналы ацп (ножки) бывают:
1. Регулярные (regular channels) - одна ячейка памяти на всех, если настроить несколько каналов новая запись будет стирать предидущую
2. Инжектированные (injected channels) - отдельные ячейки памяти, приоритет над регулярными
*В stm32 АЦП работает по методу последовательного приближения.*

#### Настройки ADC в CUBEMX
![image](https://github.com/user-attachments/assets/0e20600e-72c2-41f4-af2f-1713dd072247)
1. **Mode** - Independent mode (независимый режим) по умолчанию, если добавить больше одного АЦП, то можно выбрать парный режим
2. **Resolution** - разрешение АЦП. Min 6 Max 12 bits.
3. **Clock Prescaler** - предделитель для ADC. Частота ADC не должна превышать 14 МГц
4. **Data Alignment** - выбор выравнивания по левому или правому краю (так как регистр 16 бит, а АЦП максимум 12 бит)
5. **Scan Conversion Mode** - режим опроса нескольких каналов на одном АЦП
6. **Continious Conversion Mode** - Режим циклического опроса каналов (нет необходимости перезапуска)
7. **Discontinuous Conversion Mode** - Режим позволяет сканировать несколько каналов на одном АЦП, так чтобы опрос происходил не одновременно, а по одному или по заранее заданным группам.
8. **Enable Regular Conversions** - Enable - каналы настроены как регулярные
9. **Number Of Conversion** - количество каналов для опроса
10. **External Trigger Conversion Source** — событие, которое будет запускать АЦП. Injected Conversion launched by software - программный запуск (коллбэком).
11. **Rank** — очерёдность опроса.
12. **Channel** — номер канала. В данном случае первым будет опрашиваться канал №0, а вторым №1. Каналы можно поменять местами.
13. **Sampling Time** — время (указывается в тактах), которое тратится на заряд внутреннего конденсатора. Чем больше значение, тем точнее результат.

*АЦП работает так (очень упрощённо): поступающий сигнал сначала заряжает внутренний конденсатор, а потом происходит измерение напряжения в этом конденсаторе. Соответственно у поступающего сигнала должен быть большой ток чтоб быстро зарядить конденсатор. Если внешний сигнал слабенький, то нужно увеличить Sampling Time. Время затрачиваемое на преобразование (опрос) одного канала можно посчитать так: 1.5 такта (заряд конденсатора) + 12.5 обязательных тактов (преобразование сигнала) = 14 тактов.*

#### Работа в обычном режиме
1. Задать переменную или массив для хранения значения АЦП:
   ```
     /* USER CODE BEGIN PV */
     uint16_t adc = 0;
     /* USER CODE END PV */
   ```

2. Перед бесконечным циклом добавить калибровку АЦП:
   ```
    /* USER CODE BEGIN 2 */
    HAL_ADCEx_Calibration_Start(&hadc1);
    /* USER CODE END 2 */
   ```

3. Перед бесконечным циклом добавить калибровку АЦП:
   ```
    /* USER CODE BEGIN WHILE */
    while (1)
    {
          HAL_ADC_Start(&hadc1); // запускаем преобразование сигнала АЦП
          HAL_ADC_PollForConversion(&hadc1, 100); // ожидаем окончания преобразования
          adc = HAL_ADC_GetValue(&hadc1); // читаем полученное значение в переменную adc
          HAL_ADC_Stop(&hadc1); // останавливаем АЦП (не обязательно)
   /* USER CODE END WHILE */
   }
   ```

#### Работа в режиме прерывания

Чтобы программа не тормозилась в ожидании результата, можно воспользоваться прерыванием по окончании преобразования

1. Активация глобальных прерываний
   ![image](https://github.com/user-attachments/assets/f9f7956c-364d-491d-8256-5972f8b76cb1)

2. Добавление коллбека
   ```
    /* USER CODE BEGIN 0 */
    void HAL_ADC_ConvCpltCallback(ADC_HandleTypeDef* hadc)
    {
       if(hadc->Instance == ADC1) //check if the interrupt comes from ACD1
       {
           adc = HAL_ADC_GetValue(&hadc1);
       }
    }
    /* USER CODE END 0 */
   ```

3. Включить прерывания
   ```
    /* USER CODE BEGIN 2 */
    HAL_ADCEx_Calibration_Start(&hadc1);
    HAL_ADC_Start_IT(&hadc1);
    /* USER CODE END 2 */
   ```
   
4. Добавить включение прерывания в основной код или какую-нибудь функцию
   ```
    while (1)
         {
           snprintf(trans_str, 63, "ADC %d\n", (uint16_t)adc);
           HAL_UART_Transmit(&huart1, (uint8_t*)trans_str, strlen(trans_str), 1000);
           adc = 0;
           HAL_ADC_Start_IT(&hadc1);
           HAL_Delay(500);
           ...
   ```
   Если включить в настройках **Continious Conversion Mode** то не нужно каждый раз включать прерывания `//HAL_ADC_Start_IT(&hadc1);`
   При этом необходимо увеличить **Sampling time** (например до 13.5) чтобы опрос не происходил слишком быстро и программа не висела всегда в прерывании
   
#### Опрос при помощи DMA

1. Настраиваем DMA
![image](https://github.com/user-attachments/assets/0ec4a253-6e56-44e5-976f-425a24a4000c)

2. Добавить глобальное прерывание по DMA
![image](https://github.com/user-attachments/assets/260c5620-3e48-48d2-a3d6-9ef8f7f7e683)

3. Добавляем глобальную переменную-флаг
   ```volatile uint8_t flag = 0;```
   
4. В коллбэке поднимаем флаг (Коллбек такой же как и для IT режима)
   ```
    /* USER CODE BEGIN 0 */
    void HAL_ADC_ConvCpltCallback(ADC_HandleTypeDef* hadc)
    {
       if(hadc->Instance == ADC1)
       {
           flag = 1;
       }
    }
    /* USER CODE END 0 */
   ```
5. В бесконечном цикле или в какой-либо функции обнуляем флаг
   ```
    while(1)
      {
        if(flag)
        {
        flag = 0;

        HAL_ADC_Stop_DMA(&hadc1); // это необязательно
        adc[0] = 0;
        adc[1] = 0;
        HAL_ADC_Start_DMA(&hadc1, (uint32_t*)&adc, 2);
        }
      }
   ```
   
6. Запускаем АЦП:
   ```
    /* USER CODE BEGIN 2 */
    HAL_ADCEx_Calibration_Start(&hadc1);
    HAL_ADC_Start_DMA(&hadc1, (uint32_t*)&adc, 2); // стартуем АЦП
    /* USER CODE END 2 */
   ```

