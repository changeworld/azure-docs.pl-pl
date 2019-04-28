---
title: Optymalizowanie kosztów przepływności w usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono, jak optymalizować koszty przepustowości dla danych przechowywanych w usłudze Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 280d389875d5ac951e0a846f3331ea727176b5e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929031"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Optymalizuj koszt aprowizowanej przepływności w usłudze Azure Cosmos DB

Oferując modelu aprowizowanej przepływności, usługa Azure Cosmos DB oferuje przewidywalną wydajność, w dowolnej skali. Rezerwowanie lub inicjowania obsługi administracyjnej przepływności wcześniejsze eliminuje "hałaśliwego sąsiada efektu" na wydajność. Określ dokładne zalecenia dotyczące ilości przepływności, które są potrzebne i Azure Cosmos DB gwarantuje skonfigurowanej przepływności, objęte umową SLA.

Można uruchomić przy minimalnej przepływności równej 400 jednostek RU na sekundę i skalować do dziesiątki milionów żądań na sekundę lub nawet dłużej. Każde żądanie spodoba się względem usługi kontenera usługi Azure Cosmos lub bazy danych, takich jak żądanie odczytu, zapisu żądania, żądania zapytania, procedury składowane mają odpowiednie koszt, który jest odejmowany od aprowizowanej przepływności. Jeśli aprowizacja 400 RU/s i wystawiać zapytanie, które koszty 40 jednostek RU, będzie można wydać 10 takich zapytań na sekundę. Każde żądanie, poza tym otrzyma limited szybkości i powinna ponowić żądanie. Jeśli używasz klienta sterowniki obsługują Logika ponawiania automatyczne.

Użytkownik może aprowizować przepływność mierzoną w bazach danych lub kontenery i poszczególnych strategii mogą pomóc Ci zaoszczędzić na kosztach w zależności od scenariusza.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optymalizowanie aprowizując przepływność na różnych poziomach

* Jeśli możesz aprowizować przepływność mierzoną w bazie danych, wszystkie kontenery, na przykład kolekcje/tabel/wykresów w ramach tej bazy danych mogą udostępniać przepływności na podstawie obciążenia. Przepływnością zarezerwowaną na poziomie bazy danych jest współużytkowana nierównomiernie, w zależności od obciążenia na określonej grupy kontenerów.

* Jeśli możesz aprowizować przepływność na kontenerze, przepływność jest gwarantowane dla tego kontenera, objęte umową SLA. Wybór klucza partycji logicznej ma kluczowe znaczenie podczas równomiernego rozłożenia obciążenia między partycjami logicznymi kontenera. Zobacz [Partitioning](partitioning-overview.md) i [skalowanie w poziomie](partition-data.md) artykuły, aby uzyskać więcej informacji.

Poniżej przedstawiono wskazówki, aby wybrać strategię aprowizowana przepływność:

**Należy rozważyć aprowizowania przepływności na bazie danych Azure Cosmos DB (zawierający zestaw kontenerów), jeśli**:

1. Ma kilka tuzinów kontenerów Azure Cosmos i chcesz udostępnić przepływność w niektóre lub wszystkie z nich. 

2. Podczas migrowania z jedną dzierżawą bazy danych, przeznaczony do działania na IaaS hostowanych maszyn wirtualnych lub w środowisku lokalnym, na przykład NoSQL lub relacyjnych baz danych do usługi Azure Cosmos DB. A jeśli masz wiele kolekcji/tabel/wykresów i nie chcesz wprowadzać żadnych zmian w modelu danych. Uwaga: może być konieczne naruszyć niektóre korzyści oferowanych przez usługę Azure Cosmos DB, jeśli model danych są one aktualizowane w przypadku migracji z lokalnej bazy danych. Zalecane jest, zawsze ponownego dostępu od modelu danych, aby maksymalnie wykorzystać pod względem wydajności, a także aby zoptymalizować koszty. 

