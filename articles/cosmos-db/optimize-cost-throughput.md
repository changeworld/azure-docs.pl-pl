---
title: Optymalizacja kosztów przepływności w usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zoptymalizować koszty przepływności dla danych przechowywanych w usłudze Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: c80ab4acd745717e2e68ae7d9dc818594ad1ce9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501472"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB

Oferując aprowisowany model przepływności usługa Azure Cosmos DB oferuje przewidywalną wydajność w dowolnej skali. Rezerwowanie lub inicjowanie obsługi administracyjnej przepływności z wyprzedzeniem eliminuje "efekt hałaśliwego sąsiada" na wydajność. Określ dokładną ilość przepływności, której potrzebujesz, a usługa Azure Cosmos DB gwarantuje skonfigurowaną przepływność, popartą przez usługę SLA.

Można rozpocząć od minimalnej przepływności 400 RU/s i skalować do dziesiątek milionów żądań na sekundę lub nawet więcej. Każde żądanie, które wystawiasz dla kontenera lub bazy danych usługi Azure Cosmos, takie jak żądanie odczytu, żądanie zapisu, żądanie kwerendy, procedury przechowywane mają odpowiedni koszt, który jest odejmowany od aprowizowanej przepływności. Jeśli aprowizujesz 400 ru/s i wydasz kwerendę, która kosztuje 40 ru, będzie można wydać 10 takich zapytań na sekundę. Każde żądanie poza tym otrzyma ograniczoną stawkę i należy ponowić próbę żądania. Jeśli używasz sterowników klienta, obsługują one logikę automatycznego ponawiania.

Przepływność można aprowizować dla baz danych lub kontenerów, a każda strategia może ułatwić oszczędzanie kosztów w zależności od scenariusza.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optymalizacja przez inicjowanie obsługi administracyjnej przepływności na różnych poziomach

* Jeśli aprowizujesz przepływność w bazie danych, wszystkie kontenery, na przykład kolekcje/tabele/wykresy w tej bazie danych mogą współużytkować przepływność na podstawie obciążenia. Przepływność zarezerwowana na poziomie bazy danych jest współużytkowana nierównomiernie, w zależności od obciążenia określonego zestawu kontenerów.

* Jeśli aprowizujesz przepływność w kontenerze, przepływność jest gwarantowana dla tego kontenera, wspierane przez umowy SLA. Wybór klucza partycji logicznej ma kluczowe znaczenie dla równomiernego rozkładu obciążenia na wszystkie partycje logiczne kontenera. Zobacz [partycjonowanie](partitioning-overview.md) i [skalowanie poziome](partition-data.md) artykuły, aby uzyskać więcej informacji.

Poniżej przedstawiono kilka wskazówek, aby zdecydować się na aprowizowaną strategię przepływności:

**Rozważ inicjowanie obsługi administracyjnej przepływności w bazie danych usługi Azure Cosmos (zawierającej zestaw kontenerów), jeśli:**

1. Masz kilkadziesiąt kontenerów usługi Azure Cosmos i chcesz udostępnić przepływność w niektórych lub wszystkich z nich. 

2. Przeprowadzasz migrację z bazy danych z jedną dzierżawą, przeznaczonej do uruchamiania na maszynach wirtualnych hostowanych przez usługę IaaS lub lokalnie, na przykład z bazy danych NoSQL lub relacyjnych do usługi Azure Cosmos DB. A jeśli masz wiele kolekcji /tabel / wykresów i nie chcesz wprowadzać żadnych zmian w modelu danych. Uwaga: może być wymagane złamanie zabezpieczeń niektóre korzyści oferowane przez usługę Azure Cosmos DB, jeśli nie aktualizujesz modelu danych podczas migracji z lokalnej bazy danych. Zaleca się, aby zawsze ponownie doakcjonować model danych, aby uzyskać jak najwięcej pod względem wydajności, a także zoptymalizować pod kątem kosztów. 

