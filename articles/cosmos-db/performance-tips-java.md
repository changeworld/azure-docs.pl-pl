---
title: Azure porady dotyczące wydajności rozwiązania Cosmos DB Java | Dokumentacja firmy Microsoft
description: Dowiedz się więcej opcji konfiguracji klienta w celu poprawy wydajności bazy danych Azure DB rozwiązania Cosmos
keywords: jak poprawić wydajność bazy danych
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: sngun
ms.openlocfilehash: 92a7ed065b2ab29037e8c2467e210e7fd0ba3a07
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34613180"
---
> [!div class="op_single_selector"]
> * [Java (asynchroniczny)](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Porady dotyczące wydajności dla bazy danych Azure rozwiązania Cosmos i Java
Azure DB rozwiązania Cosmos jest szybkie i elastyczne rozproszoną bazę danych, która skaluje bezproblemowo gwarantowane opóźnienia i przepływności. Nie trzeba zmieniać architektura głównych ani pisania złożonego kodu można skalować bazy danych z bazy danych Azure rozwiązania Cosmos. Skalowanie w górę i w dół sprowadza się do jednego wywołania interfejsu API lub [zestawu SDK wywołania metody](set-throughput.md#set-throughput-java). Jednak ponieważ bazy danych Azure rozwiązania Cosmos jest dostępny za pośrednictwem połączeń sieci istnieją optymalizacje po stronie klienta, możesz wprowadzić, aby osiągnąć najwyższą wydajność przy użyciu [zestawu SDK Java SQL](documentdb-sdk-java.md).

Dlatego jeśli "jak poprawić wydajność mojej bazy danych?" należy wziąć pod uwagę następujące opcje:

## <a name="networking"></a>Networking
<a id="direct-connection"></a>

1. **Tryb połączenia: Użyj DirectHttps**

    Jak klient nawiąże połączenie bazy danych Azure rozwiązania Cosmos ma istotny wpływ na wydajność, szczególnie pod względem obserwowanych opóźnienia po stronie klienta. Brak jednego klucza konfiguracji ustawienie dostępne do konfigurowania klienta [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) — [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode).  Dwa ConnectionModes dostępne są następujące:

   1. [Brama (ustawienie domyślne)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.gateway)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.directhttps)

    Tryb bramy jest obsługiwana na wszystkich platformach zestawu SDK i jest skonfigurowana domyślna.  Gdy aplikacja działa w sieci firmowej z ograniczeń zapory strict, brama jest najlepszym rozwiązaniem, ponieważ używa standardowego portu HTTPS i jeden punkt końcowy. Jednak z zależnościami wydajności jest tryb bramy obejmuje przeskoku dodatkowe sieci, za każdym razem, gdy danych jest odczytywanych lub zapisywanych do bazy danych Azure rozwiązania Cosmos. W związku z tym trybie DirectHttps oferuje lepszą wydajność ze względu na mniejszą liczbę przeskoków sieciowych. 

    Zestaw SDK Java używa protokołu HTTPS, jako protokół transportu. Protokół HTTPS korzysta z protokołu SSL dla początkowego uwierzytelniania i szyfrowania ruchu sieciowego. Przy użyciu zestawu SDK Java, tylko port HTTPS 443 musi być otwarty. 

    ConnectionMode skonfigurowano podczas konstruowania obiektu DocumentClient z parametrem ConnectionPolicy. 

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

    ![Ilustracja zasady połączenia bazy danych Azure rozwiązania Cosmos](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Klienci, w tym samym regionie Azure wydajności w ten sposób rozmieszczać**

    Jeśli to możliwe, należy umieścić wszystkie aplikacje wywoływanie bazy danych Azure rozwiązania Cosmos w tym samym regionie co baza danych bazy danych Azure rozwiązania Cosmos. Przybliżony porównanie wywołań do bazy danych Azure rozwiązania Cosmos w ramach tego samego regionu ukończona w ciągu 1 i 2 ms, ale opóźnienie między zachód i wschodniego USA > 50 ms. Tego opóźnienia prawdopodobnie zależy od trasę przez żądanie przesyłanych z klienta do granicy centrum danych Azure do innego żądania. Najniższym opóźnieniu możliwe jest to osiągane przez zapewnienie, że aplikacja wywołująca znajduje się w tym samym regionie Azure jako punkt końcowy elastycznie bazy danych Azure rozwiązania Cosmos. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustracja zasady połączenia bazy danych Azure rozwiązania Cosmos](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Użycie zestawu SDK
1. **Zainstaluj najnowsze zestaw SDK**

    Zestawy SDK Azure rozwiązania Cosmos bazy danych jest stale ulepszana aby zapewnić najlepszą wydajność. Zobacz [zestawu SDK usługi Azure rozwiązania Cosmos DB](documentdb-sdk-java.md) strony, aby określić najbardziej aktualnych zestawu SDK i przejrzyj ulepszenia.
2. **Okres istnienia aplikacji za pomocą klienta pojedyncze bazy danych Azure rozwiązania Cosmos**

    Każdy [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) wystąpienia wątkowo i wykonuje zarządzania wydajność połączenia i adres buforowanie w trybie bezpośredniego. Aby umożliwić zarządzanie połączeniami wydajne i większą wydajność przez DocumentClient, zaleca się używać jednego wystąpienia DocumentClient dla domeny AppDomain przez czas ich istnienia aplikacji.

   <a id="max-connection"></a>
3. **Zwiększ MaxPoolSize na host w trybie bramy**

    Azure DB rozwiązania Cosmos żądania są wykonywane za pośrednictwem protokołu HTTPS/REST w trybie bramy i są poddawane domyślny limit połączeń na nazwę hosta lub adres IP. Konieczne może być równa MaxPoolSize nowszej (200-1000) tak, aby biblioteka klienta może korzystać z wielu równoczesnych połączeń do bazy danych Azure rozwiązania Cosmos. W zestawie SDK Java, wartością domyślną dla [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.getmaxpoolsize) to 100. Użyj [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setmaxpoolsize) Aby zmienić wartość.

4. **Dostrajanie równoległe zapytania dla kolekcji partycjonowanych**

    Azure rozwiązania Cosmos bazy danych SQL Java SDK w wersji 1.9.0 lub nowszym zapytania równoległe pomocy technicznej, które umożliwiają kwerenda dotycząca kolekcji partycjonowanych równoległe (zobacz [Praca z zestawów SDK](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) i pokrewnych [przykłady kodu](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) dla więcej informacji o). Zapytania równoległe są przeznaczone do poprawy opóźnienia zapytania i przepływności za pośrednictwem ich odpowiednika szeregowego.

    () ***dostrajanie setMaxDegreeOfParallelism\:***  równoległe zapytania pracy badając równocześnie wiele partycji. Jednak z poszczególnych kolekcji partycjonowanych pobieranych szeregowo względem zapytania. Tak, użyj [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxdegreeofparallelism) można ustawić liczby partycji ma maksymalną ryzyko uzyskania największą wydajność zapytań, podać inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji setMaxDegreeOfParallelism umożliwia ustawianie dużą liczbą i system wybiera minimum (liczba partycji, dane wejściowe podane przez użytkownika), jako maksymalny stopień równoległości. 

    Należy pamiętać zapytania równoległe tworzy najważniejsze korzyści, jeśli dane jest równomiernie rozłożona wszystkie partycje w odniesieniu do zapytania. Jeśli kolekcja podzielonym na partycje jest podzielona na partycje taki sposób, że wszystkie lub większość danych zwróconych przez kwerendę koncentrują się w kilku partycji (jedną partycję w najgorszym przypadku), a następnie wydajności kwerendy może być wydajność ruchu przez te partycje.

    (b) ***dostrajanie setMaxBufferedItemCount\:***  zapytania równoległe zaprojektowano w celu wstępnie pobrać wyniki podczas bieżącej partii wyników jest przetwarzana przez klienta. Pobierania pomaga w ogólnej poprawy opóźnienia zapytania. setMaxBufferedItemCount ogranicza liczbę wstępnie pobranych wyników. Przez ustawienie [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxbuffereditemcount) oczekiwanej liczby wyników zwróconych (lub większa liczba), umożliwia to zapytania do maksymalnej skorzystają z pobierania.

    Pobierania działa tak samo, niezależnie od MaxDegreeOfParallelism, i istnieje pojedynczy bufor dla danych z wszystkich partycji.  

5. **Implementowanie wycofywania odstępach getRetryAfterInMilliseconds**

    Podczas testowania wydajności, należy zwiększyć obciążenie dopóki mała liczba żądań pobrania ograniczany. Jeśli ograniczany, aplikacja kliencka powinna wycofywania na ograniczania dla interwału ponawiania określić serwer. Przestrzeganie wycofywania zapewnia spędzają na skraca czas oczekiwania między kolejnymi próbami. Obsługa zasad ponawiania znajduje się w wersji 1.8.0 lub nowszym z [zestawu Java SDK](documentdb-sdk-java.md). Aby uzyskać więcej informacji, zobacz [Exceeding zastrzeżone ograniczenia przepływności](request-units.md#RequestRateTooLarge) i [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception.getretryafterinmilliseconds).
6. **Skalowanie w poziomie obciążenia klientami**

    Jeśli testujesz na poziomach wysokiej przepływności (> 50 000 RU/s), może stać się aplikacja kliencka "wąskie gardło" z powodu maszyny nakładanie się na użycie procesora CPU lub sieci. Jeśli osiągnąć tego punktu, można nadal push dodatkowe konto bazy danych Azure rozwiązania Cosmos przez skalowania aplikacji klienta na wielu serwerach.

7. **Użyj adresowanie na podstawie nazwy**

    Użyj adresowanie na podstawie nazwy, której łącza mają format `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, zamiast SelfLinks (\_self), które mają format `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` Aby uniknąć pobierania identyfikatory zasobów wszystkich zasobów, które są używane do utworzenia łącza. Ponadto zgodnie z tych zasobów uzyskać odtworzone (prawdopodobnie w tej samej nazwy), buforowanie tych może nie pomocy.

   <a id="tune-page-size"></a>
8. **Dostosuj rozmiar strony dla źródła danych zapytania/odczytu w celu poprawy wydajności**

    Podczas wykonywania masowego odczytu dokumentów za pomocą odczytu źródła funkcji (na przykład [readDocuments]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocuments#com_microsoft_azure_documentdb__document_client_readDocuments_String_FeedOptions_c) lub podczas wystawiania zapytanie SQL, wyniki są zwracane w sposób segmentu, jeśli zestaw wyników jest zbyt duży. Domyślnie są zwracane w fragmentów 100 elementów lub 1 MB, jednego z tych limitów trafień pierwszej.

    Aby zmniejszyć liczbę sieci przekazywanych wymagany do pobrania wszystkich odpowiednich wyników, można zwiększyć za pomocą rozmiar strony [x-ms-max elementu count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) nagłówek żądania do maksymalnie 1000. W przypadkach, gdy konieczne jest wyświetlenie tylko kilka wyniki, na przykład jeśli użytkownik interfejsu lub aplikacji interfejsu API zwraca tylko 10 powoduje przez czas, można także zmniejszyć rozmiar strony do 10 do zmniejszenia przepustowości używane dla operacji odczytu i zapytań.

    Można również ustawić przy użyciu rozmiaru strony [metody setPageSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options_base.setpagesize#com_microsoft_azure_documentdb__feed_options_base_setPageSize_Integer).

## <a name="indexing-policy"></a>Zasady indeksowania
 
1. **Wyklucz ścieżki nieużywane indeksowania dla szybsze zapisy**

    Zasady indeksowania Azure DB rozwiązania Cosmos pozwala na określenie ścieżek dokumentu do dołączania lub wykluczania indeksowania dzięki wykorzystaniu indeksowania ścieżki ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setincludedpaths) i [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setexcludedpaths)). Użycie indeksowania ścieżki zaoferować zapisu lepszą wydajność i dolnym indeksu magazynu w scenariuszach, w których wzorców zapytań są znane wcześniej, jak koszty indeksowania bezpośrednio skorelowanych liczby unikatowych ścieżki zaindeksowane.  Na przykład poniższy kod przedstawia sposób wykluczyć całą sekcję dokumentów () poddrzewo) z indeksowania przy użyciu "*" symboli wieloznacznych.

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

    Do mierzenia obciążenie wszelkie operacje (Tworzenie, aktualizowanie lub usuwanie), sprawdzić [x-ms żądania — opłata](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) nagłówka (lub równoważne właściwości RequestCharge w [ResourceResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource_response) lub [FeedResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response) do mierzenia liczby jednostek żądania używanych przez te operacje.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Żądanie zwrócony w nagłówku to jest część sieci udostępnionej przepływności. Na przykład jeśli masz 2000 elastycznie RU/s, a jeśli poprzednie zapytanie zwraca 1000 dokumentów o rozmiarze 1KB, kosztów operacji 1000. Tak w ciągu sekundy, serwer będzie honorować tylko dwa takich żądań przed ograniczania kolejnych żądań. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md) i [Kalkulator jednostki żądania](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Współczynnik ograniczanie żądań szybkość dojścia za duży**

    Klient próbuje przekracza zarezerwowaną przepływnością dla konta, nie ma bez spadku wydajności na serwerze i nie stosowania przepływności poza poziomem zastrzeżone. Serwer zostanie preemptively zakończyć żądania z RequestRateTooLarge (kod stanu HTTP 429) i zwraca [x-ms ponawiania — po ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) nagłówek wskazujący ilość czasu (w milisekundach), które użytkownik musi czekać przed ponowną próbą wykonania żądanie.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Zestawy SDK wszystkie niejawnie catch tej odpowiedzi, Szanujemy określony serwer ponownych prób po nagłówka i ponów żądanie. Następna ponowna próba powiedzie się, chyba że Twoje konto jest uzyskiwany jednocześnie przez wielu klientów.

    Jeśli masz więcej niż jednego klienta zbiorczo operacyjnego stale powyżej liczby żądań domyślnej liczby ponownych prób obecnie ustawioną 9 wewnętrznie przez klienta mogą być niewystarczające; w takim przypadku klient zgłasza [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception) ze stanem kodu 429 do aplikacji. Liczby ponownych prób domyślne można zmienić za pomocą [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setretryoptions) na [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) wystąpienia. Domyślnie DocumentClientException z kodem stanu 429 po skumulowany czas oczekiwania 30 sekund jest zwracany, jeśli żądanie będzie kontynuował pracę nad liczby żądań. Dzieje się tak nawet jeśli bieżąca liczba ponownych prób jest mniejsza od liczby ponownych prób max, jest to wartość domyślną 9 lub wartości zdefiniowanej przez użytkownika.

    Podczas zachowanie zautomatyzowanych ponownych prób pomaga zwiększyć odporność oraz poziom użyteczności dla większości aplikacji, może ona w odds podczas wykonywania testów porównawczych wydajności, szczególnie w przypadku pomiaru opóźnienia.  Opóźnienie obserwowanych klienta zostanie chwilowo wzrastają Jeśli eksperyment trafienia ograniczania serwera i powoduje, że klient zestaw SDK, aby dyskretnie spróbuj ponownie. Aby uniknąć opóźnienia rzędu podczas eksperymenty wydajności, mierzyć opłata zwrócony przez każdej operacji i upewnij się, działania żądań poniżej liczby żądań zastrzeżone. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md).
3. **Projekt dla mniejszych dokumentów dotyczących wyższej przepustowości**

    Rozmiar dokumentu bezpośrednio są korelowane opłata żądania (koszt przetwarzania żądania) dla danej operacji. Operacje na dużych dokumentów koszty były wyższe niż operacje dla małych dokumentów.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat projektowania aplikacji zapewnienia skalowalności i wysokiej wydajności, zobacz [dzielenia na partycje i skalowania w usłudze Azure DB rozwiązania Cosmos](partition-data.md).