3. Chcesz ochrony przed rozproszonymi nieplanowane skoków obciążeń na podstawie puli przepływności na poziomie bazy danych, poddane nieoczekiwany wzrost obciążenia. 

4. Zamiast przepływność określone ustawienie na poszczególnych kontenerów interesujące Cię wprowadzenie zagregowanej przepływności w zestawie kontenerów w bazie danych.

**Weź pod uwagę aprowizowania przepływności na pojedynczy kontener jeśli:**

1. Masz kilka kontenerów w usłudze Azure Cosmos. Ponieważ usługi Azure Cosmos DB jest niezależny od schematów, kontener może zawierać elementy, które mają heterogenicznych schematów i nie wymaga klientom tworzenie wielu typów kontenera, jeden dla każdej jednostki. Jest to zawsze sens opcją do rozważenia, gdy grupowanie oddzielne opcję 10-20 kontenerów w jednym kontenerze. Z 400 jednostek żądania minimalną dla kontenerów buforowanie wszystkie kontenery 10-20 do jednego może być bardziej opłacalne. 

2. Chcesz kontrolować przepustowość do określonego kontenera i Uzyskaj gwarantowaną przepływność na dany kontener objęte umową SLA.

**Należy wziąć pod uwagę w środowisku hybrydowym powyżej dwóch strategii:**

1. Jak wspomniano wcześniej, usługi Azure Cosmos DB umożliwia mieszanie i dopasowywanie powyższych dwóch strategii, dzięki czemu można teraz mieć pewne kontenerów w usłudze Azure Cosmos bazy danych, która może udostępniać przepływności, którego obsługę zainicjowano na bazie danych także niektóre kontenerów w ramach tej samej bazy danych , która dedykowanego ilości aprowizowanej przepływności. 

2. Można zastosować powyższych strategii, co pozwoli uzyskać konfiguracji hybrydowych, gdzie mają zarówno bazy danych poziomie aprowizowanej przepływności za pomocą kontenerów, niektóre używania dedykowanych przepływności.

Jak pokazano w poniższej tabeli, w zależności od wybranego interfejsu API, możesz zaprowizować przepływność na różnych stopniach szczegółowości.

|Interfejs API|Dla **udostępnionego** przepływności, skonfiguruj |Dla **dedykowanych** przepływności, skonfiguruj |
|----|----|----|
|Interfejs API SQL|Database (Baza danych)|Kontener|
|Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB|Database (Baza danych)|Collection|
|Interfejs API rozwiązania Cassandra|Przestrzeń kluczy|Tabela|
|Interfejs API języka Gremlin|Konto bazy danych|Graph|
|Interfejs API tabel|Konto bazy danych|Tabela|

Przez aprowizowania przepływności na różnych poziomach można zoptymalizować koszty, w oparciu o charakterystyki obciążenia. Jak wspomniano wcześniej, można programowo i w każde zwiększenie liczby czas lub zmniejszyć aprowizowanej przepływności dla obu poszczególnych kontenerów lub zbiorczo zestaw kontenerów. Przy użyciu elastycznego skalowania przepływności jako zmiany obciążenia, zapłacisz tylko za przepływność, który został skonfigurowany. Jeśli kontener lub zestaw kontenerów jest dystrybuowane w wielu regionach, następnie przepływności można skonfigurować w kontenerze, lub zestaw kontenerów jest gwarantowane mają być dostępne we wszystkich regionach.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optymalizowanie żądań ograniczanie szybkości

W przypadku obciążeń, które nie są wrażliwe na czas oczekiwania możesz aprowizować mniejszą przepustowość i Zezwalaj aplikacji na obsługiwać ograniczanie szybkości, gdy rzeczywista przepływność przekroczy aprowizowaną przepływność. Serwer będzie prewencyjnego kończy żądanie z RequestRateTooLarge (kod stanu HTTP 429) i zwracają `x-ms-retry-after-ms` nagłówek wskazujący ilość czasu w milisekundach, które użytkownik musi czekać przed ponowieniem próby wykonania żądania. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Logika ponawiania w zestawy SDK 