3. Chcesz wchłonąć nieplanowane skoki obciążeń na podstawie puli przepływności na poziomie bazy danych poddane nieoczekiwany skok obciążenia. 

4. Zamiast ustawiania określonej przepływności dla poszczególnych kontenerów, zależy Ci na uzyskaniu agregującej przepływności w zestawie kontenerów w bazie danych.

**Należy rozważyć inicjowanie obsługi administracyjnej przepływności w kontenerze poszczególnych, jeśli:**

1. Masz kilka kontenerów usługi Azure Cosmos. Ponieważ usługa Azure Cosmos DB jest niezależna od schematu, kontener może zawierać elementy, które mają heterogeniczne schematy i nie wymaga od klientów tworzenia wielu typów kontenerów, po jednym dla każdej jednostki. Zawsze jest możliwość rozważenia, czy grupowanie oddzielnych powiedzieć 10-20 kontenerów w jednym pojemniku ma sens. Przy minimalnym poziomie 400 zł dla kontenerów, połączenie wszystkich 10-20 kontenerów w jeden może być bardziej opłacalne. 

2. Chcesz kontrolować przepływność w określonym kontenerze i uzyskać gwarantowaną przepływność w danym kontenerze wspieranym przez umowy SLA.

**Należy wziąć pod uwagę hybrydę powyższych dwóch strategii:**

1. Jak wspomniano wcześniej, usługa Azure Cosmos DB umożliwia mieszanie i dopasowywać powyższe dwie strategie, dzięki czemu można teraz mieć niektóre kontenery w bazie danych usługi Azure Cosmos, które mogą współużytkować przepływność aprowizowaną w bazie danych, a także niektóre kontenery w tej samej bazie danych , które mogą mieć dedykowane ilości aprowizowanej przepustowości. 

2. Powyższe strategie można zastosować, aby wymyślić konfigurację hybrydową, gdzie masz zarówno poziom bazy danych aprowizowana przepływność z niektórych kontenerów o dedykowanej przepływności.

Jak pokazano w poniższej tabeli, w zależności od wyboru interfejsu API, można aprowizować przepływność w różnych ziarnistości.

|interfejs API|W przypadku przepływności **współużytkowanej** skonfiguruj |Aby uzyskać **dedykowaną** przepustowość, |
|----|----|----|
|Interfejs API SQL|baza danych|Kontener|
|Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB|baza danych|Collection|
|Interfejs API rozwiązania Cassandra|Przestrzeń kluczy|Tabela|
|Interfejs API języka Gremlin|Konto bazy danych|Graph|
|Interfejs API tabel|Konto bazy danych|Tabela|

Przez inicjowanie obsługi administracyjnej przepływności na różnych poziomach, można zoptymalizować koszty na podstawie cech obciążenia. Jak wspomniano wcześniej, można programowo i w dowolnym momencie zwiększyć lub zmniejszyć aprowizowaną przepływność dla poszczególnych kontenerów lub zbiorczo w zestawie kontenerów. Elastycznie skalując przepływność w miarę zmian obciążenia, płacisz tylko za skonfigurowana przepływność. Jeśli kontener lub zestaw kontenerów jest dystrybuowany w wielu regionach, przepływność skonfigurowana w kontenerze lub zestawie kontenerów jest gwarantowana do udostępnienia we wszystkich regionach.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optymalizacja dzięki ograniczeniu szybkości twoich żądań

Dla obciążeń, które nie są wrażliwe na opóźnienia, można aprowizować mniejszą przepływność i pozwolić aplikacji do obsługi ograniczania szybkości, gdy rzeczywista przepływność przekracza aprowizowaną przepływność. Serwer będzie prewunkowo zakończyć `RequestRateTooLarge` żądanie z (kod stanu HTTP 429) i zwrócić `x-ms-retry-after-ms` nagłówek wskazujący czas, w milisekundach, że użytkownik musi czekać przed ponowieniem próby żądania. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Logika ponawiania próby w skusie SDK 

