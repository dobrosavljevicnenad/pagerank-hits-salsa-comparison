# PageRank vs. HITS vs. SALSA — poređenje algoritama za rangiranje čvorova u usmerenim grafovima

Projekat iz predmeta **Naučno izračunavanje** (Matematički fakultet, Univerzitet u Beogradu), školska 2025/26.

**Tema sa spiska**: *HITS i SALSA algoritmi za rangiranje čvorova u usmerenim grafovima / poređenje sa PageRank algoritmom*

**Autor**: Nenad Dobrosavljević

## Opis projekta

Cilj projekta je implementacija tri poznata algoritma za rangiranje čvorova u usmerenim grafovima — **PageRank** [4], **HITS** [1, 2] i **SALSA** [3] — nad istim usmerenim grafom, i poređenje dobijenih rangiranja.

Za razliku od originalnih radova o HITS-u i SALSA-i, koji su ove algoritme definisali kao *query-dependent* (operišu nad malim "neighborhood graph"-om izgrađenim oko rezultata pretrage za dati upit), u ovom projektu ih primenjujemo **globalno**, nad celim grafom — u skladu sa formulacijom teme ("rangiranje čvorova", a ne "rangiranje rezultata pretrage"). Ovo je matematički legitimna primena istih algoritama (base set = ceo skup čvorova, neighborhood graph = ceo graf), samo bez query-specifičnog filtriranja.

Pošto ne postoji skup ljudski ocenjenih relevance labela (koje koristi npr. [5] za IR metrike MAP/MRR/NDCG), rangiranja poredimo **međusobno** — korelacijom rangova (Spearman, Kendall's τ) i preklapanjem top-k skupova čvorova. Ovaj pristup je direktno inspirisan radom Borodin et al. [6], koji rangiranja algoritama poredi na isti način, iz istog razloga (nedostatak relevance labela u većini realnih scenarija).

### Istraživačka pitanja / hipoteze

- **H1**: Na grafu koji je (skoro) jedna komponenta povezanosti, SALSA-in autoritet skor treba da bude blizu proporcionalan ulaznom stepenu (in-degree) čvora — poznat teorijski rezultat iz [3] — dok HITS to svojstvo nema.
- **H2**: HITS i SALSA, uprkos tome što (u našoj globalnoj varijanti) operišu nad istim grafom, mogu proizvesti rangiranja koja su slabije korelisana nego što bi se intuitivno očekivalo — analogno zapažanju iz [5] da SALSA znatno nadmašuje HITS uprkos istom neighborhood graph-u.

## Skup podataka

**[web-Stanford](https://snap.stanford.edu/data/web-Stanford.html)** (Stanford Network Analysis Project, SNAP) — usmereni graf hiperlinkova između web stranica sa domena `stanford.edu`, prikupljen 2002. godine.

- 281.903 čvora (web stranice), 2.312.497 usmerenih grana (hiperlinkovi)
- Format: tekstualni fajl, po jedna grana u redu (`FromNodeId  ToNodeId`)
- Preuzima se automatski u prvoj svesci (`notebooks/01_priprema_i_analiza_podataka.ipynb`) sa `https://snap.stanford.edu/data/web-Stanford.txt.gz` i keš-uje lokalno u `data/raw/` — fajl se **ne komituje** u repozitorijum (vidi `.gitignore`) jer je uvek dostupan za preuzimanje sa stabilnog javnog linka.
- Osnovna analiza strukture (distribucija stepena, broj komponenti povezanosti, udeo čvorova bez ulaznih/izlaznih grana i sl.) data je u `01_priprema_i_analiza_podataka.ipynb`.

Izbor ovog dataset-a je tematski usklađen sa literaturom — HITS, SALSA i PageRank su originalno zamišljeni upravo za rangiranje web stranica po hiperlink strukturi.

## Struktura repozitorijuma

```
├── README.md
├── requirements.txt
├── paper/                          # referentni rad [5] i njegove slike
├── data/
│   ├── raw/                        # preuzeti graf (generisano, nije u git-u)
│   └── processed/                  # serijalizovani/obrađeni graf (generisano, nije u git-u)
├── notebooks/
│   ├── 01_priprema_i_analiza_podataka.ipynb   # učitavanje grafa + analiza strukture
│   ├── 02_pagerank.ipynb                       # implementacija + validacija PageRank-a
│   ├── 03_hits.ipynb                           # implementacija + validacija HITS-a
│   ├── 04_salsa.ipynb                          # implementacija + validacija SALSA-e
│   ├── 05_poredjenje_algoritama.ipynb          # kvantitativno poređenje i zaključci
│   └── 07_demo.ipynb                           # sažeta demo sveska za odbranu projekta
└── slike/                          # grafici generisani iz svesaka (za dokumentaciju/prezentaciju)
```

Sveske se pregledaju po numeričkom prefiksu (01 → 07).

## Podešavanje okruženja

```bash
python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
pip install -r requirements.txt
jupyter lab
```

Zatim pokrenuti sveske iz `notebooks/` po redosledu (01 → 07). Prva sveska automatski preuzima dataset — potrebna je internet konekcija pri prvom pokretanju.

## Korišćena literatura

1. J. M. Kleinberg. *Authoritative sources in a hyperlinked environment.* Proc. 9th Annual ACM-SIAM Symposium on Discrete Algorithms, 1998.
2. J. M. Kleinberg. *Authoritative sources in a hyperlinked environment.* Journal of the ACM, 46(5):604–632, 1999.
3. R. Lempel, S. Moran. *SALSA: The stochastic approach for link-structure analysis.* ACM Transactions on Information Systems, 19(2):131–160, 2001.
4. L. Page, S. Brin, R. Motwani, T. Winograd. *The PageRank citation ranking: Bringing order to the web.* Technical report, Stanford Digital Library Technologies Project, 1998.
5. M. Najork. *Comparing the Effectiveness of HITS and SALSA.* CIKM'07, 2007. (rad koji je poslužio kao polazna referenca za temu — slike u `paper/`)
6. A. Borodin, G. O. Roberts, J. S. Rosenthal, P. Tsaparas. *Link analysis ranking: Algorithms, theory, and experiments.* ACM Transactions on Internet Technology, 5(1):231–297, 2005.
7. A. Broder, R. Kumar, F. Maghoul, P. Raghavan, S. Rajagopalan, R. Stata, A. Tomkins, J. Wiener. *Graph structure in the web.* Computer Networks, 33(1–6):309–320, 2000.
