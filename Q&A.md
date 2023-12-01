# Questions and Answers

## Jak zrychlím hru?
### Klávesové zkratky
- `-` - zpomalení
- `=/+` - zrychlení

Lze přenastavit v nastavení hry:
- Option -> Hotkeys -> Speed Up/Down

### Steam
Zrychlení x4 a x8 je dostupné v nastavení hry [dle](https://www.youtube.com/watch?v=jGaaQ6FmxiU):
- Steam -> Library -> Right click on game -> Properties -> Set Launch Options -> `DEBUGSPEED`

## Užitečne kusy kódu

### up-build
Využívá se pro přidání budovy do fronty na postavení s podrobným nastavením.


`(up-build <PlacementType> <EscrowGoalId> <typeOp> <BuildingId>)`

PlacementType:
- `place-normal`
- `place-forward`

EscrowGoalId:
- `0`
- `with-escrow`

TypeOp: [wiki](https://airef.github.io/parameters/parameters-details.html#compareOp)

BuildingId: [wiki](https://airef.github.io/tables/objects.html)


### Psaní do konzole

``` LISP
(chat-local-to-self "Test")
(chat-to-all "Hi")
(up-chat-data-to-self "Food: %d." c: 5)
```

### Logování
- `up-log-data` 
- [wiki](https://airef.github.io/commands/commands-details.html#up-log-data)

## Uprava AI scriptů

### Boj
https://airef.github.io/commands/commands-details.html#up-retreat-to

### Jak chytat ryby?

### Jak progresivně zvyšovat počet budov pro armádu?

### Jak upravit rozložení ekonomiky?
