# Changelog

Přehled všech změn v Sir Shakes: Till The Last Drop.
Nejnovější záznamy jsou nahoře.

## 2026-10-29

### Přidáno

- Nový item **Visored Helmet**: těžší helma s hledím, víc armoru (15) i charismatu (+4)
  než Iron Helmet.
- Nový item **Militia Sword**: průměrný meč lepší než Bartender's sword (damage 22 místo 15).
- Nový item **Knight's Longsword**: nejsilnější zbraň ve hře (damage 35), nejdražší item (200 zl.).
- Model obchodu (domu): upravený model pro přidání do scény.
- `Eye Height` a `Target Height` v `NPCVisionDetector`, `AIScript` a `GuardNPC`: nastavitelná
  výška očí NPC a bodu na hráči, kam NPC míří.
- `PlayerStats.regenDelayAfterHit`: prodleva regenerace zdraví po zásahu (výchozí 7 s).

### Změněno

- **NavMesh Surface** přestavěn od nuly (nový bake).
- **Dohled NPC**: výšky byly natvrdo (1.0 a 1.5) a neodpovídaly velikosti postav, NPC
  mířil hráči na břicho. Paprsek teď jde z očí NPC na hruď hráče, takže hráč schovaný
  za nízkou překážkou zůstane nepozorovaný.
- `NPCVisionDetector.OnDrawGizmosSelected()`: gizmo ukazuje výšku očí (žlutá kulička),
  cílový bod na hráči (oranžová kulička) a paprsek mezi nimi (šedý, červený při zpozorování).
- `PlayerStats`: regenerace zdraví začne až `regenDelayAfterHit` sekund po posledním zásahu
  (`TakeDamage()`). Dřív se hráč léčil hned, takže souboje se strážnými nebyly napínavé.
  Další zásah odpočet vynuluje.

### Poznámky

- **Jak mají fungovat colidery zbraní:** každá zbraň má vlastní collider na samostatném
  prázdném objektu (child zbraně). Ten objekt nese komponenty `MeleeHitbox` a `DamageCollider`.
  `MeleeHitbox` určuje, že právě tento collider na špičce zbraně ubírá životy, a
  `DamageCollider` ho označuje jako collider, který se nesmí vypínat. Collider musí mít
  zapnuté `Is Trigger`, jinak se `OnTriggerEnter` nezavolá a zbraň neudělí damage
  (Militia Sword původně damage nedával právě kvůli tomu).
- **Nastavení dohledu** (měřítko světa, kde má postava přes 7 jednotek):
  - `Eye Height` (na NPC) = **6.4**, měřeno od pivotu NPC (na nohou).
  - `Target Height` (na hráči) = **2.5** (hruď). Hráč má pivot uprostřed těla. Hodnota 3.7
    (hlava) hráče netrefovala, protože kapsle je nahoře úzká. Níž = hráč je vidět i za
    nižšími překážkami.
  - `View Distance` = **25**, `View Angle` = **140** (výchozí 5 až 7 bylo pro menší měřítko).
- Hodnoty dohledu se nastavují **zvlášť** v každé komponentě: `NPCVisionDetector` (krádež,
  svědci), `AIScript` (pozdravy a questy) a `GuardNPC` (strážní). Při změně je potřeba
  upravit všechny tři.
- **Krádež řeší `NPCVisionDetector`.** NPC, který má hráče načapat při krádeži, musí mít
  tuhle komponentu. Strážní mají vlastní dohled v `GuardNPC`.
- `Obstacle Mask` na `NPCVisionDetector` se nepoužívá, paprsek jde přes všechny vrstvy.
- U nové postavy změřit výšku těla (spodek a vršek od pivotu) a podle toho zvolit `Eye Height`.
- Rychlost regenerace určuje `Health Regen Rate` v Inspectoru na `PlayerStats`.

## 2026-10-27

### Přidáno

- Nový item **Egg** (volské oko): lehká svačina s lepším léčením než jablko (hlad 10, HP 6).
- Nový item **Beef Slab**: nejsytější jídlo ve hře (hlad 35, HP 12), levnější než by odpovídalo síle.
- `AIScript`: nové pole `Otacet Se Na Hrace`. Když je odškrtnuté, NPC se nepootočí na hráče při pozdravu nebo oznámení questu, ale při rozhovoru přes `E` se otočí normálně.