Niejawnie natywnych zestawów SDK (.NET/.NET Core, Java, Node.js i Python) catch tej odpowiedzi, przestrzegać określony serwer nagłówka retry-after i ponowić próbę żądania. Chyba, że Twoje konto jest uzyskiwany współbieżnie przez wielu klientów, następne ponowienie próby zakończy się pomyślnie.

Jeśli masz więcej niż jeden klient łącznie operacyjnego stale powyżej żądań zakończonych, domyślną liczbę ponownych prób aktualnie jest ustawiony do 9, może nie być wystarczające. W takim przypadku klient zgłasza `DocumentClientException` ze stanem kodu 429 do aplikacji. Domyślna liczba ponownych prób może zostać zmieniona przez ustawienie `RetryOptions` wystąpieniu ConnectionPolicy. Domyślnie DocumentClientException z kodem stanu 429 po skumulowany czas oczekiwania równej 30 sekund jest zwracany, jeśli żądanie będzie kontynuował pracę nad liczba żądań. Dzieje się tak nawet gdy aktualna liczba ponownych prób jest mniejsza niż liczba ponowień max, są to domyślne 9 lub wartości zdefiniowane przez użytkownika. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) jest ustawiony na 3, więc w tym przypadku jeśli operację żądania ma prędkość ograniczoną przez przekraczające zarezerwowanej przepływności dla kolekcji, operację żądania ponawia próbę trzykrotnie przed zgłaszanie wyjątek do aplikacji.  [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) jest ustawiony na 60, w tym przypadku jeśli zbiorczą ponawiania czeka czas w kilka sekund na pierwsze żądanie przekracza 60 sekund, jest wyjątek.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 

connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 

connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Strategii partycjonowania i koszty aprowizowana przepływność

Dobre strategii partycjonowania jest ważne zoptymalizować koszty w usłudze Azure Cosmos DB. Upewnij się, że istnieje nie niesymetryczność partycji, które są udostępniane za pośrednictwem metryk usługi storage. Upewnij się, że istnieje nie niesymetryczność przepływności dla partycji, który jest uwidaczniany za pomocą metryk przepływności. Upewnij się, że istnieje nie niesymetryczność na określoną partycję kluczy. Dominujący kluczy w magazynie są udostępniane za pośrednictwem metryki, ale klucz będzie zależny od wzorca dostępu do Twojej aplikacji. Najlepiej jest myśleć o kluczu partycji logicznej bezpośrednio. Stanowi dobry klucz partycji powinien mieć następujące właściwości:

* Wybór klucza partycji, który równomiernie rozdziela obciążenia wszystkich partycji i równomiernie wraz z upływem czasu. Innymi słowy nie powinny mieć pewne klucze, aby z większości danych i klucze o mniejszej lub danych. 

* Wybór klucza partycji, który umożliwia dostęp wzorców, aby być rozłożona równomiernie między partycjami logicznymi. Obciążenie jest względnie nawet przez wszystkie klucze. Innymi słowy większość obciążeń nie powinno koncentrować się na kilku określonych kluczy. 

* Wybór klucza partycji, który ma szeroki zakres wartości. 

Wygląda to w celu rozłożenia danych i działania w kontenerze w zestawie partycjami logicznymi tak, aby zasoby dla magazynu danych i przepływności mogą być rozproszone między partycjami logicznymi. Kandydaci do kluczy partycji może zawierać właściwości, które są często wyświetlane jako filtr zapytania. Zapytania można skutecznie kierować przez uwzględnienie klucza partycji w predykacie filtru. Dzięki tej strategii partycjonowania Optymalizowanie aprowizowanej przepływności będzie o wiele prostsze. 

