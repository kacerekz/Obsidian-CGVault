# Seminář diferenciálních rovnic
## První přednáška
### Doporučená literatura
- A. Kufner - Obyčejné diferenciální rovnice (ZČU)
- J. Kurzweil - Obyčejné diferenciální rovnice (SNTL)
- M. Ráb - Metody řešení ODR (MU Brno)

Záznamy přednášek KMA/SDR jsou dostupné [zde](http://www.home.zcu.cz/~benedikt/SDR). Spolu s nimi se zde nachází i výukový text P. Tomiczka, jehož 10. kapitola se věnuje diferenciálním rovnicím, tak jak byly původně přednášeny na KMA/MA2. Obsah textu nejlépe odpovídá náplni KMA/SDR, ostatní doporučená literatura obsahuje i látku nad rámec semináře.

### Diferenciální rovnice
Diferenciální rovnice si představíme na příkladu tzv. **populačního modelu**. Začneme s jednoduchou reprezentací toho, jak se členové nějakého druhu množí. Může jít třeba o bakterie, viry, zvířata, ale i člověka.

$y(t)$ .............. **počet jedinců** v čase $t$
$y(t + \Delta t)$ ..... **počet jedinců** v čase $t+\Delta t$
```ad-note
title: Poznámka
collapse: open

Symbol $\Delta t$ musíme chápat jako jeden celek, který má význam nějakého časového úseku. Pro přehlednost se proto někdy používá např. značení $h$.

```
Počet jedinců v čase $t + \Delta t$ pak můžeme přibližně vyjádřit jako:

$$
y(t + \Delta t) \approx y(t) + r \cdot y(t) \cdot \Delta t
$$
$$
y(t + \Delta t) - y(t) \approx r \cdot y(t) \cdot \Delta t \quad /\cdot \frac{1}{\Delta t}
$$
$$
\frac{y(t + \Delta t) - y(t)}{\Delta t} \approx r \cdot y(t)
$$
$$
\lim_{\Delta t \to 0} \frac{y(t + \Delta t) - y(t)}{\Delta t} = r \cdot y(t)
$$

Tato přibližnost spočívá v tom, že náš odhad se bude zpřesňovat se zkracujícím se úsekem $\Delta t$. Symbol $\approx$ zde má význam **asymptoticky rovno**.

Můžeme si to představit tak, že řekneme, že např. bakterie se budou dělit buněčným dělením, a zajímá nás o kolik se jejich počet změní třeba za jednu hodinu. Parametr $r$ bude řídit přírůstek bakterií.
```ad-note
title: Poznámka
collapse: open

Kdyby to dávalo fyzikálně smysl, tak by ale mohl být parametr $r$ i negativní, a pak by docházelo k úbytku - například při radioaktivním rozkladu.

```
Nicméně pokud bychom řekli, že každá bakterie se rozdělí za hodinu čtyřikrát ($r = 4$), tak bychom došli k celkem zásadně chybnému výsledku. Řekněme, že bakterie se opravdu rozdělí přesně každých 15 minut. Pak se bude počet bakterií vyvíjet následovně:

| čas [min] | počet bakterií |
| --------- | -------------- |
|0 |1|
|15|$1 + 1$ = **2**|
|30|$2 + 2$ = **4**|
|45|$4 + 4$ = **8**|
|60|$8 + 8$ = **16**|

Nám ovšem vyjde:
$$y(0) = 1$$
$$y(1) \approx y(0) + 4 \cdot y(0) \cdot 1 = 1 + 4 = 5$$
Proč je zde $\Delta t$ rovno 1? Právě proto, že počet dělení uvádíme v jednotkách "počet dělění za hodinu" a vyšetřujeme stav právě po jedné hodině. Tudíž za jednu časovou jednotku. Pokud bychom zjišťovali stav po půlhodině, použili bychom $\Delta t = 0.5$.

Problém je zjevně v tom, že vyšetřujeme příliš velký časový úsek. Sice totiž známe správně parametr $r$, který řídí dělění, nicméně nedokážeme podchytit to, že nový jedici se v rámci tohoto úseku již dělí také.

Jak se tedy výsledek změní, pokud časový úsek zkrátíme, třeba na 6 minut (0.1 hodiny)?

| čas [min] | $t$ | $y(t)$ |
| --------- | - | --- |
|0 |0  |1|
|6 |0.1|$1 + 4 \cdot 1 \cdot 0.1$ = **1.4**|
|12|0.2|$1.4 + 4 \cdot 1.4 \cdot 0.1$ = **1.96**|
|18|0.3|$1.96 + 4 \cdot 1.96 \cdot 0.1$ = **2.74**|
|24|0.4|$2.74 + 4 \cdot 2.74 \cdot 0.1$ = **3.84**|
|30|0.5|$3.84 + 4 \cdot 3.84 \cdot 0.1$ = **5.38**|
|36|0.6|$5.38 + 4 \cdot 5.38 \cdot 0.1$ = **7.53**|
|42|0.7|$7.53 + 4 \cdot 7.53 \cdot 0.1$ = **10.54**|
|48|0.8|$10.54 + 4 \cdot 10.54 \cdot 0.1$ = **14.76**|
|54|0.9|$14.76 + 4 \cdot 14.76 \cdot 0.1$ = **20.66**|
|60|1  |$20.66 + 4 \cdot 20.66 \cdot 0.1$ = **28.92**|

No tak tohle my nevyšlo a nevim proč ne. Popojedem.
```ad-hint
title: Experiment [C#]
collapse: open

Podobnou tabulku si můžete nechat vygenerovat sami. Zkoušejte různé hodnoty $dt$. Hodnoty se mohou mírně lišit od tabulky víše, jelikož v tabulce bylo mezi výpočty zaokrouhlováno na setiny.

``` c#
double t = 0;
double y0 = 1;
double dt = 0.1;
double r = 4;
double yt = y0;

Console.WriteLine($"{t}\t{t * 60:0.00}\t{yt}");

double printEach = 0.1;
double nextToPrint = printEach;

while (t <= 1)
{
    yt = yt + r * yt * dt;
    t += dt;
    
    if (t >= nextToPrint)
    {
        Console.WriteLine($"{t:0.00}\t{t * 60:0.00}\t{yt:0.00}");
        nextToPrint += printEach;
    }
}
```

#### Obyčejná diferenciální rovnice 1. řádu
Říkáme, že je prvního řádu, jelikož v ní figuruje nejvýše první derivace.
$$
y^{\prime}(t) = r \cdot y(t)
$$
Obecně diferenciální rovnice dělíme na **obyčejné** a **parciální**. Parciální diferenciální rovnice řešíme pro funkce více proměnných.


$$
r = 1, \quad y^\prime = y
$$
$$
y(t) = e^t,\quad y(t) =3e^t,\quad  y(t) =-e^t,\quad y(t) =C \cdot e^t,\quad C \in R
$$
.
$$
r = 2, \quad y^\prime = 2y
$$
$$
(e^{2t})^\prime = 2 \cdot e^{2t}
$$
.
$$
r \in R
$$
$$
y(t) = C \cdot e^{rt},\quad C \in R
$$
.

Diferenciální rovnice už jsme viděli, a to při počítání integrálů.
$$
\int cos(x) \,dx = sin(x) + c \qquad c \in R
$$
Vlastně tedy hledáme funkci, která splňuje nějakou diferenciální rovnici:
$$
y(x): y^\prime(x) = cos(x)
$$

##### Malthus [1766 - 1834]
Modeloval vývoj populace. Kolem roku 1800 došlo ke sčítání, tedy k určení počáteční populace $y(0)$.

$$
y_0 = y(0) = C \cdot e^{rt} = C \qquad \text{v roce 1800}
$$
$$
y_{50} = y_0 \cdot e^{50r} \qquad \text{odhad pro rok 1850}
$$

Ale reálně se populace obecně mohou vyvíjet i nějak tahle:

![[KMA-SDR_2022-02-15 21.10.32.excalidraw]]
Naráží na **kapacitu prostředí**.

##### Verhulst
Úprava výše uvedeného modelu tak, aby bylo možné podchytit i docházející kapacitu prostředí.

$$
y(t + \Delta t) - y(t) \approx r y(t) \cdot \Delta t - my^2(t) \cdot \Delta t
$$
$$
\frac{y(t + \Delta t) - y(t)}{\Delta t} \approx ry(t) - my^2(t)
$$
$$
y^\prime = ry - my^2 = (r - my) \cdot y \qquad r, m \gt 0
$$

S rostoucím $y$ tedy $(r - my)$ klesá.

Reálnou situací by mohly být množící se bakterie, kterým dochází substrát. Ty, které se scházejí, za začínají při konzumaci substrátu předbíhat, a ty, na které nezbyde, umírají. Čím častěji se tedy budou setkávat u "jídla", tím častěji budou umírat.

#### Kvalitativní analýza
Vycházíme z tvaru
$$
y^\prime = (r - my) \cdot y
$$
a odhadujeme jak se bude model chovat pro různé intervaly počátečních hodnot $y$. Zjistíme, kdy bude $y^\prime = 0$.

$$
(r - my) \cdot y = 0 \quad \iff \quad y = 0 \quad \lor \quad y = \frac{r}{m}
$$
Nyní můžeme odhadovat chování, viz náčrtek.

![[KMA-SDR_2022-02-15 21.21.16.excalidraw]]