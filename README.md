## VIMIAC10 Mesterséges intelligencia tárgy - 2022/23. I. félév - 2. házi feladat

# Lunar Lander

## Környezet

Arstotzka állam hatalmas technológiai fejlődésen esett át az elmúlt évtizedekben, mióta a tudósoknak már nem kell 
a háborúk miatt külföldre menekülniük. E nagyszerű folyamat tetőpontján a Központi Bizottság úgy döntött, hogy immár közel 
hetven évnyi kihagyás után újból embert kell juttatnunk a Holdra, mégpedig nem mást, mint Arstotzka legkiválóbb lakosainak egyikét.
Tekintve azonban, hogy Arstotzka polgárait még a tengeribetegség is szörnyen megviseli (ez nem csoda, hiszen Arstotzka kicsiny 
de büszke nemzetének sajnos még tengerpartja sincs), így a küldetés teljes egészét úgy kell megterveznie az újonnan alakult 
Arstotzkai Űrügynökségnek, hogy az szükség esetén teljesen autonóm módon is működni tudjon. 
Mostanra a rendszer szinte minden komponense készen áll, viszont a mérnökök problémába ütköztek a leszállást irányító 
szoftver implementációjával. Így tehát ezen házi feladat során ránk hárul a nemes és megtisztelő feladat, hogy Arstotzka
egy polgárát (állapotától függetlenül) eljuttathassuk a Holdra!


## Feladat

Ebben a házi feladatban a cél egy tanuló ágens implementálása, amely képes egy egyszerű szimulációs környezetben 
biztonságos leszállást végrehajtani egy holdraszálló egységgel. Ehhez **javasoljuk a táblázatos Q-tanulás használatát**. 
Egy kiértékelés $1.000.000$ tanulási iterációt és $10$ epochnyi éles következtetést jelent. 
Egy iteráció azt jelenti, hogy az ágens megkapja a legfrissebb környezeti állapotot, és az alapján visszaadja, hogy mit cselekszik. 
Esetünkben **0: ha nem csinál semmit, 1: ha bekapcsolja a főhajtóművet, 2: ha bekapcsolja a baloldali hajtóművet, és 3: ha 
bekapcsolja a jobboldali hajtóművet**. Ha az ágens valamilyen módon landol, vagy kifut az időből (200 iteráció), 
akkor a szimuláció elölről kezdődik. Egy epoch az indulástól számítva az ágens landolásáig, illetve legfeljebb 
200 iteráción keresztül tart. Az ágensnek minden iteráció után lehetősége van tanulni, ehhez megkapja az előző állapotot, 
az új állapotot, a cselekvését és a kapott jutalmat. Az epochok és a tanítás végét is függvény jelzi az ágens számára.

A feladat megoldásához kiadunk egy, a Moodle rendszerben futtatotthoz hasonló kiértékelő környezetet. 
Kérjük, hogy első körben ezen történjen meg a házi feladat tesztelése. A tanítás $10^6$ iteráción keresztül történik. 
Ezután következik a kiértékelés, amely $10$ darab szimulációból (epoch-ból) áll. Ez a $10$ darab szimuláció képezi
a kiértékelés alapját. Minden szimulációnak ötféle kimenetele lehet:
* **landolás**: Az ágens sikeresen letette a holdraszálló egységet a platformra, a megengedett toleranciaértéken belüli sebességgel (<= 2m/s).
* **landolás (törött láb)**: Az ágens sikeresen letette a holdraszálló egységet a platformra a megengedett sebességnél gyorsabban, de emiatt csak a leszállóegység lábai sérültek, az egység működőképes maradt (<= 4m/s).
* **landolás (megsemmisült)**: Az ágens a leszállás során eltalálta a platformot, viszont túl nagy sebességgel érkezett (> 4m/s), ezért a leszállóegység jelentős sérülést szenvedett.
* **lezuhant**: Az ágens elérte a szimulációs tér szélét, és nem találta el a platformot.
* **letelt az idő**: Az előre meghatározott időkeret (200 iteráció) letelt, és az ágens még nem érte el a szimulációs tér szélét.

A kiértékelés során végrehajtott $10$ darab iteráció közül minden **landolás** eredmény $4$ pontot, a **landolás (törött láb)** 
eredmények $2$ pontot, a **landolás (megsemmisült)** eredmény pedig $1$ pontot ér. A **lezuhant** és **letelt az idő** 
egyaránt $0$ pontot érnek, az elérhető maximális pontszám pedig $12$. (Ez azt jelenti, hogy a maximum pontot azzal is meg lehet 
szerezni, hogyha az ágens 10-ből 3-szor tökéletesen landol.)


## A szimuláció folyamata és a jutalomrendszer

