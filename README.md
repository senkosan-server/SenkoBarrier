# Senko Barrier

Плагин для **Paper 1.21.11**, который превращает границу мира в живое и опасное место: чем ближе игрок подходит к краю, тем сильнее «плывёт» картинка, а совсем вплотную - игрок начинает «терять сознание»: его выкидывает вглубь карты, причём просыпается он на совершенно случайной точке.

A plugin for **Paper 1.21.11** that turns the world border into a living, dangerous place: the closer a player gets to the edge, the more the picture "swims", and right at the edge the player "passes out" - they get flung deep into the map and wake up at a completely random spot.

---

## Возможности / Features

- **Зона эффектов / Effect zone** (по умолчанию `24` блока до границы): у игрока мутнеет в глазах - эффекты **Тьма** и **Тошнота**.
- **Зона телепорта / Teleport zone** (по умолчанию `6` блоков): игрок **теряет сознание** - его телепортирует вглубь границы на расстояние от `100` до `600` блоков, с чёрным экраном на `5` секунд и случайной надписью-пробуждением (одной из 10).
- **Лежание / Lying** (50% случаев): после телепорта игрок лежит на земле без сознания. Его тело заменяется на NPC-клона в его же скине, лежащего на земле - как в моду GSit. Движение заблокировано, **Shift** - прийти в себя.
- **Сидение / Sitting** (в остальных случаях): игрок сидит на невидимом табурете; встаёт по **Shift**.
- **Иммунитет / Immunity**: право `senkpbarrier.bypass` полностью отключает все эффекты для игрока (по умолчанию иммунитета нет ни у кого, включая опов - выдаётся через LuckPerms).

- **First-person view**: in F5 / from other players' perspective you see your own body lying on the ground with your skin.
- **Коoldown**: после пробуждения игрок какое-то время защищён от повторного «обморока» (по умолчанию `5` секунд).

---

## Скриншоты / Screenshots

### Подход к границе / Approaching the border

![Зона эффектов](screenshots/effect-zone.png)

![Зона эффектов 2](screenshots/effect-zone-2.png)

### Обморок и чёрный экран / Blackout

![Чёрный экран с надписью](screenshots/blackout.png)

### Пробуждение - лежание / Waking up - lying down

![Лежит на земле в своём скине](screenshots/lying.png)

![Вид от первого лица из F5](screenshots/lying-f5.png)

### Сидение / Sitting

![Сидит на невидимой опоре](screenshots/sitting.png)

---

## Команда / Command

| Команда / Command | Описание / Description |
|---|---|
| `/senkpbarrier` | Показать расстояние до границы мира и статус зон / Shows distance to the world border and zone status |

## Права / Permissions

| Право / Permission | Описание / Description | По умолчанию / Default |
|---|---|---|
| `senkpbarrier.bypass` | Иммунитет ко всем эффектам границы / Immune to all border effects | `false` (нет ни у кого / nobody) |

```yaml
# Пример выдачи через LuckPerms / Example grant via LuckPerms:
/lp group admin permission set senkpbarrier.bypass true
```

---

## Конфигурация / Configuration

Файл: `plugins/senkpbarrier/config.yml`. Все значения можно менять без перезагрузки - плагин перечитывает файл и автоматически добавляет недостающие ключи.

| Ключ / Key | Описание / Description | По умолчанию / Default |
|---|---|---|
| `check-interval-ticks` | Частота проверки расстояния до границы (в тиках, 20 = 1 сек) / How often the border distance is checked (ticks, 20 = 1 sec) | `10` |
| `effect-zone` | Дистанция до границы, на которой действуют эффекты Тьма/Тошнота / Distance to the border at which Darkness/Nausea apply | `24` |
| `teleport-zone` | Дистанция, на которой срабатывает «обморок» / Distance at which the blackout triggers | `6` |
| `teleport-distance-min` | Минимальная дистанция телепорта вглубь миры / Minimum teleport distance into the map | `100` |
| `teleport-distance-max` | Максимальная дистанция телепорта / Maximum teleport distance | `600` |
| `border-margin` | Запас от новой границы мира при выборе точки / Margin of the world border considered when picking the spot | `12` |
| `blackout-seconds` | Длительность чёрного экрана (сек) / Black screen duration (seconds) | `5` |
| `cooldown-seconds` | Кулдаун после пробуждения (сек) / Cooldown after waking up (seconds) | `5` |
| `blackout-messages` | 10 случайных надписей при пробуждении / 10 random wake-up messages | см. ниже / see below |

```
blackout-messages:
  - "Вы потеряли сознание и очнулись в неизвестном месте..."
  - "В глазах резко потемнело... вы не понимаете, где находитесь"
  ...
```

---

## Как это работает / How it works

- Раз в `check-interval-ticks` плагин замеряет дистанцию до `WorldBorder` текущего мира и сравнивает с зонами `effect-zone` и `teleport-zone`.
- В зоне эффектов игроку выдаются эффекты Тьма и Тошнота, в статус-бар пишется предупреждение.
- На границе `teleport-zone` срабатывает обморок: нужно сначала поставить границу мира (`/worldborder set <размер>` или в конфиге мира), иначе плагин предупредит, что делать нечего.
- При телепорте выбирается **случайная** точка внутри новой границы, не ближе `teleport-distance-min` ко всем её краям (25 попыток, запасной вариант - случайная сторона).
- Затем игрок получает чёрный экран (эффект слепоты + всплывающая надпись со случайной фразой + звук), и его уносит вглубь мира.
- После пробуждения игрок либо **лежит** (NPC-клон с его скином в позе лёжа - порт механики GSit через NMS-пакеты), либо **сидит** на невидимом табурете. Движение заблокировано, пока игрок не нажмёт **Shift**.
- Команда `/senkpbarrier` показывает игроку текущую дистанцию до границы и статус.

---

## Установка / Installation

1. Остановить сервер.
2. Скопировать `senkpbarrier-<версия>.jar` в папку `plugins/`.
3. Запустить сервер, подождать генерации `config.yml`.
4. Настроить конфиг под себя и по желанию поставить границу мира: `/worldborder center 0 0`, `/worldborder set 4000`.

Requires **Paper 1.21.11** (or compatible forks). API version: `1.21`.

---

## Технические детали / Technical notes

- Сборка использует `paperweight-userdev` + Gradle 9 - плагин компилируется с серверным кодом Minecraft (mojang-mapped).
- Право `senkpbarrier.bypass` по умолчанию `false` - даже операторы сервера не имеют иммунитета, пока им его не выдадут.
- Пока игрок лежит, его ник над головой скрывается через временную команду скорборда (побочный эффект: временно пропадает префикс LuckPerms).
