---
title: Opis rachunku usługi Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zrozumieć rachunek usługi Azure Cosmos DB z kilkoma przykładami.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 5954c8eda370c0734985c47cfff6d073f5d76d17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258026"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Informacje o rachunku za korzystanie z usługi Azure Cosmos DB

Jako w pełni zarządzana usługa natywnej bazy danych w chmurze usługa Azure Cosmos DB upraszcza rozliczenia, pobierając tylko opłaty za aprowizowaną przepływność i zużyty magazyn. Nie ma żadnych dodatkowych opłat licencyjnych, sprzętu, kosztów mediów ani kosztów obiektów w porównaniu z lokalnymi lub obsługiwanymi przez IaaS alternatywami. Jeśli wziąć pod uwagę możliwości wielu regionów usługi Azure Cosmos DB, usługa bazy danych zapewnia znaczne obniżenie kosztów w porównaniu do istniejących rozwiązań lokalnych lub IaaS.

Za pomocą usługi Azure Cosmos DB są naliczane co godzinę na podstawie aprowizowanej przepływności i używanego magazynu. W przypadku aprowizowanej przepływności jednostka do fakturowania wynosi 100 RU/s za godzinę, naliczana na poziomie 0,008 USD za godzinę, przy założeniu standardowych cen publicznych, zobacz [stronę Cennik](https://azure.microsoft.com/pricing/details/cosmos-db/). W przypadku używanego magazynu naliczane są naliczane 0,25 USD za 1 GB miejsca na miesiąc, zobacz [stronę Cennik](https://azure.microsoft.com/pricing/details/cosmos-db/). 

W tym artykule przedstawiono kilka przykładów, które ułatwiają zapoznanie się z informacjami widocznymi na rachunku miesięcznym. Liczby pokazane w przykładach mogą się różnić, jeśli dla kontenerów usługi Azure Cosmos aprowizowano inną przepływność, jeśli są one dostępne w wielu regionach lub są uruchamiane dla innych okresów w ciągu miesiąca.

> [!NOTE]
> Rozliczenia są dla każdej części godziny zegara ściennego, a nie 60 minut.

## <a name="billing-examples"></a>Przykłady rozliczeń

### <a name="billing-example---throughput-on-a-container-full-month"></a>Przykład rozliczeń — przepływność kontenera (pełny miesiąc)

* Załóżmy, że konfigurujesz przepływność 1000 RU/s w kontenerze i istnieje przez 24 godziny * 30 dni dla miesiąca = łącznie 720 godzin.  

* 1000 RU/s wynosi 10 jednostek po 100 jednostek RU/s na godzinę za każdą godzinę istnienia kontenerów (czyli 1000/100 = 10). 

* Pomnożenie 10 jednostek na godzinę przez koszt $0.008 (za 100 RU/s na godzinę) = $0.08 za godzinę. 

* Pomnożenie $0.08 za godzinę przez liczbę godzin w miesiącu jest równe $0.08 * 24 godziny * 30 dni = $57.60 dla miesiąca.  

* Całkowity miesięczny rachunek pokaże 7200 jednostek (ze 100 jednostek), co będzie kosztować 57,60 USD.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Przykład rozliczeń — przepływność kontenera (miesiąc częściowy)

* Załóżmy, że tworzymy kontener z aprowizowaną przepływnością 2500 RU/s. Kontener działa przez 24 godziny w ciągu miesiąca (na przykład usuwamy go 24 godziny po jego utworzeniu).  

* Następnie zobaczymy 600 jednostek na rachunku (2500 RU / s / 100 RU / s / jednostka * 24 godziny). Koszt wyniesie 4,80 USD (600 jednostek * 0,008 USD/jednostkę).

* Całkowity rachunek za miesiąc wyniesie 4,80 USD.

### <a name="billing-rate-if-storage-size-changes"></a>Stawka rozliczeniowa w przypadku zmiany rozmiaru magazynu

Pojemność magazynu jest rozliczana w jednostkach maksymalnej godzinowej ilości danych przechowywanych w GB w okresie miesięcznym. Na przykład jeśli wykorzystano 100 GB miejsca na pół miesiąca i 50 GB w drugiej połowie miesiąca, naliczane będą naliczane koszty ekwiwalentu 75 GB miejsca w tym miesiącu.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Stawka rozliczeniowa, gdy kontener lub zestaw kontenerów jest aktywny przez mniej niż godzinę

Opłaty są rozliczane z ryczałtem za każdą godzinę istnienia kontenera lub bazy danych, niezależnie od użycia lub jeśli kontener lub baza danych jest aktywna przez mniej niż godzinę. Na przykład jeśli utworzysz kontener lub bazę danych i usuniesz go 5 minut później, rachunek będzie zawierał jedną godzinę.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Szybkość rozliczeń, gdy przepływność w kontenerze lub bazie danych jest skalowana w górę/w dół

Jeśli zwiększysz aprowizowaną przepływność o godzinie 9:30 z 400 RU/s do 1000 RU/s, a następnie obniżysz aprowizowaną przepływność o godzinie 10:45 do 400 RU/s, zostanie naliczona opłata za dwie godziny 1000 RU/s. 

Jeśli zwiększysz aprowizowaną przepływność kontenera lub zestawu kontenerów o godzinie 9:30 z 100-K RU/s do 200-K RU/s, a następnie obniżysz aprowizowaną przepustowość o godzinie 10:45 do 100-K RU/s, zostanie naliczona opłata za dwie godziny 200 K RU/s.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Przykład rozliczeń: wiele kontenerów, z których każdy ma dedykowany tryb aprowizowanej przepływności

* Jeśli utworzysz konto usługi Azure Cosmos we wschodnich stanach USA 2 z dwoma kontenerami z aprowizowaną przepływnością odpowiednio 500 RU/s i 700 RU/s, całkowita aprowizowana przepływność 1200 ru/s.  

* Opłata zostanie naliczona 1200/100 * $0.008 = $0.096/hour. 

* Jeśli twoje potrzeby dotyczące przepływności uległy zmianie, a pojemność każdego kontenera została zwiększona o 500 RU/s, tworząc jednocześnie nowy nieograniczony kontener z 20 000 RU/s, całkowita pojemność aprowizowana wyniesie 22 200 RU/s (1000 RU/s + 1200 RU/s + 20 000 RU/s).  

* Rachunek zmieni się wówczas na: $0.008 x 222 = $1.776/hour. 

* W miesiącu 720 godzin (24 godziny * 30 dni), jeśli przez 500 godzin aprowizowana przepustowość wynosiła 1200 RU/s, a dla pozostałych 220 godzin aprowizowana przepustowość wynosiła 22 200 RU/s, twój miesięczny rachunek pokazuje: 500 x $0.096/hour + 220 x $1.776/hour = $438.72/miesiąc.

![Przykład dedykowanego rachunku przepływności](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Przykład rozliczeń: kontenery z trybem przepływności współdzielonej

* Jeśli utworzysz konto usługi Azure Cosmos we wschodnich systemach US 2 z dwiema bazami danych usługi Azure Cosmos (z zestawem kontenerów udostępniających przepływność na poziomie bazy danych) z aprowizowaną przepływnością odpowiednio 50-K RU/s i 70-K RU/s, musisz mieć całkowitą aprowizowaną przepływność 120 K/s.  

* Opłata zostanie naliczona w wysokości 1200 x 0,008 USD = 9,60 USD/godz. 

* Jeśli twoje potrzeby dotyczące przepływności uległy zmianie i zwiększysz przepływność administracyjną każdej bazy danych o 10 000 RU/s dla każdej bazy danych i dodasz nowy kontener do pierwszej bazy danych z dedykowanym trybem przepływności 15-K RU/s do udostępnionej bazy danych przepływności, ogólna pojemność aprowizowana wyniesie 155-K RU/s (60 K RU/s + 80 K RU/s + 15 K/s).  

* Twój rachunek zmieni się wówczas na: 1550 * 0,008 USD = 12,40 USD/godz.  

* W ciągu miesiąca 720 godzin, jeśli przez 300 godzin aprowizowana przepustowość wynosiła 120-K RU/s, a dla pozostałych 420 godzin aprowizowana przepustowość wynosiła 155-K RU/s, Twój miesięczny rachunek pokaże: 300 x $9.60/hour + 420 x $12.40/hour = $2,880 + $5,208 = $8,088/month. 

![Przykład rachunku przepływności współdzielonej](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Przykłady rozliczeń z replikacją geograficzną i wieloma wzorcami  

Możesz dodać/usunąć regiony platformy Azure w dowolnym miejscu na świecie do konta bazy danych usługi Azure Cosmos w dowolnym momencie. Przepływność skonfigurowana dla różnych baz danych i kontenerów usługi Azure Cosmos zostanie zarezerwowana w każdym z regionów platformy Azure skojarzonych z kontem bazy danych usługi Azure Cosmos. Jeśli suma aprowizowanej przepływności (RU/s) skonfigurowana we wszystkich bazach danych i kontenerach w obrębie konta bazy danych usługi Azure Cosmos (aprowizowana na godzinę) to T, a liczba regionów platformy Azure skojarzonych z kontem bazy danych to N, następnie całkowita aprowizowana przepływność dla danej godziny dla konta bazy danych usługi Azure Cosmos, (a) skonfigurowana przy jednym regionie zapisu jest równa T x N RU/s i (b) skonfigurowana przy wszystkich regionach zdolnych do przetwarzania zapisów jest równa odpowiednio T x (N+1) RU/sec. Przepływność aprowizowana (region pojedynczego zapisu) kosztuje 0,008 USD/godzinę za 100 JEDNOSTEK RU/s, a aprowizowana przepływność z wieloma zapisywalnymi regionami (konfigur wieloelkoteralna) kosztuje 0,016 USD/godzinę za 100 JEDNOSTEK RU/s (patrz [strona cenowa).](https://azure.microsoft.com/pricing/details/cosmos-db/) Niezależnie od tego, czy jest to region pojedynczego zapisu, czy wiele regionów zapisu, usługa Azure Cosmos DB umożliwia odczytywanie danych z dowolnego regionu.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Przykład rozliczeń: wieloregionowe konto usługi Azure Cosmos, zapisy w jednym regionie

Załóżmy, że masz kontener usługi Azure Cosmos w zachodnie stany USA. Kontener jest tworzony z przepływnością 10K RU/s i przechowujesz 1 TB danych w tym miesiącu. Załóżmy, że dodasz trzy regiony (wschodnie stany USA, Europa Północna i Azja Wschodnia) do konta usługi Azure Cosmos, z których każdy ma tę samą pamięć masową i przepływność. Całkowity miesięczny rachunek będzie (przy założeniu 30 dni w miesiącu). Rachunek będzie następujący: 

|**Element** |**Użycie (miesiąc)** |**Stawka** |**Koszt miesięczny** |
|---------|---------|---------|-------|
|Rachunek przepływności kontenera w zachodnie stany USA      | 10K RU/s * 24 * 30    |$0.008 za 100 RU/s za godzinę   |zł.|
|Rachunek za przepustowość dla 3 dodatkowych regionów - Wschodnie stany USA, Europa Północna i Azja Wschodnia       | 3 * 10K RU/s * 24 * 30    |$0.008 za 100 RU/s za godzinę  |1 728 usd|
|Rachunek za magazyn kontenera w zachodnich stanach USA      | 250 GB    |zł./GB  |zł.|
|Rachunek za magazynowanie dla 3 dodatkowych regionów - Wschodnie stany USA, Europa Północna i Azja Wschodnia      | 3 * 250 GB    |zł./GB  |zł.|
|**Łącznie**     |     |  |**2 554 usd**|

*Załóżmy również, że wyjście 100 GB danych co miesiąc z kontenera w zachodnie stany USA do replikacji danych do wschodnich stanów USA, Europy Północnej i Azji Wschodniej. Opłaty są naliczane za ruch wychodzący zgodnie ze stawkami za transfer danych.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Przykład rozliczeń: wieloregionowe konto usługi Azure Cosmos, zapisy w wielu regionach

Załóżmy, że utworzysz kontener usługi Azure Cosmos w zachodnie stany USA. Kontener jest tworzony z przepływnością 10K RU/s i przechowujesz 1 TB danych w tym miesiącu. Załóżmy, że dodasz trzy regiony (wschodnie stany USA, Europa Północna i Azja Wschodnia), z których każdy ma ten sam magazyn i przepływność, a także chcesz mieć możliwość zapisu w kontenerach we wszystkich regionach skojarzonych z kontem usługi Azure Cosmos. Całkowity miesięczny rachunek będzie (przy założeniu 30 dni w miesiącu) w następujący sposób:

|**Element** |**Użycie (miesiąc)**|**Stawka** |**Koszt miesięczny** |
|---------|---------|---------|-------|
|Rachunek przepływności kontenera w zachodnie stany USA (wszystkie regiony są zapisywalne)       | 10K RU/s * 24 * 30    |$0.016 za 100 RU/s za godzinę    |zł. |
|Rachunek przepływności dla 3 dodatkowych regionów - Wschodnie stany USA, Europa Północna i Azja Wschodnia (wszystkie regiony są zapisywalne)        | (3 + 1) * 10 000 RU/s * 24 * 30    |$0.016 za 100 RU/s za godzinę   |4 608 usd |
|Rachunek za magazyn kontenera w zachodnich stanach USA      | 250 GB    |zł./GB  |zł.|
|Rachunek za magazynowanie dla 3 dodatkowych regionów - Wschodnie stany USA, Europa Północna i Azja Wschodnia      | 3 * 250 GB    |zł./GB  |zł.|
|**Łącznie**     |     |  |**6 010 usd**|

*Załóżmy również, że wyjście 100 GB danych co miesiąc z kontenera w zachodnie stany USA do replikacji danych do wschodnich stanów USA, Europy Północnej i Azji Wschodniej. Opłaty są naliczane za ruch wychodzący zgodnie ze stawkami za transfer danych.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Przykład rozliczeń: konto usługi Azure Cosmos z przepływnością wieloszerko-wzorcową, na poziomie bazy danych, w tym trybem dedykowanej przepływności dla niektórych kontenerów

Rozważmy poniższy przykład, gdzie mamy wieloregionowe konto usługi Azure Cosmos, gdzie wszystkie regiony są zapisywalne (wieloształtny config). Dla uproszczenia zakładamy, że rozmiar magazynu pozostaje stały i nie zmienia się i pomija go tutaj, aby zachować prostszy przykład. Aprowizowana przepustowość w ciągu miesiąca różniła się w następujący sposób (przy założeniu 30 dni lub 720 godzin): 

[0-100 godzin]:  

* Utworzyliśmy trzy regionowe konto usługi Azure Cosmos (zachodnie stany USA, wschodnie stany USA, Europa Północna), w którym wszystkie regiony można zapisywać 

* Utworzyliśmy bazę danych (D1) z współdzieloną przepływnością 10K RU/s 

* Utworzyliśmy bazę danych (D2) z współdzieloną przepustowością 30-K RU/s i  

* Stworzyliśmy kontener (C1) z dedykowaną przepustowością 20 K RU/s 

[101-200 godzin]:  

* Skalowaliśmy bazę danych (D1) do 50 K RU/s 

* Skalowaliśmy bazę danych (D2) do 70 K RU/s  

* Usunęliśmy kontener (C1)  

[201–300 godzin]:  

* Stworzyliśmy kontener (C1) ponownie z dedykowaną przepustowością 20 K RU/s 

[301-400 godzin]:  

* Usunęliśmy jeden z regionów z konta usługi Azure Cosmos (liczba regionów zapisywalnych jest teraz 2) 

* Zmniejszyliśmy bazę danych (D1) do 10 000 RU/s 

* Skalowaliśmy bazę danych (D2) do 80 K RU/s  

* Ponownie usunęliśmy kontener (C1) 

[401-500 godzin]:  

* Zmniejszyliśmy bazę danych (D2) do 10 000 RU/s  

* Stworzyliśmy kontener (C1) ponownie z dedykowaną przepustowością 20 K RU/s 

[501-700 godzin]:  

* Skalowaliśmy bazę danych (D1) do 20 K RU/s  

* Skalowaliśmy bazę danych (D2) do 100 K RU/s  

* Ponownie usunęliśmy kontener (C1)  

[701-720 godzin]:  

* Zmniejszyliśmy bazę danych (D2) do 50 K RU/s  

Wizualnie zmiany w całkowitej aprowizowanej przepływności w ciągu 720 godzin dla miesiąca są pokazane na rysunku poniżej: 

![Przykład z życia w prawdziwym życiu](./media/understand-your-bill/bill-example3.png)

Całkowity miesięczny rachunek będzie (przy założeniu 30 dni / 720 godzin w miesiącu) będzie obliczany w następujący sposób:

|**Godzin**  |**RU/s** |**Element** |**Użycie (co godzinę)** |**Koszty** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Rachunek przepływności kontenera w zachodnie stany USA (wszystkie regiony są zapisywalne)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |zł.  |
| | |Rachunek przepływności dla 2 dodatkowych regionów: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |2 880 usd  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1: -- |Rachunek przepływności kontenera w zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |zł.  |
| | |Rachunek przepływności dla 2 dodatkowych regionów: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |5 760 usd  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Rachunek przepływności kontenera w zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |zł.  |
| | |Rachunek przepływności dla 2 dodatkowych regionów: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |6 720 usd |
|[301-400] |D1:10K <br/>D2:80K <br/>C1: -- |Rachunek przepływności kontenera w zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |1 440 usd   |
| | |Rachunek przepływności dla 2 dodatkowych regionów: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |2 880 usd  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Rachunek przepływności kontenera w zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |zł.  |
| | |Rachunek przepływności dla 2 dodatkowych regionów: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |1 280 usd  |
|[501-700] |D1:20K <br>D2:100K <br>C1: -- |Rachunek przepływności kontenera w zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |zł.  |
| | |Rachunek przepływności dla 2 dodatkowych regionów: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |7 680 usd  |
|[701-720] |D1:20K <br/>D2:50K <br/>C1: -- |Rachunek przepływności kontenera w zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |zł.  |
| | |Rachunek przepływności dla 2 dodatkowych regionów: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |zł.  |
|| |**Całkowity koszt miesięczny**  | |**38 688 usd**   |

## <a name="billing-examples-with-free-tier-accounts"></a>Przykłady rozliczeń z kontami warstw bezpłatnych
Dzięki bezpłatnej warstwie usługi Azure Cosmos DB otrzymasz bezpłatnie pierwsze 400 ru/s i 5 GB miejsca na koncie, zastosowane na poziomie konta. Wszelkie ru/s i pamięć masowa powyżej 400 RU/s i 5 GB będą rozliczane według regularnych stawek cenowych na stronie cenowej. Na rachunku nie zobaczysz opłaty lub elementu zamówienia za darmo 400 Ru / s i 5 GB, tylko RU / s i pamięci masowej poza to, co jest objęte warstwą bezpłatną. 400 RU/s ma zastosowanie do każdego typu RU/s - aprowizowana przepustowość, autopilot (wersja zapoznawcza) i multi-master.  

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Przykład rozliczeń — kontener lub baza danych z aprowizowaną przepływnością
- Załóżmy, że tworzymy bazę danych lub kontener na koncie warstwy bezpłatnej z 400 ru/s i 5 GB miejsca.
- Rachunek nie będzie wyświetlał żadnych opłat za ten zasób. Twój koszt godzinowy i miesięczny wyniesie 0 USD.
- Teraz załóżmy, że na tym samym koncie dodajemy inną bazę danych lub kontener z 1000 RU/s i 10 GB pamięci masowej.
- Rachunek będzie teraz wyświetlał opłatę za 1000 RU/s i 10 GB miejsca. 

### <a name="billing-example---container-or-database-with-autopilot-throughput-preview"></a>Przykład rozliczeń — kontener lub baza danych z przepustowością autopilota (wersja zapoznawcza)
- Załóżmy, że na koncie warstwy bezpłatnej tworzymy bazę danych lub kontener z włączonym autopilotem, z maksymalną prędkością 4000 RU/s. Ten zasób zostanie automatycznie skalowany między 400 RU/s - 4000 RU/s. 
- Załóżmy, że w godzinie 1 do godziny 10 zasób jest na minimum 400 RU/s. Podczas godziny 11 zasób skaluje się do 1000 RU/s, a następnie z powrotem w dół do 400 RU/s w ciągu godziny.
- W godzinach od 1 do 10 zostanie naliczona 0 USD za przepustowość, ponieważ 400 RU/s zostało objętych warstwą bezpłatną. 
- W godzinie 11, zostanie naliczona naliczona za skuteczne 1000 RU / s - 400 RU / s = 600 RU / s, ponieważ jest to najwyższy RU / s w ciągu godziny. Będzie to 6 jednostek po 100 jednostek RU/s za godzinę, więc całkowity koszt przepustowości za godzinę wyniesie 6 jednostek * 0,012 USD = 0,072 USD. 
- Każda pamięć masowa poza pierwszymi 5 GB będzie rozliczana według normalnych stawek za przechowywanie. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Przykład rozliczeń — konto regionu wieloregiona, pojedynczy zapis
- Załóżmy, że na koncie warstwy bezpłatnej tworzymy bazę danych lub kontener z 1200 ru/s i 10 GB miejsca. Replikujemy konto do 3 regionów i mamy jedno-master (pojedynczy region zapisu) konto.
- W sumie bez warstwy bezpłatnej bylibyśmy rozliczani za 3 * 1200 RU/s = 3600 RU/s i 3 * 10 GB = 30 GB pamięci masowej.
- Dzięki bezpłatnemu rabatowi warstwy, po usunięciu 400 jednostek RU/s i 5 GB pamięci masowej, zostaniemy naliczone za skuteczne 3200 jednostek RU/s (32 jednostki) aprowizowanej przepływności według stawki regionu pojedynczego zapisu i 25 GB pamięci masowej.
- Miesięczny koszt ru/s wyniesie: 32 jednostki * $0.008 * 24 godziny * 31 dni = $190.46. Miesięczny koszt pamięci masowej wyniesie: 25 GB * 0,25 / GB = 6,25 USD. Całkowity koszt wyniesie $190.46 + $6.25 = $196.71.
- Uwaga: jeśli cena jednostkowa dla ru/s lub magazynu różni się w regionach, bezpłatna warstwa 400 RU/s i 5 GB będą odzwierciedlać stawki regionu, w który utworzono konto.

### <a name="billing-example---multi-region-multi-master-multiple-write-region-account"></a>Przykład rozliczeń — konto wieloregionowe, wielowładne (wiele regionów zapisu)

W tym przykładzie odzwierciedliają [ceny wielozadaniowe](https://azure.microsoft.com/pricing/details/cosmos-db/) dla kont utworzonych po 1 grudnia 2019 r. 
- Załóżmy, że na koncie warstwy bezpłatnej tworzymy bazę danych lub kontener z 1200 ru/s i 10 GB miejsca. Replikujemy konto do 3 regionów i mamy konto wielo-master (wielu regionów zapisu). 
- W sumie bez warstwy bezpłatnej bylibyśmy rozliczani za 3 * 1200 RU/s = 3600 RU/s i 3 * 10 GB = 30 GB pamięci masowej.
- Dzięki bezpłatnemu rabatowi warstwy, po usunięciu 400 jednostek RU/s i 5 GB pamięci masowej, zostaniemy naliczone za skuteczne 3200 jednostek RU/s (32 jednostki) aprowizowanej przepływności przy wielokrotnym współczynniku zapisu i 25 GB pamięci masowej.
- Miesięczny koszt ru/s wyniesie: 32 jednostki * $0.016 * 24 godziny * 31 dni = $380.93. Miesięczny koszt pamięci masowej wyniesie: 25 GB * 0,25 / GB = 6,25 USD. Całkowity koszt wyniesie $380.93 + $6.25 = $387.18.

## <a name="proactively-estimating-your-monthly-bill"></a>Proaktywne szacowanie miesięcznego rachunku  

Rozważmy inny przykład, w którym chcesz proaktywnie oszacować rachunek przed końcem miesiąca. Rachunek można oszacować w następujący sposób:

|**Koszt magazynowania** | |
|----|----|
|Średni rozmiar rekordu (KB) |1 |
|Liczba rekordów  |100,000,000  |
|Całkowita ilość miejsca na przestrzeni magazynowej (GB)  |100 |
|Miesięczny koszt za GB  |zł.  |
|Oczekiwany miesięczny koszt pamięci masowej   |zł.  |

<br>

|**Koszt przepływności** | | | |
|----|----|----|----|
|Typ operacji| Żądania/s| Średnia ru/wniosek| Potrzebne RU|
|Zapisywanie| 100 | 5 | 500|
|Odczyt| 400| 1| 400|

Całkowity koszt RU/s: 500 + 400 = 900 koszt godzinowy: 900/100 * 0,008 USD = 0,072 USD Oczekiwany koszt miesięczny dla przepustowości (przy założeniu 31 dni): $0.072 * 24 * 31 = $53.57

**Całkowity koszt miesięczny**

Całkowity koszt miesięczny = miesięczny koszt pamięci masowej + koszt miesięczny całkowitego kosztu miesięcznego przepływności = 25,00 USD + 53,57 USD = 78,57 USD

*Ceny mogą się różnić w zależności od regionu. Aby uzyskać aktualne ceny, zobacz [stronę Cennik](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Rozliczanie z pojemnością rezerwową usługi Azure Cosmos DB

Pojemność zarezerwowana usługi Azure Cosmos DB umożliwia zakup aprowizowanej przepływności z wyprzedzeniem (zarezerwowana pojemność lub rezerwacja), która może być stosowana do wszystkich baz danych i kontenerów usługi Azure Cosmos (dla dowolnego interfejsu API lub modelu danych) we wszystkich regionach platformy Azure. Ponieważ aprowizowana cena przepływności różni się w zależności od regionu, pomaga myśleć o pojemności zarezerwowanej jako kredytu pieniężnego, który został zakupiony z rabatem, które mogą być pobierane z dla aprowizowanej przepływności po odpowiedniej cenie w każdym regionie. Załóżmy na przykład, że masz konto usługi Azure Cosmos z jednym kontenerem aprowizowanym z 50-K RU/s i globalnie replikowane dwa regiony — wschodnie stany USA i Japonia Wschód. Jeśli wybierzesz opcję płatności zgodnie z rzeczywistym i wyjazdem, zapłacisz:  

* we wschodnich stanach USA: dla 50-K RU/s po kursie 0,008 USD za 100 RU/s w tym regionie 

* w Japonii Wschodniej: dla 50-K RU/s po kursie 0,009 USD za 100 RU/s w tym regionie

Całkowity rachunek (bez zarezerwowanej pojemności) wynosi (przy założeniu 30 dni lub 720 godzin): 

|**Region**| **Cena godzinowa za 100 RU/s**|**Jednostki (RU/s)**|**Kwota naliczona (co godzinę)**| **Kwota naliczona (miesięcznie)**|
|----|----|----|----|----|
|Wschodnie stany USA|zł. |50 k.|zł.|2 880 usd |
|Japonia Wschodnia|zł. |50 k.| zł. |zł. |
|Łącznie|||zł.|zł. |

Weźmy pod uwagę, że zamiast tego kupiłeś pojemność zarezerwowaną. Możesz kupić zarezerwowaną pojemność za 100-K RU/s w cenie $56,064 za jeden rok (z rabatem 20%) lub $6.40 za godzinę. Zobacz cennik pojemności zarezerwowanej na [stronie Ceny](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Koszt przepustowości (płatność zgodnie z rzeczywistymi oczekiwaniami): 100 000 RU/s/100 * 0,008 USD/godzinę * 8760 godzin w roku = 70 080 USD 

* Koszt przepustowości (z pojemnością zarezerwowaną) $70,080 zdyskontowany przy 20% = $56,064 

To, co faktycznie kupiłeś, to kredyt w wysokości $8 za godzinę, za 100 K RU/s przy użyciu ceny katalogowej we wschodnich Stanach Zjednoczonych, w cenie $6.40 za godzinę. Następnie można pobrać z tej rezerwacji przepływności pre-paid na podstawie godzinowej dla aprowizowanej przepustowości w dowolnym globalnym regionie platformy Azure po odpowiednich regionalnych cenach katalogowych ustawionych dla subskrypcji. W tym przykładzie, w którym podajesz 50 K RU/s w stanach Wschodnich stanów USA i Japonii Wschodniej, będziesz mógł pobierać aprowizowaną przepustowość o wartości 8,00 USD za godzinę i zostanie naliczona naliczona nadmierna stawka 0,50 USD za godzinę (lub 360 USD miesięcznie). 

|**Region**| **Cena godzinowa za 100 RU/s**|**Jednostki (RU/s)**| **Kwota naliczona (co godzinę)**| **Kwota naliczona (miesięcznie)**|
|----|----|----|----|----|
|Wschodnie stany USA|zł. |50 k.|zł.|2 880 usd |
|Japonia Wschodnia|zł. |50 k.| zł. |zł. |
|||Pay-as-you-go|zł.|zł.|
|Zakupiona pojemność zarezerwowana|$0.0064 (20% zniżki) |100 RU/s lub 8 USD pojemności w przedsprzedaży |zł.|zł. |
|Rachunek netto|||(0,50 USD) |zł. |

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB, wykonując następujące artykuły:

* Dowiedz się więcej o [tym, jak model cenowy Usługi Cosmos DB jest opłacalny dla klientów](total-cost-ownership.md)
* Dowiedz się więcej o [optymalizacji pod kątem rozwoju i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [optymalizacji kosztów przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji kosztów odczytów i zapisów](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizowaniu kosztów wieloregionowych kont usługi Azure Cosmos](optimize-cost-regions.md)
