# 1. win_rate — skuteczność transakcji

**Co mówi:**
Procent zwycięskich trejdów. Wysoki win-rate oznacza strategię, która częściej zarabia niż traci, ale NIE mówi nic o wielkości zysku/straty, ani czy strategia jest zyskowna w długim terminie.

**Jak się liczy:**

---

```
win_rate = liczba_zyskownych_trejdów / liczba_wszystkich_trejdów
```

**Jak używać w tradingu:**

* Mean-reversion zwykle ma **wysokie WR (60–80%)**
* Momentum/trend-following często ma **niski WR (20–40%)**, ale duże zyski
* Nie powinno się optymalizować wyłącznie pod win-rate, to łatwo prowadzi do overfittingu

---

# 2. total_trades — liczba wykonanych transakcji

**Co mówi:**
Ilu faktycznie trejdów dokonała strategia.

**Jak się liczy:**
Zliczenie wszystkich zamkniętych trejdów.

**Jak używać:**

* Zbyt mało trejdówto strategia statystycznie niestabilna
* Zbyt dużo trejdów to za duże koszty (slippage, prowizje)
* Dobre minimum dla stabilności: **>100–200 transakcji** na okres testowy
* Optymalnie używać jako filtr: „Jeżeli `total_trades < X`, odrzucamy model.”

---

# 3. sharpe_ratio — zwrot vs. ryzyko (całkowita zmienność)

**Co mówi:**
Ile strategia zarabia na jednostkę ryzyka (zmienności).
Im wyżej, tym lepiej.

**Jak się liczy:**

```
(mean(returns) / std(returns)) * sqrt(annualization_factor)
```

**Jak używać:**

* Podstawowa risk-adjusted metryka
* Sharpe > 1.0 = OK
* Sharpe > 2.0 = dobry system
* Sharpe > 3.0 = profesjonalny poziom / hedge funds
* Sharpe > 4.0 = rzadko spotykane, wręcz idealny model

W optymalizacji filtruje się najlepsze kombinacje parametrów.

---

# 4. sortino_ratio — zwrot vs. negatywna zmienność

**Co mówi:**
Jak strategia zarabia w stosunku do **złego ryzyka** (tylko strat).

**Jak się liczy:**

```
(mean(returns) / std(negatywnych zwrotów))
```

**Jak używać:**

* Lepszy niż Sharpe, bo ignoruje „dobre” zmiany, czyli nie przywiązuje uwagę do odchyleń zysków, nie kara ich.
* W praktyce bardziej stabilny.
* Jeśli Sortino > Sharpe strategia ma dobry korzystną asymetrię („skew”), czyli ryzyko po dobrej stronie.
* Świetny do mean-reversion lub systemów hedgingowych.

---

# 5. total_return — całkowity zwrot

**Co mówi:**
Ile procent portfel urósł od początku do końca.

**Jak się liczy:**

```
(final_value - initial_value) / initial_value
```

**Jak używać:**

* Najbardziej intuicyjny wynik strategii
* Nie bierze pod uwagę ryzyka, dlatego musi być zestawiony z największą procentową stratą / Sharpe

---

# 6. max_dd — maksymalne obsunięcie kapitału

**Co mówi:**
Największa procentowa strata od lokalnego szczytu do dołka (szczyt -> dołek zwrotu).

**Jak się liczy:**
Liczymy obsunięcia kapitału, czyli momenty ektremów zwrotu i od nich odejmujemy pojawiające się po nich nijniższe dołki dla każdego ekstremum. Wybieramy największe procentowe obsunięcie kapitału

**Jak używać:**

* Jedna z **najważniejszych** metryk ryzyka
* max_dd > 30% jest trudne psychologicznie do przeżycia dla trejdera, i praktycznie nie możliwe do przejścia w prop firmach (dostarczających wirtualny kapitał)
* Najlepsze strategie mają **max_dd < 20–25%**

---

# 7. annualized_return — zwrot roczny

**Co mówi:**
Ile strategia zarabia rocznie, z uwzględnieniem compounding.

**Jak się liczy:**

```
((1 + total_return) ** (1 / lata)) - 1
```

**Jak używać:**

* Porównywanie strategii o różnym okresie testowym

---

# 8. annualized_volatility — roczna zmienność portfela

**Co mówi:**
Jak bardzo zmienia się wartość portfela każdego dnia/tygodnia.

**Jak się liczy:**

```
std(returns) * sqrt(annualization_factor)
```

