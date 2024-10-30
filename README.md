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
   