Natywne pliki SDK (.NET/.NET Core, Java, Node.js i Python) niejawnie przechwytują tę odpowiedź, przestrzegają nagłówka ponawiania po próbie określonej przez serwer i ponów próbę żądania. Jeśli konto nie jest dostępne jednocześnie przez wielu klientów, następna ponowna próby zakończy się pomyślnie.

Jeśli masz więcej niż jednego klienta łącznie działającego konsekwentnie powyżej szybkości żądania, domyślna liczba ponownych prób, która jest obecnie ustawiona na 9, może nie być wystarczająca. W takich przypadkach klient zgłasza `RequestRateTooLargeException` do aplikacji kod stanu 429 z kodem stanu. Domyślną liczbę ponownych prób można `RetryOptions` zmienić, ustawiając w wystąpieniu ConnectionPolicy. Domyślnie `RequestRateTooLargeException` z kodem stanu 429 jest zwracany po skumulowanym czasie oczekiwania 30 sekund, jeśli żądanie nadal działa powyżej stawki żądania. Dzieje się tak nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, czy to domyślna wartość 9, czy wartość zdefiniowana przez użytkownika. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) jest ustawiona na 3, więc w tym przypadku, jeśli operacja żądania jest ograniczona przez przekroczenie przepływności zarezerwowanej dla kontenera, operacja żądania po raz pierwszy po raz pierwszy przed zgłoszeniem wyjątku do aplikacji. [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) jest ustawiona na 60, więc w tym przypadku, jeśli skumulowany czas oczekiwania ponawiania próby w sekundach od pierwszego żądania przekracza 60 sekund, wyjątek.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Strategia partycjonowania i koszty aprowizowana przepływność

Dobra strategia partycjonowania jest ważne, aby zoptymalizować koszty w usłudze Azure Cosmos DB. Upewnij się, że nie ma pochylenia partycji, które są udostępniane za pośrednictwem metryk magazynu. Upewnij się, że nie ma pochylenia przepływności dla partycji, która jest narażona na dane przepływności. Upewnij się, że nie ma pochylenia w kierunku określonych kluczy partycji. Dominujące klucze w magazynie są udostępniane za pośrednictwem metryki, ale klucz będzie zależeć od wzorca dostępu do aplikacji. Najlepiej jest myśleć o prawym logicznym kluczu partycji. Oczekuje się, że dobry klucz partycji będzie miał następujące cechy:

* Wybierz klucz partycji, który rozkłada obciążenie równomiernie na wszystkie partycje i równomiernie w czasie. Innymi słowy, nie należy mieć kilka kluczy do większości danych i niektóre klucze z mniej lub bez danych. 

* Wybierz klucz partycji, który umożliwia wzorce dostępu, które mają być równomiernie rozłożone na partycje logiczne. Obciążenie jest rozsądnie nawet we wszystkich kluczach. Innymi słowy większość obciążenia nie powinny koncentrować się na kilku określonych kluczy. 

* Wybierz klucz partycji, który ma szeroki zakres wartości. 

Podstawową ideą jest rozłożenie danych i działania w kontenerze na zestaw partycji logicznych, dzięki czemu zasoby do przechowywania danych i przepływności mogą być dystrybuowane między partycjami logicznymi. Kandydaci do kluczy partycji może zawierać właściwości, które są często wyświetlane jako filtr w zapytaniach. Zapytania mogą być skutecznie kierowane przez dołączenie klucza partycji w predykacie filtru. Przy takiej strategii partycjonowania optymalizacja aprowizowanej przepływności będzie o wiele łatwiejsze. 

### <a name="design-smaller-items-for-higher-throughput"></a>Projektowanie mniejszych elementów w celu zwiększenia przepustowości 

Opłata za żądanie lub koszt przetwarzania żądania danej operacji jest bezpośrednio skorelowany z rozmiarem towaru. Operacje na dużych towarach będą kosztować więcej niż operacje na mniejszych towarach. 