**Jak używać:**

* Momentum strategie często ma wyższą zmienność
* Mean-reversion strategie często mają niską
* z Sharpe daje dobry obraz ryzyka

---

# 9. beta — korelacja z benchmarkiem

**Co mówi:**
Jak bardzo strategia porusza się razem z rynkiem.

**Jak się liczy:**

```
covariance(portfolio, benchmark) / variance(benchmark)
```

**Jak używać:**

* Beta ~ 1 -> zachowuje się jak rynek
* Beta >> 1 -> agresywna strategia
* Beta << 1 -> niska korelacja / hedging

Dla crypto może pokazać, że system żyje własnym życiem vs BTC trend.

---

# 10. alpha — nadwyżkowy zwrot ponad benchmark

**Co mówi:**
Czy strategia zarabia **więcej niż benchmark** przy tym samym ryzyku.

**Jak się liczy:**

```
alpha = portfolio_return - (beta * benchmark_return)
```

**Jak używać:**

* Alpha > 0 = strategia generuje dodatkową wartość
* W tradingu stosuj, jeśli porównuje się do SPX / BTC albo innych głownych etf-ów, aktyw na danym rynku

---

# 11. calmar_ratio

**Co mówi:**
Ile strategia zarabia rocznie na jednostkę największej straty.

**Jak się liczy:**

```
annualized_return / max_drawdown
```

**Jak używać:**

* O wiele ważniejsza metryka niż Sharpe, gdy są duże obsunięcia kapitału
* Calmar > 1 = dobry system
* Calmar > 3 = świetny system, bardzo stabilny

Bardzo lubiane przez fundusze hedgingowe.

---

# 12. omega_ratio — prawdopodobieństwo zysku vs straty

**Co mówi:**
Stosunek obszaru profitów > return_threshold do obszaru strat.

**Jak się liczy:**
Całki z rozkładu zwrotów, czyli sumowanie łącznej wielkości wszystkich zwrotów powyżej ustalonego progu (np. 0%) oraz łącznej wielkości wszystkich zwrotów poniżej tego progu. Inaczej obliczenie pól obu części rozkładu.
- Dla części powyżej progu zbieramy wszystkie nadwyżki zysków ponad ten próg i je sumujemy.
- Dla części poniżej progu zbieramy wszystkie straty względem progu i również je sumujemy.

**Jak używać:**

* Lepszy niż Sharpe gdy rozkład zwrotów jest **niesymetryczny**
* Dobre dla mean reversion, gdzie jest dużo małych zysków

---

# 13. tail_ratio — skrajne zyski / skrajne straty

**Co mówi:**
Porównuje ekstremalne zyski do ekstremalnych strat.

**Jak się liczy:**

```
tail_ratio = abs(lower_tail_percentile) / upper_tail_percentile
```

**Jak używać:**

* Tail > 1 to większe skrajne zyski niż straty
* Tail < 1 to strategia podatna na flash-crashe, czyli mocne obsunięcia

---

# 14. value_at_risk (VaR) — ryzyko straty z prawdopodobieństwem X%

**Co mówi:**
„Z 95% pewnością nie stracisz więcej niż X%”.

**Jak się liczy:**
Percentyl strat z rozkładu zwrotów.

**Jak używać:**

* Standard w risk management w bankach i podobnych instytucjach
* Przydatne w systemach high-frequency (dużo i często trejdy na niskich interwałach) lub modelach z anty-drawdown

---

# 15. trades_profit_factor

**Co mówi:**
Ile suma zysków jest większa niż suma strat.

**Jak się liczy:**

```
profit_factor = sum(profity) / sum(straty)
```

**Jak używać:**

* PF > 1 → strategia zarabia
* PF > 1.3 → używalna
* PF > 1.5 → dobra
* PF > 2.0 → świetna

Bardzo ważne przy strategiach low-win-rate typu trend-follow.

---

# 16. dd_avg_drawdown — średnie obsunięcie

**Co mówi:**
Jaka jest średnia głębokość wszystkich obsunięć kapitału.

**Jak się liczy:**
Średnia ze wszystkich wykrytych epizodów obsunięć kapitału.

**Jak używać:**

* Pokazuje regularność obsunięć
* Jeśli jest zbyt duży, strategia jest niestabilna i może być frustrująca, ponieważ większość czasu w stracie w porównaniu z największym zwrotem strategii
* Używane jako wskaźnik stabilności equity curve