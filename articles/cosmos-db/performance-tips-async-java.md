---
title: Azure Cosmos DB wskazówki dotyczące wydajności dla asynchronicznego języka Java
description: Informacje na temat opcji konfiguracji klienta w celu poprawy wydajności bazy danych Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 2c2d776012e702469be4fd3217fb89be0ad419bf
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261629"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Porady dotyczące wydajności Azure Cosmos DB i asynchronicznego języka Java

> [!div class="op_single_selector"]
> * [Java (asynchroniczny)](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB to szybka i elastyczna dystrybuowana baza danych, która bezproblemowo skaluje się do gwarantowanych opóźnień i przepływności. Nie trzeba wprowadzać głównych zmian architektury ani pisać złożonego kodu w celu skalowania bazy danych za pomocą Azure Cosmos DB. Skalowanie w górę i w dół jest tak proste, jak wykonywanie pojedynczego wywołania interfejsu API lub wywołania metody zestawu SDK. Ponieważ jednak dostęp do Azure Cosmos DB jest uzyskiwany za pośrednictwem wywołań sieciowych, istnieją optymalizacje po stronie klienta, które umożliwiają osiągnięcie szczytowej wydajności podczas korzystania z [zestawu asynchronicznego Java SDK języka SQL](sql-api-sdk-async-java.md).

Tak więc w przypadku pytania "jak można poprawić wydajność bazy danych?" należy wziąć pod uwagę następujące opcje:

## <a name="networking"></a>Networking
   <a id="same-region"></a>
1. **Kolokacja klientów w tym samym regionie świadczenia usługi Azure na potrzeby wydajności**

    Jeśli to możliwe, należy umieścić dowolne aplikacje wywołujące Azure Cosmos DB w tym samym regionie, co baza danych Azure Cosmos DB. Dla przybliżonego porównania, wywołania do Azure Cosmos DB w tym samym regionie, kompletne w ciągu 1-2 MS, ale opóźnienie między zachodnim i wschodnim wybrzeżem Stanów Zjednoczonych jest > 50 ms. To opóźnienie może się różnić od żądania żądania w zależności od trasy wykonywanej przez żądanie, gdy przechodzi od klienta do granicy centrum danych platformy Azure. Najniższe możliwe opóźnienie jest realizowane przez zagwarantowanie, że aplikacja wywołująca znajduje się w tym samym regionie platformy Azure, co punkt końcowy Azure Cosmos DB aprowizacji. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustracja zasad połączenia Azure Cosmos DB](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>Użycie zestawu SDK
1. **Zainstaluj najnowszy zestaw SDK**

    Zestawy SDK Azure Cosmos DB są stale ulepszane w celu zapewnienia najlepszej wydajności. Zobacz strony [zestawu sdk Azure Cosmos DB](sql-api-sdk-async-java.md) , aby określić najnowszy zestaw SDK i zapoznać się z ulepszeniami.
2. **Używanie pojedynczego klienta Azure Cosmos DB w okresie istnienia aplikacji**

    Każde wystąpienie AsyncDocumentClient jest bezpieczne wątkowo i wykonuje wydajne zarządzanie połączeniami oraz buforowanie adresów. Aby umożliwić efektywne zarządzanie połączeniami i lepszą wydajność dzięki AsyncDocumentClient, zaleca się użycie jednego wystąpienia elementu AsyncDocumentClient na domenę aplikacji przez okres istnienia programu.

   <a id="max-connection"></a>

3. **Dostrajanie ConnectionPolicy**

    Żądania Azure Cosmos DB są wykonywane za pośrednictwem protokołu HTTPS/REST podczas korzystania z asynchronicznego zestawu Java SDK i podlegają domyślnemu maksymalnemu rozmiarowi puli połączeń (1000). Ta wartość domyślna powinna być idealnym rozwiązaniem w przypadku większości przypadków użycia. Jednak w przypadku dużej kolekcji z wieloma partycjami można ustawić maksymalny rozmiar puli połączeń na większą liczbę (Powiedz, 1500) przy użyciu setMaxPoolSize.

4. **Dostrajanie równoległych zapytań dla kolekcji partycjonowanych**

    Azure Cosmos DB SQL Async Java SDK obsługuje zapytania równoległe, które umożliwiają równoległe wykonywanie zapytań do kolekcji partycjonowanej. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) związane z pracą z zestawami SDK. Zapytania równoległe są przeznaczone do poprawiania opóźnienia zapytań i przepływności w porównaniu z ich odpowiednikami seryjnymi.

    (a) ***dostrajanie\:***  równoległych zapytań setMaxDegreeOfParallelism działają przez wykonywanie zapytań na wielu partycjach równolegle. Jednak dane z pojedynczej kolekcji partycjonowanej są pobierane sekwencyjnie w odniesieniu do zapytania. W tym celu należy użyć setMaxDegreeOfParallelism, aby ustawić liczbę partycji, które mają maksymalną szansę osiągnięcia najbardziej wydajnego zapytania, pod warunkiem, że wszystkie inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji, możesz użyć setMaxDegreeOfParallelism, aby ustawić dużą liczbę, a system wybierze minimalną (liczbę partycji, dane wejściowe podane przez użytkownika) jako maksymalny stopień równoległości.

    Należy pamiętać, że zapytania równoległe generują najlepsze korzyści, jeśli dane są równomiernie dystrybuowane we wszystkich partycjach w odniesieniu do zapytania. Jeśli partycjonowana kolekcja jest partycjonowana w taki sposób, że wszystkie lub większość danych zwróconych przez zapytanie jest skoncentrowana na kilku partycjach (jedna partycja w najgorszym przypadku), wydajność zapytania zostałaby przekazana przez te partycje.

    (b) ***dostrajanie\:***  równoległe zapytania setMaxBufferedItemCount zaprojektowano w celu wstępnego pobrania wyników podczas przetwarzania bieżącej partii wyników przez klienta. Wstępne pobieranie pomaga w ogólnym ulepszaniu opóźnienia zapytania. setMaxBufferedItemCount ogranicza liczbę wstępnie pobranych wyników. Ustawienie setMaxBufferedItemCount na oczekiwaną liczbę zwracanych wyników (lub wyższą liczbę) powoduje, że zapytanie otrzymuje maksymalną korzyść przed pobraniem.

    Przed pobraniem działa w taki sam sposób, niezależnie od MaxDegreeOfParallelism, i istnieje jeden bufor dla danych ze wszystkich partycji.

