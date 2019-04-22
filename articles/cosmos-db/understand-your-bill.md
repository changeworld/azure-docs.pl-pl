---
title: Informacje o rachunku Azure Cosmos DB
description: W tym artykule wyjaśniono, jak informacje o rachunku Azure Cosmos DB przy użyciu kilka przykładów.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: d3bfe1b54409fd57f7535bac2362dc7040975061
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877642"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Informacje o rachunku Azure Cosmos DB

Jako usługę w pełni zarządzana baza danych natywnych dla chmury Azure Cosmos DB upraszcza rozliczenia opłat tylko za aprowizowaną przepływność i używane magazynu. Istnieją, nie dodatkowych opłat, sprzętu, kosztów narzędzie lub funkcji koszty w porównaniu do środowiska lokalnego lub hostowanego w modelu IaaS alternatyw. Po zastanowieniu się nad wielu regionów możliwości usługi Azure Cosmos DB, usługa bazy danych zapewnia znaczną redukcję kosztów w porównaniu z istniejącym lokalnym lub rozwiązań IaaS.

Usługa Azure Cosmos DB są rozliczane godzinowo na podstawie aprowizowaną przepływność i użytego miejsca do magazynowania. Aprowizowana przepływność jednostki na potrzeby rozliczeń jest 100 jednostek żądań na sekundę na godzinę, kosztuje $0.008 za godzinę, zakładając, że standardowej ceny publicznych, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/). Dla użytego miejsca do magazynowania, są rozliczane 0,25 USD na 1 GB pamięci na miesiąc, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/). 

W tym artykule używa kilka przykładów, aby lepiej zrozumieć szczegóły, które są wyświetlane na miesięcznym rachunku. Liczby wyświetlane w przykładach mogą się różnić, jeśli kontenerów usługi Azure Cosmos ma inną ilość przepływnością aprowizowaną span w wielu regionach, lub uruchamianie dla innego przez okres ponad miesiąc.

## <a name="billing-examples"></a>Przykłady rozliczeń

### <a name="billing-example---throughput-on-a-container-full-month"></a>Przykład rozliczenia — przepływność na kontenerze (pełny miesiąc)

* Załóżmy, że można skonfigurować przepływność 1000 jednostek żądań na sekundę na kontener i istnieje 24 godziny * 30 dni w miesiącu = 720 godzin.  

* 1000 jednostek żądań na sekundę jest 10 jednostek po 100 jednostek żądań na sekundę na godzinę za każdą godzinę używania kontenerów istnieje (oznacza to, 1000/100 = 10). 

* Mnożenie 10 jednostek na godzinę przez koszt $0.008 (na 100 jednostek ru na sekundę na godzinę) = 0,08 USD / godz. 

* Mnożenie 0,08 USD za godzinę przez liczbę godzin w miesiącu jest równe $0.08 * 24 godziny * 30 dni = $57.60 na miesiąc.  

* Łączna kwota rachunku miesięcznego pokaże 7200 jednostki (100 jednostek), które będzie kosztować 57.60 $.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Przykład rozliczenia — przepływność na kontenerze (część miesiąca)

* Załóżmy, że możemy utworzyć kontener z aprowizowaną przepływnością 2500 jednostek RU na sekundę. Kontener są przechowywane przez 24 godziny w ciągu miesiąca (na przykład, zostaną usunięte po upływie 24 godzin po jego utworzeniu).  

* Następnie zajmiemy się tym 600 jednostki, na rachunku (klient może dysponować 2500 jednostek ru / 100 RU/sek/jednostkę * 24 godziny). Koszt będzie 4.80 $ (600 jednostek * $0.008 na jednostkę).

* Łączny rachunek za miesiąc będzie 4.80 $.

### <a name="billing-rate-if-storage-size-changes"></a>Oprocentowanie, jeśli zmieni się rozmiar magazynu

Pojemność magazynu jest rozliczana w jednostkach maksymalnej godzinnej ilości przechowywanych danych (w GB) w miesiącu. Jeśli na przykład w pierwszej połowie miesiąca będzie używane 100 GB miejsca w magazynie, a w drugiej połowie miesiąca będzie używane 50 GB, opłata będzie odpowiadać użyciu 75 GB miejsca w magazynie w tym miesiącu.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Naliczania, gdy kontener lub zestaw kontenerów są aktywne przez mniej niż godzinę

