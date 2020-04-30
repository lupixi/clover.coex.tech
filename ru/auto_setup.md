# Пошаговая инструкция по настройке автономного полета Клевера 4

Данная инструкция содержит ссылки на другие статьи, в которых каждая из затронутых тем разобрана более подробно. Если вы столкнулись с трудностями во время прочтения одной из таких статей, рекомендуется вернуться к данной инструкции, так как здесь многие операции описаны пошагово, а также отсутствуют ненужные шаги.

## Первоначальная настройка Raspberry Pi

- Установите Raspberry Pi и камеру на квадрокоптер по [инструкции](assemble_3.md#монтаж-raspberry).
- Скачайте образ системы по [ссылке](image.md).
- Запишите образ на MicroSD карту.
- Вставьте карту в Raspberry Pi.
- Подключите питание к Raspberry Pi и ожидайте появления Wi-Fi-сети. Для этого подключите Raspberry Pi к компьютеру через MicroUSB-кабель.
  На Raspberry Pi должен периодически мигать зеленый светодиод. Он сигнализирует о нормальной работе операционной системы.

  > **Warning** Перед подключением Raspberry Pi к компьютеру по USB необходимо вытащить из Raspberry Pi провод питания (который идет от BEC). Иначе могут быть проблемы с питанием.

- Подключитесь к Wi-Fi и зайдите в веб-интерфейс ([статья](wifi.md)).

  Во время первого включения сеть появляется не сразу. Нужно дождаться полной загрузки системы. Если в списке сетей долго не появляется сети Клевера, закройте окно с выбором сети и откройте снова. Тогда список сетей обновится.

  > **Hint** Если на этом шаге вы подключились к Wi-Fi-сети коптера, рекомендуется открыть [локальную версию этой статьи](http://192.168.11.1/docs/ru/auto_setup.html), иначе ссылки не будут работать.

- Подключитесь к Raspberry Pi по SSH.

  Самый быстрый способ – веб-доступ. Следуйте инструкциям в статье "[Доступ по SSH](ssh.md#веб-доступ)".

- Если необходимо, можно поменять название и пароль сети. См. статью "[Настройка сети](network.md#изменение-пароля-или-ssid-имени-сети)". Остальные операции с сетью производить не нужно.

- Для редактирования файлов пользуйтесь редактором nano. [Инструкция по работе с редактором](cli.md#editing).

  > **Hint** В редакторе перемещать курсор можно только стрелками на клавиатуре.

- Перезагрузите Raspberry Pi:

  ```bash
  sudo reboot
  ```

  Соединение временно закроется, создастся новая сеть. К ней надо подключиться заново.

- Убедитесь в корректной работе камеры. В браузере зайдите на адрес http://192.168.11.1:8080 и выберите `image_raw`.

  Более подробно можно прочитать в статье "[Просмотр изображений с камер](web_video_server.md#просмотр)".

  Если изображение размыто, необходимо сфокусировать линзу. Для этого покрутите объектив в одну или в другую сторону. Продолжайте крутить, пока изображение не станет четким.

  > **Hint** На камере должен гореть красный светодиод: он означает, что камера в данный момент производит съемку. Если светодиод не горит: либо камера подключена неправильно, либо операционная система не загрузилась, либо в настройках допущена ошибка.

## Базовые команды

Вам пригодятся основные команды Linux, а также специальные команды Clover, чтобы уверенно работать в системе.

Показать список файлов:

```bash
ls
```

Перейти в папку с прописыванием пути к ней:

```bash
cd catkin_ws/src/clever/clever/launch/
```

Перейти в домашнюю директорию:

```bash
cd
```

Открыть файл file.py:

```bash
nano file.py
```

Открыть файл clever.launch с прописыванием полного пути к нему (сработает, если вы находитесь в другой папке):

```bash
nano ~/catkin_ws/src/clever/clever/launch/clever.launch
```

Сохранить файл (нажимать последовательно):

```bash
Ctrl+X; Y; Enter
```

Удалить файл или папку с названием name (ВНИМАНИЕ: операция выполнится без подтверждения. Будьте осторожны!):

```bash
rm -rf name
```

Создать папку с названием myfolder:

```bash
mkdir myfolder
```

Полная перезагрузка Raspberry Pi:

```bash
sudo reboot
```

Перезапуск только систем Клевера:

```bash
sudo systemctl restart clever
```

Выполнить самопроверку Клевера:

```bash
rosrun clever selfcheck.py
```

Остановить программу

```bash
Ctrl+C
```

Запустить программу myprogram.py на Питоне:

```bash
python myprogram.py
```

Журнал событий процессов Клевера. Пролистывать список можно нажатием Enter или сочетанием клавиш Ctrl+V (пролистывает быстрее):

```bash
journalctl -u clever
```

Открыть файл sudoers от имени администратора (он не откроется без прописывания sudo. Через sudo можно запускать другие команды, если они не открываются без прав администратора):

```bash
sudo nano /etc/sudoers
```

## Настройка параметров Raspberry Pi для автономного полета

Большинство параметров, необходимых для полета, хранится в папке `~/catkin_ws/src/clever/clever/launch/`.

- Зайти в папку:

  ```bash
  cd ~/catkin_ws/src/clever/clever/launch/
  ```

  Символ `~` обозначает домашнюю директорию вашего пользователя. Если вы уже находитесь в ней, можно обойтись командой:
`cd catkin_ws/src/clever/clever/launch/`

  > **Hint** Клавишей Tab можно автоматически дополнить названия файлов, папок или команд. Нужно начать вводить желаемое название и нажать Tab. Если не будет конфликтов, название напишется полностью. Например, чтобы быстро ввести путь к папке с настройками, после ввода `cd` можно начать вводить следующую комбинацию клавиш: `c-Tab-s-Tab-c-Tab-c-Tab-l-Tab`. Таким образом можно сэкономить много времени при написании длинной команды, а также избежать возможных ошибок в написании пути.

- В этой папке необходимо сконфигурировать несколько файлов:

  - `clever.launch`
  - `aruco.launch`
  - `main_camera.launch`

- Открыть файл `clever.launch`:

  ```bash
  nano clever.launch
  ```

  Вы должны находиться в папке, в которой располагается файл. Если вы находитесь в другой папке, файл можно открыть, прописав полный путь к нему:

  ```bash
  nano ~/catkin_ws/src/clever/clever/launch/clever.launch
  ```

  Если файл одновременно редактируют два пользователя, а также если в прошлый раз закрытие файла произошло некорректно, программа nano не отобразит файл сразу, а попросит дополнительное разрешение. Для этого нужно нажать клавишу Y.

  Если содержимое файла все равно пусто, возможно, вы неверно ввели имя файла. Нужно обращать внимание на расширение и вписывать его полностью. Если вы вписали неверное имя или расширение, программа nano создаст пустой файл с этим названием, что нежелательно. Такой файл следует удалить.

- В файле clever.launch найти строчку:

  ```
  <arg name="aruco" default="false"/>
  ```

  и заменить `false` на `true`:

  ```
  <arg name="aruco" default="true"/>.
  ```

  Это активирует модуль распознавания ArUco-маркеров.
- Откройте файл `aruco.launch`.
- В нем нужно активировать несколько параметров. Подробнее в [статье](aruco_map.md).

  Должно получиться:

  ```
  <arg name="aruco_detect" default="true"/>
  <arg name="aruco_map" default="true"/>
  <arg name="aruco_vpe" default="true"/>`
  ```

- Сгенерируйте поле с метками. Смотрите подробности в статье [Навигация по картам ArUco-маркеров](aruco_map.md#настройка-карты-маркеров). Для генерации меток нужно ввести команду с определенными значениями.

  Пример команды для генерации поля, где:

  - длина маркера = 0.335 м (`length`)
  - 10 столбцов (x)
  - 10 строк (y)
  - расстояние между центрами меток по оси x = 1 м (`dist_x`)
  - расстояние между центрами меток по оси y = 1 м (`dist_y`)
  - номер первого маркера = 0 (`first`)
  - название карты остается стандартным: map.txt
  - нумерация идет с верхнего левого угла (ключ `--top-left`)

  ```bash
  rosrun aruco_pose genmap.py 0.335 10 10 1 1 0 > ~/catkin_ws/src/clever/aruco_pose/map/map.txt --top-left
  ```

  В большинстве полей нумерация начинается с нулевой метки. Также в большинстве случаев нумерация начинается с верхнего левого угла, поэтому при генерации очень важно указывать ключ `--top-left`.

  > **Hint** Если вы зададите другое имя для файла с картой, его нужно прописать в файле `aruco.launch`. Найдите строку
`<param name="map" value="$(find aruco_pose)/map/map.txt"/>`
и замените название map.txt на название вашего файла.

- Отредактируйте файл `main_camera.launch` для настройки камеры:

  Подробнее в статье "[Настройка расположения основной камеры](camera_setup.md#frame)".

  В этом файле необходимо отредактировать строку с параметрами расположения камеры. Строка выглядит так:

  ```xml
  <node pkg="tf2_ros" type="static_transform_publisher" name="main_camera_frame" args="0.05 0 -0.07 -1.5707963 0 3.1415926 base_link main_camera_optical"/>
  ```

  В файле вы найдете много строк, похожих на эту, но большинство из них закомментированы (то есть не читаются) и только одна раскомментирована. Это заранее заготовленные настройки, из которых можно выбрать нужную вам.

  Комментарий в языке XML — это символы `<!--` в начале строки и `-->` в конце строки. Пример закомментированной строки:

  ```xml
  <!--<node pkg="tf2_ros" type="static_transform_publisher" name="main_camera_frame" args="0.05 0 -0.07 -1.5707963 0 3.1415926 base_link main_camera_optical"/>-->
  ```

  Пример незакомментированной строки (строка будет учитываться программой):

  ```xml
  <node pkg="tf2_ros" type="static_transform_publisher" name="main_camera_frame" args="0.05 0 -0.07 -1.5707963 0 3.1415926 base_link main_camera_optical"/>
  ```

  Над этими строками написано, какому расположению камеры соответствует настройка. Если шлейф от камеры выходит вперед относительно коптера, а камера направлена вниз, нужно выбрать настройку:

  ```xml
  <!-- camera is oriented downward, camera cable goes forward  [option 2] -->
  ```

  Чтобы выбрать нужную настройку, необходимо раскомментировать соответствующую строку, и закомментировать другую аналогичную строку, чтобы не возникло конфликтов.

- Сохранить изменения. Последовательно нажмите:

  ```
  Ctrl+x; y; Enter
  ```

- Перезагрузите модуль Клевер:

  ```bash
  sudo systemctl restart clever
  ```

## Настройка полетного контроллера для автономного полета

- Перепрошить полетный контроллер модифицированной прошивкой. Скачать её можно [здесь](setup.md) в разделе "Загрузка прошивки в полетный контроллер".

- Инструкция по прошивке и настройке полетного контроллера — в той же статье.

> **Warning** Обязательно выберете файл скачанной прошивки после нажатия Firmware.

## Соединение полетного контроллера и Raspberry Pi

- Соедините Raspberry Pi и Pixracer через MicroUSB-кабель. Кабель должен быть аккуратно плотно закручен и пропущен снизу коптера, чтобы не попасть в пропеллеры.

- Удаленно подключитесь к полётному контроллеру через QGroundControl.
  В системе Clover уже выставлены нужные настройки, остается лишь создать новое подключение в QGroundControl, выбрать его и подключиться. Настраивается оно, как на картинке в статье "[Подключение QGroundControl по Wi-Fi](gcs_bridge.md)".

## Настройка пульта

- Настройка полетных режимов описана в статье "[Полетные режимы](modes.md)".

  Канал 5 должен располагаться на переключателе SwC; Канал 6 - на SwA. Однако вы можете настроить эти каналы любым удобным для вас образом.

## Выполнение автоматической проверки

Проверку следует выполнить, когда вы полностью настроили дрон, а также при возникновении неполадок. Подробно процедура описана в статье "[Автоматическая проверка](selfcheck.md)".

- Выполнить команду:

  ```bash
  rosrun clever selfcheck.py
  ```

## Написание программы

В статье "[Автономный полет](simple_offboard.md)" описана работа с модулем `simple_offboard`, который создан для простого программирования дрона. В ней даны описания основных функций, а также примеры кода.

- Скопируйте из раздела "Использование из языка Python" пример кода и вставьте в редактор (например, в Visual Studio Code, PyCharm, Sublime Text, Notepad++).

- Сохраните документ с расширением .py для включения подсветки текста.

- Далее необходимо добавить полётные команды в программу. Примеры таких команд представлены в статье. Нужно написать функции для взлета и полета в точку, а также для посадки.

- Взлет.

  Для взлета можно использовать функцию `navigate`:

  ```python
  navigate(x=0, y=0, z=1.5, speed=0.5, frame_id='body', auto_arm=True)
  ```

  Добавьте эту строку внизу программы.

  Также добавьте команду ожидания:

  ```python
  rospy.sleep(3)
  ```

> **Hint** Важно выделить время на выполнение команды `navigate`, иначе коптер, не дожидаясь выполнения предыдущей команды, сразу перейдет к выполнению следующей. Для этого используется команда `rospy.sleep()`. В скобках указывается время в секундах. Функция `rospy.sleep()` относится к предыдущей команде `navigate`, а не к последующей, то есть это время, которое мы даем на то, чтобы долететь до точки, обозначенной в предыдущем `navigate`.

- Зафиксировать положение дрона в системе координат маркерного поля.

  Для этого нужно выполнить `navigate` и указать в нем необходимые координаты (например, x=1, y=1, z=1.5) и выбрать систему координат (`frame_id`):

  ```python
  navigate(x=1, y=1, z=1.5, speed=1, frame_id='aruco_map')
  ```

- В итоге должно получиться:

  ```python
  navigate(x=0, y=0, z=1.5, speed=0.5, frame_id='body', auto_arm=True)
  rospy.sleep(3)
  navigate(x=1, y=1, z=1.5, speed=1, frame_id='aruco_map')
  ```

  > **Warning** Обратите внимание, что параметр `auto_arm=True` ставится только при первом взлете. В остальных случаях его выставлять нельзя, иначе возникнут проблемы с перехватом управления.

- Если вы хотите добавить другие точки для пролета, нужно дописать еще один `navigate` и `rospy.sleep()`. Время нужно вычислить отдельно для каждой точки в зависимости от скорости полета и расстояния между точками.

  Например, если мы хотим полететь в точку (3, 3, 1.5):

    ```python
    navigate(x=3, y=3, z=1.5, speed=1, frame_id=‘aruco_map’)
    rospy.sleep(3)
    ```

  > **Warning** Координаты не должны выходить за пределы вашего поля. Если поле имеет размер 4х4 метра, максимальное значение координат, которое стоит указывать, — 4.

- После пролета по точкам нужно приземлиться. Следующая строка ставится в конце программы:

  ```
  land()
  ```

## Запись программы на дрон

Самый простой способ – это скопировать текст программы, создать новый файл в командной строке Клевера и вставить текст программы в файл.

- Для создания файла `myprogram.py` введите команду:

  ```bash
  nano myprogram.py
  ```

  Название можно выбрать любое, однако не рекомендуется использовать пробелы и специальные символы. Также расширение у программы всегда должно быть `.py`.

- Вставить текст в поле ввода. Если вы пользуетесь веб-доступом Butterfly на Windows или Linux:

  ```
  Ctrl+Shift+V
  ```

  На Mac нажмите `cmd+v`.

- Сохранить файл:

  ```
  Ctrl+x; Y; Enter
  ```

## Запуск программы

- Необходимо тщательно подготовить дрон, пульт и программу. Запустите `selfcheck.py`. Убедитесь, что дрон летает в ручном режиме.
- Включите дрон и дождитесь, пока загрузится система. Красный огонек на камере означает, что систем загрузилась.
- Проверьте полет в режиме POSCTL.

  Для этого взлетите над метками в режиме STABILIZED и переведите переключатель SwC в нижнее положение - режим POSCTL.

  > **Warning** Будьте готовы сразу же переключиться обратно в режим STABILIZED в случае выхода дрона из-под контроля!

  Установите левый стик (газ) в центральное положение. Дрон должен зависнуть на месте. В таком случае можно сажать дрон и переходить к следующему шагу. Если нет, нужно разобраться в проблеме.

- Установите переключатель SwC в центральное положение. С помощью него вы будете перехватывать дрон: стоит лишь переключить его в верхнее положение.
- Установите левый стик (газ) в центральное положение, чтобы в случае перехвата дрон не упал на пол.
- Запустите программу. Для этого выполните команду:

  ```bash
  python my_program.py
  ```

  > **Warning** После выполнения программы дрон может некорректно приземлиться и продолжать лететь над полом. В таком случае нужно перехватить управление.