A szimuláció egy $300$ méter széles, és $200$ méter magas térben történik. A holdraszálló egység a tér közepe fölött, $180$ méteres 
magasságban kezd, és az a feladata, hogy a tér alján véletlenszerűen elhelyezett, 40 méter szélességű platformon landoljon a hajtóművek segítségével. Emellett a holdraszálló egység kezdősebessége is véletlenszerű, a kezdősebességét leíró vektor vízszintes komponensét 
$-1$ és $1$, függőleges komponensét pedig $1$ és $5$ között (itt a sebességvektor függőleges komponense esetén a pozitív irány lefelé mutat) 
egyenletes eloszlás szerint sorsoljuk. A szimuláció során az ágens használhatja a leszállóegység három hajtóművét, 
amelyek közül kettő oldalirányba, egy pedig lefelé "mutat". 
A leszállóegységre folyamatosan hat a lefelé mutató gravitációs erő, amelyhez hozzáadódik egy-egy megfelelő irányú erővektor 
a három hajtómű bármelyikének aktiválása esetén.
A szimuláció véget ér, hogyha a leszállóegység eléri a szimulációs tér bármelyik szélét, 
vagy ha letelik a rendelkezésre álló idő. A szimuláció lehetséges kimeneteinek listájához lásd: előző bekezdés. 
Minden szimuláció kezdetén a jutalom értéke $0$, és minden iterációban módosul az alábbi szabályok szerint:
* Hogyha a leszállóegység használta valamelyik hajtóművét a háromból (tehát `[1, 2, 3]` cselekvések): $-0.01$
* Hogyha a platformra mutató vektor vízszintes komponensének abszolútértéke csökkent az előző iterációhoz képest: $+0.1$
* Hogyha a platformra mutató vektor vízszintes komponensének abszolútértéke növekedett az előző iterációhoz képest: $-0.1$
* Hogyha a szimuláció véget ért, akkor az egyes kimenetelekhez tartozó jutalmakat az alábbi táblázat írja le (itt $V$ a beérkezés pillanatában mért sebességet jelöli):

|**Kimenetel**|**Jutalom**|
|---          |---       |
|**landolás**|$+100$|
|**landolás (törött láb)**|$+(50 - 10V)$|
|**landolás (megsemmisült)**|$+10$|
|**lezuhant**|$-100$|
|**letelt az idő**|$-10$|


## A megfigyelési tér

Az ágens minden iterációban megkapja a leszállóegységtől a platform közepébe mutató vektor X és Y komponensét, illetve a leszállóegység 
pillanatnyi sebességvektorának X és Y komponensét. Így tehát a környezet állapotát leíró vektor: `[TargetX, TargetY, VelocityX, VelocityY]` 
A vektor mind a négy értéke folytonos (float), és az alábbi tartományban lehet:
|Érték|min|max|
|:-:|---|---|
|`TargetX`|$-300$|$300$|
|`TargetY`|$0$|$200$|
|`VelocityX`|$-7$|$7$|
|`VelocityY`|$-7$|$7$|


## Beadandó

* A **Moodle**-re python és java nyelven írt implementáció esetében is egy-egy fájlt kell feltölteni. Ehhez kiadunk egy-egy sablont, amiben a szükséges függvények megtalálhatóak. Ezek szignatúráit nem érdemes módosítani, különben nem fog lefutni a beadás. 
* Java implementáció esetén nem lehet használni semmilyen külső csomagot, csakis a java 11.0.7 beépített könyvtárait. A Q-táblázatos megoldáshoz előre megírtunk egy egyszerű osztályt a [LunarLanderAgentBase.java](LunarLanderAgentBase.java) fájlban. 
* Python-ban (3.7.3-as verzió) írt megoldás esetén lehet használni a `numpy` könyvtárat (1.16.2-es verzió). 
* Mindkét esetben **pontosan egy fájlt kell feltölteni**: [LunarLanderAgentBase.java](LunarLanderAgentBase.java) vagy [lunar_lander_agent_base.py](lunar_lander_agent_base.py) névvel.


## A futtatókörnyezet beállítása

