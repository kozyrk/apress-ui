# apress-ui

* [Введение](#Введение)
* [Работа с библиотекой компонентов](#Работа-с-библиотекой-компонентов)
* [Выпуск-новой-версии](#Выпуск-новой-версии)
* [FAQ](#FAQ)


## Введение

Архитектура компонентов построена вокруг методологии БЭМ, которую очень важно понимать перед тем как приступать к работе.
Исчерпывающее руководство по БЭМ - https://ru.bem.info/methodology/.


## Версионирование

Для версионирования библиотеки используется [semver](https://semver.org/).

При внесении изменений в компоненты библиотеки очень важно понимать какого рода изменения вы производите - мажорное,
минорное или патч.

Мажорное  (MAJOR) изменение предполагает нарушение обратной совместимости. Т.е. нельзя будет безболезненно обновиться
на новую версию. Что-то сломается, поедет верстка и тд и тп.

Минорное (MINOR) изменение предполагает добавление новой фичи. В нашем случае, например, добавление нового БЭМ блока или
модификатора к существующему блоку. Можно безболезненно обновиться до этой версии.

Патч (PATCH) предполагает фикс какого-либо откровенного бага, не ломающего обратную совместимость. Можно безболезненно
обновиться до этой версии.

При отправке пулл-реквеста в репозиторий, необходимо оставлять релиз ноутсы для того чтобы более верно выпускать версию
библиотеки.
[Подробнее о релиз-ноутсах](https://github.com/abak-press/guides/blob/master/frontend/workflow/common-functionality/README.md#release-notes).


Если на проекте используется старая версия библиотеки и на данный момент нет времени обновиться до актуальной версии,
но обнаружился баг в этой версии который нужно поправить, необходимо отправлять пулл-реквест не в мастер (где находится
последняя актуальная версия библиотеки) а в ветку вашей версии.

Например, в мастере последняя актуальная версия - `5.4.3`. На проекте используется версия `5.3.0`, в которой нашелся
баг. Вы не можете быстро обновиться до последней версии `5.4.3`, но баг необходимо поправить. Поэтому переходим в ветку
`5.3.x`, вносим правку туда, в пулл-реквесте указываем, что это `patch` правка и отправляем ее в ветку `5.3.x`. После 
принятия реквеста будет выпущена новая версия `5.3.1`. **ВАЖНО данные изменения никак не повлияют на актуальную версию
библиотеки в мастере `5.4.3`, это нужно учитывать**.


## Работа с библиотекой компонентов

Библиотеку компонентов можно использовать в 2х режимах:

1. Подключение готовой собранной версии из [npm registry](https://registry.railsc.ru) в проект.
2. Подключение библиотеки в проект в режиме разработки.


### Подключение готовой версии в проект

Подключение выполняется по следующему алгоритму:

1. Зайти в [npm registry](https://registry.railsc.ru) и посмотреть последнюю актуальную версию библиотеки
   `@apress/apress-ui` (например, `1.0.0`.

2. В корне проекта, в файле `package.local.json` в раздел `dependencies` добавить `"@apress/apress-ui": "1.0.0"`.

3. Выполнить `dip package.json:compile; dip yarn install;`.

4. Подключить нужные блоки (компоненты) в `packs'ы` проекта, например:

   ```js
   // frontend/packs/pulscen/assets/catalogue_show.js
   
   // (...)
   import '@apress/apress-ui/dist/pulscen/colors.css';
   import '@apress/apress-ui/dist/pulscen/button.css';
   ```

   Путь для импорта строится по следующему шаблону:

  `@apress/apress/ui/dist/[SCOPE]/[COMPONENT_NAME].[EXT]`

  где:
  
  * `[SCOPE]`          - уровень переопределения, может быть `common` - для общих блоков, или название проекта (pulscen,
    blizko, lookmart);
  * `[COMPONENT_NAME]` - название компонента, можно посмотреть либо в исходниках либо в
    [интерфейсе библиотеки](https://abak-press.github.io/apress-ui);
  * `[EXT]`            - расширение файла. Каждый компонент (блок) обязательно будет иметь 2 файла подключения - css и
    js. Если компонент состоит только из css то специально подключать js не нужно.

  После этого, на тех страницах, где подключен данный `pack` (в примере это
  `frontend/packs/pulscen/assets/catalogue_show.js`) будут доступны подключенные компоненты (в примере это `colors` и
  `button`).

### Подключение библиотеки в режиме разработки

Подключение выполняется по следующему алгоритму:

1. Клонируем репозиторий с библиотекой в папку `gems`, которая находится на том же уровне в файловой системе, что и
   папка с проектом. Например:
   ```
   > pulscen/
   > gems/
   |--> apress-ui/
   ```

2. Проверяем, что в системе доступны `docker`, `docker-compose`, `npm`:
   ```sh
   docker -v;
   docker-compose -v;
   npm -v;
   ```

   Если не установлены, то устанавливаем.
   * https://docs.docker.com/install/
   * https://docs.docker.com/compose/install/
   * https://nodejs.org/en/download/package-manager/

3. Из корня библиотеки производим первичную установку зависимостей:
   ```sh
   npm run provision;
   ```

4. В отдельном терминале запускаем сервер разработки:
   ```sh
   npm run dev;
   ```

   Теперь, по адресу `localhost:8081/` будет доступен интерфейс библиотеки компонентов, в котором можно наблюдать
   документацию и внешний вид разрабатываемых блоков (компонентов). 

5. В проекте, в котором будем использовать библиотеку, в файле `package.local.json` в раздел `dependencies` добавляем
   или заменяем существующую строчку `"@apress/apress-ui:": "MJ.MN.P"`
   на `"@apress/apress-ui": "file:/localgems/apress-ui"`.

6. Выполняем шаги `3-4` из инструкции [Подключение готовой версии в проект](#Подключение-готовой-версии-в-проект).
   Дополнительно из корня проекта, к которому подключаем `apress-ui` выполняем `dip yarn run linklocal`.
   Теперь, при внесении любых изменений в компоненты из библиотеки компонентов `apress-ui` они автоматически будут
   видны на страницах портала.


## Выпуск новой версии

Для выпуска новой версии библиотеки необходимо:

1. Убедиться что у вас есть права на запись в [npm registry](https://registry.railsc.ru). Если их нет, то нужно
   написать админам и запросить доступ.

2. После добавления изменений в библиотеку (добавили/изменили компонент/документацию) и проверки, что все работает
   в режиме разработки на проекте, необходимо выполнить сборку проекта и еще одну проверку, что все работает:
   ```
   npm run build;
   ```
   
   Сборку проекта необходимо выполнять при отключенном сервере разработки (который запускается через `npm run dev`).

3. В файле `package.json` в корне библиотеки заменить версию библиотеки на более свежую в строке `"verion": "MJ.MN.P"`.
   Номер версии зависит от типа вносимых вами изменений. Подробнее - https://semver.org/lang/ru/.

4. Отправить пулл реквест, дождаться аппрува ответственного и разрешения на выпуск версии.

5. Выпустить новую версию:
   ```sh
   npm publish;
   ```

6. Подключить новую версию на проект через npm registry и убедиться что все прошло хорошо и все работает.


## FAQ

**падает ошибка на start file watcher Error: ENOSPC: System limit for number of file watchers reached:**

Во-первых, стоит проверить, возможно, у вас кончается место на диске:
https://github.com/facebook/jest/issues/3254#issuecomment-293188965

Или увеличитьте число системных вотчеров и перезапустите систему:
https://github.com/gatsbyjs/gatsby/issues/11406#issuecomment-458769756
