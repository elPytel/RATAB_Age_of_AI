# Age of AI 
Pojď si zahrát legendární strategii Age of Empires II, naučit se základy programování a zjistit, jak funguje umělá inteligence ve hrách. Na konci akce se utkáš s ostatními účastníky v epickém souboji vašich civilizací.

![Age of AI](./assets/Age_of_AI.jpeg)

Vícuc zdrojů ke hře [AoE2](AoEIIDE.md).

## Instalace Age of Empires 2
Stažení a nainstalace ze [Steamu](https://store.steampowered.com/app/813780/Age_of_Empires_II_Definitive_Edition/).

## Scriptování AI
![AI](assets/AI.jpg)

Jak se nyní dozvíte, tak naše AI není "Artificial Intelligence", ale "Accumulated IFs". Všechny AI v Age of Empires 2 jsou napsané v jazyce [LISP](https://cs.wikipedia.org/wiki/Lisp). LISP je velmi starý jazyk, který byl vytvořen v roce 1958. 

### Jak definovat pavidlo (IFs)

Základem je podmíněné vykonávání příkazů IF -> THEN. To pro nás bude $Alfa$ a $Omega$ celého scriptování. Je důležité pochopit následůjící část kódu, protže od teň na ní budeme pořád stavět. 

``` LISP
(defrule       ;Toto začíná pravidlo. Defrule je zkratka pro "definovat pravidlo".
    (fact 1)   ;Toto je "if" část pravidla. AI zkontroluje, zda jsou tato fakta
    (fact 2)   ;pravdivá. Pokud jsou všechny fakty pravdivé, pokračuje AI na
    (fact 3)   ;akce. Ne všechna pravidla mají 3 fakty, ale všechna mají alespoň jeden.
    ...
=>             ;Toto je "then" část pravidla.
    (action 1)
    (action 2) ;Pokud jsou všechny fakty pravdivé, dojde k těmto akcím. Ne všechna 
    (action 3) ;pravidla mají tři akce, ale všechna mají alespoň jednu.
    ...
) ;tato závorka ukončuje pravidlo.
```

![My head hurts](assets/My-head-hurts.gif)

Povšimněte si, že všechny odsazení jsou zcela volitelná. Byly zahrnuty pouze proto, aby bylo každé pravidlo snazší číst.

#### Jednorázové akce
Pokud chceme pravidlo pouštět pouze jednou (disable-self action). Například při jednorázovém nastavení proměnné.

``` LISP
(defrule 
    (true) ; Toto pravidlo se spustí vždy, když je AI spuštěna.
=>
    (set-strategic-number sn-enable-boar-hunting 1) ; Zapnutí lovu divokých prasat. 
    (disable-self)
)
```

### Konstatny a proměnné (const & goals)

#### Konstanty

V AI obvykle raději používáme jména než čísla. To počítači nepomáhá, ale nám, abychom pochopili, co se v kódu děje.

Pokud chceme číslo uložit nod nějakým jménem, můžeme použít `defconst`:

``` LISP
(defconst my-number 5)
```

To znamená, že kdykoli napíšeme `my-number`, hra to bude interpretovat jako `5`.

#### Cíle (Goals)
Pokud chceme s našimi AI dělat něco opravdu složitějšího, musíme být schopni si pamatovat určitá čísla. `defconst` nám ale nepomůže, protože takové číslo nelze změnit. Zde přichází do hry systém cílů.

Akce typu Goal se definuje následovně:

``` LISP
(set-goal GOAL_NUMBER VALUE)
```

Nejsme však blázni abychom číslům přiřazovali další hodnoty. Člověk zde na to může nahlížet jako na pole, kde `GOAL_NUMBER` je index a `VALUE` je hodnota na daném indexu. 

Pro snadnější práci bychom v jazycích typu C použili kontrukci zvanou jako výčet (enumarate), zde nic takového sice není, ale můžeme to napodobit pomocí `defconst`, kdy jednotlivým konstantám přiřadíme čísla ručně.

![Confusion](assets/confusion.jpeg)

``` LISP
(defconst gl-train-militia 1) ; Nastavi goal gl-train-militia na 1
(defconst gl-train-archers 2) ; Nastavi goal gl-train-archers na 2

; Nastavi pocatecni hodnotu cile na 0
(defrule
    (true)
=>
    (set-goal gl-train-militia 0)
    (set-goal gl-train-archers 0)
    (disable-self)
)

; Nekde hloubeji v kodu jsme se rozhodli, ze chceme nastavit cil gl-train-militia na 1 a tim povolit trenovani vojaku
(defrule
    (goal gl-train-militia 1)
    (can-train militiaman-line)
=>
    (train militiaman-line)
)
```

### Boolovské operátory (AND, OR, NOT)
![2bOR!2b](assets/2bOR!2b.jpg)

Pro návrh složitějších pravidel lze jednotlivé výrazy spojovat pomocí boolovských operátorů. Existují zde tři boolovské operátory: `OR`, `AND` a `NOT`:

- `OR` "nebo" bere dvě podmínky. Podmínka může být další boolovský výraz nebo výrok.
- `AND` "a" také bere dvě podmínky a jeho jediným účelem je být použita v ORu.
- `NOT` "nikoli" bere pouze jednu podmínku a je pravdivá, pokud je podmínka nepravdivá.

``` LISP
(defrule
    (or
        (condition 1)
        (condition 2)
    )
=>
    (action)
)

(defrule
    (or
        (condition 1)
        (and
            (condition 2)
            (condition 3)
        )
    )
=>
    (action)
)

(defrule
    (not
        (condition)
    )
=>
    (action)
)
```

#### Porovnávání ve výrazech

Výrazy mohou být také porovnávány pomocí následujících operátorů:

- `>` (greater than) větší než
- `<` (less than) menší než
- `>=` (greater than or equal to) větší nebo rovno než
- `<=` (less than of equal to) menší nebo rovno než
- `==` (equal to) rovno než

Příklad:

``` LISP
(defrule
    (unit-type-count villager > 0)
=>
    (chat-to-all "I just made my first villager!")
    (disable-self)
)
```

### Micro management (Strategic Numbers)
Strategic number říkají AI, jak se chovat na nejzákladnější úrovni. Jsou převážně používány k přidělování vesničanů k různým zdrojům:

``` LISP
(defrule
    (current-age == dark-age)
=>
    (set-strategic-number sn-food-gatherer-percentage 85)
    (set-strategic-number sn-wood-gatherer-percentage 15)
    (set-strategic-number sn-gold-gatherer-percentage 0)
    (set-strategic-number sn-stone-gatherer-percentage 0)
    (disable-self)
)
```

Význam tohoto pravidla je: "Pokud je aktuální věk temný věk, pak přiřaďte 85% vesničanů k jídlu a 15% k dřevu. Udělejte to pouze jednou."

### Stavění budov

Pro postavení jakékoliv budovy můžeme použít podmínku:

``` LISP
(can-build BUILDING)
```

A následně akci:

``` LISP
(build BUILDING)
```

Například, pro postavení hradu můžeme použít:

``` LISP
(defrule
    (can-build castle)
=>
    (build castle)
)
```

Je také důležité poznamenat, že můžeme budovu postavit dopředu směrem k nepříteli pomocí příkazu `build-forward` místo pouhého `build`.

Můžeme zkontrolovat, kolik budov každého typu máme pomocí této podmínky:

``` LISP
(building-type-count-total BUILDING > NUMBER)
```

Všimněte si, že rozdíl mezi počtem budov a celkovým počtem budov je v tom, že ten druhý zahrnuje budovy ve frontě pro stavbu. (stejně to je i pro počty jednotek)

#### Domečky
Domečky nám umožnují vytvářet další vesničany. Pro kotrolu, zda jsme méně než 5 jednotek od stavu, kdy nemáme dostatek domů:

``` LISP
(housing-headroom < 5)
```

Následující příkaz nám umožní zkontrolovat zda máme dostatek domů na to, abychom podporovali maximální populaci. 

``` LISP
(population-headroom != 0)
```

Výsledný kód může vypadat takto:

``` LISP
(defrule
    (housing-headroom < 5)
    (population-headroom != 0)
    (can-build house)
=>
    (build house)
)
```

#### Skladiště (Dropoff points)

Kempy se staví stejně jako jakákoliv jiná budova, ale stejně jako domy, chceme je stavět pouze tehdy, když jsou splněny určité podmínky. Tato podmínka je užitečná:

``` LISP
(dropsite-min-distance RESOURCE > NUMBER_TILES)
```

Toto zkontroluje, zda je nejbližší `RESOURCE` (surovina) vzdálena více než `NUMBER_TILES` (počet kroků) od bodu dropoff. Další užitečnou podmínkou je:

``` LISP
(resource-found RESOURCE)
```

To ověří, zda AI prozkoumala konkrétní zdroj. Nechceme stavět body dropoff pro zdroj, který neexistuje!

Zde je příklad pro tábor na dřevo, který by měl věci ujasnit:

``` LISP
(defrule
    (dropsite-min-distance wood > 3)
    (resource-found wood)
    (can-build lumber-camp)
=>
    (build lumber-camp)
    (up-modify-sn sn-camp-max-distance c:+ 3)
)
```

AI nebude sbírat suroviny, když jsou příliš daleko, i když jim doma došly, což nechceme. Jednoduchým řešením, které rád používám, je upravit `sn-camp-max-distance`, kdykoli postavím tábor.


### Boj
Ve hře je několik různých jednotek a jsou pro ně potřeba různé budovy. Následující tabulka ukazuje, které budovy jsou potřeba pro které jednotky:

| jednotka | kasárna | technologie |
| --- | --- | --- |
`archer-line`       | `archery-range`  | 
`militiaman-line`   | `barracks`       | 
`knight-line`       | `stable`         | 
`monk`              | `monastery`      | 
`siege-weapon-line` | `siege-workshop` |

#### Jednotky a výzkum (Units + Researching)
Stejné jako budovy, ale s (`can-train UNIT`) a (`train UNIT`), (`can-research RESEARCH`) a (`research RESEARCH`). Příklad:

``` LISP
(defrule
    (can-research ri-ballistics)
=>
    (research ri-ballistics)
)
```

Tenhle příklad bude však vyžadovat budovu univerzity.

#### Vzorové kódu pro armádu
- Příklad verbování armády podle obtížnosti [zde](./src/army_training.per).
- Příklad útoku na nepřítele s časováním [zde](./src/attack.per).
  - V tomto kódu bude AI pomocí metody attack-groups čekat 20 až 40 sekund a poté na 20 sekund zaútočí.

### Náhodné číslo
Pokud chceme vložit do našit návrhů trochu nahodilosti a nepředvídatelnosti, tak můžeme využít náhodná čísla. Náhodná čísla můžeme generovat pomocí akce `generate-random-number`:

``` LISP
(defconst gl-randnum 1)

(defrule 
	(true)
=> 
	(generate-random-number 100)
	(up-get-fact random-number 0 gl-randnum)
	(up-chat-data-to-all "gl-randnum %d" g: gl-randnum)
	(disable-self)
)

(defrule 
	(up-compare-goal gl-randnum > 50)
=> 
	(chat-to-all "Random number is greater than 50")
	(disable-self)
)

(defrule 
	(up-compare-goal gl-randnum < 51)
=> 
	(chat-to-all "Random number is less than 51")
	(disable-self)
)
```

### Časovače (Timers)
Pokud chceme provést sadu akcí po určité době, můžeme použít časovače. Časovač nastavíme pomocí akce `enable-timer`:

``` LISP
(enable-timer TIMER_NUMBER SECONDS)
```

Pro zrušení časovače můžeme použít akci `disable-timer`:

``` LISP
(disable-timer TIMER_NUMBER)
```

Toto spustí časovač `TIMER_NUMBER`, který bude odpočítávat `SECONDS` v sekundách. Můžeme zkontrolovat, zda časovač skončil pomocí podmínky `timer-triggered`:

``` LISP
(timer-triggered TIMER_NUMBER)
```

Pokud chceme, aby AI něco prohlásila přesně po 30 sekundách, můžeme to snadno provést kombinací všech výše uvedených příkazů: 

``` LISP
(defconst timer-shout 1) ; Toto je cislo casovace

(defrule ; Pro spusteni casovace
    (true)
=>
    (enable-timer timer-shout 30)
    (disable-self)
)

(defrule
    (timer-triggered timer-shout)
=>
    (chat-to-all "GRRR!")
    (disable-timer timer-shout)
    (enable-timer timer-shout 30)
)
```

Když chceme, aby naše AI komunikovala každých 30 sekund, můžeme náš příkaz `disable-timer` následovat příkazem `enable-timer` přímo poté.


### Přidání AI scriptu do hry
![AI_ifff](assets/AI_ifff.jpg)

Nyní jste pravděpodobně netrpěliví, abyste své pravidlo chování otestovali. Nejprve musíte svou AI uložit. Po té co jste pro ní vybrali jméno, uložte soubor s příponu `.per`. Všechny AI jsou uloženy ve složce AI. Přejděte do adresáře `My Computer/Local Disk (C)/Program Files/Microsoft Games/Age of Empires II/AI/` a do této složky uložte svou AI. 

Dále vytvořte nový prázdný dokument a ten pojmenujte stejným názvem jako vaši AI, ale s příponou `.ai` místo `.per`.

Tedy názvy vašich dvou souborů mohou být:

- `Moje_AI.per`
- `Moje_AI.ai`

Soubor `.per` (personality) obsahuje veškerý kód, který hra čte. Hra však potřebuje soubor `.ai`, aby mohla otevřít soubor AI. Takže každý soubor AI, který vytvoříte, bude potřebovat dva soubory, jeden s příponou `.per` a jeden s příponou `.ai`.

Nyní, abyste viděli svou AI v akci, otevřete Age of Empires a připravte se na spuštění náhodné mapové hry. Chcete-li se svou AI hrát v náhodné hře, klikněte na šipku vlevo od vašeho hráčského souboru a vyberte název zvolené AI.

## Demo soubory
Zde je jednoduchý příklad AI scriptu, na kterém můžte stavět:

- [basic.ai](./src/basic.ai)
- [basic.per](./src/basic.per)

### Nahrání dílčích personalit
Můžete načíst soubory osobnosti z dalších souborů. To však nejde použít v pravidlech.
``` LISP
(load "persons\darkage")
```
(Toto předpokládá, že existuje soubor s názvem *darkage.per* ve složce s názvem *persons*)

## Vy toho chcete víc?

- rozšíření scriptů o aritmetiku [zde](https://airef.github.io/parameters/parameters-details.html#mathOp).

## Zdroje:
- [Creating simple AI scripts for your custom campaigns](https://forums.ageofempires.com/t/creating-simple-ai-scripts-for-your-custom-campaigns/210881)
- [A guide to scripting your own AI for AOEII HD](https://steamcommunity.com/sharedfiles/filedetails/?id=1238296169)
- [AI Scripting Encyclopedia](https://airef.github.io/)
- [YouTube : aoe2 ai scripting tutorial](https://www.youtube.com/playlist?list=PL8UM8VqZhE0tRwdn-uvTUSvugUOArNc_j)