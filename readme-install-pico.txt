Инструкция по проверка плат управления:

0) Скачать среду разработки: https://thonny.org/

1) Установить micropython на pi pico:
    а) подключить плату к компьютеру 
    б) перекинуть файл "rp2-pico-20210902-v1.17" на появившийся внешний диск, после чего плата перезагрузиться.

2) Открыть среду thonny, после чего в меню "выполнить", выбрать пункт "Выберите интерпретатор, после чего должно открыться окно в котором
   необходимо указать в качестве интерпретатора "micropython (общий)", также необходимо указать порт к которому подключена pi pico.

3) Запустить в среде разработки следующие юнит тесты:
    a) unit_test_servo_cam.py - тест сервопривода камеры
        Ожидаемый результат: сервопривод должен циклически поворачиваться из одной стороны в другую
    б) unit_test_motor.py - тест нулевого мотора 
        (для работы двигателей необходимо подключить питание +12v)
        Ожидаемый результат: должен активироваться мотор подключенный к нулевому разъему
    в) unit_test_serial_port.py - тест модуля rs485 
        Ожидаемый результат: на модуле rs485 (синяя плата), должен начать мигать один из светодиодов
    г) unit_test_volt_and_amper.py - тест вольтметра и амперметра
        Ожидаемый результат: Скрипт должен вывести примерное напряжение +- 1 вольт, ток может отличаться. 
        Для корректной работы необходима калибровка. 
    

4) отчистить все вайлы на raspberry pico, после чего скопировать на нее все содержимое папки /SoftAcademic/pico-apparat 

5) после окончаания загрузки скрипт будет запускаться автоматически при подключении питания в плате 

При приёме частота у нас меняется незначительно. Максимальный доплеровский сдвиг достигал 3 kHz, при неизменной ширине канала в 38 kHz, это менее 8 процентов, если учесть что сдвиг максимален на влёте спутника в зону видимости и уходе за горизонт, когда усиления антенны не хватает для приёма, то этот сдвиг оказывается ещё меньше. Плюс к этому при демодулировании сигнала Gnu Radio немного подстраивает частоту, то учитывая все эти факторы мы провели эксперимент и  выяснилось что для хорошего приёма частоту двигать не надо, так как уже говорил, что сдиг незначителен и автоматически компенсируется. Sample rate на изменять тоже нельзя. если в SDR# аудиопоток выдаётся в устройство вывода согласно его частоте дискретизации, то аудиофайл с сигналом должен быть минимум унифицированный, максимум поддаваться лёгкой конвертации в другую частоту дискретизации. Также если учесть что RTL-SDR и AIRSPY могут работать с разной шириной спектра (sample rate) и частоту можно  только кратно понижать, то имеем, что AIRSPY работает на 3MHz, а RTL-SDR на 250 kHz, для того, чтобы итоговый файл не различался и имел частоту дискретизации в 50 kHz. RF gain изменять надо для получения лучшего сигнала, но чтобы получить лучший сигнал при его низком уровне мы поднимаем усиление, чтобы уго громкость была выше, а при чрезмерной громкости или появлении внешних помех в диапазоне работы уменьшаем усиление. Помех в диапазоне появляться не может, т.к. это выделенный диапазон для метеорологических спутников, а громкость нам неважна для частотной демодуляции. И в итоге нам надо только повысить усиление в начале приёма, для получения сигнала раньше и следовательно получить большее изображение, тогда зачем нам играть усилением если мы можем выставить почти максимальное и прекрасно принимать. Offset. если речь шла про offset tuning, то вот выдержка с сайта rtl-sdr.ru [Offset Tuning]- Это полезная опция для владельцев тюнера E4000. Переключает режим работы входа RTL с нулевой частоты на промежуточную не нулевую. Установка этой галочки позволяет полностью избавится от палки посередине экрана. Для 820 тюнеров эта опция безразлична и игнорируется в коде драйвера а мы как раз используем RTL-SDR на чипе R820T2 и она полностью бесполезна. Также подобной настройки нет в драйвере под линукс, т.к. даже если бы она там была она ничего бы не сделала. Также для AIRSPY нам тоже это не надо, т.к. полосы посередине нет, а спектр чистый.

Начнем с того что вам давали инструкцию ? Просто у нас сейчас в штатном комлекте софта который предлагается для установки пользователю нет программы SDR Studio, а использается программа SDR#. И Именно под программу SDR# преднозначена плагин DDE который 
позволяет получать из Орбитрона актуальную частоту спутника. То что вы назвали смещением которое надо подбирать руками на самом деле давно известный эффект доплера который изменяет частоту и программа Орбитрон умеет расчитывать это смещение относительно
центральной частоты спутника. Если вы изучали инструкцию по приему то сейчас даже на существуемом софте, прием заключается в том что надо запустить три программы, выбрать спутник и нажать одну кнопку. Изменение bandwidth так же является абсолютно нецелесообразным
так как с спутника идет фиксированная полоса 38 kHz. Она статична для всех НОАА. Параметр sample rate отвечает за частоту дискретизации сигнала. Для приемника RLT SDR Лучше всего использовать 2.4 MHz. Менять ее в процессе приема это просто усложнение работы декодировщику 
в качестве которого у нас используется WX.  RF gain изменять надо для получения лучшего сигнала, но чтобы получить лучший сигнал при его низком уровне мы поднимаем усиление, чтобы уго громкость была выше, а при чрезмерной громкости или появлении внешних помех в диапазоне работы уменьшаем усиление. Помех в диапазоне появляться не может, т.к. это выделенный диапазон для метеорологических спутников, а громкость нам неважна для частотной демодуляции. И в итоге нам надо только повысить усиление в начале приёма, для получения сигнала раньше и следовательно получить большее изображение, тогда зачем нам играть усилением если мы можем выставить почти максимальное и прекрасно принимать.Offset. если речь шла про offset tuning, то вот выдержка с сайта rtl-sdr.ru [Offset Tuning]- Это полезная опция для владельцев тюнера E4000. Переключает режим работы входа RTL с нулевой частоты на промежуточную не нулевую. Установка этой галочки позволяет полностью избавится от палки посередине экрана. Для 820 тюнеров эта опция безразлична и игнорируется в коде драйвера а мы как раз используем RTL-SDR на чипе R820T2 и она полностью бесполезна. Резюмируя для нового софта критичные настройки это именно настройки координат так, как это влияет на расписание пролетов, и можно автоматизировать только с помощью установки дополнительного Gps датчика. Тогда для приема необходимо будет просто запустить софт. 