5. **Implementowanie wycofywania w interwałach getRetryAfterInMilliseconds**

    Podczas testowania wydajności należy zwiększyć obciążenie, dopóki nie zostanie ograniczona niewielka liczba żądań. W przypadku ograniczenia przepustowości aplikacja kliencka powinna wycofywania dla interwału ponawiania określonych przez serwer. Poszanowanie wycofywania gwarantuje, że spędzasz minimalny czas oczekiwania między ponownymi próbami.
6. **Skalowanie obciążenia klienta**

    Jeśli testujesz na poziomach o wysokiej przepływności (> 50 000 RU/s), aplikacja kliencka może stać się wąskim gardłem z powodu ograniczenia przepustowości procesora lub sieci. Jeśli docierasz do tego punktu, możesz kontynuować wypychanie konta Azure Cosmos DB przez skalowanie aplikacji klienckich na wiele serwerów.

7. **Użyj adresowania na podstawie nazwy**

    Użyj adresowania opartego na nazwach, gdzie linki `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`mają format zamiast SelfLinks (\_własny), który ma format `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` , aby uniknąć pobierania ResourceID wszystkich zasobów użytych do skonstruowania łącza. Ponadto, ponieważ te zasoby zostaną utworzone ponownie (prawdopodobnie z tą samą nazwą), buforowanie może nie pomóc.

   <a id="tune-page-size"></a>
8. **Dostosuj rozmiar strony dla zapytań/Odczytaj źródła w celu uzyskania lepszej wydajności**

    Podczas wykonywania zbiorczej odczytu dokumentów przy użyciu funkcji odczytywania kanału informacyjnego (na przykład readDocuments) lub podczas wystawiania zapytania SQL wyniki są zwracane w postaci segmentacji, jeśli zestaw wyników jest zbyt duży. Domyślnie wyniki są zwracane w fragmentach 100 elementów lub 1 MB, w zależności od tego, który limit zostanie osiągnięty jako pierwszy.

    Aby zmniejszyć liczbę podróży sieci wymaganych do pobrania wszystkich stosownych wyników, można zwiększyć rozmiar strony przy użyciu nagłówka żądania [x-MS-Max-Item-Count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) do 1000. W przypadkach, gdy konieczne jest wyświetlenie tylko kilku wyników, na przykład jeśli interfejs użytkownika lub interfejsu API aplikacji zwraca tylko 10 wyników, można również zmniejszyć rozmiar strony do 10, aby zmniejszyć przepływność wykorzystywaną do odczytu i zapytań.

    Rozmiar strony można również ustawić przy użyciu metody setMaxItemCount.

