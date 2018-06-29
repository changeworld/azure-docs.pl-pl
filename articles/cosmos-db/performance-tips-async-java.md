---
title: Azure porady dotyczące wydajności rozwiązania Cosmos DB Java Async | Dokumentacja firmy Microsoft
description: Dowiedz się więcej opcji konfiguracji klienta w celu poprawy wydajności bazy danych Azure DB rozwiązania Cosmos
keywords: jak poprawić wydajność bazy danych
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: sngun
ms.openlocfilehash: e3ee75a07f19fef50d9aca61773bd7ea860f2ca4
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102476"
---
> [!div class="op_single_selector"]
> * [Java (asynchroniczny)](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Porady dotyczące wydajności bazy danych Azure rozwiązania Cosmos i asynchronicznych w języku Java
Azure DB rozwiązania Cosmos jest szybkie i elastyczne rozproszoną bazę danych, która skaluje bezproblemowo gwarantowane opóźnienia i przepływności. Nie trzeba zmieniać architektura głównych ani pisania złożonego kodu można skalować bazy danych z bazy danych Azure rozwiązania Cosmos. Skalowanie w górę i w dół sprowadza się do jednego wywołania interfejsu API lub zestawu SDK wywołania metody. Jednak ponieważ bazy danych Azure rozwiązania Cosmos jest dostępny za pośrednictwem połączeń sieci istnieją optymalizacje po stronie klienta, możesz wprowadzić, aby osiągnąć najwyższą wydajność przy użyciu [zestawu Java SDK Async SQL](sql-api-sdk-async-java.md).

Dlatego jeśli "jak poprawić wydajność mojej bazy danych?" należy wziąć pod uwagę następujące opcje:

## <a name="networking"></a>Networking
   <a id="same-region"></a>
1. **Klienci, w tym samym regionie Azure wydajności w ten sposób rozmieszczać**

    Jeśli to możliwe, należy umieścić wszystkie aplikacje wywoływanie bazy danych Azure rozwiązania Cosmos w tym samym regionie co baza danych bazy danych Azure rozwiązania Cosmos. Przybliżony porównanie wywołań do bazy danych Azure rozwiązania Cosmos w ramach tego samego regionu ukończona w ciągu 1 i 2 ms, ale opóźnienie między zachód i wschodniego USA > 50 ms. Tego opóźnienia prawdopodobnie zależy od trasę przez żądanie przesyłanych z klienta do granicy centrum danych Azure do innego żądania. Najniższym opóźnieniu możliwe jest to osiągane przez zapewnienie, że aplikacja wywołująca znajduje się w tym samym regionie Azure jako punkt końcowy elastycznie bazy danych Azure rozwiązania Cosmos. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustracja zasady połączenia bazy danych Azure rozwiązania Cosmos](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Użycie zestawu SDK
1. **Zainstaluj najnowsze zestaw SDK**

    Zestawy SDK Azure rozwiązania Cosmos bazy danych jest stale ulepszana aby zapewnić najlepszą wydajność. Zobacz [zestawu SDK usługi Azure rozwiązania Cosmos DB](sql-api-sdk-async-java.md) strony, aby określić najbardziej aktualnych zestawu SDK i przejrzyj ulepszenia.
2. **Okres istnienia aplikacji za pomocą klienta pojedyncze bazy danych Azure rozwiązania Cosmos**

    Każde wystąpienie AsyncDocumentClient wątkowo i wykonuje zarządzania połączeniami wydajne i buforowanie adresów. Aby umożliwić zarządzanie połączeniami wydajne i większą wydajność przez AsyncDocumentClient, zaleca się używać jednego wystąpienia AsyncDocumentClient dla domeny AppDomain przez czas ich istnienia aplikacji.

   <a id="max-connection"></a>

3. **Dostrajanie ConnectionPolicy**

    Azure DB rozwiązania Cosmos żądania są wykonywane za pośrednictwem protokołu HTTPS/REST podczas przy użyciu zestawu SDK Java Async i są poddawane domyślny rozmiar puli połączeń maksymalna (w 1000). Ta wartość domyślna powinna być idealne w większości przypadków użycia. Jednak w przypadku, gdy masz dużą kolekcję wiele partycji, można ustawić połączenia maksymalny rozmiar puli na większą liczbę (powiedzieć, 1500) przy użyciu setMaxPoolSize.

4. **Dostrajanie równoległe zapytania dla kolekcji partycjonowanych**

    Azure rozwiązania Cosmos bazy danych SQL Async Java SDK obsługuje zapytania równoległe, które umożliwiają kwerenda dotycząca kolekcji partycjonowanych równoległe (zobacz [Praca z zestawów SDK](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) i pokrewnych [przykłady kodu](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) Aby uzyskać więcej informacji). Zapytania równoległe są przeznaczone do poprawy opóźnienia zapytania i przepływności za pośrednictwem ich odpowiednika szeregowego.

    () ***dostrajanie setMaxDegreeOfParallelism\:***  równoległe zapytania pracy badając równocześnie wiele partycji. Jednak z poszczególnych kolekcji partycjonowanych pobieranych szeregowo względem zapytania. Tak Użyj setMaxDegreeOfParallelism można ustawić liczby partycji ma maksymalną prawdopodobieństwo osiągnięcia większości kwerendy wydajności, pod warunkiem inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji setMaxDegreeOfParallelism umożliwia ustawianie dużą liczbą i system wybiera minimum (liczba partycji, dane wejściowe podane przez użytkownika), jako maksymalny stopień równoległości. 

    Należy pamiętać zapytania równoległe tworzy najważniejsze korzyści, jeśli dane jest równomiernie rozłożona wszystkie partycje w odniesieniu do zapytania. Jeśli kolekcja podzielonym na partycje jest podzielona na partycje taki sposób, że wszystkie lub większość danych zwróconych przez kwerendę koncentrują się w kilku partycji (jedną partycję w najgorszym przypadku), a następnie wydajności kwerendy może być wydajność ruchu przez te partycje.

    (b) ***dostrajanie setMaxBufferedItemCount\:***  zapytania równoległe zaprojektowano w celu wstępnie pobrać wyniki podczas bieżącej partii wyników jest przetwarzana przez klienta. Pobierania pomaga w ogólnej poprawy opóźnienia zapytania. setMaxBufferedItemCount ogranicza liczbę wstępnie pobranych wyników. Ustawienie setMaxBufferedItemCount do oczekiwanej liczby wyników zwróconych (lub większa liczba) umożliwia wykonanie zapytania w celu maksymalnego skorzystają z pobierania.

    Pobierania działa tak samo, niezależnie od MaxDegreeOfParallelism, i istnieje pojedynczy bufor dla danych z wszystkich partycji.  

5. **Implementowanie wycofywania odstępach getRetryAfterInMilliseconds**

    Podczas testowania wydajności, należy zwiększyć obciążenie dopóki mała liczba żądań pobrania ograniczany. Jeśli ograniczany, aplikacja kliencka powinna wycofywania dla interwału ponawiania określić serwer. Przestrzeganie wycofywania zapewnia spędzają na skraca czas oczekiwania między kolejnymi próbami. Aby uzyskać więcej informacji, zobacz [Exceeding zastrzeżone ograniczenia przepływności](request-units.md#RequestRateTooLarge) i DocumentClientException.getRetryAfterInMilliseconds.
6. **Skalowanie w poziomie obciążenia klientami**

    Jeśli testujesz na poziomach wysokiej przepływności (> 50 000 RU/s), może stać się aplikacja kliencka "wąskie gardło" z powodu maszyny nakładanie się na użycie procesora CPU lub sieci. Jeśli osiągnąć tego punktu, można nadal push dodatkowe konto bazy danych Azure rozwiązania Cosmos przez skalowania aplikacji klienta na wielu serwerach.

7. **Użyj adresowanie na podstawie nazwy**

    Użyj adresowanie na podstawie nazwy, której łącza mają format `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, zamiast SelfLinks (\_self), które mają format `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` Aby uniknąć pobierania identyfikatory zasobów wszystkich zasobów, które są używane do utworzenia łącza. Ponadto zgodnie z tych zasobów uzyskać odtworzone (prawdopodobnie w tej samej nazwy), buforowanie ich może nie pomocy.

   <a id="tune-page-size"></a>
8. **Dostosuj rozmiar strony dla źródła danych zapytania/odczytu w celu poprawy wydajności**

    Podczas wykonywania masowego odczytu dokumentów za pomocą funkcji (na przykład readDocuments) źródło odczytu lub zapytania SQL, wyniki są zwracane w sposób segmentu, jeśli zestaw wyników jest zbyt duży. Domyślnie są zwracane w fragmentów 100 elementów lub 1 MB, jednego z tych limitów trafień pierwszej.

    Aby zmniejszyć liczbę sieci przekazywanych wymagany do pobrania wszystkich odpowiednich wyników, można zwiększyć za pomocą rozmiar strony [x-ms-max elementu count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) nagłówek żądania do maksymalnie 1000. W przypadkach, gdy konieczne jest wyświetlenie tylko kilka wyniki, na przykład jeśli użytkownik interfejsu lub aplikacji interfejsu API zwraca tylko 10 powoduje przez czas, można także zmniejszyć rozmiar strony do 10 do zmniejszenia przepustowości używane dla operacji odczytu i zapytań.

    Mogą również ustawić za pomocą metody setMaxItemCount rozmiar strony.
    
9. **Użyć odpowiednich harmonogramu (należy unikać kradzież zdarzeń pętli we/wy Netty wątków)**

    Zestaw SDK Java Async używa [netty](https://netty.io/) dla nieblokujące we/wy. Zestaw SDK używa stałej liczby wątków pętli zdarzenia netty we/wy (rdzeni tyle Procesora, które na komputerze) do wykonywania operacji We/Wy. Według zwracane przez interfejs API emituje wyników na jednym z udostępnionego pętli zdarzenia we/wy: netty wątków. Dlatego należy blokuje udostępnionego pętli zdarzenia we/wy: netty wątków. Podczas pracy znacznym procesora CPU lub blokowanie operacji We/Wy wątku netty pętli zdarzenia mogą spowodować zakleszczenie lub znacznie ograniczyć przepustowość zestawu SDK.

    Na przykład następujący kod wykonuje intensywne prac procesora cpu w pętli zdarzenia netty wątków We/Wy:

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

    Po otrzymaniu wyników, jeśli chcesz wykonać intensywnie z Procesora działają w wyniku należy unikać wykonywania wątku netty we/wy pętli zdarzeń itd. Zamiast tego możesz podać własne harmonogramu do zapewnienia systemem pracy własne wątku.

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

    Na podstawie typu danych, należy użyć odpowiedniego istniejącego harmonogramu RxJava do pracy. W tym miejscu odczytu [ ``Schedulers`` ](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Aby uzyskać więcej informacji, zapoznaj się [Github strony](https://github.com/Azure/azure-cosmosdb-java) dla zestawu SDK Java asynchronicznego.

10. **Wyłącz rejestrowanie w netty** rejestrowanie biblioteki Netty jest chatty i musi być wyłączona (pomijanie logowania w konfiguracji może być za mało) aby uniknąć dodatkowych kosztów procesora CPU. Jeśli nie jesteś w trybie debugowania, netty Wyłącz rejestrowanie całkowicie. Dlatego w przypadku korzystania z narzędzia log4j do usunięcia dodatkowych kosztów Procesora poniesionych przez ``org.apache.log4j.Category.callAppenders()`` z netty Dodaj następujący wiersz do baza kodu:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **OS otwartych plików Limit zasobów** systemy Linux niektóre (na przykład Red Hat) mają górny limit liczby otwarte pliki i dlatego całkowitą liczbę połączeń. Uruchom następujące polecenie, aby wyświetlić bieżące ograniczenia:

    ```bash
    ulimit -a
    ```

    Liczba otwartych plików (nofile) musi być wystarczająco duży, aby mieć wystarczającej ilości miejsca na rozmiar puli połączeń skonfigurowanych i inne pliki otwarte przez system operacyjny. Może być modyfikowany umożliwiające większy rozmiar puli połączeń.

    Otwórz plik limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Dodaj/Zmień następujące wiersze:

    ```
    * - nofile 100000
    ```

12. **Użyj natywnego implementacji protokołu SSL dla netty** Netty umożliwia osiągnąć większą wydajność biblioteki OpenSSL bezpośrednio do stosu implementacji protokołu SSL. W przypadku braku tej konfiguracji netty powróci do jego Java Domyślna implementacja protokołu SSL.

    na Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    i dodaj następujące zależności do maven zależności projektu:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.7.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Dla innych platform (Red Hat, Windows, Mac, itp.) dotyczą te instrukcje https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Zasady indeksowania
 
1. **Wyklucz ścieżki nieużywane indeksowania dla szybsze zapisy**

    Zasady indeksowania Azure DB rozwiązania Cosmos umożliwia określenie ścieżek dokumentu do dołączania lub wykluczania indeksowania dzięki wykorzystaniu indeksowania ścieżki (setIncludedPaths i setExcludedPaths). Użycie indeksowania ścieżki zaoferować zapisu lepszą wydajność i dolnym indeksu magazynu w scenariuszach, w których wzorców zapytań są znane wcześniej, jak koszty indeksowania bezpośrednio skorelowanych liczby unikatowych ścieżki zaindeksowane.  Na przykład poniższy kod przedstawia sposób wykluczyć całą sekcję dokumentów () poddrzewo) z indeksowania przy użyciu "*" symboli wieloznacznych.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Aby uzyskać więcej informacji, zobacz [Azure DB rozwiązania Cosmos zasady indeksowania](indexing-policies.md).

## <a name="throughput"></a>Przepływność
<a id="measure-rus"></a>

1. **Pomiar i dostrajania dla żądania niższe jednostek na sekundę użycia**

    Azure DB rozwiązania Cosmos oferuje bogaty zestaw operacji bazy danych, w tym relacyjnych i hierarchicznych zapytania z funkcji UDF, procedury składowane i wyzwalaczy — wszystkie działania w dokumentach w ramach kolekcji bazy danych. Koszt związany z każdej z tych operacji zależy od Procesora, we/wy i pamięci wymaganej do ukończenia tej operacji. Zamiast analiza zasobów i zarządzania nimi sprzętu można traktować jednostki żądań (RU) jako pojedynczy miary dla zasobów wymaganych do wykonywania różnych operacji bazy danych i usługi żądania aplikacji.

    Przepływność zostanie zainicjowana na podstawie liczby [jednostek żądania](request-units.md) ustawić dla każdego kontenera. Zużycie jednostka żądania jest szacowana jako szybkość na sekundę. Aplikacje, które przekroczyć współczynnika jednostki żądania elastycznie ich kontenera są ograniczone, dopóki częstotliwość spadnie poniżej poziomu elastycznie kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność sieci przez Inicjowanie obsługi administracyjnej jednostki dodatkowe żądania. 

    Złożoność kwerendy wpływa na liczbę jednostek żądania są używane dla operacji. Liczba predykatów, rodzaj predykaty, liczba funkcji UDF i rozmiaru zestawu danych źródła wszystkich wpływ kosztów operacji zapytania.

    Aby zmierzyć obciążenie żadnej operacji (Tworzenie, aktualizowanie lub usuwanie), sprawdzić [x-ms żądania — opłata](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) nagłówka do mierzenia liczby jednostek żądania używanych przez te operacje. Można również sprawdzić równoważne właściwości RequestCharge w ResourceResponse<T> lub FeedResponse<T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```             

    Żądanie zwrócony w nagłówku to jest część sieci udostępnionej przepływności. Na przykład jeśli masz 2000 elastycznie RU/s, a jeśli poprzednie zapytanie zwraca 1000 dokumentów o rozmiarze 1KB, kosztów operacji 1000. Tak w ciągu sekundy, serwer będzie honorować tylko dwa takich żądań przed tempa ograniczania kolejnych żądań. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md) i [Kalkulator jednostki żądania](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Współczynnik ograniczanie żądań szybkość dojścia za duży**

    Klient próbuje przekracza zarezerwowaną przepływnością dla konta, nie ma bez spadku wydajności na serwerze i nie stosowania przepływności poza poziomem zastrzeżone. Serwer zostanie preemptively zakończyć żądania z RequestRateTooLarge (kod stanu HTTP 429) i zwraca [x-ms ponawiania — po ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) nagłówek wskazujący ilość czasu (w milisekundach), które użytkownik musi czekać przed ponowną próbą wykonania żądanie.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Zestawy SDK wszystkie niejawnie catch tej odpowiedzi, Szanujemy określony serwer ponownych prób po nagłówka i ponów żądanie. Następna ponowna próba powiedzie się, chyba że Twoje konto jest uzyskiwany jednocześnie przez wielu klientów.

    Jeśli masz więcej niż jednego klienta zbiorczo operacyjnego stale powyżej liczby żądań domyślnej liczby ponownych prób obecnie ustawioną 9 wewnętrznie przez klienta mogą być niewystarczające; w takim przypadku klient zgłasza DocumentClientException z kodem stanu 429 do aplikacji. Za pomocą setRetryOptions w wystąpieniu ConnectionPolicy można zmienić domyślną liczbę ponownych prób. Domyślnie DocumentClientException z kodem stanu 429 po skumulowany czas oczekiwania 30 sekund jest zwracany, jeśli żądanie będzie kontynuował pracę nad liczby żądań. Dzieje się tak nawet jeśli bieżąca liczba ponownych prób jest mniejsza od liczby ponownych prób max, jest to wartość domyślną 9 lub wartości zdefiniowanej przez użytkownika.

    Podczas zachowanie zautomatyzowanych ponownych prób pomaga zwiększyć odporność oraz poziom użyteczności dla większości aplikacji, może ona w odds podczas wykonywania testów porównawczych wydajności, szczególnie w przypadku pomiaru opóźnienia.  Opóźnienie obserwowanych klienta zostanie chwilowo wzrastają Jeśli eksperyment trafienia ograniczania serwera i powoduje, że klient zestaw SDK, aby dyskretnie spróbuj ponownie. Aby uniknąć opóźnienia rzędu podczas eksperymenty wydajności, mierzyć opłata zwrócony przez każdej operacji i upewnij się, działania żądań poniżej liczby żądań zastrzeżone. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md).
3. **Projekt dla mniejszych dokumentów dotyczących wyższej przepustowości**

    Rozmiar dokumentu bezpośrednio są korelowane opłata żądania (koszt przetwarzania żądania) dla danej operacji. Operacje na dużych dokumentów koszty były wyższe niż operacje dla małych dokumentów.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat projektowania aplikacji zapewnienia skalowalności i wysokiej wydajności, zobacz [dzielenia na partycje i skalowania w usłudze Azure DB rozwiązania Cosmos](partition-data.md).
