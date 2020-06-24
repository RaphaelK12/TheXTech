# TheXTech

SMBX-движок, переписанный на C++ с VisualBasic 6


# Часто задаваемые вопросы
В этом параграфе представлен список различных вопросов, которые вы бы хотели мне задать. Я отвечу на большинство из них.


## Что это такое?
Это порт старого SMBX-движка (изначально созданного на VB6), полностью написанный на C++. Он полностью повторяет старый движок (кроме редактора), включая баги (часть багов была исправлена, первым делом те, которые ломают игру).


## Зачем ты это сделал?
Зачем? У меня имеется несколько целей, почему я это сделал:
- Это - очень удобная живая модель для исследований, которую я хочу использовать для разработки PGE-движка.
- Чтобы предоставить полностью совместимую со старым SMBX-движком игру для современных платформ, чтобы можно было играть старые уровни и эпизоды, сохранив первозданную атмосферу оригинальной игры SMBX, писанной на VB6.
- Чтобы игра работала на не-Windows платформах без необходимости использовать Wine, а также запускать игру на других процессорах, отличных от x86 (например ARM).
- Чтобы была возможность оптимизировать игру, используя меньше аппаратных ресурсов, в отличии от оригинальной игры, основанной на VB6.


## У тебя есть PGE-движок, зачем ты портатил больше месяца на то, чтобы создать эту вещь?
Мне оно нужно для разработки PGE-движка напрямую. С ним мне намного проще исследовать и экспериментировать, чем через старую и неудобную среду VB6.


## Какое будущее у PGE-движка с тех пор как существует TheXTech?
Я продолжу разработку PGE-движка, поскльку мне всё равно надо исполнить вторую цель PGE-Проекта.
С момента основания у PGE-проекта было две цели: 1) спасти SMBX; 2) предоставить гибкий набор для создания новых игр-платформеров. Открытие исходников SMBX и связанное с этим появление TheXTech, полностью исполнило первую цель: SMBX спасён, и теперь это свободное ПО с открытым исходным кодом. PGE-движок останется для исполнения второй цели - предоставить набор для новых игр. В отличии от TheXTech, PGE-движок даёт максимальную гибкость, которая позволяет каждому создать что-либо с нуля, без необходимости наследовать старую игровую базу. Однако, TheXTech нужен для PGE-движка в качестве модели для исследований, чтобы разработать новый движок. В итоге, результат будет схож с портами игры Doom: GZDoom и Chocolate Doom. GZDoom - мощный и функциональный движок, лучший выбор для моддеров. Chocolate Doom - это высокоточный порт оригинальной игры на современные платформы с целью предоставить оригинальную игрву включая даже баги. PGE-движок стремится быть похожим на GZDoom в то время, как TheXTech будет аналогом Chocolate Doom с целью повторить оригинальную игру на современных платформах.


## Сможет LunaLua работать в этой игре?
Нет, LunaLua не сможет работать: данный проект бинарно несовместим с LunaLua. Это ещё значит, что контент, предназначенный для SMBX2, также будет несовместим.


## Почему здесь такой плохой плохой?
Изначально, большинство кода в папке "src" было написано на VB6 его изначальным автором. Я сделал полное преобразование кода с упором на максимально точное воспроизведения. То есть, большинство кода практически идентично тому, что было изначально написано на VB6. Платформа VB6 имела огромное количество всевозможных трудностей и ограничений:
- Все переменные глобальны и доступны из каждого модуля и формы по умолчанию, без каких-либо включений или импортов. Это и есть причина, почему существует "globals.h": этот файл содержит в себе весь полный список глобально доступных переменных.
- Ограниченная и неудобная поддержка классов, почему большая часть кода нагло злоупотребляла большим колличеством глобальных переменных и массивов (также свою лепту привнесло отсутствие опыта у изначального автора, которое стало дополнительным фактором существования всего этого безобразия).
- Все функции во всех модулях глобальны, и могут вызываться из кажого модуля напрямую (за исключением вызовов, помеченных как "приватные"). Из-за чего мне пришлось проделать дополнительную работу по включению заголовков в файлы, где эти вызовы применялись.
- Почему так много `if-elseif-elseif-elseif-elseif-....`? Да, здесь, скорее всего, было бы правильно использовать оператор `switch()` (аналог в VB6: `Select Case`). Ещё одна причина, которая объясняет, что у изначального автора на тот момент времени не хватало опыта, пока он работал над этим проектом.
- Почему такие глубокая лазанья из `if() { if {} if { .... } }`? На то есть две причины: 1) мало опыта у изначального автора, 2) костыль, предназначенный для того, чтобы не проверять все условия логического выражения в случаях, способных вызвать крэш. В C++, множество условий, разделённых с помощью оператора `&&`, никогда не будут проверены, если одно из них даёт отрицательный результат. В VB6 наборот, ВСЕ узлы выражения исполняются, не зависимо от их реузльтата. Эта разница даёт следующее: в VB6, выражение `if A < 5 And Array(A) = 1 Then` спровоцирует ошибку, если A будет больше пяти. В C++, аналогичное выражение `if(A < 5 && Array[A] == 1)` никогда не спровоцирует ошибку, потому что вторая часть выражения никогда не проверяется, если первая дала отрицательный результат.
- Почему такие длинные выражения по типу `if(id == 1 || id == 3 || id == 4 || ... id == N)`? Вместо того, чтобы создавать кучи подобных выражений, было бы лучше использовать классы и полиморфизм с отдельной логикой для каждого объекта между разными классами. Это также должно решаться с помощью указателей на функции (которые невозможны в VB6 без костылей, однако легко возможны в C++). Однако снова, отсутствие опыта у изначального опыта одновременно с огромной кучей ограничений VB6 спровоцировало появление подобных конструкций.



