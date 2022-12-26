# Краткое описание проекта

Шаблон для быстрого создания проектов 1С.

## Требуемое программное окружение

* Git
* [OneScript](https://oscript.io) с библиотеками: gitsync, v8runner

## Состав проекта

```text
bootstrap-1c/
├── build/
├── docs/
├── examples/
├── features/
├── lib/
├── src/
├── test/
├── tools/
├── web/
├── .bsl-language-server.json
├── .gitlab-ci.yml
├── feature.bat
├── README.md
└── sonar-project.properties
```

### Расшифровка

#### Каталоги

`./build` - для хранения файловых баз данных, файлов хранилища конфигурации и т. д., используемых в процессе разработки и тестирования. Данные из этого каталога не выкладываются в удаленный репозиторий, поэтому должны быть добавлены в `.gitignore`;

`./docs` - для хранения файлов документации в формате Markdown. Содержит рекомендации разработчикам и команде проекта, описание релизов (RELEASE NOTES) и оперативного плана (ROADMAPS). **Не используется** для хранения пользовательских инструкций;

`./examples` - для хранения примеров кода c расширением bsl и хранения текстовых описаний на языках Gherkin и Markdown;

`./features` - для хранения feature-файлов на языке Gherkin (BDD);

`./lib` - для хранения внешних отчетов и обработок необходимых для работы продукта, разработанные в рамках создания продукта и не являющиеся сторонними разработками;

`./src` - для хранения исходного кода в текстовом формате. Для обычных форм файлы .bin должны быть распарсены до файлов модуля формы;

`./test` - для хранения тестов (TDD);

`./tools` - для хранения любых сторонних утилит, необходимых для настройки проекта или для дополнительной установки, например:

* allure
* vanessa-automation
* onescript
* powershell
* wget
* ...

##### Windows

Для установки через командную строку в Windows используется http://chocolatey.org

##### Linux

В зависимости от установленной операционной системы - используется пакетные менеджеры `deb` или `yum`.

`./web` - для хранения настроек веб-серверов;

#### Файлы

`.bsl-language-server.json` - [конфигурационный файл с настройками LSP сервера](https://1c-syntax.github.io/bsl-language-server/#configuration);

`.gitignore` - для скрытия файлов и папок от системы контроля версий Git;

`.gitlab-ci.yml` - файл для настройки CI/CD сервера GitLab;

`feature.bat` - файл для запуска утилит, автоматизирующих процесс разработки (создание базы данных, хранилища конфигурации и т. д.);

`README.md` - файл с описанием проекта в формате Markdown. Предполагается, что сначала описание подготавливается в файле README.md (мастер), а затем импортируется в confluence через "Вставить разметку -  Вставить Confluence Wiki". Преобразовывать оформление можно через [конвертер разметки](http://chunpu.github.io/markdown2confluence/browser/). "Вставить разметку - Вставить Markdown" в Confluence 6.15.6 работает некорректно, ломая разметку. В более поздних версиях ситуация может измениться;

`sonar-project.properties` - файл с настройками SonarQube Scanner;

## Пример разработки в своей ветке

### Создание окружения для новой feature (новая ветка git)

Добавить в переменные окружения `DEV1C-USER`, в котором указать имя пользователя (разработчика) также, как это сделано в основном хранилище. Дополнительно можно добавить и `DEV1C-PASSWORD`с паролем пользователя. Эти переменные используются для автоматического создания пользователя в новом хранилище конфигурации, создаваемой для текущего локального проекта. Если эти переменные не были созданы, то после создания локального хранилища вручную добавьте пользователя для этого хранилища.

Открыть консоль CMD и перейти в каталог, где вы хотите разместить выбранный проект:

```bash
cd C:\w\1c\tmp
```

Клонировать проект с удаленного репозитория:

```bash
git clone git@git.a:dev1c/ut_10_3.git
```

Переименовать появившуюся папку с проектом. Новое имя должно совпадать с номером задачи Jira или SRS (получить информацию у TL). Например, DEV1C-4568:

```bash
rename ut_10_3 DEV1C-4568
```

Перейти в переименованный каталог:

```bash
cd DEV1C-4568
```

Переключиться на branch или commit, от которого вы хотите начать свою разработку, например, последний commit ветки основного хранилища (develop).

```bash
git checkout develop
```

Отредактируйте, если это необходимо, конфигурационный файл `./tools/users.json` с перечнем пользователей и назначенным им ролям, используемый в обработке инициализации пустой базы данных.

Запустите команду создания окружения:

```bash
feature start
```

В результате:

* В каталоге `C:\w\1c\tmp\DEV1C-4568\build` появится каталог `base` с файловой базой данных в которую загружена (с обновлением) конфигурация из папки src проекта.
* В каталоге `C:\w\1c\tmp\DEV1C-4568\build` появится каталог `storage` с хранилищем конфигурации, подключенному к файловой базе из каталога `base`. Пользователь: `Администратор`, пароль: `Администратор`. Если в параметрах окружения был задан `DEV1C-USER`, то в хранилище будет добавлен и данный пользователь (с паролем, если он был установлен в `DEV1C-PASSWORD`).
* В локальном репозитории `C:\w\1c\tmp\DEV1C-4568` будет создана новая ветка `feature/DEV1C-4568` с переключением в эту ветку и синхронизацией ее с удаленным репозиторием (--set-upstream origin "feature/DEV1C-4568").
* В списке баз будет зарегистрирована созданная информационная база `DEV1C-4568`.

### Выкладывание разработки в удаленный репозиторий

После окончания разработки выполнить конвертацию данных из хранилища 1С в требуемый формат (используется `gitsync`):

```bash
feature export
```

Отправить изменения в удаленный репозиторий:

```bash
feature push
```

Выполнить WIP PR (будут задействованы механизмы CI/CD).
