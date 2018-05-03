<!--
Used in:
sql-database-performance-guidance.md 
sql-database-single-database-resources.md 
-->

### <a name="basic-service-tier"></a>Warstwa Podstawowa usług
| **Poziom wydajności** | **Podstawowa** |
| :--- | --: |
| Maksymalna liczba jednostek DTU | 5 |
| Dołączony magazyn (GB) | 2 |
| Maksymalna liczba opcji magazynu (GB) | 2 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) |ND |
| Maksymalna liczba równoczesnych procesów roboczych (liczba żądań) | 30 |
| Maksymalna liczba współbieżnych logowań | 30 |
| Maksymalna liczba współbieżnych sesji | 300 |
|||

### <a name="standard-service-tier"></a>Warstwa Standardowa usług
| **Poziom wydajności** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Maksymalna liczba jednostek Dtu ** | 10 | 20 | 50 | 100 |
| Dołączony magazyn (GB) | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcji magazynu (GB) * | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | ND | ND | ND | ND |
| Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)| 60 | 90 | 120 | 200 |
| Maksymalna liczba współbieżnych logowań | 60 | 90 | 120 | 200 |
| Maksymalna liczba współbieżnych sesji |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standardowa usługa warstwy (kontynuacja)
| **Poziom wydajności** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Maksymalna liczba jednostek Dtu ** | 200 | 400 | 800 | 1600 | 3000 |
| Dołączony magazyn (GB) | 250 | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcji magazynu (GB) * | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | ND | ND | ND | ND |ND |
| Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)| 400 | 800 | 1600 | 3200 |6000 |
| Maksymalna liczba współbieżnych logowań | 400 | 800 | 1600 | 3200 |6000 |
| Maksymalna liczba współbieżnych sesji |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Warstwa Premium usług 
| **Poziom wydajności** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Dołączony magazyn (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Maksymalna liczba opcji magazynu (GB) * | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maksymalna liczba współbieżnych logowań | 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maksymalna liczba współbieżnych sesji | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> \* Magazyn o rozmiarze większym niż ilość miejsca do magazynowania są dostępne w wersji zapoznawczej dodatkowych kosztów za dodatkową opłatą. Szczegóły można znaleźć w [cenniku usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
>
>\* W warstwie Premium ponad 1 TB miejsca do magazynowania jest obecnie dostępny w następujących regionach: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Kanada Wschodnia, Środkowe stany USA, Francja Środkowa, Niemcy Środkowe, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa, Północno-środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Wschodnie stany USA 2, Zachodnie stany USA, Administracja USA — Wirginia i Europa Zachodnia. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
>\*\* Maksymalna liczba Dtu na bazę danych, uruchamianie w 200 Dtu i wyższych w standardzie są w wersji zapoznawczej.
>