### Změněno

- `AIScript.StartTalking()`: `lookAtTarget` se nastavuje jen při zapnutém `Otacet Se Na Hrace`. `ForceStartTalking()` (rozhovor) zůstává beze změny.

### Opraveno

- Barman se otáčel na hráče, který jen prošel kolem. Uložená "původní rotace" se pak při rozhovoru brala už z otočeného stavu, takže se po dialogu nevrátil správně.

### Poznámky

- U barmana je potřeba v Inspectoru odškrtnout `Otacet Se Na Hrace`. Výchozí hodnota je zapnuto, takže ostatní NPC se chovají jako dřív.

## 2026-10-25

### Přidáno
- **`ITEMS.md`**: přehled všech itemů ve hře (zbraně, brnění, jídlo a pití, ostatní).
- Nový item **Mead** (medovina), nahrazuje Mysterious Potion.
- Nový item **Kombucha**: snižuje hladinu alkoholu (záporná hodnota v `Item Data`),
  slouží k vystřízlivění.
- Nový item **Cheap Wine**: levné a slabší víno (+15 alkoholu), nejslabší alkohol ve hře.
- Vtipné popisky u všech itemů.

### Změněno
- **Přebalancování itemů** (realističtější hodnoty hladu, HP, alkoholu, váhy a ceny).
  - Jídlo doplňuje méně, pivo a medovina mají reálný obsah alkoholu.
  - Zbraně a brnění jsou lehčí, helma dává menší bonus charismatu.
  - Cena klíčů snížena na 1.
- Mysterious Potion přejmenován na Mead (jméno i Item Type ID).
- `ItemData.Use()`: alkohol se nově aplikuje i při záporné hodnotě (`alcoholValue != 0`),
  dřív se záporná hodnota ignorovala a item se nespotřeboval.
- `PlayerStats.Drink()`: hladina alkoholu je nově ohraničena na 0 až 100 (`Mathf.Clamp`).
- `InventoryManager.OpenExamine()`: v popisu se zobrazí i záporný alkohol (např. `Alcohol: -15%`).

### Opraveno
- Ukradené předměty (kategorie `Steal`) v inventáři nezobrazovaly ikonky hladu a HP.
  - `InventorySlotUI.SetItem()` nově čte `categoryAfterPickup`.
  - `InventoryManager.PickupItem()` a `ExecuteTransfer()` při přesunu do inventáře
    přepíšou `category` na `categoryAfterPickup`.

## 2026-10-24

### Přidáno
- **Indikátor cravingu (abstaku)**: nová UI ikonka se sliderem, který se
  zobrazí, když ti dochází alkohol.
  - Alkohol 20 → 10: slider se plní oranžově (varování).
  - Alkohol 10 → 0: slider se plní znovu od nuly červeně (kritické).
- **Skript `LockOwner`**: umožňuje NPC držet ID klíče, aniž by bylo witness
  (využito pro barmana).
- `StealableBag` má teď dvě pole majitele: `Lock Owner` (jen ID klíče)
  a `Witness NPC` (ID klíče + hlásí krádež strážím).

### Změněno
- Stav cravingu (`Abstak`) nově začíná pod 10 alkoholu místo pod 25.
  Hranice se nastavují přes `abstakCriticalLimit` / `abstakWarningLimit`
  (už žádné natvrdo napsané hodnoty v `DetermineDrunkState`).
- Ubírání zdraví při cravingu je mnohem mírnější: roste podle hloubky
  cravingu a má vlastní `abstakHealthDrainRate`, místo drsné rychlosti
  z hladu a spánku. `DrainHealth` teď přijímá rychlost jako parametr.
- `LootBagManager` bere lock ID klíče buď z `Lock Owner`, nebo z `Witness NPC`.

### Opraveno
- Předměty vzaté z bedny nešlo equipnout (chyběl `OriginalScaleStore`).
  Komponenta se teď zajišťuje v `InventoryManager.PickupItem`,
  `InventoryManager.ExecuteTransfer` a `Chest.Start`, vždy před přesunem
  pod nového rodiče.

### Poznámky
- `WitnessNPC` si drží vlastní pole `lockID`.
- Po přejmenování polí majitele na `StealableBag` je potřeba znovu přiřadit
  majitele u bagů ve scéně.