## Как пользоваться игрой?
Имеется несколько способов, как пользоваться этой игрой:
- Имеются уже готовые к работе сборки, просто взять использовать также, как SMBX.
- [macOS users, skip this]: use by the same way as an original game: put an executable file into the game root folder with an "thextech.ini" that contains next text:
- [Пропустить этот пункт пользователям macOS]: Использовать тем же способом, что и оригинальная игра: положить исполняемый файл в кореневую папку игры совместно с INI-файлом "thextech.ini", содержащим следующий текст:
```
[Main]
force-portable = true
```
, файлами "music.ini", "sounds.ini" и с дополнительной папкой "graphics/ui".
Важно: вся штатная графика должна быть преобразована в PNG. Используйте утилиту GIFs2PNG из комплекта PGE-Project, применив на папке "graphics" с флагом "-d". Не используйте флаг "-r", чтобы сохранить исходные GIF-файлы совместно с новоиспечёнными PNG, если есть в планах продолжить использовать оригинальную игру, написанную на VB6.
- Использовать это в отладочном режиме: в Вашем домашнем каталоге, создайте папку `.PGE_Project` (обязательно должно начинаться с точки), и затем внутри `.PGE_Project`, создать папку "thextech" (на macOS вместо этого нужно создать путь "`~/Library/Application Support/PGE Project/thextech`"). Это отладочный корень игры, в коротом необходимо разместить полный комплект ресурсов игры и данные эпизодов. Этот каталог будет действовать аналогично корневому каталогу оригинальной игры. Этот режим позволяет запускать исполняемый файл игры из абсолютно любого места на Вашем компьютере, и при этом использовать одно общее местоположение игровых ресурсов для всех сборок (за исключением тех, которые были помечены как портативные с помощью INI-файла).


## Как добавить сторонние эпизоды в версии для macOS?
Если у вас укомплектованная сборка TheXTech, то все встроенные ресурсы находятся внутри вашего .app: "Content/Resources/assets/". Можно изменить содержиеме, однако, этого не рекомендуется делать! Вместо этого, после первого запуска игры в вашем домашнем каталоге появятся следующая директория:

```
   ~/TheXTech Episodes
```
Внутри этой директории будут две пустые папки: "battle" и "worlds", в которые вы сможете положить ваши эпизоды. Логи, настройки и сохранения игры будут сохранены в папке "`~/Library/Application Support/PGE Project/thextech`".
Если же вы хотите заменить встроенные ресурсы на собственные, можно изменить содержимое пакета приложения, или собрать новую сборку с указанием необходимых флагов CMake, с помощью которых вы сможете упаковать ваш собственный набор ресурсов и иконку для нового пакета, либо же сделать безресурсную сборку (по умолчанию, если не указать никаких аргументов, будет собираться безресурсная сборка по-умолчанию). Поэтому вам нужно разместить полный комплект игровых ресурсов в папку "`~/Library/Application Support/PGE Project/thextech`", включая все основные ресурсы (графику, музыку, звуки, вводный и завершающий уровни, уровни битв и папки миров по умолчанию).


