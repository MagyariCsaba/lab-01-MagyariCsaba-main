# Labor 1-2.

# Párhuzamos programozás - háttér és célok

A párhuzamos programozás a számítástechnika egy olyan ága, amely a műveletek egyszerre, több processzor vagy számítógép segítségével történő végrehajtására koncentrál. Ez szemben áll a hagyományos soros programozással, ahol a műveletek egymás után kerülnek végrehajtásra egyetlen számítási egységen.

[Iványi Antal - PÁRHUZAMOS ALGORITMUSOK]([http://compalg.inf.elte.hu/~tony/Elektronikus/Parhuzamos/P1H.xml](http://compalg.inf.elte.hu/~tony/Elektronikus/Parhuzamos/P1H.xml)) elektronikus tankönyvében kifejti::

> A számítógépes feladatmegoldás természetes és hagyományos világa a soros adatfeldolgozás. A különböző alkalmazási területek nagy teljesítményigényének és környezetünk párhuzamos jellegének hatására azonban rohamosan fejlődik a párhuzamos feldolgozás is.
> ...
> **Párhuzamos algoritmusok**: Ha egy feladatot együttműködő processzorok segítségével oldunk meg, akkor *a számítási idő rendszerint csökken*, mivel bizonyos műveletek egyidejűleg elvégezhetők. Ennek a csökkenésnek az ára a nagyobb beruházásigény és az elvégzendő munka mennyiségének növekedése.

### Miért fontos a párhuzamos programozás?

A párhuzamos algoritmusok lehetővé teszik, hogy egy adott feladatot több processzor vagy számítógép együttes erőfeszítéseivel oldjunk meg. Ennek eredményeként a számítási idő rendszerint csökken, mivel bizonyos műveletek egyidejűleg hajthatók végre. Például egy nagy adathalmaz szétosztható kisebb részekre, amelyek párhuzamosan feldolgozhatók, majd a végeredmény összegyűjtésre kerül.

### Milyen kihívásokkal nézünk szembe a párhuzamos programozás során?

A párhuzamos algoritmusoknak számos előnye van, de ezek kiaknázása nem mindig egyszerű. Az egyidejű feldolgozás kihasználása gyakran nagyobb beruházásigényt és az elvégzendő munka mennyiségének növekedését eredményezi. További kihívások közé tartozik az adatmegosztás, a szinkronizáció, a holtidők kezelése és az erőforrások hatékony felhasználása.


# Feladatok párhuzamos jellege

Mint láttuk, a párhuzamos programozás alapvető célja, hogy a rendelkezésre álló erőforrásokat (például több processzormagot vagy számítógépet) hatékonyan használja ki, gyorsítva ezzel a feladatok elvégzését. Egy feladat párhuzamos jellegének kiaknázásához először fel kell ismerni és felbontani azt kisebb, egymástól független vagy részben független alfeladatokra.

## Felbontási lehetőségek

1.  **Adatfelbontás**: A feladatban érintett adathalmazt több részre osztjuk, és mindegyik részen ugyanazt a műveletsort végezzük el.
2.  **Funkcionális felbontás**: Különböző alfeladatok vagy függvények végrehajtása párhuzamosan. Ezen felbontások egymással kevéssé vagy egyáltalán nem érintkeznek.

## Adatpárhuzamos programozás

Az adatpárhuzamosság a párhuzamos programozás egyik típusa, ahol az adatokon párhuzamosan hajtanak végre műveleteket. Itt az adatokra mint egységre koncentrálunk, és az adathalmazon belül azonos műveletet hajtunk végre.

**Példa**: Egy képfeldolgozási alkalmazás esetén az adatpárhuzamosság során minden egyes képpontot vagy képrészletet egyidejűleg dolgozhatunk fel (pl. színkorrekció vagy élességállítás).

## Funkcionális párhuzamos programozás

A funkcionális párhuzamosságnál a különböző függvényeket vagy eljárásokat hajtjuk végre egyidejűleg. Az ilyen függvények gyakran különböző adathalmazokon dolgoznak, és különböző számítási feladatokat végeznek el.

**Példa**: Egy játék-motorban a grafikai renderelés, az AI számítások, a fizikai szimuláció és a hangfeldolgozás funkcionálisan párhuzamosan hajtódhatnak végre, mivel ezek különböző számítási feladatokat képviselnek.

## Nem vagy gyengén párhuzamosítható feladatok

Miközben a párhuzamosítás nagy előnyökkel járhat, fontos felismerni és megérteni, hogy nem minden feladat alkalmas erre. A hatékony párhuzamos programozás nem csak a műveletek párhuzamos végrehajtására összpontosít, hanem felismeri azt is, mely részei a kódnak nem párhuzamosíthatóak hatékonyan, és amennyiben lehetséges, átfogalmazza, optimalizálja azokat a részeket.

Vannak olyan problémák vagy algoritmusok, ahol az egyes lépések erősen függenek az előző lépések eredményeitől, ezért nem lehet őket egyszerűen feldarabolni kisebb, párhuzamosan futtatható részekre.

1.  **Rekurzív feladatok**: Bizonyos rekurzív algoritmusok, mint például a *hagyományos* Fibonacci-sorozat számítása, nem párhuzamosíthatóak hatékonyan, mert minden lépés az előző két lépés eredményétől függ.
    
2.  **Soros függőségű adatok**: Például egy banki tranzakciós rendszerben, ahol az egyes tranzakciók sorban történnek, és az egyik tranzakció eredménye befolyásolhatja a következő tranzakció kimenetelét. Itt a párhuzamosítás korlátozott lehet, mivel a tranzakciók sorrendjét meg kell őrizni.
    
3.  **Iteratív algoritmusok**: Néhány iteratív algoritmus, mint a Jacobi módszer vagy Gauss-Seidel módszer, nem párhuzamosítható hatékonyan, mert az egyes iterációk az előző iterációs eredményeitől függnek.

# Szálkezelés C++11-ben (és C++17-es kiegészítések)

A C++11 óta a nyelv natív  módon támogatja a többszálas programozást. Ez a támogatás több új fejléc formájában jelent meg. A szálak létrehozására és kezelésére szolgáló osztályok és függvények a `<thread>` fejlécen belül találhatóak, míg a megosztott adatok védelmét célzó eszközök, mint a zárak és a kondicionális változók, további fejlécekben helyezkednek el.

## Szálak létrehozása és kezelése

Minden szálnak rendelkeznie kell egy "kezdeti" függvénnyel, amely meghatározza a végrehajtandó utasításokat. Az alkalmazás fő szálában ez a `main()` függvény. Más szálak esetében a belépési pontot meg kell adni, pl.  az `std::thread` konstruktorában. Egy szálbelépési pont lehet egy függvénynév, egy lambdafüggvény, vagy egy funktor (olyan osztály/objektum, amelyik rendelkezik `operator()` tagfüggvénnyel)..

Egy új szál indítása után a kezdeti szál folytatja a végrehajtást. Megtörténhet, hogy a `main()` végére ér, ezáltal befejezve a programot, még mielőtt az új szál lefuthatott volna, vagy befejezhette volna a munkáját. Ezért, a szál bevárása érdekében meg kell hívni a szálobjektum `join()` tagfüggvényét.

## [`std::thread`](https://en.cppreference.com/w/cpp/thread/thread)
-   Konstruktorába függvénynevet, függvénypointert, funktort vagy lambdát adhatunk
	-   továbbá a függvények paramétereit
-   Konstruktorhívás után új szálat indít, paraméterként kapott függvényt az új szálban hajtja végre
- C++17-ben a `std::thread` képes az `std::invoke` használatával változatos hívási lehetőségeket kezelni, ami még rugalmasabbá teszi a használatát
-   `thread.join()`
	-   Hívó szálat *blokkolja*, amíg a létrehozott szál nem végez.
	-   Ha függvény végzett és a szál destruktora meghívódott `join()`  nélkül   az`std::terminate` hívásra kerül és a program`std::system_error`  kivételt dob.
	-   Írhatunk akár egy saját RAII-elvű `scoped_thread`  osztályt, ami becsomagolja `std::thread`-et  és destruktorában meghívja `join()`-t.
-   `thread.detach()`
	-   Lehetővé teszi a létrehozó száltól való leválást.
	-   Ne várja meg a főszál, míg a detach-elt szál befejeződjön, függetlenül hajtódik végre.
	-   detach-elt szál futása végén felszabadítja erőforrásait és nem dob `std::system-error`-t.

## Párhuzamos Hello World:

Ez az egyszerű példa bemutatja a `std::thread` használatát:

```cpp
	#include <iostream>
	#include <thread>
	void hello(){
		std::cout << "Hello Concurrent World!\n";
	}

	int main() {
		std::thread t(hello);
		t.join();
		return  0;
	}
```
Amennyiben nem kívánjuk bevárni a szálat, ez leválasztható a `detach()` tagfüggvény  meghívásával.

# Argumentumok átadása

Amikor argumentumokat adunk át az `std::thread` konstruktorának, a C++ alapértelmezésben az argumentumokat érték szerint adja át, másolva őket. Több okból is érdemes megérteni, miért van így:

### Elkerülésre kerülnek a "lógó" referenciák

Amikor a fő szál lefut és befejeződik, míg egy másik szál még fut, a fő szálban létrehozott változók élettartama véget érhet. Ha a változókat referenciaként adtuk át a szálnak, a szál egy már nem létező memóriaterületre fog hivatkozni. Ezt a problémát "lógó" vagy "érvénytelen" referenciának nevezik. Az érték szerinti átadás másolatot készít a változóról, így a szál ezen másolattal fog dolgozni, és nem áll fent a nem definiált viselkedés lehetősége.

### Kifejezőség és biztonság

Az átadás érték szerinti módja világosan jelzi a programozó számára, hogy a szálnak saját példánya van az adott változóból. Ez csökkenti az esetleges oldalhatások kockázatát, mivel a szál nem módosíthatja az eredeti változót. Emellett a programozók számára is egyértelművé teszi, hogy a szálak működés közben nem osztanak meg állapotot, kivéve, ha a programozó explicit módon referencia vagy pointer használatával ezt kérte.

### Konzisztencia az `std::bind`-dal

Az `std::bind` funkció, amely funkciósablonok létrehozásához használható, szintén érték szerint veszi át az argumentumokat. Az `std::thread` és az `std::bind` következetes működése egyszerűsíti a kód olvasását és megértését.

De mi van, ha hivatkozást szeretnénk átadni, például módosítani szeretnénk az eredeti változót a szálon belül? Erre szolgál az `std::ref` segédosztály, amely lehetővé teszi a hivatkozások átadását.Mint megglátjuk, az `std::ref` használata a fejlesztő felelősségére bízza az érvénytelen referenciák és az állapot megosztásának kérdését.

## Érték szerinti átadás

Az `std::thread` konstruktorban, a  funktor vagy függvény specifikálása után, egyszerűen felsoroljuk az átadásra szánt argumentumokat, paramétereket:

```cpp
	#include <iostream>
	#include <string>
	#include <thread>

	void thread_callback(int x, std::string str){
		std::cout << "Passed Number = "<< x << std::endl;
		std::cout << "Passed String = "<< str << std::endl;
	}
	
	int main(){
		int x = 42;
		std::string str = "Hello World";
		std::thread t(thread_callback, x, str);
		t.join();
		return 0;
	}
```

## Hivatkozás szerinti átadás `std::ref` segítségével

Amennyiben változót szeretnénk módosítani a szálon belül, szükségünk van hivatkozások átadására.

### [`std::ref`](https://en.cppreference.com/w/cpp/utility/functional/ref)
Az `std::ref` funkciót a C++ Standard Könyvtárban definiálják, és az a célja, hogy "csomagolóobjektumot" hozzon létre, amely referenciát tárol egy változóra. Ezt az objektumot érték szerint lehet átadni, ugyanakkor a tárolt referencia segítségével közvetlen hozzáférést biztosít az eredeti változóhoz.

Az `std::ref` a háttérben egy `std::reference_wrapper` típusú objektumot hoz létre. A `std::reference_wrapper` egy sablonosztály, amely lehetővé teszi egy változó referenciájának "becsomagolását", de mégis érték szerint történő átadását.

Például:

```cpp
int x = 10;
std::reference_wrapper<int> refWrapper = std::ref(x);
```
Ebben az esetben a `refWrapper` egy érték, amit másolni lehet, de mégis hivatkozik az `x` változóra. Ha módosítjuk a `refWrapper` által hivatkozott értéket, az `x` változót módosítjuk:

```cpp
refWrapper.get() = 20;  // x most 20
```

Amikor egy szálnak átadunk egy `std::ref` által létrehozott objektumot, valójában ezt az `std::reference_wrapper` objektumot adjuk át, ami lehetővé teszi számunkra a referenciaként történő átadást egy értékszerű csomagolásban.

### Példa

```cpp
#include <iostream>
#include <string>
#include <thread>
#include <functional>  // std::ref-hez szükséges

void thread_callback_ref(int &x, std::string &str) {
    x *= 2;
    str = "Modified inside the thread";
    std::cout << "Inside Thread - Modified Number = " << x << std::endl;
    std::cout << "Inside Thread - Modified String = " << str << std::endl;
}

int main() {
    int x = 42;
    std::string str = "Hello World";
    std::thread t(thread_callback_ref, std::ref(x), std::ref(str));
    t.join();

    // Módosított értékek a szálon kívül is
    std::cout << "Outside Thread - Modified Number = " << x << std::endl;
    std::cout << "Outside Thread - Modified String = " << str << std::endl;

    return 0;
}
```
A fenti kódban a `std::ref` segítségével hivatkozásokat adunk át az `thread_callback_ref` függvénynek, így a függvény módosításai az eredeti változókat érintik.

Fontos emlékezni arra, hogy amikor hivatkozásokat adunk át, biztosítani kell, hogy a hivatkozott változók élettartama meghaladja a szál élettartamát. Ellenkező esetben nem definiált viselkedés léphet fel.

# Laborgyakorlatok elvégzésének szabályzata

1. **Feladatkiadás és -beküldés:** A feladatok a GitHub Classroom platformon keresztül kerülnek kiadásra és a **lépésenként felépített megoldásoknak** is ezen az úton kell bekerülniük.
  
2.  **Megoldások elnevezése:** A kész megoldások fájlneve a `labX-fY.cpp` mintát kövesse, ahol `X` a laborgyakorlat sorszáma és `Y` az adott feladat sorszáma. Amennyiben a laborgyakorlat két hétig tart, `X` mindkét hét során az első hét sorszámát kell hogy kövesse.
    
3.  **Válaszok dokumentálása:** A felmerülő kérdésekre adott válaszokat egy `válaszok.md` nevű fájoklban kell rögzíteni.
    
4.  **Commitolás:** Minden egyes részfeladatnak külön commitban kell megjelennie. Feladatonként kötelező legalább egy commitot létrehozni. A commit üzeneteknek röviden és világosan kell bemutatniuk a megvalósított funkciót vagy az elvégzett változtatást. A commit historynak érzékeltetnie kell a megoldás időbeli, lépésről-lépésre történő kialakulását. **A teljes laborgyakorlatot, vagy több nem triviális feladatból álló, egyetlen commitban beküldő munkák nem elfogadhatók.**
    
5.  **Pusholás:** Minden laborgyakorlat során, az óra második felében, ***kötelezően legalább egyszer pusholni kell*** a kód változtatásokat!

Az említett szabályok betartása elősegíti a rendszerezett és átlátható munkavégzést, valamint a gyakorlatok hatékony ellenőrzését.

# Feladatok

1. Írjuk át a párhuzamos Hello World példát lambda függvény és funktor meghívásra. A szál a Hello World üzenet után írja ki az aktuális dátumot és időt is. Kompilálásra használjuk a terminálból a következő felépítésű parancsot:
	```
	g++ -std=c++17 -pthread filename.cpp -o programname
	```
	vagy
	```
	clang++-7 -std=c++17 -pthread filename.cpp -o programname
	```
	attól függően, milyen kompilátor áll a rendelkezésünkre.
2. Mi történik, ha a program végéről elhagyjuk a `t.join()` utasítást? Készíts egy képernyőfelvételt melyben látszik a program kimenete (ne felejtsd, a képernyőfelvétel feltöltése után commit & push).
	
3. Írjunk egy Hello World programot mely egy [`for` ciklus és `std::vector<std::thread>`  segítségével](https://thispointer.com/c11-how-to-create-vector-of-thread-objects/) hoz létre `n` szálat. A szálak a köszönésen kívül írjak ki a ciklus változót is, mely egyben szálazonosítóként is értelmezhető. Többszöri futtatás során, figyeljük meg ha szálak ütemezése (üzenetek sorrendje) determinisztikus-e?

4. Írjunk egy programot mely két szál segítségével, párhuzamosan számolja és írja ki a képernyőre egy négyzetes mátrix *főátló fölötti* és *alatti* elemek összegét.

5. Módosítsuk a 4. programot, a szálak pointer argumentumokon keresztül térítsék vissza az összeget, a főszál írja ki az eredményeket.

6. Írjunk egy programot mely két szál segítségével, párhuzamosan rendezi buborékos rendezéssel, egy négyzetes mátrix főátló fölötti és alatti elemeit, *külön-külön*. A rendezett elemeket a szálak egy bemeneti paraméterként kapott vektorokba térítik vissza. Készíts egy képernyőfelvételt melyben látszik a függvény implementációja és egy általad választott teszt eredménye (ne felejtsd, a képernyőfelvétel feltöltése után commit & push).

7. A [time](https://man7.org/linux/man-pages/man1/time.1.html) Linux parancs segítségével, hasonlítsuk össze az 6. program párhuzamos verzióját egy szekvenciálissal. Mekkora mátrix méretre észlelünk már gyorsulást? Igazoljuk a gyorsulást egy képernyőfelvétellel. 
