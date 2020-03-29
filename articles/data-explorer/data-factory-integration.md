---
title: Integracja usługi Azure Data Explorer z usługą Azure Data Factory
description: W tym temacie zintegruj Eksploratora danych platformy Azure z usługą Azure Data Factory w celu użycia działań kopiowania, wyszukiwania i poleceń
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293627"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integracja eksploratora danych platformy Azure z fabryką danych platformy Azure

[Usługa Azure Data Factory](/azure/data-factory/) (ADF) to usługa integracji danych oparta na chmurze, która umożliwia integrację różnych magazynów danych i wykonywanie działań na danych. ADF umożliwia tworzenie opartych na danych przepływów pracy do organizowania i automatyzacji przenoszenia danych i przekształcania danych. Usługa Azure Data Explorer jest jednym z [obsługiwanych magazynów danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) w usłudze Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Działania usługi Azure Data Factory dla Eksploratora danych platformy Azure

Różne integracje z usługą Azure Data Factory są dostępne dla użytkowników Usługi Azure Data Explorer:

### <a name="copy-activity"></a>Działanie kopiowania
 
Działanie kopiowania usługi Azure Data Factory copy służy do przesyłania danych między magazynami danych. Usługa Azure Data Explorer jest obsługiwana jako źródło, w którym dane są kopiowane z Usługi Azure Data Explorer do dowolnego obsługiwanego magazynu danych i zlewu, w którym dane są kopiowane z dowolnego obsługiwanego magazynu danych do Eksploratora danych platformy Azure. Aby uzyskać więcej informacji, zobacz [kopiowanie danych do lub z Usługi Azure Data Explorer przy użyciu usługi Azure Data Factory](/azure/data-factory/connector-azure-data-explorer). oraz szczegółowe informacje na temat szczegółów można [wyświetlić dane dotyczące ładowania danych z usługi Azure Data Factory do usługi Azure Data Explorer.](data-factory-load-data.md)
Usługa Azure Data Explorer jest obsługiwana przez usługę Azure IR (Integration Runtime), używane podczas kopiowania danych na platformie Azure i samodzielnego hostowania środowiska IR, używanego podczas kopiowania danych z/do magazynów danych znajdujących się lokalnie lub w sieci z kontrolą dostępu, takich jak sieć wirtualna platformy Azure. Aby uzyskać więcej informacji, zobacz, [które](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Podczas korzystania z działania kopiowania i tworzenia **połączonej usługi** lub **zestawu danych**wybierz magazyn danych Azure Data **Explorer (Kusto),** a nie stary magazyn danych **Kusto**.  

### <a name="lookup-activity"></a>Działanie odnośnika
 
Działanie odnośnika służy do wykonywania zapytań w Eksploratorze danych platformy Azure. Wynik kwerendy zostanie zwrócony jako dane wyjściowe działania odnośnika i może być używany w następnym działaniu w potoku, zgodnie z opisem w [dokumentacji wyszukiwania ADF](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Oprócz limitu rozmiaru odpowiedzi 5000 wierszy i 2 MB, działanie ma również limit czasu kwerendy 1 godzinę.

### <a name="command-activity"></a>Działanie polecenia

Działanie Command umożliwia wykonywanie [poleceń sterujących](/azure/kusto/concepts/#control-commands)usługi Azure Data Explorer . W przeciwieństwie do kwerend polecenia kontroli można potencjalnie modyfikować dane lub metadane. Niektóre polecenia sterujące są przeznaczone do pozyskiwania danych do Usługi Azure `.ingest`Data `.set-or-append`Explorer, przy użyciu poleceń, takich jak lub `.export`) lub kopiowania danych z Usługi Azure Data Explorer do zewnętrznych magazynów danych przy użyciu poleceń, takich jak .
Aby uzyskać szczegółowe informacje na temat działania polecenia, zobacz [uruchamianie poleceń sterujących usługi Azure Data Factory za pomocą działania polecenia usługi Azure Data Factory.](data-factory-command-activity.md)  Za pomocą polecenia formantu do kopiowania danych może czasami być szybszą i tańszą opcją niż działanie Kopiowanie. Aby określić, kiedy używać działania Polecenia w porównaniu z działaniem Kopiowanie, zobacz [wybieranie działań Kopiowania i polecenia podczas kopiowania danych](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Kopiowanie zbiorczo z szablonu bazy danych

[Kopiowanie zbiorczo z bazy danych do Usługi Azure Data Explorer przy użyciu szablonu usługi Azure Data Factory](data-factory-template.md) jest wstępnie zdefiniowany potok usługi Azure Data Factory. Szablon jest używany do tworzenia wielu potoków na bazę danych lub na tabelę w celu szybszego kopiowania danych. 

### <a name="mapping-data-flows"></a>Przepływy danych mapowania 

[Przepływy danych mapowania usługi Azure Data Factory](/azure/data-factory/concepts-data-flow-overview) to wizualnie zaprojektowane przekształcenia danych, które umożliwiają inżynierom danych opracowanie logiki transformacji danych graficznych bez pisania kodu. Aby utworzyć przepływ danych i pozyskiwania danych do Usługi Azure Data Explorer, należy użyć następującej metody:

1. Utwórz [przepływ danych mapowania](/azure/data-factory/data-flow-create).
1. [Eksportuj dane do obiektu Blob platformy Azure](/azure/data-factory/data-flow-sink). 
1. Zdefiniuj działanie [kopiowania usługi Event Grid](/azure/data-explorer/ingest-data-event-grid) lub [ADF](/azure/data-explorer/data-factory-load-data) w celu pozyskiwania danych do Eksploratora danych platformy Azure.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Wybieranie między działaniami polecenia kopiowania i eksploratora danych platformy Azure podczas kopiowania danych 

Ta sekcja pomoże Ci w wyborze właściwej aktywności dla twoich potrzeb kopiowania danych.

Podczas kopiowania danych z lub do Usługi Azure Data Explorer istnieją dwie dostępne opcje w usłudze Azure Data Factory:
* Kopiowanie aktywności.
* Działanie polecenia usługi Azure Data Explorer, które wykonuje jedno z poleceń sterujących, które przesyłają dane w Eksploratorze danych platformy Azure. 

### <a name="copy-data-from-azure-data-explorer"></a>Kopiowanie danych z Eksploratora danych platformy Azure
  
Dane z usługi Azure Data Explorer można [`.export`](/azure/kusto/management/data-export/) skopiować za pomocą działania kopiowania lub polecenia. Polecenie `.export` wykonuje kwerendę, a następnie eksportuje wyniki kwerendy. 

Zobacz poniższą tabelę do porównania `.export` działania kopiowania i polecenia kopiowania danych z Usługi Azure Data Explorer.

| | Działanie kopiowania | .export, polecenie |
|---|---|---|
| **Opis przepływu** | ADF wykonuje kwerendę na Kusto, przetwarza wynik i wysyła go do docelowego magazynu danych. <br>**(ADX > ADF > magazyn danych ujścia)** | Usługa ADF `.export` wysyła polecenie sterujące do usługi Azure Data Explorer, która wykonuje polecenie i wysyła dane bezpośrednio do docelowego magazynu danych. <br>**(AdX > magazyn danych ujścia)** |
| **Obsługiwane docelowe magazyny danych** | Szeroka gama [obsługiwanych magazynów danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Azure Blob, baza danych SQL |
| **Wydajność** | Scentralizowane | <ul><li>Rozproszone (domyślnie), eksportowanie danych z wielu węzłów jednocześnie</li><li>Szybszy i wydajny COGS.</li></ul> |
| **Limity serwerów** | [Limity zapytań](/azure/kusto/concepts/querylimits) można rozszerzyć/wyłączyć. Domyślnie kwerendy ADF zawierają: <ul><li>Limit rozmiaru 500 000 rekordów lub 64 MB.</li><li>Limit czasu 10 minut.</li><li>`noTruncation`ustawiona na false.</li></ul> | Domyślnie rozszerza lub wyłącza limity kwerend: <ul><li>Limity rozmiaru są wyłączone.</li><li>Limit czasu serwera jest wydłużony do 1 godziny.</li><li>`MaxMemoryConsumptionPerIterator`i `MaxMemoryConsumptionPerQueryPerNode` są rozszerzone do max (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Jeśli miejsce docelowe kopiowania jest jednym `.export` z magazynów danych obsługiwanych przez polecenie, a żadna z funkcji działania kopiowania nie ma kluczowego znaczenia dla Twoich potrzeb, wybierz `.export` polecenie.

### <a name="copying-data-to-azure-data-explorer"></a>Kopiowanie danych do Eksploratora danych platformy Azure

Dane można kopiować do Eksploratora danych platformy Azure przy użyciu poleceń działania `.set-or-replace` `.set`kopiowania lub pozyskiwania, takich jak pozyskiwania [z kwerendy](/azure/kusto/management/data-ingestion/ingest-from-query) (`.set-or-append`, , , `.replace)`, i [pozyskiwania z magazynu](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`). 

Zobacz poniższą tabelę do porównania działania kopiowania i pozyskiwania poleceń do kopiowania danych do usługi Azure Data Explorer.

| | Działanie kopiowania | Pozyskiwania z kwerendy<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Połknienie z magazynu <br> `.ingest` |
|---|---|---|---|
| **Opis przepływu** | ADF pobiera dane z magazynu danych źródłowych, konwertuje je na format tabelaryczny i wykonuje wymagane zmiany mapowania schematu. Następnie usługa ADF przekazuje dane do obiektów blob platformy Azure, dzieli je na fragmenty, a następnie pobiera obiekty blob, aby pobrać je do tabeli ADX. <br> **(Źródłowy magazyn danych > > obiektów blob platformy Azure > ADX)** | Polecenia te mogą wykonywać kwerendę lub `.show` polecenie i wysuwania wyników kwerendy do tabeli (**ADX > ADX**). | To polecenie posuwa dane do tabeli przez "wyciąganie" danych z jednego lub więcej artefaktów magazynu w chmurze. |
| **Obsługiwane źródłowe magazyny danych** |  [różnorodność opcji](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL (przy użyciu wtyczki sql_request), Cosmos (przy użyciu wtyczki cosmosdb_sql_request) i wszelkie inne magazyny danych, który zapewnia interfejsy API HTTP lub Python. | System plików, magazyn obiektów blob platformy Azure, ADLS Gen 1, ADLS Gen 2 |
| **Wydajność** | Połknienia są umieszczane w kolejce i zarządzane, co zapewnia małe rozmiary pozyskiwania i zapewnia wysoką dostępność, zapewniając równoważenie obciążenia, ponownych prób i obsługi błędów. | <ul><li>Te polecenia nie zostały zaprojektowane do importowania danych o dużej objętości.</li><li>Działa zgodnie z oczekiwaniami i taniej. Jednak w scenariuszach produkcyjnych i gdy natężenie ruchu i rozmiary danych są duże, użyj działania Kopiowanie.</li></ul> |
| **Limity serwera** | <ul><li>Brak limitu rozmiaru.</li><li>Maksymalny limit czasu: 1 godzina na pochłonięty obiekt blob. |<ul><li>W części kwerendy jest tylko limit rozmiaru, który można `noTruncation=true`pominąć, określając opcję .</li><li>Maksymalny limit czasu: 1 godzina.</li></ul> | <ul><li>Brak limitu rozmiaru.</li><li>Maksymalny limit czasu: 1 godzina.</li></ul>|

> [!TIP]
> * Podczas kopiowania danych z podajnika `ingest from query` ADF do usługi Azure Data Explorer użyj poleceń.  
> * W przypadku dużych zestawów danych (>1 GB) użyj działania Kopiowanie.  

## <a name="required-permissions"></a>Wymagane uprawnienia

W poniższej tabeli wymieniono wymagane uprawnienia dla różnych kroków integracji z usługą Azure Data Factory.

| Krok | Operacja | Minimalny poziom uprawnień | Uwagi |
|---|---|---|---|
| **Tworzenie usługi połączonej** | Nawigacja w bazie danych | *przeglądarka baz danych* <br>Zalogowany użytkownik korzystający z usługi ADF powinien być upoważniony do odczytywania metadanych bazy danych. | Użytkownik może podać nazwę bazy danych ręcznie. |
| | Testuj połączenie | *monitor bazy danych* lub *ingestor tabeli* <br>Podmiot usługi powinien być autoryzowany `.show` do wykonywania poleceń na poziomie bazy danych lub pozyskiwania na poziomie tabeli. | <ul><li>TestConnection weryfikuje połączenie z klastrem, a nie z bazą danych. Może się powieść, nawet jeśli baza danych nie istnieje.</li><li>Uprawnienia administratora tabeli nie są wystarczające.</li></ul>|
| **Tworzenie zestawu danych** | Nawigacja po tabeli | *monitor bazy danych* <br>Zalogowany użytkownik przy użyciu podajnika ADF `.show` musi być autoryzowany do wykonywania poleceń na poziomie bazy danych. | Użytkownik może podać nazwę tabeli ręcznie.|
| **Tworzenie zestawu danych** lub **działania kopiowania** | Podgląd danych | *przeglądarka baz danych* <br>Podmiot usługi musi być autoryzowany do odczytywania metadanych bazy danych. | | 
|   | Schemat importu | *przeglądarka baz danych* <br>Podmiot usługi musi być autoryzowany do odczytywania metadanych bazy danych. | Gdy ADX jest źródłem kopii tabelarycznej do tabelarycznej, ADF zaimportuje schemat automatycznie, nawet jeśli użytkownik nie zaimportować schematu jawnie. |
| **ADX jako zlew** | Tworzenie mapowania kolumn według nazwy | *monitor bazy danych* <br>Podmiot zabezpieczeń usługi musi być `.show` autoryzowany do wykonywania poleceń na poziomie bazy danych. | <ul><li>Wszystkie obowiązkowe operacje będą działać z *ingestor tabeli*.</li><li> Niektóre operacje opcjonalne mogą zakończyć się niepowodzeniem.</li></ul> |
|   | <ul><li>Tworzenie mapowania CSV w tabeli</li><li>Upuść mapowanie</li></ul>| *ingestor tabeli* lub *administrator bazy danych* <br>Podmiot obsługujący musi być upoważniony do wprowadzania zmian w tabeli. | |
|   | Pozyskiwanie danych | *ingestor tabeli* lub *administrator bazy danych* <br>Podmiot obsługujący musi być upoważniony do wprowadzania zmian w tabeli. | | 
| **ADX jako źródło** | Wykonanie kwerendy | *przeglądarka baz danych* <br>Podmiot usługi musi być autoryzowany do odczytywania metadanych bazy danych. | |
| **Komenda Kusto** | | Zgodnie z poziomem uprawnień każdego polecenia. |

## <a name="performance"></a>Wydajność 

Jeśli Źródłem jest Eksplorator danych platformy Azure i używane jest działanie odnośnika, kopiowania lub polecenia zawierające kwerendę, w której zapoznaj się z [najlepszymi rozwiązaniami kwerendy](/azure/kusto/query/best-practices) w celu uzyskania informacji o wydajności i [dokumentacji usługi ADF dla działania kopiowania](/azure/data-factory/copy-activity-performance).
  
Ta sekcja dotyczy użycia działania kopiowania, gdzie Usługa Azure Data Explorer jest ujście. Szacowana przepływność dla ujścia usługi Azure Data Explorer wynosi 11-13 MB/s. W poniższej tabeli opisano parametry wpływające na wydajność ujścia usługi Azure Data Explorer.

| Parametr | Uwagi |
|---|---|
| **Komponenty bliskość geograficzna** | Umieść wszystkie komponenty w tym samym regionie:<ul><li>magazynów danych źródłowych i zlewów.</li><li>Środowisko uruchomieniowe integracji podajnika ADF.</li><li>Klaster ADX.</li></ul>Upewnij się, że co najmniej środowisko wykonawcze integracji znajduje się w tym samym regionie co klaster ADX. |
| **Liczba diu** | 1 maszyna wirtualna na każde 4 diu używane przez ADF. <br>Zwiększenie diu pomoże tylko wtedy, gdy źródłem jest magazyn oparty na plikach z wieloma plikami. Każda maszyna wirtualna będzie przetwarzać inny plik równolegle. W związku z tym kopiowanie pojedynczego dużego pliku będzie miało większe opóźnienie niż kopiowanie wielu mniejszych plików.|
|**Kwota i jednostka SKU klastra usługi ADX** | Duża liczba węzłów ADX zwiększy czas przetwarzania pozyskiwania.|
| **Równoległości prostych** | Aby skopiować bardzo dużą ilość danych z bazy danych, partycjonuj dane, a następnie użyj ForEach pętli, która kopiuje każdą partycję równolegle lub użyj [kopiowania zbiorczego z bazy danych do szablonu Eksploratora danych Platformy Azure](data-factory-template.md). Uwaga:**Stopień równoległości** **ustawień** > w działaniu Kopiowanie nie jest odpowiedni dla adx. |
| **Złożoność przetwarzania danych** | Opóźnienie różni się w zależności od formatu pliku źródłowego, mapowania kolumn i kompresji.|
| **Maszyna wirtualna z uruchomionym środowiskom wykonywania integracji** | <ul><li>W przypadku kopiowania platformy Azure nie można zmienić maszyn wirtualnych i jednostek SKU maszyn.</li><li> W przypadku kopii na platformie Azure należy określić, że maszyna wirtualna obsługująca samodzielnie hostowane podczerwone urządzenie IR jest wystarczająco silne.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Porady i typowe pułapki

### <a name="monitor-activity-progress"></a>Monitorowanie postępu aktywności

* Podczas monitorowania postępu *działania, Data written* właściwość może być znacznie większa niż *Data read* właściwości, ponieważ *odczyt danych* jest obliczany zgodnie z rozmiarem pliku binarnego, podczas gdy *dane zapisywane* jest obliczana zgodnie z rozmiarem w pamięci, po danych jest de-serializacji i dekompresji.

* Podczas monitorowania postępu działania, można zobaczyć, że dane są zapisywane do ujścia usługi Azure Data Explorer. Podczas wykonywania zapytań z tabeli Eksploratora danych platformy Azure widać, że dane nie dotarły. Jest tak, ponieważ istnieją dwa etapy podczas kopiowania do Usługi Azure Data Explorer. 
    * Pierwszy etap odczytuje dane źródłowe, dzieli je na fragmenty 900 MB i przekazuje każdy fragment do obiektu blob platformy Azure. Pierwszy etap jest widoczny w widoku postępu aktywności ADF. 
    * Drugi etap rozpoczyna się po przekazaniu wszystkich danych do obiektów blob platformy Azure. Węzły aparatu Usługi Azure Data Explorer pobierają obiekty BLOB i pochłoają dane do tabeli ujścia. Dane są następnie widoczne w tabeli Eksploratora danych platformy Azure.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Niepodjęcie zleceń na pliki CSV z powodu niewłaściwej ucieczki

Usługa Azure Data Explorer oczekuje, że pliki CSV będą zgodne z [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt).
Oczekuje:
* Pola zawierające znaki wymagające ucieczki (takie jak " i nowe wiersze) powinny rozpoczynać się i kończyć znakiem **"** bez odstępów. Wszystkie **znaki "** *wewnątrz* pola są unikuszne za pomocą podwójnego znaku **"** **(""**( Na przykład _"Hello, ""World"""_ jest prawidłowym plikiem CSV z pojedynczym rekordem o pojedynczej kolumnie lub polu z zawartością _Hello, "World"_.
* Wszystkie rekordy w pliku muszą mieć taką samą liczbę kolumn i pól.

Usługa Azure Data Factory umożliwia ukośnik wsteczny (ucieczka). Jeśli wygenerujesz plik CSV ze znakiem ukośnika odwrotnego przy użyciu usługi Azure Data Factory, użycie pliku do Usługi Azure Data Explorer zakończy się niepowodzeniem.

#### <a name="example"></a>Przykład

Następujące wartości tekstowe: Hello, "World"<br/>
ABC DEF<br/>
"ABC\D"EF<br/>
"ABC DEF<br/>

Powinien pojawić się w odpowiednim pliku CSV w następujący sposób: "Hello, ""World"""<br/>
"ABC DEF"<br/>
"""ABC DEF"<br/>
""ABC\D""EF"<br/>

Przy użyciu domyślnego znaku ucieczki (ukośnik odwrotny), następujący zestaw CSV nie będzie działać z Eksploratorem danych platformy Azure: "Hello, \"World\""<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Zagnieżdżone obiekty JSON

Podczas kopiowania pliku JSON do Usługi Azure Data Explorer należy pamiętać, że:
* Tablice nie są obsługiwane.
* Jeśli struktura JSON zawiera typy danych obiektów, usługa Azure Data Factory spłaszczy elementy podrzędne obiektu i spróbuje zamapować każdy element podrzędny na inną kolumnę w tabeli Usługi Azure Data Explorer. Jeśli chcesz, aby cały element obiektu był mapowany na jedną kolumnę w Eksploratorze danych platformy Azure:
    * Ponajmowanie całego wiersza JSON do pojedynczej kolumny dynamicznej w Eksploratorze danych platformy Azure.
    * Ręcznie edytuj definicję potoku przy użyciu edytora JSON usługi Azure Data Factory. W **mapowaniach**
       * Usuń wiele mapowań utworzonych dla każdego elementu podrzędnego i dodaj pojedyncze mapowanie, które mapuje typ obiektu do kolumny tabeli.
       * Po zamknięciu nawiasu kwadratowego dodaj przecinek, po którym następuje:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Określanie właściwości dodatkowych podczas kopiowania do Eksploratora danych platformy Azure

> [!NOTE]
> Ta funkcja jest obecnie dostępna po ręcznej edycji ładunku JSON. 

Dodaj pojedynczy wiersz w sekcji "pochłaniacz" działania kopiowania w następujący sposób:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Ucieczka od wartości może być trudne. Użyj następującego fragmentu kodu jako odwołania:

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

Wydrukowana wartość:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [kopiować dane do Eksploratora danych platformy Azure przy użyciu usługi Azure Data Factory](data-factory-load-data.md).
* Dowiedz się więcej o używaniu [szablonu usługi Azure Data Factory do kopiowania zbiorczego z bazy danych do Eksploratora danych platformy Azure.](data-factory-template.md)
* Dowiedz się więcej o używaniu [działania polecenia usługi Azure Data Factory do uruchamiania poleceń sterujących usługi Azure Data Explorer](data-factory-command-activity.md).
* Dowiedz się więcej o [kwerendach usługi Azure Data Explorer](/azure/data-explorer/web-query-data) dotyczących zapytań dotyczących danych.



