---
title: Usługa Azure porady dotyczące wydajności usługi Cosmos DB for Java (asynchroniczny)
description: Dowiedz się więcej opcji konfiguracji klienta w celu poprawy wydajności bazy danych Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: fa6ab58de09e26683cdd958ef77b0fa01d88e2e0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225584"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Porady dotyczące wydajności dla usługi Azure Cosmos DB i Java (asynchroniczny)

> [!div class="op_single_selector"]
> * [Java (asynchroniczny)](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Usługa Azure Cosmos DB jest szybka i elastyczna rozproszonej bazy danych, która miało miejsce płynne skalowanie dzięki gwarancji opóźnień i przepływności. Nie trzeba wprowadzić zmiany architektury głównych lub pisania złożonego kodu do skalowania bazy danych za pomocą usługi Azure Cosmos DB. Skalowanie w górę i w dół jest bardzo proste — wystarczy jedno wywołanie interfejsu API lub wywołanie metody zestaw SDK. Ponieważ usługi Azure Cosmos DB jest dostępna za pośrednictwem wywołań sieci istnieją optymalizacje po stronie klienta, które można wprowadzić do osiągnięcia maksymalnej wydajności, korzystając z [zestawu SDK Java Async SQL](sql-api-sdk-async-java.md).

Dlatego jeśli "jak mogę poprawić wydajność mojej bazy danych?" należy wziąć pod uwagę następujące opcje:

## <a name="networking"></a>Networking
   <a id="same-region"></a>
1. **W ten sposób rozmieszczać klientów, w tym samym regionie platformy Azure dla wydajności**

    Jeśli to możliwe, należy umieścić wszystkie aplikacje podczas wywoływania usługi Azure Cosmos DB w tym samym regionie co baza danych usługi Azure Cosmos DB. Przybliżony porównanie wywołań do usługi Azure Cosmos DB w ramach tego samego regionu zakończyć w ciągu 1 – 2 ms, ale opóźnienie między zachodnim i wschodnim wybrzeżem Stanów Zjednoczonych wynosi > 50 ms. Ten czas oczekiwania prawdopodobnie zależy do innego żądania trasy żądania przekazywanego z klienta do granicy centrum danych platformy Azure. Najniższe możliwe opóźnienie jest osiągane poprzez zapewnienie, że aplikacja wywołująca znajduje się w obrębie tego samego regionu platformy Azure aprowizowane punktu końcowego usługi Azure Cosmos DB. Aby uzyskać listę dostępnych regionów, zobacz [regionów platformy Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustracja zasad połączenia usługi Azure Cosmos DB](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>Użycie zestawu SDK
1. **Instalowanie najnowszych zestawu SDK**

    Zestawów SDK usługi Azure Cosmos DB są ciągle ulepszane w celu zapewnienia najlepszej wydajności. Zobacz [zestawu SDK usługi Azure Cosmos DB](sql-api-sdk-async-java.md) strony do określenia najnowszej zestawu SDK i przejrzyj ulepszenia.
2. **Okres istnienia aplikacji za pomocą pojedynczego wystąpienia klienta usługi Azure Cosmos DB**

    Każde wystąpienie AsyncDocumentClient jest metodą o bezpiecznych wątkach i wykonuje połączenie efektywne zarządzanie i buforowanie adresów. Aby zezwolić na połączenia skutecznego zarządzania i zwiększając wydajność przez AsyncDocumentClient, zalecane jest pojedyncze wystąpienie AsyncDocumentClient dla domeny AppDomain na użytek istnienia aplikacji.

   <a id="max-connection"></a>

3. **Dostrajanie ConnectionPolicy**

    Usługa Azure Cosmos DB żądania są wykonywane za pośrednictwem protokołu HTTPS REST podczas korzystania z zestawu SDK Java Async i są poddawane domyślny rozmiar puli połączeń maksymalna (w 1000). Ta wartość domyślna powinna być idealne dla większości zastosowań. Jednak w przypadku, gdy masz duża kolekcja zasobów z wielu partycji, można ustawić rozmiar puli połączeń max na większą liczbę (na przykład 1500) przy użyciu setMaxPoolSize.

4. **Dostosowywanie zapytania równolegle do kolekcji podzielonych na partycje**

    Usługa Azure Cosmos DB SQL Async zestawu SDK Java obsługuje zapytania równolegle, które umożliwiają kwerenda dotycząca kolekcji partycjonowanej równolegle. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) związanych z pracą z zestawami SDK. Zapytania równoległe są przeznaczone do kwerendy opóźnienia i przepływności starają się ich odpowiedników szeregowe.

    () ***dostrajania setMaxDegreeOfParallelism\:***  równoległe zapytania pracy, badając równocześnie wiele partycji. Jednak dane z poszczególnych kolekcji partycjonowanej pobieranych szeregowo w odniesieniu do zapytania. Tak Użyj setMaxDegreeOfParallelism, aby ustawić liczbę partycji ma maksymalną prawdopodobieństwo osiągnięcia większość wydajna, podanym zapytaniem inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji setMaxDegreeOfParallelism umożliwia ustawianie dużą liczbą, a system wybiera co najmniej (liczba partycji, dane wejściowe podane przez użytkownika), jako maksymalny stopień równoległości.

    Należy pamiętać, zapytania równoległe generuje najważniejsze korzyści, jeśli danych jest równomiernie rozłożona na wszystkie partycje w odniesieniu do zapytania. Jeśli kolekcji partycjonowanej jest podzielona na partycje tak, wszystkie lub większość danych zwróconych przez kwerendę koncentrują się w kilku partycji (jedną partycję w najgorszym przypadku), a następnie wykonywania zapytania, czy bottlenecked przez te partycje.

    (b) ***dostrajania setMaxBufferedItemCount\:***  zapytanie równoległe zaprojektowano w celu wstępnego pobierania wyników, podczas gdy bieżącej partii wyników jest przetwarzany przez klienta. Pobierania pomaga w ogólnej poprawy opóźnienie kwerendy. setMaxBufferedItemCount ogranicza liczbę wstępnie pobrano wyniki. Ustawienie setMaxBufferedItemCount do oczekiwanej liczby wyników zwróconych (lub większa liczba zostanie podana) umożliwia zapytania do odbierania maksymalnych korzyści z pobierania z wyprzedzeniem.

    Pobierania z wyprzedzeniem działa tak samo niezależnie od tego, MaxDegreeOfParallelism i istnieje pojedynczy bufor dla danych ze wszystkich partycji.

5. **Implementowanie wycofywania w odstępach czasu getRetryAfterInMilliseconds**

    Podczas testowania wydajności, należy zwiększyć obciążenie do momentu ograniczeni mała liczba żądań. Jeśli jest dławiona, aplikacja kliencka powinna wycofywania interwał ponawiania określony serwer. Uwzględnienie wycofywania daje pewność, możesz wydać skraca czas oczekiwania między ponownymi próbami.
6. **Skalowanie w poziomie obciążenia klientami**

    Jeśli testujesz na poziomach wysoką przepływność (> 50 000 jednostek RU/s), aplikacja kliencka może stać się wąskim gardłem ze względu na maszynie są takie same się na użycie procesora CPU lub sieci. Jeśli przekroczysz ten punkt, może nadal wypychania dalsze za pomocą konta usługi Azure Cosmos DB skalowanie aplikacji klienckich na wielu serwerach.

7. **Użyj nazwy na podstawie adresów**

    Użyj adresowania opartego na nazwie, w których łącza mają format `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, zamiast SelfLinks (\_samodzielnie), mają format, którego `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` Aby uniknąć pobierania identyfikatory zasobów programu wszystkie zasoby, które są używane do utworzenia łącza. Ponadto jako tych zasobów, Pobierz odtworzone (prawdopodobnie o takiej samej nazwie), buforowanie ich może nie pomóc.

   <a id="tune-page-size"></a>
8. **Dostosuj rozmiar strony dla źródeł danych zapytania/odczytu w celu zapewnienia lepszej wydajności**

    Podczas wykonywania masowego odczytywania dokumentów za pomocą funkcji (na przykład readDocuments) źródło odczytu lub zapytania SQL, wyniki są zwracane w sposób segmentu, jeśli zestaw wyników jest za duży. Domyślnie wyniki są zwracane we fragmentach po 100 elementów lub 1 MB, jednego z tych limitów zostanie osiągnięty jako pierwszy.

    Aby zmniejszyć liczbę rund sieci, wymagany do pobrania wszystkich odpowiednich wyników, można zwiększyć, używając rozmiaru strony [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) nagłówek żądania do 1000. W przypadkach, w których należy wyświetlić tylko kilka wyników, na przykład, jeśli użytkownik interfejsu lub aplikacji interfejsu API zwróci tylko 10 powoduje przez czas, można także zmniejszyć rozmiar strony do 10, aby ograniczyć przepustowość używana dla odczytów i zapytań.

    Możesz też ustawić rozmiar strony, przy użyciu metody setMaxItemCount.

9. **Za pomocą odpowiedniego harmonogramu (należy unikać kradzież zdarzeń pętli we/wy Netty wątków)**

    Korzysta z zestawu SDK Java Async [netty](https://netty.io/) dla operacji We/Wy bez blokowania. Zestaw SDK używa stałej liczby operacji We/Wy zdarzeń netty pętli wątków (jak wiele rdzeni Procesora komputera ma) do wykonywania operacji We/Wy. Możliwość obserwowania zwracana przez interfejs API emituje wynik w jednym udostępnionym pętli zdarzenia we/wy: netty wątków. Dlatego warto blokuje udostępnionego pętli zdarzenia we/wy: netty wątków. Wykonywania prac intensywnie korzystających z procesora CPU lub blokowanie operacji w wątku netty pętli zdarzenia we/wy może spowodować zakleszczenia lub znacznie zmniejszyć zestaw SDK przepływności.

    Na przykład poniższy kod wykonuje utwór intensywnie korzystających z procesora cpu w pętlę zdarzeń, operacji We/Wy wątek netty:

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    Po otrzymaniu wyników, jeśli chcesz zrobić intensywnie z Procesora pracować nad wyników, należy unikać wykonywania wątków netty we/wy pętlę zdarzeń itd. Możesz zamiast tego podać harmonogramu zapewnienie własnych wątków do uruchamiania swoją pracę.

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    Na podstawie typu danych, należy użyć odpowiedniej istniejący harmonogram RxJava dla danego zadania. Tutaj dowiesz się [ ``Schedulers`` ](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Aby uzyskać więcej informacji, zajrzyj [strony GitHub](https://github.com/Azure/azure-cosmosdb-java) dla zestawu SDK Java Async.

10. **Wyłącz rejestrowanie firmy netty** rejestrowanie Netty biblioteki jest duża liczba i musi być wyłączona (z pominięciem logowania w konfiguracji może nie być wystarczająca) aby uniknąć dodatkowych kosztów procesora CPU. Jeśli nie jesteś w trybie debugowania, wyłącz netty rejestrowania całkowicie. Jeśli używasz log4j do usunięcia dodatkowych kosztów Procesora poniesione przez ``org.apache.log4j.Category.callAppenders()`` z netty bazie kodu Dodaj następujący wiersz:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **System operacyjny otwartych plików Limit zasobów** systemy Linux niektóre (np. Red Hat) mają górnego limitu liczby Otwórz pliki, a tym samym całkowita liczba połączeń. Uruchom następujące polecenie, aby wyświetlić bieżące limity:

    ```bash
    ulimit -a
    ```

    Liczba otwartych plików (nofile) musi być wystarczająco duży, aby mieć wystarczającą ilość miejsca na rozmiar puli skonfigurowane połączenie i inne pliki otwarte przez system operacyjny. Można to modyfikować, aby umożliwić większy rozmiar puli połączeń.

    Otwórz plik limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Dodaj/Modyfikuj następujące wiersze:

    ```
    * - nofile 100000
    ```

12. **Natywnych implementacji protokołu SSL na użytek netty** Netty można użyć biblioteki OpenSSL bezpośrednio do stosu implementacji SSL osiągnąć większą wydajność. W przypadku braku tej konfiguracji netty powróci do implementacji SSL domyślne w języku Java.

    on Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    i dodaj następującą zależność do zależności projektu maven:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Dla innych platform (Red Hat, Windows, Mac, itp.) dotyczą te instrukcje https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Zasady indeksowania
 
1. **Wyklucz ścieżki nieużywane indeksowania do szybszego zapisu**

    Zasady indeksowania danych usługi Azure Cosmos DB pozwala określić które ścieżki dokumentu do dołączania lub wykluczania z indeksowania dzięki wykorzystaniu indeksowania ścieżki (setIncludedPaths i setExcludedPaths). Użycie indeksowania ścieżki oferują zapisu ulepszoną wydajność i niższe magazyn indeksów dla scenariuszy, w których wzorców zapytań są znane wcześniej, ponieważ koszty indeksowania bezpośrednio skorelowanych liczby unikatowych ścieżek indeksowane. Na przykład poniższy kod pokazuje, jak (zwany również wykluczyć całą sekcję dokumentów poddrzewo) z pomocą indeksowaniem "*" symboli wieloznacznych.

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

    Do mierzenia obciążenie związane z każdej operacji (Tworzenie, aktualizowanie lub usuwanie), sprawdź [x-ms żądanie — opłata](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) nagłówek w celu mierzenia liczby jednostek żądania, używanych przez te operacje. Można również przeglądać równoważne właściwość RequestCharge ResourceResponse<T> lub FeedResponse<T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Opłata za żądanie wyrażana zwrócony w nagłówku to jest część aprowizowanej przepływności. Na przykład jeśli masz 2000 jednostek RU/s jest obsługiwana, a jeśli poprzednie zapytanie zwraca 1KB 1000 dokumentów, koszty działania wynosi 1000. Jako takie w ciągu sekundy, serwer honoruje tylko dwa takich żądań przed kolejnymi żądaniami ograniczania szybkości. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md) i [kalkulatora jednostek żądania](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Uchwyt współczynnik ograniczanie żądań zakończonych zbyt duży**

    Gdy klient próbuje przekracza zarezerwowaną przepływnością dla konta, istnieje bez spadku wydajności na serwerze i zakaz używania przepływność poza poziomem zastrzeżone. Serwer będzie prewencyjnego kończy żądanie z RequestRateTooLarge (kod stanu HTTP 429) i zwracają [x-ms ponawiania — po ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) nagłówek wskazujący ilość czasu w milisekundach, które użytkownik musi czekać przed ponowną próbą wykonania żądanie.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Zestawy SDK wszystkie niejawnie catch tej odpowiedzi, przestrzegać określony serwer nagłówka retry-after i ponowić próbę żądania. Chyba, że Twoje konto jest uzyskiwany współbieżnie przez wielu klientów, następne ponowienie próby zakończy się pomyślnie.

    Jeśli masz więcej niż jeden klient łącznie operacyjnego stale powyżej żądań zakończonych, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta mogą być niewystarczające; w takim przypadku klient zgłasza DocumentClientException z kodem stanu 429 do aplikacji. Domyślna liczba ponownych prób, można zmienić za pomocą setRetryOptions wystąpieniu ConnectionPolicy. Domyślnie DocumentClientException z kodem stanu 429 po skumulowany czas oczekiwania równej 30 sekund jest zwracany, jeśli żądanie będzie kontynuował pracę nad liczba żądań. Dzieje się tak nawet gdy aktualna liczba ponownych prób jest mniejsza niż liczba ponowień max, są to domyślne 9 lub wartości zdefiniowane przez użytkownika.

    Podczas sposób automatycznego ponawiania pomaga zwiększyć odporność i użyteczność dla większości aplikacji, jego mogą pochodzić w odds podczas wykonywania testów porównawczych wydajności, szczególnie w przypadku, gdy pomiaru opóźnienia. Opóźnienie zaobserwowane klienta będzie tymczasowe, jeśli eksperymentu trafienia ograniczania serwera i powoduje, że klient zestawu SDK, aby dyskretnie ponowić próbę. Aby uniknąć opóźnienia rzędu podczas wydajności eksperymentów, zmierzyć opłaty zwrócony przez każdej operacji i upewnij się, czy żądania działają poniżej stawki żądanie zastrzeżonego. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md).
3. **Projektowanie pod kątem mniejszych dokumentów w celu uzyskania wyższej przepustowości**

    Opłata za żądanie wyrażana (koszt przetwarzania żądania) dla danej operacji są bezpośrednio skorelowane do rozmiaru dokumentu. Operacje na dużych dokumentów kosztuje więcej niż operacji przy małym dokumentom.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o projektowaniu aplikacji w celu zapewnienia skalowalności i wysokiej wydajności, zobacz [partycjonowanie i skalowanie w usłudze Azure Cosmos DB](partition-data.md).
