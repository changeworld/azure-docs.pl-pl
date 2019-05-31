---
title: Usługa Azure porady dotyczące wydajności usługi Cosmos DB dla języka Java
description: Dowiedz się więcej opcji konfiguracji klienta w celu poprawy wydajności bazy danych Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 2ce8c0b369cd59ac61279fe3c7acd2cdecfc007c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225603"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Porady dotyczące wydajności usługi Azure Cosmos DB i Java

> [!div class="op_single_selector"]
> * [Java (asynchroniczny)](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Usługa Azure Cosmos DB jest szybka i elastyczna rozproszonej bazy danych, która miało miejsce płynne skalowanie dzięki gwarancji opóźnień i przepływności. Nie trzeba wprowadzić zmiany architektury głównych lub pisania złożonego kodu do skalowania bazy danych za pomocą usługi Azure Cosmos DB. Skalowanie w górę i w dół jest łatwe jak tworzenie jednego wywołania interfejsu API. Aby dowiedzieć się więcej, zobacz [sposób aprowizowania przepływności kontenerów](how-to-provision-container-throughput.md) lub [sposób aprowizowania przepływności bazy danych](how-to-provision-database-throughput.md). Ponieważ usługi Azure Cosmos DB jest dostępna za pośrednictwem wywołań sieci istnieją optymalizacje po stronie klienta, które można wprowadzić do osiągnięcia maksymalnej wydajności, korzystając z [zestawu SDK Java usługi SQL](documentdb-sdk-java.md).

Dlatego jeśli "jak mogę poprawić wydajność mojej bazy danych?" należy wziąć pod uwagę następujące opcje:

## <a name="networking"></a>Networking
<a id="direct-connection"></a>

1. **Tryb połączenia: Użyj DirectHttps**

    Jak klient nawiąże połączenie do usługi Azure Cosmos DB ma istotny wpływ na wydajność, szczególnie w zakresie odczytaną opóźnienie po stronie klienta. Istnieje jedna konfiguracja klucza ustawienie dostępne do konfigurowania klienta [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) — [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode).  Dwa ConnectionModes dostępne są następujące:

   1. [Brama (ustawienie domyślne)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      Tryb bramy jest obsługiwane na wszystkich platformach zestawu SDK i jest domyślnie skonfigurowany.  Jeśli aplikacja działa w sieci firmowej za pomocą ograniczeń zapory strict, brama jest najlepszym wyborem, ponieważ używa standardowego portu HTTPS i jeden punkt końcowy. Kosztem wydajności, jest tryb bramy obejmuje przeskok dodatkowe sieci, za każdym razem, gdy danych jest odczytywanych lub zapisywanych do usługi Azure Cosmos DB. W związku z tym trybu DirectHttps oferuje lepszą wydajność, ze względu na mniejszą liczbę przeskoków sieciowych. 

      Zestaw SDK Java używa protokołu HTTPS jako protokołu transportowego. Protokół HTTPS używa protokołu SSL dla początkowego uwierzytelniania i szyfrowania ruchu sieciowego. Podczas korzystania z zestawu SDK Java, musi być otwarte tylko portu HTTPS 443. 

      Podczas tworzenia wystąpienia DocumentClient z parametr ConnectionPolicy jest konfigurowana ConnectionMode. 

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      ![Ilustracja zasad połączenia usługi Azure Cosmos DB](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **W ten sposób rozmieszczać klientów, w tym samym regionie platformy Azure dla wydajności**

    Jeśli to możliwe, należy umieścić wszystkie aplikacje podczas wywoływania usługi Azure Cosmos DB w tym samym regionie co baza danych usługi Azure Cosmos DB. Przybliżony porównanie wywołań do usługi Azure Cosmos DB w ramach tego samego regionu zakończyć w ciągu 1 – 2 ms, ale opóźnienie między zachodnim i wschodnim wybrzeżem Stanów Zjednoczonych wynosi > 50 ms. Ten czas oczekiwania prawdopodobnie zależy do innego żądania trasy żądania przekazywanego z klienta do granicy centrum danych platformy Azure. Najniższe możliwe opóźnienie jest osiągane poprzez zapewnienie, że aplikacja wywołująca znajduje się w obrębie tego samego regionu platformy Azure aprowizowane punktu końcowego usługi Azure Cosmos DB. Aby uzyskać listę dostępnych regionów, zobacz [regionów platformy Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustracja zasad połączenia usługi Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Użycie zestawu SDK
1. **Instalowanie najnowszych zestawu SDK**

    Zestawów SDK usługi Azure Cosmos DB są ciągle ulepszane w celu zapewnienia najlepszej wydajności. Zobacz [zestawu SDK usługi Azure Cosmos DB](documentdb-sdk-java.md) strony do określenia najnowszej zestawu SDK i przejrzyj ulepszenia.
2. **Okres istnienia aplikacji za pomocą pojedynczego wystąpienia klienta usługi Azure Cosmos DB**

    Każdy [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) wystąpienie jest metodą o bezpiecznych wątkach i wykonuje połączenie efektywne zarządzanie i buforowanie adresów, podczas pracy w trybie bezpośredniego. Aby zezwolić na połączenia skutecznego zarządzania i zwiększając wydajność przez DocumentClient, zaleca się używać jednego wystąpienia DocumentClient dla domeny AppDomain dla cyklu życia aplikacji.

   <a id="max-connection"></a>
3. **Zwiększ MaxPoolSize na każdym hoście podczas korzystania z trybu bramy**

    Usługa Azure Cosmos DB żądania są wykonywane za pośrednictwem protokołu HTTPS REST podczas korzystania z trybu bramy i są poddawane domyślny limit połączeń dla hosta lub adres IP. Konieczne może być równa MaxPoolSize wyższa wartość (200-1000) tak, aby biblioteki klienta mogą wykorzystywać wiele jednoczesnych połączeń z usługą Azure Cosmos DB. W zestawie SDK języka Java, wartością domyślną dla [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) wynosi 100. Użyj [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) Aby zmienić wartość.

4. **Dostosowywanie zapytania równolegle do kolekcji podzielonych na partycje**

    Usługa Azure Cosmos DB SQL zestawu Java SDK w wersji 1.9.0 i nowszych zapytania równoległe pomocy technicznej, które umożliwiają kwerenda dotycząca kolekcji partycjonowanej równolegle. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) związanych z pracą z zestawami SDK. Zapytania równoległe są przeznaczone do kwerendy opóźnienia i przepływności starają się ich odpowiedników szeregowe.

    () ***dostrajania setMaxDegreeOfParallelism\:***  równoległe zapytania pracy, badając równocześnie wiele partycji. Jednak dane z poszczególnych kolekcji partycjonowanej pobieranych szeregowo w odniesieniu do zapytania. Tak, użyj [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) można ustawić liczby partycji ma maksymalną prawdopodobieństwo osiągnięcia najbardziej wydajne zapytania, pod warunkiem inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji setMaxDegreeOfParallelism umożliwia ustawianie dużą liczbą, a system wybiera co najmniej (liczba partycji, dane wejściowe podane przez użytkownika), jako maksymalny stopień równoległości. 

    Należy pamiętać, zapytania równoległe generuje najważniejsze korzyści, jeśli danych jest równomiernie rozłożona na wszystkie partycje w odniesieniu do zapytania. Jeśli kolekcji partycjonowanej jest podzielona na partycje tak, wszystkie lub większość danych zwróconych przez kwerendę koncentrują się w kilku partycji (jedną partycję w najgorszym przypadku), a następnie wykonywania zapytania, czy bottlenecked przez te partycje.

    (b) ***dostrajania setMaxBufferedItemCount\:***  zapytanie równoległe zaprojektowano w celu wstępnego pobierania wyników, podczas gdy bieżącej partii wyników jest przetwarzany przez klienta. Pobierania pomaga w ogólnej poprawy opóźnienie kwerendy. setMaxBufferedItemCount ogranicza liczbę wstępnie pobrano wyniki. Ustawiając [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) oczekiwana liczba wyników zwróconych (lub większa liczba zostanie podana), umożliwia to zapytanie, aby otrzymywać maksymalnych korzyści z pobierania z wyprzedzeniem.

    Pobierania z wyprzedzeniem działa tak samo niezależnie od tego, MaxDegreeOfParallelism i istnieje pojedynczy bufor dla danych ze wszystkich partycji.  

5. **Implementowanie wycofywania w odstępach czasu getRetryAfterInMilliseconds**

    Podczas testowania wydajności, należy zwiększyć obciążenie do momentu ograniczeni mała liczba żądań. Jeśli jest dławiona, aplikacja kliencka powinna wycofywania na ograniczenie przepustowości przez dany interwał ponawiania określony serwer. Uwzględnienie wycofywania daje pewność, możesz wydać skraca czas oczekiwania między ponownymi próbami. Obsługa zasad ponawiania jest uwzględniona w wersji 1.8.0 lub nowszym z [zestawu Java SDK](documentdb-sdk-java.md). Aby uzyskać więcej informacji, zobacz [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Skalowanie w poziomie obciążenia klientami**

    Jeśli testujesz na poziomach wysoką przepływność (> 50 000 jednostek RU/s), aplikacja kliencka może stać się wąskim gardłem ze względu na maszynie są takie same się na użycie procesora CPU lub sieci. Jeśli przekroczysz ten punkt, może nadal wypychania dalsze za pomocą konta usługi Azure Cosmos DB skalowanie aplikacji klienckich na wielu serwerach.

7. **Użyj nazwy na podstawie adresów**

    Użyj adresowania opartego na nazwie, w których łącza mają format `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, zamiast SelfLinks (\_samodzielnie), mają format, którego `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` Aby uniknąć pobierania identyfikatory zasobów programu wszystkie zasoby, które są używane do utworzenia łącza. Ponadto jako tych zasobów, Pobierz odtworzone (prawdopodobnie o takiej samej nazwie), buforowania te może nie pomóc.

   <a id="tune-page-size"></a>
8. **Dostosuj rozmiar strony dla źródeł danych zapytania/odczytu w celu zapewnienia lepszej wydajności**

    Podczas wykonywania masowego odczytu dokumentów za pomocą odczytu zestawienia (na przykład [readDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) lub podczas wystawiania zapytania SQL, wyniki są zwracane w sposób segmentu, jeśli zestaw wyników jest za duży. Domyślnie wyniki są zwracane we fragmentach po 100 elementów lub 1 MB, jednego z tych limitów zostanie osiągnięty jako pierwszy.

    Aby zmniejszyć liczbę rund sieci, wymagany do pobrania wszystkich odpowiednich wyników, można zwiększyć, używając rozmiaru strony [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) nagłówek żądania do 1000. W przypadkach, w których należy wyświetlić tylko kilka wyników, na przykład, jeśli użytkownik interfejsu lub aplikacji interfejsu API zwróci tylko 10 powoduje przez czas, można także zmniejszyć rozmiar strony do 10, aby ograniczyć przepustowość używana dla odczytów i zapytań.

    Można również ustawić przy użyciu rozmiaru strony [metoda setPageSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize).

## <a name="indexing-policy"></a>Zasady indeksowania
 
1. **Wyklucz ścieżki nieużywane indeksowania do szybszego zapisu**

    Zasady indeksowania danych usługi Azure Cosmos DB pozwala określić które ścieżki dokumentu do dołączania lub wykluczania z indeksowania dzięki wykorzystaniu indeksowania ścieżki ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) i [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)). Użycie indeksowania ścieżki oferują zapisu ulepszoną wydajność i niższe magazyn indeksów dla scenariuszy, w których wzorców zapytań są znane wcześniej, ponieważ koszty indeksowania bezpośrednio skorelowanych liczby unikatowych ścieżek indeksowane.  Na przykład poniższy kod pokazuje, jak (zwany również wykluczyć całą sekcję dokumentów poddrzewo) z pomocą indeksowaniem "*" symboli wieloznacznych.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Aby uzyskać więcej informacji, zobacz [zasady indeksowania usługi Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Przepływność
<a id="measure-rus"></a>

1. **Pomiar i dostrajania dla żądania niższe jednostek na sekundę użycia**

    Usługa Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, takich jak relacyjne i hierarchiczne zapytania z funkcji UDF, procedury składowane i wyzwalacze. wszystkie na dokumentach w obrębie kolekcji bazy danych. Koszt związany z każdą z tych operacji zależy od procesora CPU, we/wy i pamięci wymaganej do ukończenia tej operacji. Zamiast myśleć o zasobach i zarządzaniu nimi sprzętu można traktować jednostek żądań (RU) jako pojedynczej mierze zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługiwania żądania aplikacji.

    Przepływność jest przygotowany na podstawie liczby [jednostek żądania](request-units.md) ustawić dla każdego kontenera. Użycie jednostek żądania zostanie ocenione jako wskaźnik na sekundę. Aplikacje, które wykraczają stawki za jednostkę elastycznie żądania dla ich kontenera są ograniczone, aż współczynnik nie spadnie poniżej poziomu aprowizowaną dla kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność przez udostępnienie dodatkowych jednostek żądań. 

    Złożoność zapytania ma wpływ na liczbę jednostek żądania są używane dla operacji. Liczba predykatów, rodzaj predykaty, liczba funkcji zdefiniowanych przez użytkownika oraz rozmiar źródłowy zestaw danych, wszystkie mają wpływ na koszt operacje zapytań.

    Do mierzenia obciążenie związane z każdej operacji (Tworzenie, aktualizowanie lub usuwanie), sprawdzić [x-ms żądanie — opłata](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) nagłówka (lub równoważne właściwość RequestCharge [ResourceResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) lub [FeedResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse) do mierzenia liczby jednostek żądania, używanych przez te operacje.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Opłata za żądanie wyrażana zwrócony w nagłówku to jest część aprowizowanej przepływności. Na przykład jeśli masz 2000 jednostek RU/s jest obsługiwana, a jeśli poprzednie zapytanie zwraca 1KB 1000 dokumentów, koszty działania wynosi 1000. Jako takie w ciągu sekundy, serwer honoruje tylko dwa takich żądań przed kolejnymi żądaniami ograniczania szybkości. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md) i [kalkulatora jednostek żądania](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **Uchwyt współczynnik ograniczanie żądań zakończonych zbyt duży**

    Gdy klient próbuje przekracza zarezerwowaną przepływnością dla konta, istnieje bez spadku wydajności na serwerze i zakaz używania przepływność poza poziomem zastrzeżone. Serwer będzie prewencyjnego kończy żądanie z RequestRateTooLarge (kod stanu HTTP 429) i zwracają [x-ms ponawiania — po ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) nagłówek wskazujący ilość czasu w milisekundach, które użytkownik musi czekać przed ponowną próbą wykonania żądanie.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Zestawy SDK wszystkie niejawnie catch tej odpowiedzi, przestrzegać określony serwer nagłówka retry-after i ponowić próbę żądania. Chyba, że Twoje konto jest uzyskiwany współbieżnie przez wielu klientów, następne ponowienie próby zakończy się pomyślnie.

    Jeśli masz więcej niż jeden klient łącznie operacyjnego stale powyżej żądań zakończonych, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta mogą być niewystarczające; w takim przypadku klient zgłasza [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) ze stanem kodu 429 do aplikacji. Domyślna liczba ponownych prób, można zmienić za pomocą [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) na [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) wystąpienia. Domyślnie DocumentClientException z kodem stanu 429 po skumulowany czas oczekiwania równej 30 sekund jest zwracany, jeśli żądanie będzie kontynuował pracę nad liczba żądań. Dzieje się tak nawet gdy aktualna liczba ponownych prób jest mniejsza niż liczba ponowień max, są to domyślne 9 lub wartości zdefiniowane przez użytkownika.

    Podczas sposób automatycznego ponawiania pomaga zwiększyć odporność i użyteczność dla większości aplikacji, jego mogą pochodzić w odds podczas wykonywania testów porównawczych wydajności, szczególnie w przypadku, gdy pomiaru opóźnienia.  Opóźnienie zaobserwowane klienta będzie tymczasowe, jeśli eksperymentu trafienia ograniczania serwera i powoduje, że klient zestawu SDK, aby dyskretnie ponowić próbę. Aby uniknąć opóźnienia rzędu podczas wydajności eksperymentów, zmierzyć opłaty zwrócony przez każdej operacji i upewnij się, czy żądania działają poniżej stawki żądanie zastrzeżonego. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md).
3. **Projektowanie pod kątem mniejszych dokumentów w celu uzyskania wyższej przepustowości**

    Opłata za żądanie wyrażana (koszt przetwarzania żądania) dla danej operacji są bezpośrednio skorelowane do rozmiaru dokumentu. Operacje na dużych dokumentów kosztuje więcej niż operacji przy małym dokumentom.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o projektowaniu aplikacji w celu zapewnienia skalowalności i wysokiej wydajności, zobacz [partycjonowanie i skalowanie w usłudze Azure Cosmos DB](partition-data.md).