## <a name="data-access-patterns"></a>Wzorce dostępu do danych 

Zawsze dobrą praktyką jest logiczne rozdzielenie danych na kategorie logiczne na podstawie tego, jak często uzyskujesz dostęp do danych. Kategoryzując je jako gorące, średnie lub zimne dane, można dostosować zużyte magazyny i wymaganą przepustowość. W zależności od częstotliwości dostępu można umieścić dane w oddzielnych kontenerach (na przykład tabele, wykresy i kolekcje) i dostosować aprowizowaną przepływność na nich, aby dostosować do potrzeb tego segmentu danych. 

Ponadto jeśli używasz usługi Azure Cosmos DB i wiesz, że nie zamierzasz wyszukiwać według określonych wartości danych lub rzadko będzie do nich uzyskiwać dostęp, należy przechowywać skompresowane wartości tych atrybutów. Dzięki tej metodzie można zaoszczędzić miejsce na dysku, miejsce indeksu i aprowizowaną przepływność i spowodować niższe koszty.

## <a name="optimize-by-changing-indexing-policy"></a>Optymalizacja przez zmianę zasad indeksowania 

Domyślnie usługa Azure Cosmos DB automatycznie indeksuje każdą właściwość każdego rekordu. Ma to na celu ułatwienie rozwoju i zapewnienie doskonałej wydajności w wielu różnych typach zapytań ad hoc. Jeśli masz duże rekordy z tysiącami właściwości, płacenie kosztu przepływności do indeksowania każdej właściwości może nie być przydatne, zwłaszcza jeśli kwerendy tylko względem 10 lub 20 z tych właściwości. Jak zbliżyć się do uzyskania uchwyt na określonego obciążenia, nasze wskazówki jest dostrojenie zasad indeksu. Szczegółowe informacje na temat zasad indeksowania usługi Azure Cosmos DB można znaleźć [tutaj](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitorowanie aprowizowanej i zużytej przepływności 

Można monitorować całkowitą liczbę aprowizowanych obiektów ru, liczbę żądań o ograniczonej szybkości, a także liczbę procesorów RU, które zostały zużyte w witrynie Azure portal. Na poniższej ilustracji przedstawiono przykładową metrykę użycia:

![Monitorowanie jednostek żądań w witrynie Azure portal](./media/optimize-cost-throughput/monitoring.png)

Można również ustawić alerty, aby sprawdzić, czy liczba żądań o ograniczonej szybkości przekracza określony próg. Zobacz [Jak monitorować artykuł usługi Azure Cosmos DB, aby](use-metrics.md) uzyskać więcej informacji. Te alerty mogą wysyłać wiadomości e-mail do administratorów kont lub wywoływać niestandardowy element webhook HTTP lub funkcję platformy Azure, aby automatycznie zwiększyć aprowizowaną przepływność. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Elastyczne skalowanie przepływności i na żądanie 

Ponieważ są rozliczane za aprowizowaną przepływność, dopasowanie aprowizowanej przepływności do potrzeb może pomóc uniknąć opłat za nieużywane przepływności. W razie potrzeby można skalować aprowizowaną przepływność w górę lub w dół. Jeśli twoje potrzeby dotyczące przepływności są bardzo przewidywalne, można użyć usługi Azure Functions i użyć wyzwalacza czasomierza, aby [zwiększyć lub zmniejszyć przepływność zgodnie z harmonogramem.](scale-on-schedule.md) 

* Monitorowanie zużycia obiektów rUs i stosunek żądań o ograniczonej szybkości może ujawnić, że nie trzeba przechowywać aprowizacji przez cały stały przez cały dzień lub tydzień. Możesz otrzymać mniejszy ruch w nocy lub w weekendy. Za pomocą witryny Azure portal lub azure cosmos DB natywnych zestawów SDK lub REST API, można skalować aprowizowaną przepływność w dowolnym momencie. Interfejs API REST usługi Azure Cosmos DB zapewnia punkty końcowe do programowej aktualizacji poziomu wydajności kontenerów, dzięki czemu można łatwo dostosować przepływność z kodu w zależności od porę dnia lub dnia tygodnia. Operacja jest wykonywana bez żadnych przestojów i zazwyczaj staje się skuteczna w mniej niż minutę. 

* Jednym z obszarów, które należy skalować przepływność jest podczas pozyskiwania danych do usługi Azure Cosmos DB, na przykład podczas migracji danych. Po zakończeniu migracji można skalować aprowizowaną przepływność w dół, aby obsłużyć stan stały rozwiązania.  

* Pamiętaj, że rozliczenia są ziarniste w ciągu jednej godziny, więc nie zaoszczędzisz pieniędzy, jeśli zmienisz aprowizowaną przepustowość częściej niż jedną godzinę naraz.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Określanie przepływności potrzebnej dla nowego obciążenia 

Aby określić aprowizowaną przepływność dla nowego obciążenia, można wykonać następujące kroki: 

1. Wykonaj wstępną, przybliżone oceny przy użyciu planowania pojemności i dostosuj swoje szacunki za pomocą Eksploratora usługi Azure Cosmos w witrynie Azure portal. 

2. Zaleca się utworzenie kontenerów o wyższej przepływności niż oczekiwano, a następnie skalowanie w dół w razie potrzeby. 

3. Zaleca się użycie jednego z natywnych zestawów SDK usługi Azure Cosmos DB, aby korzystać z automatycznych ponownych prób, gdy żądania są ograniczone. Jeśli pracujesz na platformie, która nie jest obsługiwana i używasz interfejsu API REST usługi `x-ms-retry-after-ms` Cosmos DB, zaimplementuj własne zasady ponawiania prób przy użyciu nagłówka. 

4. Upewnij się, że kod aplikacji bezpiecznie obsługuje przypadku, gdy wszystkie ponownych prób zakończyć się niepowodzeniem. 

5. Alerty można skonfigurować z witryny Azure Portal, aby otrzymywać powiadomienia o ograniczaniu szybkości. Możesz zacząć od konserwatywnych limitów, takich jak 10 żądań o ograniczonej szybkości w ciągu ostatnich 15 minut i przełączyć się na bardziej chętne reguły, gdy dowiesz się o rzeczywistą konsumpcję. Sporadyczne limity stawek są w porządku, pokazują, że grasz z ustawionymi limitami i to jest dokładnie to, co chcesz zrobić. 

6. Użyj monitorowania, aby zrozumieć wzorzec ruchu, dzięki czemu można rozważyć potrzebę dynamicznego dostosowywania obsługi administracyjnej przepływności w ciągu dnia lub tygodnia. 

7. Monitoruj aprowizowaną a zużytą przepływność ratio regularnie, aby upewnić się, że nie zostały zainicjowane więcej niż wymagana liczba kontenerów i baz danych. Posiadanie nieco ponad aprowizowaną przepustowość jest dobrą kontrolą bezpieczeństwa.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Najważniejsze wskazówki dotyczące optymalizacji aprowizowanej przepływności 

Poniższe kroki pomagają w osiągnięciu wysoce skalowalnych i opłacalnych rozwiązań w przypadku korzystania z usługi Azure Cosmos DB.  

1. Jeśli masz znacznie ponad aprowizowaną przepływność między kontenerami i bazami danych, należy przejrzeć procesory administracyjne aprowizowanego vs używane procesory RU i dostosować obciążenia.  

2. Jedną z metod szacowania ilości zarezerwowanej przepływności wymaganej przez aplikację jest rejestrowanie opłaty RU jednostki żądania skojarzonej z uruchamianiem typowych operacji względem reprezentatywnego kontenera lub bazy danych usługi Azure Cosmos używanej przez aplikację i następnie oszacuj liczbę operacji, które można przewidzieć do wykonania co sekundę. Pamiętaj, aby zmierzyć i dołączyć typowe zapytania i ich użycie, jak również. Aby dowiedzieć się, jak programowo oszacować koszty zapytań ru lub za pomocą portalu, zobacz [Optymalizacja kosztu kwerend](../synapse-analytics/sql-data-warehouse/backup-and-restore.md). 

3. Innym sposobem, aby uzyskać operacje i ich koszty w usłudze RUs jest włączenie dzienników usługi Azure Monitor, który zapewni podział operacji/czasu trwania i opłaty za żądanie. Usługa Azure Cosmos DB zapewnia opłatę za żądanie dla każdej operacji, dzięki czemu każda opłata za operację może być przechowywana z powrotem z odpowiedzi, a następnie używana do analizy. 

4. Można elastycznie skalować w górę iw dół aprowizowaną przepływność, zgodnie z potrzebami obciążenia. 

5. Można dodawać i usuwać regiony skojarzone z kontem usługi Azure Cosmos zgodnie z potrzebami i kontrolować koszty. 

6. Upewnij się, że masz równomierny rozkład danych i obciążeń między partycjami logicznymi kontenerów. Jeśli masz nierównomierny rozkład partycji, może to spowodować aprowizowanie większej ilości przepływności niż wartość, która jest potrzebna. Jeśli zidentyfikujesz, że masz pochylony dystrybucji, zaleca się redystrybucji obciążenia równomiernie między partycjami lub ponowne partycjonowanie danych. 

7. Jeśli masz wiele kontenerów i te kontenery nie wymagają instalacji SLA, można użyć oferty opartej na bazie danych w przypadkach, gdy instaly SLA przepływności dla kontenera nie mają zastosowania. Należy określić, które kontenery usługi Azure Cosmos, które mają zostać przeniesione do oferty przepływności na poziomie bazy danych, a następnie migracji ich przy użyciu rozwiązania opartego na pliku danych. 

8. Rozważ użycie "Cosmos DB Free Tier" (bezpłatnie przez rok), Wypróbuj usługa Cosmos DB (maksymalnie trzy regiony) lub emulator usługi Cosmos DB do pobrania dla scenariuszy deweloperskich/testowych. Korzystając z tych opcji dla test-dev, można znacznie obniżyć koszty.  

9. Można dodatkowo wykonywać optymalizacje kosztów specyficzne dla obciążenia — na przykład zwiększenie rozmiaru partii, równoważenie obciążenia odczytuje w wielu regionach i de-duplikowanie danych, jeśli ma to zastosowanie.

10. Dzięki pojemności zarezerwowanej usługi Azure Cosmos DB możesz uzyskać znaczne rabaty dla maksymalnie 65% przez trzy lata. Model zarezerwowanej pojemności usługi Azure Cosmos DB jest zobowiązaniem początkowym w zakresie jednostek żądań potrzebnych w czasie. Rabaty są warstwowe w taki sposób, że im więcej jednostek żądania używasz przez dłuższy okres, tym większy będzie rabat. Rabaty te są stosowane natychmiast. Wszystkie procesory RU używane powyżej aprowizowanych wartości są naliczane na podstawie niezarezerwowanego kosztu pojemności. Zobacz [Cosmos DB zarezerwowanej pojemności](cosmos-db-reserved-capacity.md)), aby uzyskać więcej informacji. Rozważ zakup pojemności zarezerwowanej, aby jeszcze bardziej obniżyć koszty aprowizowanej przepływności.  

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB, aby uzyskać następujące artykuły:

* Dowiedz się więcej o [optymalizacji pod kątem rozwoju i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [opisie rachunku za usługę Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji kosztów odczytów i zapisów](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizowaniu kosztów wieloregionowych kont usługi Azure Cosmos](optimize-cost-regions.md)

