# Инструкция по работе в keil

## Содержание

[1. Git, расположение, интерфейс keil](#ann)

[2. Перепрошивка МК](#prog)

[3. Режим отладки](#debug)


<a name="ann"><h2>1. Git, расположение, интерфейс keil</h2></a>

Проект находится на [bitbucket](https://bitbucket.inviewlab.com/projects/FLIFTBOT/repos/stm32_lift/browse?at=refs%2Fheads%2Fpid-ver). Рабочая ветка __pid-ver__.

[Информация о работе в гите](https://github.com/denismox/STM32HAL/blob/main/git.md)

На рабочем столе есть папка __stm32_lift__ с проектом

![image](https://github.com/user-attachments/assets/5b33ffd6-4bae-40e4-bca1-70bc12e93bb9)

Чтобы открыть проект необходимо открыть файл F407_USB_CDC (файл с расширением _.uvprofx_). Расположение файла ```C:\Users\leusa\Desktop\stm32_lift\MDK-ARM```

![image](https://github.com/user-attachments/assets/f58b1d43-c626-4bd7-90b5-d7aad680407c)

Рабочее окно keil:

![image](https://github.com/user-attachments/assets/f4badd6e-15c0-408f-99a7-fcb634cfaddc)

Директория и файлы:

![image](https://github.com/user-attachments/assets/937eb8c6-8809-4c2a-adba-7efa3344c280)

Основные файлы проекта: __main.c__ и __stm32f4xx_it.c__.

![image](https://github.com/user-attachments/assets/9aa380e5-91ef-46a4-ba50-02132b0055b0)

Навигация по файлам. Смена файла одним нажатием.

![image](https://github.com/user-attachments/assets/2ae0462b-9295-4f47-b643-6c87828cd4bb)

<a name="prog"><h2>1. Перепрошивка МК</h2></a>

__Перед перепрошивкой в коде программы необходимо выбрать модель погрузчика!__

Переход между строками _CTRL + G_

В main.c файле строка 37-38 (раскомментировать нужное):

![image](https://github.com/user-attachments/assets/3415f363-9dae-441c-bae8-c0adb1aaaa61)

В stm32f4xx_it.c файле строка 45-46:

![image](https://github.com/user-attachments/assets/56fcec32-c428-41cd-add6-63d375e6a280)

Если появится необходимость сменить автокалибровку на ручную калибровку (мультиметром), то изменить значение переменной wheel на 0.

![image](https://github.com/user-attachments/assets/f361986a-c37b-4ee3-85b3-1cbc4ede9d48)

Когда код готов нужно сделать rebuild всего проекта

![image](https://github.com/user-attachments/assets/9f78ccd8-eba0-404a-88f0-3fbcc804ddc1)

Если в коде нет ошибок, то то в аутпут области будет сообщение об отсутствии ошибок (иначе исправлять ошибки)

![image](https://github.com/user-attachments/assets/d0ba44e8-4dde-4300-a6fb-8721afbf3413)

Далее нужно загрузить прошивку на МК кнопкой Load (F8)

![image](https://github.com/user-attachments/assets/83209249-d1ca-40b9-8075-97860d64d81d)

При успешной перепрошивке будет сообщение об успехе

![image](https://github.com/user-attachments/assets/42ec04a9-365b-4856-890c-30530b468766)

<a name="prog"><h2>3. Режим отладки</h2></a>

Чтобы войти в режим отладки необходимо загрузить код на МК (см. пункт 2). Нажать на кнопку debug (ctrl + F5).

![image](https://github.com/user-attachments/assets/ec8e99cc-f3e0-4eab-8a81-7c21a06acbfd)

В данный момент мы зашли в режим отладки, но программа еще не запущена. В нижней части экрана распологаются значения переменных, можно добавить свою (отслеживает только глобальные)

![image](https://github.com/user-attachments/assets/321057ac-a2ef-46b2-b9a6-b1445a331a76)

Точки останова можно поставить на полях программы при помощи ЛКМ. 

![image](https://github.com/user-attachments/assets/85933016-12b1-4b7c-9721-390105177a43)

Убрать точки можно как через ЛКМ, так и кнопки kill all breakpoints (ctrl + shift + f9)

![image](https://github.com/user-attachments/assets/501cb45b-38c8-4bb0-8742-ca80a0e9e4f8)

Запустить программу можно при помощи кнопки _Run_ (F5)

![image](https://github.com/user-attachments/assets/33d606c6-32cd-4422-aef4-5368a8ce7474)

Reset контроллера по кнопке на самой плате или при помощи кнопки _reset_ в keil

![image](https://github.com/user-attachments/assets/6235aa85-dc94-4f12-b890-2673a12f1742)

Остановить программу при помощи кнопки _stop_

![image](https://github.com/user-attachments/assets/701acdb7-58bc-4caa-a0d8-3730db64d3c6)

После остановки текущее место выполнения программы будет отмечено желтым треугольником. Программа может остановиться сама, если были выставлены точки останова.

![image](https://github.com/user-attachments/assets/7a4b3e19-fc69-4f28-8330-3b493d83c700)