### <a name="design-smaller-items-for-higher-throughput"></a>Projektowanie mniejszych elementów dla wyższej przepustowości 

Opłata za żądanie wyrażana lub koszt przetwarzania żądania danej operacji są bezpośrednio skorelowane rozmiar elementu. Operacje na elementach dużych koszt więcej niż operacji na elementach mniejsze. 

## <a name="data-access-patterns"></a>Wzorce dostępu do danych 

Zawsze jest dobrym rozwiązaniem do logicznego odseparowania danych w kategorie logiczne, w oparciu o częstotliwości dostępu do danych. Kategoryzacja jako gorąca, średni czy zimnych danych można dostosować zajmowanego w magazynie i wymaganej przepływności. W zależności od częstotliwości dostępu można umieścić dane w oddzielnych kontenerów (na przykład tabele, wykresy i kolekcji) i dostosowywać aprowizowanej przepływności je, aby dopasować do potrzeb danego segmentu danych. 

Ponadto jeśli używasz usługi Azure Cosmos DB, a wiadomo, nie są kierowane do wyszukiwania według wartości niektórych danych lub rzadko będą do nich dostęp, należy przechowywać skompresowany wartościami tych atrybutów. Przy użyciu tej metody, skracają miejsca do magazynowania, miejsca indeksu i aprowizowanej przepływności i spowodować obniżenie kosztów.

## <a name="optimize-by-changing-indexing-policy"></a>Optymalizowanie zmiana zasad indeksowania 