## В чём разница между этой и оригинальной сборкой игры, созданной на VB6?
- Первым делом, эта игра написана на C++, когда оригинал (как мы уже знаем) был создан на VisualBasic6.
- Отсутствует редактор. Вместо этого, игра имеет глубокую интеграцию с PGE-редактором, который позволяет использовать игру с той же функциональностью, что и оригинальный редактор (Была сохранена функциональность "волшебной руки", которая позволяет редактировать уровень в реальном времени во время тестирования).
- Полная поддержка UTF-8 в именах файлов, путях и внутренних текстовых ресурсах (оригинальная игра поддерживала только 8битные кодировки ANSI).
- Игра использует SDL2 для графики и управления в то время, как оригинальная игра использовала вызовы WinAPI и библиотеки GDI.
- Игра использует библиотеку PGE-FL для улучшенной поддержки форматов файлов.
- Имеется поддержка карт мира WLDX, которые позволяют сохранять неограниченное число строк титров, а также встроенная поддежка использования сторонней музыки без необходимости использовать music.ini.
- В игре работают некоторые уникальные возможности уровней LVLX: вертикальное соединение; двунаправленные проходы; возможность указать собственное сообщение, чтобы рассказать о необходимом числе звёзд для взода в проход; событие входа в проход; возможность отключить отображение числа звёзд над определёнными дверями в корридорных мирах; возможность отключить входной экран при переходе между уровнями через проход.
- Встроенная поддержка файлов "music.ini" и "sounds.ini" для замены музыки и звуков на отдельных уровнях, и даже в целых эпизодах.
- Карты мира отныне поддерживают собственную директорию, чтобы размещать в ней любые специфичные ресурсы (ландшафт, декорации, пути, уровни), и при этом больше не засорять корневую папкпу эпизода ресурсами карты мира.
- Формат настроек теперь INI, старый формат config.dat больше не поддерживается, первым делом из-за несовместимых форматов кодов клавиш (SDL_Scancode против VirtualKeys из Windows API).
- Сохранения игры отныне используют формат SAVX вместо классического SAV. Однако, если у вас уже имеется старое сохранение, вы всё ещё можете продожить игру, используя новый движок (при следующем сохранеии игры, будет создан файл в новом SAVX-формате, старое сохранение игры останется нетронутым).
- Встроенная поддкржика PNG для сторонней и встроенной графики. Масковые GIF по прежнему поддерживаются для обратной совместимости, однако, без автопреобразований, как это обычно делает SMBX-38A.
- Контрольные точки отныне можно использовать по много раз! Вы сможете использовать их на ваших уровнях по несколько раз без каких либо ограничений!
- Игра использует алгоритм ленивой распаковки, чтобы ускорить время загрузки игры и уменьшить использование памяти.
- Игра использует библиотеку MixerX для звука и музыки, которая поддерживает огрномное число различных аудиоформатов!
- Игра не внедряет в сборку какую-либо графику: отныне в игре больше НИКАКИХ истинно "внедрённых" ресурсов, теперь вся графика представлена в виде внешних ресурсов!
- Расширены некоторые встроенные ограничения.
- Встроення запись GIF с помощью клавиши F11 (на macOS используется клавиша F10, F11 используется системой, чтобы показать/скрыть рабочий сто).
- Игра запускается быстрее: игра загружается почти мгновенно (зависит от компьютера и производительности его диска).
- Игра использует меньше оперативной памяти (вместо 600...800 мегабайт, игра теперь в среднем использует 80...150 мегабайт), а также, она теперь чиста от утечек в памяти, вызванных MCI-интерфейсом, который использовался в оригинальной игре.
- Игра больше не перегружает процессор до 100% (проблема была из-за неправильного способа обработки вечных циклов, я также исправил этот деффект и в VB6-сборке в ветке "smbx-experiments").
- Игра способна работать даже на "тостере" (старом/слабом компьютере), пока VB6-SMBX не может.
- Игра полностью кросс-платформенна, и больше не зависит от Windows, а также больше не зависит от процессоров x86: игра сможет работать на ARM и MIPS-процессорах тоже (VB6-SMBX зависит от Windows и x86).


## Как собрать игру?
Вы можете почитать подробно инструкцию сборки этого проекта из исходников тут: https://github.com/Wohlstand/TheXTech/wiki/Building-the-game

Чтобы собрать игру, нужно иметь несколько вещей:
- CMake
- Ninja, не обязательно (чтобы ускорить процесс сборки)
- Совместимый компилятор C/C++ (GCC, Clang, MinGW, MSVC не проверялся)
- Git (нужен для загрузки подмодулей и клонирования исходников зависисимых библиотек для их сборки на месте)
- Mercurial (необходим для загрузки исходников с официального репозитория SDL2, чтобы собрать его на месте)
- Не обязательно: зависимости, установленные в систему: SDL2, libFreeImageLite (модифицированная реализация FreeImage), звуковая библиотека MixerX, коллекция библиотек AudioCodecs. Если все эти зависимости установлены в системе, процесс сборки будет идти значительно быстрее. Однако, возможно собирать все зависимости прямо на месте, но при этом сборка будет идти дольше.
