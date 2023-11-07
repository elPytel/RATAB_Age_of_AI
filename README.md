# Age of AI 
Pojď si zahrát legendární strategii Age of Empires II, naučit se základy programování a zjistit, jak funguje umělá inteligence ve hrách. Na konci akce se utkáš s ostatními účastníky v epickém souboji vašich civilizací.

![Age of AI](./assets/Age_of_AI.jpeg)

## Instalace Age of Empires 2
Stažení a nainstalace ze [Steamu](https://store.steampowered.com/app/813780/Age_of_Empires_II_Definitive_Edition/).

## Scriptování AI
![AI](assets/AI.jpg)

Jak se nyní dozvíte, tak naše AI není "Artificial Intelligence", ale "Accumulated IFs". Všechny AI v Age of Empires 2 jsou napsané v jazyce [LISP](https://cs.wikipedia.org/wiki/Lisp). LISP je velmi starý jazyk, který byl vytvořen v roce 1958. 

### jak definovat pavidlo (IFs)
Základem je podmíněné vykonávání příkazů IF -> THEN.

``` LISP
(defrule       ;Toto začíná pravidlo. Defrule je zkratka pro "definovat pravidlo".
    (fact 1)   ;Toto je "if" část pravidla. AI zkontroluje, zda jsou tato fakta
    (fact 2)   ;pravdivá. Pokud jsou všechny fakty pravdivé, pokračuje AI na
    (fact 3)   ;akce. Ne všechna pravidla mají 3 fakty, ale všechna mají alespoň jeden.
    ...
    =>         ;Toto je "then" část pravidla.
    (action 1)
    (action 2) ;Pokud jsou všechny fakty pravdivé, dojde k těmto akcím. Ne všechna 
    (action 3) ;pravidla mají tři akce, ale všechna mají alespoň jednu.
    ...
) ;tato závorka ukončuje pravidlo.
```

### Přidání AI scriptu do hry
![AI_ifff](assets/AI_ifff.jpg)

Nyní jste pravděpodobně netrpěliví, abyste své pravidlo chování otestovali. Nejprve musíte svou AI uložit. Po té co jste pro ní vybrali jméno, uložte soubor s příponu `.per`. Všechny AI jsou uloženy ve složce AI. Přejděte do adresáře `My Computer/Local Disk (C)/Program Files/Microsoft Games/Age of Empires II/AI/` a do této složky uložte svou AI. 

Dále vytvořte nový prázdný dokument a ten pojmenujte stejným názvem jako vaši AI, ale s příponou `.ai` místo `.per`.

Tedy názvy vašich dvou souborů mohou být:

- `Moje_AI.per`
- `Moje_AI.ai`

Soubor `.per` obsahuje veškerý kód, který hra čte. Hra však potřebuje soubor `.ai`, aby mohla otevřít soubor AI. Takže každý soubor AI, který vytvoříte, bude potřebovat dva soubory, jeden s příponou `.per` a jeden s příponou `.ai`.

Nyní, abyste viděli svou AI v akci, otevřete Age of Empires a připravte se na spuštění náhodné mapové hry. Chcete-li se svou AI hrát v náhodné hře, klikněte na šipku vlevo od vašeho hráčského souboru a vyberte název zvolené AI.

### Demo
- [basic.ai](./src/basic.ai)
- [basic.per](./src/basic.per)

## Zdroje:
- [Creating simple AI scripts for your custom campaigns](https://forums.ageofempires.com/t/creating-simple-ai-scripts-for-your-custom-campaigns/210881)
- [A guide to scripting your own AI for AOEII HD](https://steamcommunity.com/sharedfiles/filedetails/?id=1238296169)
- [AI Scripting Encyclopedia](https://airef.github.io/)
- [YouTube : aoe2 ai scripting tutorial](https://www.youtube.com/playlist?list=PL8UM8VqZhE0tRwdn-uvTUSvugUOArNc_j)