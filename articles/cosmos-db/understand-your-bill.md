---
title: Informacje na temat Azure Cosmos DB rachunku
description: W tym artykule wyjaśniono, jak zrozumieć Azure Cosmos DB rachunku przy użyciu przykładów.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 1dc4120ec9f1db8ac34800096ae407b5581758a4
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614168"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Zapoznaj się z Azure Cosmos DB rachunku

Jako w pełni zarządzana usługa bazy danych w chmurze, Azure Cosmos DB upraszcza rozliczenia przez naliczanie opłat tylko za zainicjowaną przepływność i magazyn użyty. Nie ma dodatkowych opłat licencyjnych, sprzętu, kosztów narzędzi ani kosztów infrastruktury w porównaniu z rozwiązaniami lokalnymi lub IaaS. Gdy rozważasz możliwości wieloregionu Azure Cosmos DB, usługa bazy danych zapewnia znaczną redukcję kosztów w porównaniu do istniejących rozwiązań lokalnych lub IaaS.

W przypadku Azure Cosmos DB opłaty są naliczane godzinowo na podstawie zainicjowanej przepływności i zużywanej pamięci masowej. W przypadku przewidzianych przepływności jednostką rozliczeniową jest 100 RU/s na godzinę, naliczana na $0,008 za godzinę, przy założeniu, że standardowe ceny publiczne są dostępne, zobacz [stronę](https://azure.microsoft.com/pricing/details/cosmos-db/)z cennikiem. W przypadku zużytego magazynu opłaty są naliczane $0,25 za 1 GB magazynu miesięcznie, zobacz [stronę](https://azure.microsoft.com/pricing/details/cosmos-db/)z cennikiem. 

W tym artykule przedstawiono kilka przykładów, które ułatwiają zapoznanie się z informacjami wyświetlanymi na rachunku miesięcznym. Liczby pokazane w przykładach mogą się różnić, jeśli kontenery usługi Azure Cosmos mają różną przepływność, jeśli są one dostępne w wielu regionach lub są uruchamiane dla różnych okresów w ciągu miesiąca.

>! Korygując Opłaty są naliczane za każdą część godziny zegarowej, a nie czas trwania 60 minuty.

## <a name="billing-examples"></a>Przykłady rozliczeń

### <a name="billing-example---throughput-on-a-container-full-month"></a>Przykład rozliczeń — przepływność na kontenerze (pełny miesiąc)

* Załóżmy, że skonfigurowano przepływność 1 000 RU/s w kontenerze i istnieje przez 24 godziny * 30 dni w miesiącu = 720 godz.  

* 1 000 RU/s to 10 jednostek 100 RU/s na godzinę dla każdej godziny, gdy kontenery istnieją (czyli 1000/100 = 10). 

* Mnożenie 10 jednostek na godzinę przez koszt $0,008 (za 100 RU/s na godzinę) = $0,08 na godzinę. 

* Mnożenie $0,08 na godzinę przez liczbę godzin w miesiącu równą $0,08 * 24 godziny * 30 dni = $57,60 przez miesiąc.  

* Łączny rachunek miesięczny będzie przedstawiał 7 200 jednostek (z 100 jednostek ru), które będą kosztowały $57,60.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Przykład rozliczeń — przepływność na kontenerze (część miesiąca)

* Załóżmy, że utworzymy kontener z zainicjowaną przepływność wynoszącą 2 500 RU/s. Kontener jest przez 24 godziny w ciągu miesiąca (na przykład po jego utworzeniu zostanie on usunięty przez 24 godziny).  

* Następnie zobaczymy 600 jednostek na rachunku (2 500 RU/s/100 RU/sek/Unit * 24 godziny). Koszt będzie wynosił $4,80 (600 jednostek * $0.008/jednostka).

* Łączny rachunek za miesiąc będzie wynosił $4,80.

### <a name="billing-rate-if-storage-size-changes"></a>Stawka rozliczeń w przypadku zmiany rozmiaru magazynu

Pojemność magazynu jest rozliczana w jednostkach maksymalnej godzinnej ilości przechowywanych danych (w GB) w miesiącu. Jeśli na przykład w pierwszej połowie miesiąca będzie używane 100 GB miejsca w magazynie, a w drugiej połowie miesiąca będzie używane 50 GB, opłata będzie odpowiadać użyciu 75 GB miejsca w magazynie w tym miesiącu.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Stawka rozliczeniowa, gdy kontener lub zestaw kontenerów są aktywne przez czas krótszy niż godzina

Jest naliczana stała stawka za każdą godzinę istnienia kontenera lub bazy danych, niezależnie od użycia lub aktywności kontenera lub bazy danych krótszej niż godzina. Jeśli na przykład utworzysz kontener lub bazę danych i usuniesz ją 5 minut później, rachunek będzie obejmował godzinę.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Stawka rozliczeniowa w przypadku skalowania w górę/w dół przepływności na kontenerze lub w bazie danych

W przypadku zwiększenia przepływności aprowizacji o godzinie 9:30 od 400 RU/s do 1 000 RU/s, a następnie obniżenia przepływności o przepustowości do 10:45 z powrotem do 400 RU/s, opłata zostanie naliczona za dwie godziny, w ciągu 1 000 RU/s. 

W przypadku zwiększenia elastycznej przepływności dla kontenera lub zestawu kontenerów o godzinie 9:30 od 100 – K RU/s do 200-K RU/s, a następnie obniżenia przepływności w systemie 10:45 z powrotem do 100-K ru/s, zostanie naliczona opłata za dwie godziny na 200 K RU/s.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Przykład rozliczeń: wiele kontenerów, z których każdy ma dedykowany Tryb przepływności

* Jeśli utworzysz konto usługi Azure Cosmos w regionie Wschodnie stany USA 2 z dwoma kontenerami z zainicjowaną przepływność wynoszącą 500 RU/s i 700 RU/s, należy dysponować łączną przepustowością dla 1 200 RU/s.  

* Opłata zostanie naliczona za 1200/100 * $0,008 = $0.096/godzinę. 

* Jeśli wymagana jest zmiana przepływności i zwiększono pojemność każdego kontenera o 500 RU/s, podczas tworzenia nowego nieograniczonego kontenera z 20 000 RU/s, ogólna pojemność będzie równa 22 200 RU/s (1 000 RU/s + 1 200 RU/s + 20, 000RU/s).  

* Następnie rachunek zostanie zmieniony na: $0,008 x 222 = $1.776/godz. 

* W miesiącu 720 godzin (24 godziny * 30 dni), jeśli w przypadku przepływności zainicjowanej przez 500 godz. została 1 200 RU/s i w przypadku pozostałego 22 200 czasu żądania przewidzianych przez program 220 godz. 500 x $0.096/godzinę + 220 x $1.776/godz. = $438.72/miesiąc.

![Przykład dedykowanej opłaty za przepływność](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Przykład rozliczania: kontenery z trybem przepływności udostępnionej

* Jeśli utworzysz konto usługi Azure Cosmos w regionie Wschodnie stany USA 2 z dwiema bazami danych usługi Azure Cosmos (z zestawem kontenerów, które współużytkują przepływność na poziomie bazy danych) z elastyczną przepływność wynoszącą 50-K RU/s i 70-K RU/s, należy udostępnić łącznie przepływność 120 KB/s.  

* Opłata zostanie naliczona 1200 x $0,008 = $9.60/godz. 

* W przypadku zmiany przepływności i zwiększenia przepływności każdej bazy danych, która została zainicjowana przez 10 jednostek RU/s dla każdej bazy danych, i dodanie nowego kontenera do pierwszej bazy danych z dedykowanym trybem przepływności o wartości 15 – K/s do udostępnionej bazy danych przepływności Ogólnie zainicjowana pojemność będzie 155-K RU/s (60 K RU/s + 80 K RU/s + 15 K RU/s).  

* Opłata zmieniłaby się na: 1 550 * $0,008 = $12.40/godz.  

* W miesiącu 720 godzin, jeśli w przypadku przepływności z obsługą administracyjną 300 godz. została 120-K RU/s i w przypadku pozostałego czasu na 155 czas dla pozostałych usług 420 godz. 300 x $9.60/godzinę + 420 x $12.40/godzinę = $2 880 + $5 208 = $8088/miesiąc. 

![Przykład wspólnej przepływności](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Przykłady rozliczeń z replikacją geograficzną i wieloma wzorcami  

W dowolnym momencie możesz dodawać i usuwać regiony platformy Azure w dowolnym miejscu na świecie do konta usługi Azure Cosmos Database. Przepływność skonfigurowana dla różnych baz danych i kontenerów usługi Azure Cosmos zostanie zarezerwowana w każdym z regionów świadczenia usługi Azure skojarzonych z kontem bazy danych usługi Azure Cosmos. Jeśli suma zainicjowanej przepływności (RU/s) skonfigurowana dla wszystkich baz danych i kontenerów w ramach konta usługi Azure Cosmos Database (zainicjowana na godzinę) to T, a liczba regionów platformy Azure skojarzonych z kontem bazy danych to N, suma zainicjowana przepływność przez daną godzinę dla konta bazy danych Azure Cosmos (a) skonfigurowanego za pomocą jednego regionu zapisu jest równa T x N RU/s i (b) skonfigurowany ze wszystkimi regionami, w których przetwarzanie zapisów jest równe T x (N + 1) RU/s piwo. Elastyczna przepływność (pojedynczy region zapisu) koszty $0.008/godz. na 100 RU/s i zainicjowana przepływność z wieloma regionami do zapisu (Konfiguracja z wieloma wzorcami) — koszty $0,016;/za godzinę na 100 RU/s (zobacz [stronę](https://azure.microsoft.com/pricing/details/cosmos-db/)z cennikiem). Niezależnie od tego, czy jego pojedynczy region zapisu lub wiele regionów zapisu Azure Cosmos DB umożliwia odczytywanie danych z dowolnego regionu.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Przykład rozliczeń: wieloregionowe konto usługi Azure Cosmos, zapisy jednego regionu

Załóżmy, że masz kontener platformy Azure Cosmos w regionie zachodnie stany USA. Kontener jest tworzony z szybkością przepływności na 10 000 jednostek RU/s i przechowujesz 1 TB danych w tym miesiącu. Załóżmy, że dodasz trzy regiony (Wschodnie stany USA, Europa Północna i Azja Wschodnia) do konta usługi Azure Cosmos, z których każdy ma ten sam magazyn i przepływność. Łączny rachunek miesięczny będzie wynosić (przy założeniu 30 dni w miesiącu). Rachunek będzie następujący: 

|**Element** |**Użycie (miesiąc)** |**Transmisji** |**Koszt miesięczny** |
|---------|---------|---------|-------|
|Rachunek za przepływność dla kontenera w regionie Zachodnie stany USA      | 10 000 RU/s * 24 * 30    |$0,008 za 100 RU/s na godzinę   |$576|
|Rachunek za przepływność dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia       | 3 * 10 tys./s * 24 * 30    |$0,008 za 100 RU/s na godzinę  |$1 728|
|Rachunek za przestrzeń dyskową dla kontenera w regionie Zachodnie stany USA      | 250 GB    |$0,25/GB  |$62,50|
|Rachunek za przestrzeń dyskową dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Łączna liczba**     |     |  |**$2 554**|

*Załóżmy również, że wychodzące 100 GB danych co miesiąc z kontenera w regionie zachodnie stany USA w celu replikowania danych do regionu Wschodnie stany USA, Europa Północna i Azja Wschodnia. Opłaty są naliczane za ruch wychodzący zgodnie z stawkami za transfer danych.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Przykład rozliczeń: wieloregionowe konto platformy Azure Cosmos, zapisy z wieloregionem

Załóżmy, że utworzysz kontener usługi Azure Cosmos w regionie zachodnie stany USA. Kontener jest tworzony z szybkością przepływności na 10 000 jednostek RU/s i przechowujesz 1 TB danych w tym miesiącu. Załóżmy, że dodasz trzy regiony (Wschodnie stany USA, Europa Północna i Azja Wschodnia), z których każdy ma ten sam magazyn i przepływność, i chcesz mieć możliwość zapisu w kontenerach we wszystkich regionach skojarzonych z kontem usługi Azure Cosmos. Łączny rachunek miesięczny będzie wynosić (przy założeniu 30 dni w miesiącu) w następujący sposób:

|**Element** |**Użycie (miesiąc)**|**Transmisji** |**Koszt miesięczny** |
|---------|---------|---------|-------|
|Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)       | 10 000 RU/s * 24 * 30    |$0,016 za 100 RU/s na godzinę    |$1 152 |
|Rachunek przepływności dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia (wszystkie regiony są zapisywalne)        | (3 + 1) * 10 000 RU/s * 24 * 30    |$0,016 za 100 RU/s na godzinę   |$4 608 |
|Rachunek za przestrzeń dyskową dla kontenera w regionie Zachodnie stany USA      | 250 GB    |$0,25/GB  |$62,50|
|Rachunek za przestrzeń dyskową dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Łączna liczba**     |     |  |**$6 010**|

*Załóżmy również, że wychodzące 100 GB danych co miesiąc z kontenera w regionie zachodnie stany USA w celu replikowania danych do regionu Wschodnie stany USA, Europa Północna i Azja Wschodnia. Opłaty są naliczane za ruch wychodzący zgodnie z stawkami za transfer danych.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Przykład rozliczeń: Konto usługi Azure Cosmos z wieloma głównymi przepływami na poziomie bazy danych, w tym dedykowanym trybem przepływności dla niektórych kontenerów

Rozważmy poniższy przykład, w którym mamy wieloregionowe konto platformy Azure Cosmos, w którym wszystkie regiony są zapisywalne (Konfiguracja z wieloma wzorcami). Dla uproszczenia przyjęto, że rozmiar magazynu pozostaje stały i nie zostanie zmieniony i pominięty w tym miejscu, aby uprościć przykład. Zainicjowana przepływność w miesiącu, w którym nadano następujące czynności (przy założeniu 30 dni lub 720 godzin): 

[0-100 godzin]:  

* Utworzyliśmy trzy regiony konto platformy Azure Cosmos (Zachodnie stany USA, Wschodnie stany USA, Europa Północna), gdzie wszystkie regiony są zapisywalne 

* Utworzyliśmy bazę danych (D1) z udostępnioną przepływności 10 000 jednostek/s 

* Utworzyliśmy bazę danych (D2) z udostępnioną przepływności 30-K RU/s i  

* Utworzyliśmy kontener (C1) z dedykowaną przepustowością 20 KB/s 

[101-200 godzin]:  

* Skalowanie bazy danych (D1) do 50 K RU/s 

* Skalowanie bazy danych (D2) do 70 K RU/s  

* Usunięto kontener (C1)  

[201-300 godzin]:  

* Utworzono kontener (C1) z dedykowaną przepływności 20 KB/s 

[301-400 godzin]:  

* Usunięto jeden z regionów z konta usługi Azure Cosmos (liczba regionów z możliwością zapisu to teraz 2) 

* Skalowanie bazy danych (D1) do 10 000 jednostek RU na sekundę 

* Skalowanie bazy danych (D2) do 80 K RU/s  

* Usunięto kontener (C1) 

[401-500 godzin]:  

* Skalowanie bazy danych (D2) do 10 000 jednostek RU na sekundę  

* Utworzono kontener (C1) z dedykowaną przepływności 20 KB/s 

[501-700 godzin]:  

* Skalowanie bazy danych (D1) do 20 KB/s  

* Skalowanie bazy danych (D2) do 100 K RU/s  

* Usunięto kontener (C1)  

[701-720 godzin]:  

* Skalowanie bazy danych (D2) do 50 K RU/s  

Na poniższej ilustracji przedstawiono wizualnie zmiany całkowitej alokowanej przepływności w czasie 720 godzin dla miesiąca. 

![Przykład rzeczywistej żywotności](./media/understand-your-bill/bill-example3.png)

Łączny rachunek miesięczny (przy założeniu, że 30 dni/720 godzin w miesiącu) będzie obliczany w następujący sposób:

|**Liczb**  |**RU/s** |**Element** |**Użycie (co godzinę)** |**Koszty** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10 TYS. <br/>D2:30K <br/>C1:20 000 |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2 880  |
|[101-200] |D1:50 000 <br/>D2:70K <br/>C1:-- |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5 760  |
|[201-300]  |D1:50 000 <br/>D2:70K <br/>C1:20 000 |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2 240  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6 720 |
|[301-400] |D1:10 TYS. <br/>D2:80K <br/>C1:-- |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1 440   |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2 880  |
|[401-500] |D1:10 TYS. <br>D2:10 TYS. <br>C1:20 000 |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1 280  |
|[501-700] |D1:20 000 <br>D2:100 000 <br>C1:-- |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3 840  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7 680  |
|[701-720] |D1:20 000 <br/>D2:50 000 <br/>C1:-- |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Łączny koszt miesięczny**  | |**$38 688**   |

## <a name="proactively-estimating-your-monthly-bill"></a>Proaktywne szacowanie rachunku miesięcznego  

Rozważmy inny przykład, w którym chcesz proaktywnie oszacować swój rachunek przed końcem miesiąca. Możesz oszacować swój rachunek w następujący sposób:

|**Koszt magazynu** | |
|----|----|
|Średni rozmiar rekordu (KB) |1 |
|Liczba rekordów  |100 000 000  |
|Łączny magazyn (GB)  |100 |
|Koszt miesięczny za GB  |$0,25  |
|Oczekiwany miesięczny koszt magazynu   |$25,00  |

<br>

|**Koszt przepływności** | | | |
|----|----|----|----|
|Typ operacji| Liczba żądań na sekundę| Średni RU/żądanie| Jednostek ru|
|Zapis| 100 | 5 | 500|
|Odczyt| 400| 1| 400|

Łączna liczba jednostek RU na sekundę: 500 + 400 = 900 koszt godzinowy: 900/100 * $0,008 = $0,072 oczekiwany miesięczny koszt dla przepływności (przy założeniu 31 dni): $0,072 * 24 * 31 = $53,57

**Łączny koszt miesięczny**

Łączny koszt miesięczny = miesięczny koszt magazynu i miesięczny koszt dla przepływności łączny koszt miesięczny = $25,00 + $53,57 = $78,57

*Ceny mogą się różnić w zależności od regionu. Aby uzyskać aktualną cenę, zobacz [stronę](https://azure.microsoft.com/pricing/details/cosmos-db/)z cennikiem.*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Rozliczanie za pomocą zarezerwowanej pojemności Azure Cosmos DB

Azure Cosmos DB zarezerwowana pojemność umożliwia zakupienie alokowanej przepływności (zarezerwowanej pojemności lub rezerwacji), która może zostać zastosowana do wszystkich baz danych i kontenerów platformy Azure Cosmos (dla dowolnego interfejsu API lub modelu danych) we wszystkich regionach świadczenia usługi Azure. Ze względu na to, że zainicjowana cena przepływności różni się w zależności od regionu, można traktować zastrzeżoną pojemność jako środki pieniężne, które zostały nabyte w ramach rabatu, które mogą być naliczane z tytułu przepustowości w poszczególnych regionach. Załóżmy na przykład, że masz konto usługi Azure Cosmos z jednym kontenerem z obsługą jednostki 50 – K RU/s i globalnie zreplikowane dwa regiony — Wschodnie stany USA i Japonia Wschodnia. Jeśli wybierzesz opcję płatność zgodnie z rzeczywistym użyciem, płacisz:  

* we wschodnich stanach USA: dla 50 – K RU/s według stawki $0,008 za 100 RU/s w tym regionie 

* w regionie Japonia Wschodnia: dla 50 – K RU/s o stawce $0,009 za 100 RU/s

Łączny rachunek (bez zarezerwowanej pojemności) (przy założeniu 30 dni lub 720 godzin): 

|**Region**| **Cena godzinowa za 100 RU/s**|**Jednostki (RU/s)**|**Kwota rozliczana (co godzinę)**| **Kwota rozliczana (miesięcznie)**|
|----|----|----|----|----|
|East US|$0,008 |50 K|$4|$2 880 |
|Japonia Wschodnia|$0,009 |50 K| $4,50 |$3 240 |
|Łącznie|||$8,50|$6 120 |

Rozważmy, że zamiast tego zakupiono zastrzeżoną pojemność. Możesz kupić zarezerwowaną pojemność dla 100 – K RU/s w cenie $56 064 przez jeden rok (za 20% rabatu) lub $6,40 na godzinę. Zobacz cennik pojemności zarezerwowanych na [stronie cennika](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Koszt przepływności (płatność zgodnie z rzeczywistym użyciem): 100 000 RU/s/100 * $0.008/godz. * 8760 godz. w roku = $70 080 

* Koszt przepływności (z zarezerwowaną pojemnością) $70 080 z rabatem w wysokości 20% = $56 064 

Rzeczywiste zakupione dane to kredyt z $8 USD za godzinę dla 100 K/s, przy użyciu cennika na wschód, przy cenie $6,40 za godzinę. Następnie można wystawić na podstawie tej wstępnie płatnej rezerwacji dotyczącej przepływności co godzinę dla alokowanej przepływności w dowolnym globalnym regionie platformy Azure, zgodnie z odpowiednimi cenami list regionalnych ustawionymi dla Twojej subskrypcji. W tym przykładzie, gdzie zainicjujesz 50 K RU/s każdego w regionach Wschodnie stany USA i Japonia Wschodnia, będziesz mieć możliwość naliczania na $8,00. zainicjowanej przepływności na godzinę i zostanie naliczona nadwyżka za $0,50 za godzinę (lub USD za miesiąc). 

|**Region**| **Cena godzinowa za 100 RU/s**|**Jednostki (RU/s)**| **Kwota rozliczana (co godzinę)**| **Kwota rozliczana (miesięcznie)**|
|----|----|----|----|----|
|East US|$0,008 |50 K|$4|$2 880 |
|Japonia Wschodnia|$0,009 |50 K| $4,50 |$3 240 |
|||Płatność zgodnie z rzeczywistym użyciem|$8,50|$6120|
|Zakupiona pojemność zarezerwowana|$0,0064 (20% rabatu) |100 jednostki RU/s lub $8 pojemności wstępnie kupione |-$8|-$5 760 |
|Rachunek netto|||$0,50 |$360 |

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej o tym, [jak Cosmos DB model cenowy jest ekonomiczny dla klientów](total-cost-ownership.md)
* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się [](optimize-cost-throughput.md) więcej na temat optymalizowania kosztu przepływności
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej [na temat optymalizowania kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)