Wszystko jest stosowana jest stała stawka za każdą godzinę używania kontenera lub bazy danych, niezależnie od użycia lub Jeśli kontener lub baza danych jest aktywna krócej niż godzinę. Na przykład jeśli utworzysz kontener lub bazy danych i usuniesz ją 5 minut później, opłata będzie zawierać jedną godzinę.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Oprocentowanie, gdy przepływność na kontenerze lub bazy danych jest skalowana w górę/w dół

Jeśli zwiększysz aprowizowaną przepływność na 9:30 uaktualnienie z 400 jednostek RU na sekundę do 1000 jednostek żądań na sekundę i zmniejszysz o 10:45 ponownie do 400 jednostek RU na sekundę, opłata wyniesie dla dwie godziny użycia 1000 jednostek żądań na sekundę. 

Jeśli zwiększysz aprowizowaną przepływność dla kontenera lub zestaw kontenerów o 9:30 uaktualnienie z 100 tysięcy jednostek ru do 200 K jednostek RU na sekundę i następnie niższe aprowizowanej przepływności o 10:45 ponownie 100 tysięcy jednostek RU, użytkownik zostanie naliczona opłata dwóch godzin od 200 jednostek RU KB/s.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Przykładowe rozliczenia: wiele kontenerów, każdy z trybem dedykowanych aprowizowana przepływność

* Jeśli utworzysz konto usługi Azure Cosmos w regionie wschodnie stany USA 2 z dwóch kontenerów z aprowizowaną przepływnością 500 jednostek RU na sekundę i 700 jednostek RU na sekundę, odpowiednio, trzeba łączna aprowizowana przepływność 1200 jednostek RU na sekundę.  

* Opłaty będą naliczane 1200/100 * $0.008 = 0,096 USD / godz. 

* Jeśli zmienione w zakresie przepływności, a po zwiększenia pojemności każdego kontenera o 500 jednostek RU na sekundę oraz utworzenia nowego nieograniczonego kontenera z 20 000 jednostek RU na sekundę, łączna aprowizowana pojemność będzie wyniosłaby 22 200 jednostek RU na sekundę (1000 jednostek żądań na sekundę + 1200 jednostek RU na sekundę + 20 000RU na sekundę).  

* Opłata zmieniłaby się na: $0.008 x 222 = 1.776 $/ godz. 

* W danym miesiącu 720 godzin (24 godz. * 30 dni), jeśli 500 godzin aprowizowana przepływność został 1200 jednostek RU na sekundę, a pozostałe godziny 220 aprowizowanej przepływności był wyniosłaby 22 200 jednostek RU na sekundę, na miesięcznym rachunku zawiera: 500 x 0,096 USD / godzina + 220 x $1.776 / godzina = 438.72 $/ miesiąc.