9. **Użyj odpowiedniego harmonogramu (Unikaj kradzieży wątków wielosieciowych we/wy pętli zdarzeń)**

    Asynchroniczny zestaw SDK języka [Java używa sieci](https://netty.io/) na potrzeby nieblokującego we/wy. Zestaw SDK używa ustalonej liczby wątków pętli zdarzeń we/wy (w przypadku wielu rdzeni procesora) na potrzeby wykonywania operacji we/wy. Zauważalny zwracany przez interfejs API emituje wynik na jednym ze współużytkowanych wątków pętli zdarzeń we/wy. W związku z tym ważne jest, aby nie blokować wspólnych wątków pętli na potrzeby operacji we/wy. Wykonywanie zadań intensywnie korzystających z procesora CPU lub operacji blokowania w wątku sieci w pętli zdarzeń we/wy może spowodować zakleszczenie lub znacząco zredukować przepływność zestawu SDK.

    Na przykład poniższy kod wykonuje prace intensywnie korzystające z procesora CPU w wątku wielosieciowych operacji we/wy pętli:

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

    Po otrzymaniu wyniku, jeśli chcesz wykonać intensywną moc procesora CPU w wyniku, należy unikać wykonywania tej operacji w wątku z wielosieciowymi pętlami we/wy. Zamiast tego możesz wprowadzić własny harmonogram, aby zapewnić własny wątek do uruchamiania pracy.

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

    Na podstawie typu pracy należy użyć odpowiedniego istniejącego harmonogramu RxJava do pracy. Przeczytaj tutaj [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Aby uzyskać więcej informacji, zapoznaj się ze [stroną usługi GitHub](https://github.com/Azure/azure-cosmosdb-java) dla asynchronicznego zestawu Java SDK.

10. **Wyłącz rejestrowanie sieci na sieć** Rejestrowanie biblioteki sieci z sieciami zawiera czat i musi być wyłączone (Pomijanie logowania może być niewystarczające), aby uniknąć dodatkowych kosztów procesora. Jeśli nie jesteś w trybie debugowania, wyłącz rejestrację sieci na sieć. Dlatego jeśli używasz Log4J do usuwania dodatkowych kosztów procesora CPU ponoszonych przez ``org.apache.log4j.Category.callAppenders()`` z sieci, Dodaj następujący wiersz do bazy kodu:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Limit zasobów otwartych plików systemu operacyjnego** Niektóre systemy Linux (np. Red Hat) mają górny limit liczby otwartych plików, a więc łączną liczbę połączeń. Uruchom następujące, aby wyświetlić bieżące limity:

    ```bash
    ulimit -a
    ```

    Liczba otwartych plików (nofile) musi być wystarczająco duża, aby mieć wystarczającą ilość miejsca na skonfigurowany rozmiar puli połączeń i inne otwarte pliki w systemie operacyjnym. Można ją zmodyfikować tak, aby zezwalała na większy rozmiar puli połączeń.

    Otwórz plik Limits. conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Dodaj/zmodyfikuj następujące wiersze:

    ```
    * - nofile 100000
    ```

12. **Użyj natywnej implementacji protokołu SSL dla** sieci z sieciami Sieci mogą korzystać z OpenSSL bezpośrednio dla stosu implementacji SSL, aby osiągnąć lepszą wydajność. W przypadku braku tej konfiguracji te konfiguracje zostaną przywrócone do domyślnej implementacji protokołu SSL w języku Java.

    w Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    i Dodaj następującą zależność do zależności projektu Maven:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

W przypadku innych platform (Red Hat, Windows, Mac itp.) zapoznaj się z tymi instrukcjami https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Zasady indeksowania
 
1. **Wykluczanie nieużywanych ścieżek z indeksowania w celu przyspieszenia operacji zapisu**

    Zasady indeksowania Azure Cosmos DB umożliwiają określenie, które ścieżki dokumentów mają być dołączone lub wykluczone z indeksowania przy użyciu ścieżek indeksowania (setIncludedPaths i setExcludedPaths). Użycie ścieżek indeksowania może oferować ulepszoną wydajność zapisu i niższy indeks magazynu dla scenariuszy, w których wzorce zapytania są znane wcześniej, ponieważ koszty indeksowania są bezpośrednio skorelowane z liczbą unikatowych ścieżek indeksowanych. Na przykład poniższy kod pokazuje, jak wykluczyć całą sekcję dokumentów (vel poddrzewo) z indeksowania przy użyciu symbolu wieloznacznego "*".

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB zasad indeksowania](indexing-policies.md).

## <a name="throughput"></a>Przepływność
<a id="measure-rus"></a>

1. **Mierzenie i dostrajanie dla niższych jednostek żądań/drugiego użycia**

    Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, w tym relacyjne i hierarchiczne zapytania z UDF, procedurami składowanymi i wyzwalaczami — wszystko to działa na dokumentach w ramach kolekcji baz danych. Koszt związany z każdą z tych operacji różni się w zależności od procesora CPU, operacji we/wy i pamięci wymaganej do ukończenia tej operacji. Zamiast rozważać zasoby sprzętowe i zarządzać nimi, można traktować jednostkę żądania (RU) jako pojedynczą miarę dla zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługi żądania aplikacji.

    Obsługa przepływności zależy od liczby [jednostek żądania](request-units.md) ustawionych dla każdego kontenera. Użycie jednostki żądania jest oceniane jako stawka na sekundę. Aplikacje, które przekraczają zainicjowaną stawkę jednostkową żądania dla ich kontenera, są ograniczone do momentu spadku stawki poniżej poziomu aprowizacji kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność przez zainicjowanie obsługi dodatkowych jednostek żądania.

    Złożoność zapytania wpływa na liczbę jednostek żądań używanych dla operacji. Liczba predykatów, charakter predykatów, liczba UDF i rozmiar zestawu danych źródłowych wpływają na koszt operacji zapytania.

    Aby zmierzyć obciążenie związane z jakąkolwiek operacją (tworzenie, aktualizowanie lub usuwanie), Sprawdź nagłówek [x-MS-Request-opłata](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) , aby zmierzyć liczbę jednostek żądań używanych przez te operacje. Możesz również przyjrzeć się równoważnej właściwości RequestCharge w ResourceResponse\<t > lub FeedResponse\<t >.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Opłata za żądanie zwrócona w tym nagłówku jest częścią alokowanej przepływności. Na przykład jeśli masz 2000 RU/s, a poprzednia kwerenda zwróci 1000 rozmiarze 1 KB-Documents, koszt operacji to 1000. W związku z tym w ciągu jednej sekundy serwer honoruje tylko dwa takie żądania przed szybkością ograniczania kolejnych żądań. Aby uzyskać więcej informacji, zobacz [jednostki żądań](request-units.md) i [Kalkulator jednostek żądania](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Zbyt duży współczynnik obsługi — limit liczby żądań**

    Gdy klient próbuje przekroczyć zarezerwowaną przepływność dla konta, nie ma obniżenia wydajności na serwerze i nie będzie korzystać z wydajności przepływności poza poziomem zarezerwowanym. Serwer zapobiegawczo zakończyć żądanie z RequestRateTooLarge (kod stanu HTTP 429) i zwróci nagłówek [x-MS-retry-After-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) wskazujący ilość czasu (w milisekundach), przez który użytkownik musi czekać przed ponowną próbą wykonania żądania.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Zestaw SDK niejawnie przechwytuje tę odpowiedź, przestrzegając określonego przez serwer nagłówka retry-After i ponów próbę wykonania żądania. O ile Twoje konto nie jest dostępne współbieżnie przez wielu klientów, kolejna próba powiodła się.

    Jeśli masz więcej niż jeden klient, który działa w sposób ciągły nad częstotliwością żądań, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta może nie być wystarczająca; w takim przypadku klient zgłasza DocumentClientException z kodem stanu 429 do aplikacji. Domyślną liczbę ponownych prób można zmienić za pomocą setRetryOptions w wystąpieniu ConnectionPolicy. Domyślnie DocumentClientException z kodem stanu 429 jest zwracany po skumulowanym czasie oczekiwania 30 sekund, jeśli żądanie będzie nadal działać powyżej stawki żądania. Dzieje się tak nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, być wartością domyślną 9 lub wartości zdefiniowanej przez użytkownika.

    Mimo że automatyczne zachowanie ponowienia próby pozwala zwiększyć odporność i użyteczność dla większości aplikacji, może się to zdarzyć szanse podczas wykonywania testów wydajnościowych, szczególnie podczas mierzenia opóźnień. Opóźnienie obserwowane przez klienta zostanie wykonane, jeśli eksperyment trafi na ograniczenia serwera i spowoduje, że zestaw SDK klienta zostanie ponownie powtórzony. Aby uniknąć opóźnień opóźnienia podczas eksperymentów w wydajności, należy zmierzyć opłaty zwrócone przez poszczególne operacje i upewnić się, że żądania działają poniżej zarezerwowanej stawki żądania. Aby uzyskać więcej informacji, zobacz [jednostki żądania](request-units.md).
3. **Projektowanie dla mniejszych dokumentów w celu zwiększenia przepływności**

    Opłata za żądanie (koszt przetwarzania żądania) danej operacji jest bezpośrednio skorelowana z rozmiarem dokumentu. Operacje na dużych dokumentach są droższe niż operacje w przypadku małych dokumentów.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat projektowania aplikacji pod kątem skalowania i wysokiej wydajności, zobacz [partycjonowanie i skalowanie w Azure Cosmos DB](partition-data.md).