Domyślnie usługa Azure Cosmos DB automatycznie indeksuje dla każdej właściwości każdego rekordu. Ta wartość jest przeznaczona do ułatwić sobie projektowanie i zapewnić doskonałą wydajność w wielu różnych rodzajów zapytań ad hoc. W przypadku dużych rekordy z tysiącami właściwości ponoszenia kosztów przepustowości dla indeksowania wszystkich właściwości może nie być użyteczny, zwłaszcza, jeśli jest to tylko zapytań względem 10 lub 20 tych właściwości. Jak można uzyskać bliższe pobranie dojścia na określonego obciążenia, nasze wskazówki jest można dostrajanie indeks zasadach. Szczegółowe informacje o zasad indeksowania usługi Azure Cosmos DB można znaleźć [tutaj](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitorowanie zainicjowano obsługę administracyjną i używane przepływności 

Można monitorować, całkowita liczba jednostek ru zaprowizowanych, liczba żądań limited stawki, a także liczbę jednostek zarezerwowanych, wykorzystano w witrynie Azure portal. Na poniższej ilustracji przedstawiono przykład metryka użycia:

![Monitor jednostki żądań w witrynie Azure portal](./media/optimize-cost-throughput/monitoring.png)

Można również ustawić alerty, aby sprawdzić, jeśli liczba żądań limited współczynnik przekroczy określony próg. Zobacz [jak monitorować usługę Azure Cosmos DB](use-metrics.md) artykuł, aby uzyskać więcej informacji. Te alerty mogą wysłać wiadomość e-mail do administratorów konta lub wywołać niestandardowy element Webhook protokołu HTTP lub funkcji platformy Azure, aby automatycznie zwiększać aprowizowanej przepływności. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Elastycznie skalować przepływność i na żądanie 

Ponieważ opłaty są naliczane za aprowizowaną przepływność, dopasowanie aprowizowanej przepływności do Twoich potrzeb może pomóc uniknąć opłat za nieużywane przepływności. Aprowizowanej przepływności można skalować w górę lub dół w dowolnym momencie, zgodnie z potrzebami.  

* Monitorowanie użycia usługi (RUS) i współczynnik limited współczynnika żądań może spowodować ujawnienie konieczne zapewnienie aprowizowane w całym stała w ciągu dnia lub tygodnia. W nocy lub w weekend, może pojawić się mniej ruchu. Za pomocą witryny Azure portal lub usługi Azure Cosmos DB natywnych zestawów SDK lub interfejsu API REST, możesz skalować w dowolnym momencie aprowizowanej przepływności. Interfejs API REST danych usługi Azure Cosmos DB zapewnia programowe aktualizowanie poziomu wydajności Twoich kontenerów, dzięki czemu proste dostosowywać przepływność w kodzie, zależnie od godziny, dzień lub dzień tygodnia punktów końcowych. Operacja jest wykonywana bez żadnego przestoju i zwykle zaczyna obowiązywać w ciągu niecałej minuty. 

* Jednego z obszarów, powinny być skalowane przepływność wynosi po użytkownik pozyskiwania danych w usłudze Azure Cosmos DB, na przykład podczas migracji danych. Po zakończeniu migracji aprowizowanej przepływności można skalować w dół do obsługi rozwiązania stanie stabilności.  

* Należy pamiętać, że rozliczenia na poziomie szczegółowości jednej godziny, dzięki czemu nie będzie zapisać Twoich pieniędzy częściej niż co godzinę w danym momencie zmiany aprowizowanej przepływności.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Ustalenia przepływności potrzebne dla nowych obciążeń 

Aby określić aprowizowana przepływność dla nowych obciążeń, można użyć następujące czynności: 

1. Wykonaj początkową, nierównej wersji ewaluacyjnej przy użyciu capacity planner i dostosować swoje szacunki, za pomocą Eksploratora usługi Azure Cosmos w witrynie Azure portal. 

2. Zaleca się tworzenie kontenerów za pomocą wyższą przepływność niż oczekiwano, a następnie skalowanie w dół odpowiednio do potrzeb. 

3. Zaleca się skorzystanie z natywnych zestawów SDK usługi Azure Cosmos DB do korzystania z Automatyczne ponawianie prób, gdy współczynnik ograniczone żądania. Jeśli pracujesz na platformie, która nie jest obsługiwana i za pomocą interfejsu API REST usługi Cosmos DB, zaimplementować własną ponawiania zasad za pomocą `x-ms-retry-after-ms` nagłówka. 

4. Upewnij się, że kod aplikacji bez problemu zmieniała obsługuje przypadek, gdy wszystkie próby połączenia się nie powieść. 

5. Można skonfigurować alerty uzyskane z portalu Azure, aby otrzymywać powiadomienia dla szybkości. Można uruchomić z limitami zachowawcze np. 10 limited współczynnika żądań w ciągu ostatnich 15 minut, a przełącznik, aby bardziej eager reguły po ustalić rzeczywiste zużycie. Sporadyczne limitów szybkości są w dobrym stanie, pokazują, że odtwarzany z limitami ustawiono i który jest dokładnie co chcesz zrobić. 

6. Użyj opcji monitorowania, aby zrozumieć swoje wzorzec ruchu, więc należy wziąć pod uwagę potrzebę dynamicznego dostosowania aprowizowania przepływności za pośrednictwem dnia lub tygodnia. 

7. Monitorowanie usługi aprowizowane a wskaźnik wykorzystanych produktywności regularnie, aby upewnij się, że nie ma więcej niż wymagana liczba kontenerów i baz danych aprowizowano. Nieco za pośrednictwem aprowizowana przepływność jest sprawdzanie dobre bezpieczeństwa.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Najlepsze rozwiązania w celu zoptymalizowania elastycznie przepływności 

Poniższe kroki ułatwiają upewnij rozwiązania wysoce skalowalny i ekonomiczny, korzystając z usługi Azure Cosmos DB.  

1. Jeśli masz znacznie za pośrednictwem aprowizowanej przepływności kontenerów i baz danych, należy przejrzeć jednostek ru aprowizowanych Vs wykorzystanych (RUS) i dostosowywać obciążeń.  

2. Jedną z metod oszacowania zarezerwowanej przepływności wymaganej przez aplikację jest rejestrowanie żądania opłata za jednostki RU, związanych z uruchamianiem typowych operacji względem reprezentatywny kontenera usługi Azure Cosmos lub bazy danych używane przez aplikację i następnie należy oszacować liczbę operacji, które planujesz wykonać co sekundę. Pamiętaj zmierzyć i obejmują typowe zapytania i ich użycia, jak również. Aby dowiedzieć się, w jaki sposób programowo oszacować koszty RU zapytań lub za pomocą portalu, zobacz [optymalizacji kosztów zapytania](online-backup-and-restore.md). 

3. Inny sposób pobrania operacji i ich kosztów w (RUS) jest włączenie dzienników usługi Azure Monitor, które zapewni podział czasu trwania i operacji i opłata za żądanie wyrażana. Usługa Azure Cosmos DB zapewnia opłata za żądanie wyrażana dla każdej operacji, dzięki czemu każdy opłaty za operację może być przechowywana powrót po awarii z odpowiedzi i następnie używany na potrzeby analizy. 

4. Można elastycznie skalować aprowizowaną przepływność w górę i w dół na potrzeby dostosowania potrzeb obciążenia. 

5. Można dodawać i usuwać regionów skojarzonych z Twoim kontem usługi Azure Cosmos, zgodnie z potrzebami i kontrolowanie kosztów. 

6. Upewnij się, że masz równomiernego rozłożenia danych i obciążeń między partycjami logicznymi kontenerów. W przypadku partycji nierównomierny rozkład może to spowodować do aprowizowania większej liczby przepływność niż wartość, która jest wymagana. Po zidentyfikowaniu, że masz niesymetryczne dystrybucji, firma Microsoft zaleca, aby równomiernie Redystrybucja obciążenia w partycjach lub podziału danych. 

7. Jeśli masz wiele kontenerów i kontenery te nie wymagają umowy SLA, można użyć oferty opartej na bazie danych, w przypadkach, gdzie na przepływności kontenera nie są stosowane umowy SLA. Należy zidentyfikować, które mają zostać zmigrowane do poziomu przepływności bazy danych kontenery usługi Azure Cosmos oferty, a następnie ich migracja za pomocą rozwiązania na podstawie kanału informacyjnego zmian. 

8. Rozważ użycie "Cosmos DB bezpłatnej warstwy" (wersja bezpłatna przez rok), wypróbuj usługę Cosmos DB (maksymalnie trzy regiony) lub emulator usługi Cosmos DB do pobrania dla scenariuszy deweloperskich lub testowych. Korzystając z tych opcji dla deweloperów testu, można znacznie niższe koszty.  

9. Specyficznego dla obciążenia koszt optymalizacje — na przykład, można przeprowadzić dalsze zwiększenie odczyty rozmiar partii, równoważenia obciążenia w wielu regionach i cofnąć Duplikowanie danych, jeśli ma to zastosowanie.

10. Pojemność usługi Azure Cosmos DB, zarezerwowana możesz uzyskać znaczne zniżki do 65% przez trzy lata. Model rezerwowanie pojemności w usłudze Azure Cosmos DB jest zobowiązanie na potrzeby wraz z upływem czasu jednostek żądań. Rabaty są rozmieszczone warstwowo tak, aby większej liczby jednostek żądania, można używać w dłuższym okresie więcej zostanie rabat. Te rabaty zostaną zastosowane natychmiast. Wszelkie RUs powyżej udostępnione wartości są obciążani opłatami na kosztach niezarezerwowane pojemności. Zobacz [wydajności rezerwowej usługi Cosmos DB](cosmos-db-reserved-capacity.md)) Aby uzyskać więcej informacji. Należy rozważyć zakup rezerwowanie pojemności, aby dodatkowo obniżyć koszty aprowizowanej przepływności.  

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz przejść do Dowiedz się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB z następujących artykułów:

* Dowiedz się więcej o [Optymalizacja na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [informacje o rachunku Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji koszt odczyty i zapisy](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizacji kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)