![Przykład rachunek przepływność w wersji dedykowanej](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Przykładowe rozliczenia: kontenery z trybem udostępnionej przepływności

* Jeśli utworzysz konto usługi Azure Cosmos w regionach wschodnie stany USA 2 z dwóch Azure Cosmos baz danych (z zestaw kontenerów udostępnianie przepływności na poziomie bazy danych) z aprowizowaną przepływnością 50 K jednostek żądań na sekundę i 70-K jednostek RU na sekundę, odpowiednio, czy masz łącznie aprowizacji Przepływność 120 tysięcy jednostek RU na sekundę.  

* Opłaty będą naliczane 1200 x $0.008 = 9.60 $/ godz. 

* Jeśli zmienione w zakresie przepływności i zwiększenia aprowizowanej przepływności przez 10 K jednostek żądań na sekundę dla każdej bazy danych, każda baza danych, Dodaj nowy kontener pierwszej bazy danych z trybem dedykowanej przepływności 15 K jednostek żądań na sekundę do udostępnionej przepływności bazy danych, usługi Łączna aprowizowana pojemność będzie 155-K jednostek żądań na sekundę (60 tysięcy jednostek RU na sekundę + 80 tysięcy jednostek RU na sekundę + 15 K jednostek żądań na sekundę).  

* Opłata zmieniłaby się na: 1550 * $0.008 = 12.40 $/ godz.  

* W danym miesiącu 720 godzin, jeśli 300 godzin aprowizowanej przepływności została 120 tysięcy jednostek RU na sekundę, a pozostałe godziny 420 aprowizowanej przepływności był 155 tysięcy jednostek RU na sekundę, miesięczne rachunku będzie widoczne: 300 x $9.60 / godzinę + 420 x $12.40 / godz. = 2,880 $ + $5,208 = 8,088 $/ miesiąc. 

![Udostępnione przykład rachunek przepływności](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Przykłady rozliczeń za pomocą replikacji geograficznej i Multi-Master  

Można dodawać/usuwać regiony platformy Azure w dowolnym miejscu na świecie do Twojego konta bazy danych Azure Cosmos DB w dowolnym momencie. Przepływność, którą skonfigurowano dla różnych baz danych Azure Cosmos DB i kontenerów zostanie zarezerwowany we wszystkich regionach platformy Azure skojarzony z Twoim kontem bazy danych Azure Cosmos. Jeśli skonfigurowano sumę aprowizowana przepływność (RU/s) we wszystkich bazach danych i kontenerów w bazie danych Azure Cosmos konto (za godzinę) jest T, jak i liczbie regionów platformy Azure, powiązany z Twoim kontem bazy danych jest N, a następnie Suma aprowizowana przepływność dla danej godziny, dla usługi Cosmos Azure () skonfigurowano z regionem zapisu jednego konto bazy danych jest równy T x N jednostek żądań na sekundę oraz (b) skonfigurowano we wszystkich regionach, która może przetwarzać zapisu jest równy T x (N + 1) jednostek żądań na sekundę , odpowiednio. Aprowizowana przepływność (region zapisu jednego) kosztuje $0.008 za godzinę dla 100 jednostek żądań na sekundę i aprowizowaną przepływność z wieloma regionami zapisu (Konfiguracja wielu wzorców) koszty 0,016; $/ za godzinę za 100 jednostek żądań na sekundę (zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)). Czy jego pojedynczego zapisu, regionu lub wielu regionach zapisu, usługi Azure Cosmos DB umożliwia odczytywanie danych z dowolnego regionu.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Przykładowe rozliczenia: zapisuje jednym regionie konta usługi Azure Cosmos wielu regionów,

Załóżmy, że masz kontener usługi Azure Cosmos w regionie zachodnie stany USA. Ten kontener jest tworzony przy przepływności 10 tys jednostek żądań na sekundę i przechowujesz 1 TB danych w tym miesiącu. Załóżmy, że możesz dodać w trzech regionach (wschodnie stany USA, Europa Północna i Azja Wschodnia) do swojego konta usługi Azure Cosmos, każdy z tego samego magazynu i przepływności. Łączny rachunek miesięczny będzie mieć (przy założeniu 30 dni w miesiącu). Na rachunku będzie następujący: 

|**Element** |**Użycie (miesiąc)** |**Kurs** |**Koszt miesięczny** |
|---------|---------|---------|-------|
|Rachunek za przepływność dla kontenera w regionie Zachodnie stany USA      | 10 K jednostek żądań na sekundę * 24 * 30    |$0.008 na 100 jednostek żądań na sekundę na godzinę   |$576|
|Rachunek za przepływność dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia       | K 3 * 10 jednostek żądań na sekundę * 24 * 30    |$0.008 na 100 jednostek żądań na sekundę na godzinę  |$1,728|
|Rachunek za przestrzeń dyskową dla kontenera w regionie Zachodnie stany USA      | 250 GB    |0,25 USD/GB  |$62.50|
|Rachunek za przestrzeń dyskową dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia      | 3 * 250 GB    |0,25 USD/GB  |$187.50|
|**Łączna liczba**     |     |  |**$2,554**|

*Załóżmy również, że wychodzi 100 GB danych miesięcznie z kontenera w regionie zachodnie stany USA do replikacji danych w regionie wschodnie stany USA, Europa Północna i Azja Wschodnia. Opłaty naliczane za wychodzący ruch według stawki za transfer danych.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Przykładowe rozliczenia: konta usługi Azure Cosmos wielu regionów, obsługa wielu regionów zapisuje

Załóżmy, że należy utworzyć kontener usługi Azure Cosmos w regionie zachodnie stany USA. Ten kontener jest tworzony przy przepływności 10 tys jednostek żądań na sekundę i przechowujesz 1 TB danych w tym miesiącu. Załóżmy, że możesz dodać w trzech regionach (wschodnie stany USA, Europa Północna i Azja Wschodnia), każdy z tego samego magazynu i przepływności i mają możliwość zapisu do kontenerów we wszystkich regionach skojarzonych z Twoim kontem usługi Azure Cosmos. Łączna kwota rachunku miesięcznego będzie mieć (przy założeniu 30 dni w miesiącu) w następujący sposób:

|**Element** |**Użycie (miesiąc)**|**Kurs** |**Koszt miesięczny** |
|---------|---------|---------|-------|
|Rachunek za przepływność dla kontenera w regionie zachodnie stany USA (we wszystkich regionach są zapisywalne)       | 10 K jednostek żądań na sekundę * 24 * 30    |0,016; $ na 100 jednostek żądań na sekundę na godzinę    |$1,152 |
|Rachunek za przepływność dla 3 dodatkowych regionów — wschodnie stany USA, Europa Północna i Azja Wschodnia (we wszystkich regionach są zapisywalne)        | (3 + 1) * 10 K jednostek żądań na sekundę * 24 * 30    |0,016; $ na 100 jednostek żądań na sekundę na godzinę   |$4,608 |
|Rachunek za przestrzeń dyskową dla kontenera w regionie Zachodnie stany USA      | 250 GB    |0,25 USD/GB  |$62.50|
|Rachunek za przestrzeń dyskową dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia      | 3 * 250 GB    |0,25 USD/GB  |$187.50|
|**Łączna liczba**     |     |  |**$6,010**|

*Załóżmy również, że wychodzi 100 GB danych miesięcznie z kontenera w regionie zachodnie stany USA do replikacji danych w regionie wschodnie stany USA, Europa Północna i Azja Wschodnia. Opłaty naliczane za wychodzący ruch według stawki za transfer danych.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Przykład rozliczeń: Konto usługi Azure Cosmos z przepływności wielu wzorców, poziom bazy danych, w tym trybie dedykowanej przepływności dla niektórych kontenerów

Rozważmy poniższy przykład, w których istnieje na koncie usługi Azure Cosmos multiregionalne gdzie we wszystkich regionach są zapisywalne (Konfiguracja wielu wzorców). Dla uproszczenia przyjęto założenie, że rozmiar magazynu pozostaje stała i nie zmianę i pominąć tu zapewnienie prostsze w przykładzie. Aprowizowana przepływność w miesiącu zróżnicowane w następujący sposób (przyjmuje 30 dni lub godzin 720): 

[0-100 godzin]:  

* Utworzone konto usługi Azure Cosmos trzy region (zachodnie stany USA, wschodnie stany USA, Europa Północna), gdzie we wszystkich regionach są zapisywalne 

* Utworzyliśmy bazę danych (D1) z udostępnionej przepływności 10 tys. jednostek żądań na sekundę 

* Utworzyliśmy bazę danych (D2) z udostępnionej przepływności 30-K jednostek żądań na sekundę i  

* Utworzyliśmy kontener (C1) za pomocą dedykowanej przepływności 20 K jednostek żądań na sekundę 

[101 – 200 godziny]:  

* Przeskalowaliśmy bazy danych (D1) do 50 tysięcy jednostek RU na sekundę 

* Przeskalowaliśmy bazy danych (D2) do 70 K jednostek żądań na sekundę  

* Firma Microsoft usuniętego kontenera (C1)  

[201-300 godzin]:  

* Utworzyliśmy kontener (C1) z ponownie z dedykowanej przepływności 20 K jednostek żądań na sekundę 

[301 400 godzin]:  

* Firma Microsoft usunęła jeden z regionów z konta usługi Azure Cosmos (liczba regionów zapisu wynosi teraz 2) 

* Firma Microsoft skalowania bazy danych (D1) w celu 10 K jednostek żądań na sekundę 

* Przeskalowaliśmy bazy danych (D2) do 80 tysięcy jednostek RU na sekundę  

* Firma Microsoft usuniętego kontenera (C1) 

[godziny 401-500]:  

* Firma Microsoft skalowania bazy danych (D2) w celu 10 K jednostek żądań na sekundę  

* Utworzyliśmy kontener (C1) z ponownie z dedykowanej przepływności 20 K jednostek żądań na sekundę 

[501-700 godziny]:  

* Przeskalowaliśmy bazy danych (D1) do 20 K jednostek żądań na sekundę  

* Przeskalowaliśmy bazy danych (D2) do 100 tysięcy jednostek RU na sekundę  

* Firma Microsoft usuniętego kontenera (C1)  

[701 720 godziny]:  

* Firma Microsoft skalowania bazy danych (D2) w celu 50 tysięcy jednostek RU na sekundę  

Wizualnie zmiany łączna aprowizowana przepływność podczas 720 godzin na miesiąc, są wyświetlane na poniższej ilustracji: 

![Przykład realnym](./media/understand-your-bill/bill-example3.png)

Łączna kwota rachunku miesięcznego, będzie można (przy założeniu 30 dni/720 godziny w miesiącu) będą obliczane w następujący sposób:

|**godz.**  |**RU/s** |**Element** |**Użycie (co godzinę)** |**Koszty** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Rachunek za przepływność dla kontenera w regionie zachodnie stany USA (we wszystkich regionach są zapisywalne)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Rachunek za przepływność dla 2 dodatkowych regionach: Wschodnie stany USA, Europa Północna (we wszystkich regionach są zapisywalne)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1: -- |Rachunek za przepływność dla kontenera w regionie zachodnie stany USA (we wszystkich regionach są zapisywalne)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Rachunek za przepływność dla 2 dodatkowych regionach: Wschodnie stany USA, Europa Północna (we wszystkich regionach są zapisywalne)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5,760  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Rachunek za przepływność dla kontenera w regionie zachodnie stany USA (we wszystkich regionach są zapisywalne)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2,240  |
| | |Rachunek za przepływność dla 2 dodatkowych regionach: Wschodnie stany USA, Europa Północna (we wszystkich regionach są zapisywalne)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6,720 |
|[301-400] |D1:10K <br/>D2:80K <br/>C1: -- |Rachunek za przepływność dla kontenera w regionie zachodnie stany USA (we wszystkich regionach są zapisywalne)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1,440   |
| | |Rachunek za przepływność dla 2 dodatkowych regionach: Wschodnie stany USA, Europa Północna (we wszystkich regionach są zapisywalne)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Rachunek za przepływność dla kontenera w regionie zachodnie stany USA (we wszystkich regionach są zapisywalne)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Rachunek za przepływność dla 2 dodatkowych regionach: Wschodnie stany USA, Europa Północna (we wszystkich regionach są zapisywalne)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1,280  |
|[501-700] |D1:20K <br>D2:100K <br>C1: -- |Rachunek za przepływność dla kontenera w regionie zachodnie stany USA (we wszystkich regionach są zapisywalne)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3,840  |
| | |Rachunek za przepływność dla 2 dodatkowych regionach: Wschodnie stany USA, Europa Północna (we wszystkich regionach są zapisywalne)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7,680  |
|[701-720] |D1:20K <br/>D2:50K <br/>C1: -- |Rachunek za przepływność dla kontenera w regionie zachodnie stany USA (we wszystkich regionach są zapisywalne)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Rachunek za przepływność dla 2 dodatkowych regionach: Wschodnie stany USA, Europa Północna (we wszystkich regionach są zapisywalne)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Łączny koszt miesięczny**  | |**$38,688**   |

## <a name="proactively-estimating-your-monthly-bill"></a>Proaktywnie szacowania, na miesięcznym rachunku  

Rozważmy inny przykład, w którym ma być aktywnie Szacowanie wysokości rachunku przed końcem miesiąca. Można oszacować swój rachunek w następujący sposób:

|**Koszt usługi Storage** | |
|----|----|
|Rekord średni rozmiar (KB) |1 |
|Liczba rekordów  |100,000,000  |
|Całkowita ilość miejsca (GB)  |100 |
|Miesięczny koszt za GB  |$0.25  |
|Oczekiwany koszt miesięczny magazynu   |$25.00  |

<br>

|**Koszt przepływności** | | | |
|----|----|----|----|
|Typ operacji| Żądań na sekundę| Średni RU/request| Wymagane (RUS)|
|Zapisywanie| 100 | 5 | 500|
|Odczyt| 400| 1| 400|

Łączna liczba jednostek RU na sekundę: 500 + 400 = 900 koszt na godzinę: 900/100 * $0.008 = $0.072 oczekiwany koszt miesięczny przepływności (przy założeniu 31 dni): $0.072 * 24 * 31 = 53.57 $

**Łączny koszt miesięczny**

Łączny koszt miesięczny = miesięczny koszt magazynu i przepływności Całkowity miesięczny koszt miesięczny koszt = 25,00 + $53.57 = 78.57 $

*Ceny mogą się różnić od regionu. Aktualne o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Opłaty za pomocą usługi Azure Cosmos DB wydajności rezerwowej

Pojemność na platformie Azure Cosmos DB zastrzeżone umożliwia zakup przepływnością wcześniej (rezerwowanie pojemności lub zastrzeżenie) można zastosować do wszystkich baz danych Azure Cosmos DB i kontenerów (dla dowolnego modelu danych i interfejsów API) we wszystkich regionach platformy Azure. Ponieważ aprowizowanej przepływności różnorodny cennik regionu, warto traktować rezerwowanie pojemności jako środków pieniężnych, zakupionego z rabatem, które mogą być wystawiane z aprowizowaną przepływność w danym czasie cenie odpowiednie w każdym regionie. Załóżmy na przykład, że masz konto z jednym kontenerze obsługiwana za pomocą jednostek żądań na sekundę z 50-K i globalnie replikowane dwóch regionów — wschodnie stany USA i Japonia, część wschodnia usługa Azure Cosmos. Jeśli wybierzesz opcję zgodnie z rzeczywistym użyciem, zapłacisz:  

* w regionie wschodnie stany USA: dla 50 tysięcy jednostek RU na sekundę zgodnie ze stawką $0.008 na 100 jednostek żądań na sekundę w tym regionie 

* w regionie Japonia Wschodnia: dla 50 tysięcy jednostek RU na sekundę zgodnie ze stawką 0.009 $ na 100 jednostek żądań na sekundę w tym regionie

Łączny rachunek (bez rezerwowanie pojemności) będzie (przyjmuje 30 dni lub godzin 720): 

|**Region**| **Cena za godzinę dla 100 jednostek RU/s**|**Jednostki (RU/s)**|**Naliczona opłata (co godzinę)**| **Naliczona opłata (miesięcznie)**|
|----|----|----|----|----|
|Wschodnie stany USA|$0.008 |50 K|$4|$2,880 |
|Japonia Wschodnia|$0.009 |50 K| $4.50 |$3,240 |
|Łącznie|||$8.50|$6,120 |

Zastanówmy się, że zakupiono rezerwowanie pojemności zamiast tego. Możesz kupić rezerwowanie pojemności dla 100 tysięcy jednostek RU na sekundę w cenie 56,064 $ przez jeden rok (20% rabatem) lub 6.40 $ / godz. Zobacz cennik rezerwowanie pojemności [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Koszt przepływności (płatność za rzeczywiste użycie): 100 000 RU/s/100 * $0.008/godzinę * 8760 godzin w roku = 70,080 $ 

* Koszt przepustowości (za pomocą rezerwowanie pojemności) $70,080 z rabatem 20% = 56,064 $ 

Co skutecznie zakupione jest środki w wysokości 8 USD za godzinę dla 100 tysięcy jednostek żądań na sekundę przy użyciu ceny w regionach wschodnie stany USA, po cenie 6.40 $ / godz. Można tworzyć w dół od tę rezerwację przepustowości przedpłacony godzinowo za aprowizowaną przepływność w dowolnej globalnego regionie platformy Azure w odpowiednich cen listy regionalnych dla Twojej subskrypcji. W tym przykładzie, w których jest aprowizowane 50 tysięcy jednostek ru każdej w regionie wschodnie stany USA i Japonia Wschodnia, będzie można narysować 8,00 $ warte z aprowizowaną przepływność na godzinę i będzie rozliczana nadwyżkowe użycie w wysokości 0,50 USD na godzinę (lub 360 $/ miesiąc). 

|**Region**| **Cena za godzinę dla 100 jednostek RU/s**|**Jednostki (RU/s)**| **Naliczona opłata (co godzinę)**| **Naliczona opłata (miesięcznie)**|
|----|----|----|----|----|
|Wschodnie stany USA|$0.008 |50 K|$4|$2,880 |
|Japonia Wschodnia|$0.009 |50 K| $4.50 |$3,240 |
|||Płatność zgodnie z rzeczywistym użyciem|$8.50|$6120|
|Zakupiona pojemność zarezerwowana|$0.0064 (20% rabatu) |100 jednostek żądań na sekundę lub wstępnie nabyte miejsce w 8 USD |-$8|-$5,760 |
|Rachunek netto|||$0.50 |$360 |

## <a name="next-steps"></a>Następne kroki

Następnie możesz przejść do Dowiedz się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB z następujących artykułów:

* Dowiedz się więcej o [jak Cosmos DB w modelu cen to ekonomiczne dla klientów](total-cost-ownership.md)
* Dowiedz się więcej o [Optymalizacja na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [optymalizacji kosztów przepustowości](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji koszt odczyty i zapisy](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizacji kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)