A feladatot **java** és **python** nyelven egyaránt meg lehet oldani, viszont a kiértékelőt python nyelven implementáltuk,
így saját számítógépen való teszteléshez szükséges egy Python környezet telepítése.
A telepítéshez elsőként telepítsük a python környezetet, amennyiben az nincs eleve telepítve. 
Ezt megtehetjük a hivatalos [Python telepítő](https://www.python.org/downloads/) vagy a - valamelyest könnyebben kezelhető -
[Anaconda keretrendszer](https://www.anaconda.com/products/distribution) segítségével. 
Mindkét opció egyformán elérhető Windows, Linux és Mac OS operációs rendszerekre.
Ezt követően telepítsük a futtatáshoz szükséges python könyvtárakat, amelyek listáját a [requirements.txt](requirements.txt) fájlban adtuk meg. 
A telepítés legegyszerűbben a `pip` parancs segítségével eszközölhető, az alábbi módon: ```pip install -r requirements.txt```
### Java-specifikus beállítások
Amennyiben a megoldásunkat java nyelven szeretnénk elkészíteni, meg kell adnunk a keretrendszer számára a java 
futtatókörnyezet helyét. Ezt a [lunar_lander_java_agent.py](lunar_lander_java_agent.py) fájlon belül a `java_path` 
változó beállításával tehetjük meg. Például, hogyha a java executable a `/usr/lib/jvm/java-11-openjdk-amd64/bin/` mappában található, 
akkor ezt az alábbi módon állíthatjuk be a [lunar_lander_java_agent.py](lunar_lander_java_agent.py) fájlban:
```
java_path = '/usr/lib/jvm/java-11-openjdk-amd64/bin/'
```
Ezt követően implementáljuk a megoldásunkat a [LunarLanderAgentBase.java](LunarLanderAgentBase.java) fájl módosításával, a lentebb
részletezett interfész-specifikáció alapján. Az implementált megoldást ki lehet próbálni egy GUI-val rendelkező megjelenítő 
([lunar_lander_gui.py](lunar_lander_gui.py)) vagy a Moodle által is használt kiértékelő 
([lunar_lander_evaluator.py](lunar_lander_evaluator.py)) futtatásával.
### Python-specifikus beállítások
Hogyha a megoldást python nyelven szeretnénk elkészíteni, győződjünk meg róla, hogy a [lunar_lander_gui.py](lunar_lander_gui.py) és a
[lunar_lander_evaluator.py](lunar_lander_evaluator.py) fájlokban a `LunarLanderAgent` osztály egy példányát adjuk meg az `agent` változó 
értékeként, és **NEM** a `LunarLanderJavaAgent` osztály példányát. Ennek hatására a keretrendszer a 
[lunar_lander_agent_base.py](lunar_lander_agent_base.py) fájlban található implementációt fogja alapul venni a futása során.

Ezt követően implementáljuk a megoldást a [lunar_lander_agent_base.py](lunar_lander_agent_base.py) fájl módosításával, a lentebb 
részletezett interfész-specifikáció alapján. Az implementált megoldást ki lehet próbálni egy GUI-val rendelkező megjelenítő 
([lunar_lander_gui.py](lunar_lander_gui.py)) vagy a Moodle által is használt kiértékelő 
([lunar_lander_evaluator.py](lunar_lander_evaluator.py)) futtatásával.


## Az interfészről

A feladat megoldása során csak a [LunarLanderAgentBase.java](LunarLanderAgentBase.java) vagy [lunar_lander_agent_base.py](lunar_lander_agent_base.py) 
fájlt szabad módosítani, illetve ez az egyetlen fájl, amelyet a Moodle rendszerbe be is kell adni. Az ebben a fájlban fájlban található `LunarLanderAgentBase` osztályt 
implementálja majd a `LunarLanderAgent` osztály, amely kiegészíti azt egy `step()` függvénnyel. A `LunarLanderAgentBase` osztály konstruktorában alapból 
definiálásra kerül egy Q táblát leíró változó (`qTable` vagy `q_table`), és egy `epsilon` paraméter. Ezek létezése a `step()` függvény futása miatt fontos, 
**enélkül az nem fog működni**.

Emellett a `LunarLanderAgentBase` osztályt definiáló fájl tartalmaz egy `OBSERVATION_SPACE_RESOLUTION` nevű statikus változót, amelyben meg kell adnunk, 
hogy az állapottér 4 értékét egyenként hány szintre fogjuk kvantálni. Ez azért fontos, mert **ez határozza meg a Q tábla méretét**. Emiatt az állapot 
kvantálásáért felelős függvény (`quantizeState()` vagy `quantize_state()`) mindig egy olyan integer listát/tömböt kell visszaadjon, amellyel a 
Q tábla indexelhető. Például, hogyha a kvantálási szinteket a következő módon választjuk meg: `[15, 10, 15, 8]` akkor a kvantáló függvény 
visszatérési értéke értékkészletének a következőnek kell lennie: `[0-14, 0-9, 0-14, 0-7]` (tehát például az első állapotleírónak legalább 0 és legfeljebb 14 **integer** értékűnek kell lennie).

**Mielőtt belekezdenénk az implementációba, javasolt tanulmányozni a `LunarLanderAgent` osztályban definiált `step()` függvény működését!**

A megvalósítandó függvények szignatúráinak leírása az alábbi:

**konstruktor** `LunarLanderAgentBase()`, `LunarLanderAgentBase()`
  * `observation space`: minimum-maximum párok listája/tömbje, amelyek az állapotteret leíró változók által fölvett legkisebb és legnagyobb értéket (float) tartalmazzák: `[platformra mutató vektor X komponense, platformra mutató vektor Y komponense, sebességvektor X komponense, sebességvektor Y komponense]`
  * `action space`: integer lista/tömb, a lehetséges cselekvésekkel (`[0, 1, 2, 3]`, ahol a 0 a "ne csinálj semmit", 1 a "fő hajtómű", 2 a "hajtómű jobbra" és 3 a "hajtómű balra")
  * `number of iterations`: tanulási iterációk száma

**állapotkvantálás** `quantizeState()`, `quantize_state()` - Visszaadja a kapott folytonos állapothoz tartozó kvantált értéket.
  * `observation space`: a konstruktor által is megkapott minimum-maximum párok listája/tömbje, az állapottér változóihoz
  * `state`: a kvantálandó állapot

**epoch vége** `epochEnd()`, `epoch_end()` - Minden epoch végén meghívódik.
  * `epoch reward sum`: az epochban szerzett jutalmak összege

**tanulás** `learn()` - Minden iterációban meghívódik a tanulás során, ez alapján lehet tanítani az ágenst.
  * `old state`: előző állapot
  * `action`: az előző állapotban végrehajtott cselekvés
  * `new state`: a cselekvés eredményeképp létrejött állapot
  * `reward`: az új állapotba lépéssel együtt kapott jutalom

**tanítás vége** `trainEnd()`, `train_end()` - Jelzi a tanítás végét, ez után kezdődik a kiértékelés.


## Javaslatok

* A feladat megoldható maximális pontszámmal Q táblázatos módszerrel.
* A Q tábla méretének (tehát az állapottér változóira alkalmazott kvantálási szintek számának) megfelelő meghatározása kulcsfontosságú. Túlzottan nagy felbontású kvantálás mellett az ágens nem fogja kellő alapossággal fölfedezni az állapotteret, és a számításigény és a memóriaigény is jelentősen megnövekszik. Túlságosan durva (alacsony felbontású) kvantálás mellett pedig előfordulhat, hogy az ágens nem jut kellő mennyiségű információhoz, és így nem tud majd megfelelően dönteni.
* **Az állapotteret nem csak lineárisan lehet kvantálni.** Az állapottér kevésbé fontos részeit (tehát azokat a részeket, ahol feltételezzük, hogy mindenképp csak egyféle helyes döntés létezik, például a sebességvektor szélsőértékeinek közelében, ahol biztosan lassítani kell) kvantálhatjuk alacsonyabb felbontással is, mint a többi részt. Ezáltal a futásidő javul, és a tanulás során az ágens jobban fel tudja fedezni az állapotteret.
* Erősen ajánlott tanulás közben bizonyos időközönként módosítani az `epsilon` ("explore/exploit") paramétert (epsilon-decay), valamint a Q-tanulás frissítési szabályainak paramétereit. Emellett akár a jutalmakat is át lehet skálázni az ágens működésén belül.
* A `trainEnd()`/`train_end()` függvények iránymutatásként vannak jelen.
* Bármilyen felmerülő kérdés esetén a Mesterséges Intelligencia Teams-csoport "Házi Feladat" csatornájába érdemes írni. Itt egyrészről hamarabb érkezik majd válasz a föltett kérdésekre, illetve a válaszokból így mások is okulhatnak.
* Érdemes a beadási határidő előtt legalább néhány nappal elkezdeni a házi feladat megoldását, mert a határidő közeledtével a rendszer terhelése és az oktatók válaszideje is jelentősen megnőhet.


## Egyéb fontos tudnivalók

* A megoldás forráskódja nem tartalmazhat ékezetes vagy nem ASCII[0:127] karaktert.
* A megoldásnak nem kell, hogy kimenete legyen, ezért ne legyen bennefelejtett debug print, mert errort jelezhet a kiértékelő.
* A feltöltött megoldás megengedett futásideje CPU-időben 40 másodperc. Időtúllépés esetén a rendszer automatikusan leállítja a kód futását.
* A feltöltött megoldás összesen legfeljebb 1000 MB memóriát allokálhat. Ezen érték túllépése esetén a rendszer automatikusan leállítja a kód futását.
* A 2. beadástól kezdve 10 percenként lehet újra próbálkozni.
* Az újonnan beadott megoldás eredménye felülírja az előzőt.
* Minden bugreportot/javaslatot szívesen fogadunk, igyekszünk javítani.
