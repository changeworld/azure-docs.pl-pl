---
title: Integracja z usługą Azure Eksplorator danych z usługą Azure Data Factory
description: W tym temacie Integruj Eksplorator danych platformy Azure z usługą Azure Data Factory, aby korzystać z operacji kopiowania, wyszukiwania i poleceń
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 51683e529f832e06efbe8eb71466f3b27d95fcb1
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819141"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integracja Eksplorator danych platformy Azure z usługą Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) to oparta na chmurze usługa integracji danych, która umożliwia integrację różnych magazynów danych i wykonywanie działań na danych. ADF umożliwia tworzenie przepływów pracy opartych na danych na potrzeby organizowania i automatyzowania przenoszenia i przekształcania danych. Usługa Azure Eksplorator danych to jeden z [obsługiwanych magazynów danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) w programie Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Działania Azure Data Factory dla Eksplorator danych platformy Azure

Różne integracje z Azure Data Factory są dostępne dla użytkowników usługi Azure Eksplorator danych:

### <a name="copy-activity"></a>Działanie kopiowania
 
Działanie kopiowania Azure Data Factory służy do przesyłania danych między magazynami danych. Usługa Azure Eksplorator danych jest obsługiwana jako źródło, gdzie dane są kopiowane z usługi Azure Eksplorator danych do dowolnego obsługiwanego magazynu danych i ujścia, gdzie dane są kopiowane z dowolnego obsługiwanego magazynu danych do usługi Azure Eksplorator danych. Aby uzyskać więcej informacji, zobacz [Kopiowanie danych do lub z platformy Azure Eksplorator danych przy użyciu Azure Data Factory](/azure/data-factory/connector-azure-data-explorer). Szczegółowe instrukcje znajdują się w temacie [ładowanie danych z Azure Data Factory do platformy Azure Eksplorator danych](data-factory-load-data.md).
Usługa Azure Eksplorator danych jest obsługiwana przez Azure IR (Integration Runtime), używana podczas kopiowania danych na platformę Azure oraz do samodzielnego udostępniania środowiska IR, używanego podczas kopiowania danych z/do magazynów danych znajdujących się lokalnie lub w sieci z kontrolą dostępu, takich jak Virtual Network platformy Azure. Aby uzyskać więcej informacji, zobacz [Korzystanie z podczerwieni](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Korzystając z działania kopiowania i tworzenia **połączonej usługi** lub **zestawu**danych, wybierz pozycję Magazyn danych **Azure Eksplorator danych (Kusto)** , a nie stary magazyn danych **Kusto**.  

### <a name="lookup-activity"></a>Działanie Lookup
 
Działanie Lookup służy do wykonywania zapytań w usłudze Azure Eksplorator danych. Wynik zapytania zostanie zwrócony jako dane wyjściowe działania Lookup i może być używany w następnym działaniu w potoku, zgodnie z opisem w [dokumentacji wyszukiwania ADF](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Oprócz limitu rozmiaru odpowiedzi wynoszącego 5 000 wierszy i 2 MB, działanie ma również limit czasu zapytania wynoszący 1 godzinę.

### <a name="command-activity"></a>Działanie polecenia

Działanie polecenia umożliwia wykonywanie [poleceń sterowania](/azure/kusto/concepts/#control-commands)Eksplorator danych platformy Azure. W przeciwieństwie do zapytań, polecenia sterujące mogą potencjalnie modyfikować dane lub metadane. Niektóre polecenia sterowania są celem do pozyskiwania danych na platformie Azure Eksplorator danych przy użyciu poleceń takich jak `.ingest`lub `.set-or-append`) lub kopiowania danych z Eksplorator danych platformy Azure do zewnętrznych magazynów danych przy użyciu poleceń takich jak `.export`.
Aby zapoznać się ze szczegółowym opisem działania poleceń, zobacz [używanie Azure Data Factory działania polecenia do uruchamiania poleceń sterowania platformy Azure Eksplorator danych](data-factory-command-activity.md).  Użycie polecenia sterowania do kopiowania danych może czasami być szybsze i tańsze niż działanie kopiowania. Aby określić, kiedy należy użyć działania polecenia zamiast działania kopiowania, zobacz [Zaznaczanie między działaniami kopiowania i poleceń podczas kopiowania danych](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Kopiuj zbiorczo z szablonu bazy danych

[Kopiowanie masowe z bazy danych na platformę Azure Eksplorator danych przy użyciu szablonu Azure Data Factory](data-factory-template.md) jest wstępnie zdefiniowanym potokiem Azure Data Factory. Szablon służy do tworzenia wielu potoków na bazę danych lub na tabelę w celu przyspieszenia kopiowania danych. 

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Wybierz działania kopiowania i Eksplorator danych platformy Azure przy kopiowaniu danych 

Ta sekcja pomoże Ci w wyborze odpowiedniego działania dla potrzeb związanych z kopiowaniem danych.

Podczas kopiowania danych z lub do usługi Azure Eksplorator danych dostępne są dwie opcje w Azure Data Factory:
* Działanie kopiowania.
* Działanie polecenia platformy Azure Eksplorator danych, które wykonuje jeden z poleceń sterowania, które przesyłają dane na platformie Azure Eksplorator danych. 

### <a name="copy-data-from-azure-data-explorer"></a>Kopiowanie danych z usługi Azure Eksplorator danych
  
Dane można kopiować z usługi Azure Eksplorator danych przy użyciu działania kopiowania lub [`.export`](/azure/kusto/management/data-export/) . Polecenie `.export` wykonuje zapytanie, a następnie eksportuje wyniki zapytania. 

Zapoznaj się z poniższą tabelą, aby zapoznać się z porównaniem działania kopiowania i `.export` kopiowania danych z usługi Azure Eksplorator danych.

| | Działanie kopiowania | . Export — polecenie |
|---|---|---|
| **Opis przepływu** | ADF wykonuje zapytanie w Kusto, przetwarza wynik i wysyła je do docelowego magazynu danych. <br>(**ADX > ADF > Magazyn danych ujścia**) | ADF wysyła polecenie sterowania `.export` do usługi Azure Eksplorator danych, które wykonuje polecenie i wysyła dane bezpośrednio do docelowego magazynu danych. <br>(**Magazyn danych ADX > ujścia**) |
| **Obsługiwane docelowe magazyny danych** | Szeroka gama [obsługiwanych magazynów danych](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, obiekt blob platformy Azure, SQL Database |
| **Wydajność** | Centralny | <ul><li>Dystrybuowane (domyślnie), eksportowanie danych z wielu węzłów współbieżnie</li><li>Szybsza i KWS.</li></ul> |
| **Limity serwera** | [Limity zapytań](/azure/kusto/concepts/querylimits) można rozszerzać/wyłączać. Domyślnie zapytania ADF zawierają: <ul><li>Limit rozmiaru wynoszący 500 000 rekordów lub 64 MB.</li><li>Limit czasu wynoszący 10 minut.</li><li>`noTruncation` ustawiona na wartość false.</li></ul> | Domyślnie program rozszerza lub wyłącza limity zapytania: <ul><li>Limity rozmiaru są wyłączone.</li><li>Limit czasu serwera jest przedłużony do 1 godziny.</li><li>`MaxMemoryConsumptionPerIterator` i `MaxMemoryConsumptionPerQueryPerNode` są rozszerzone do max (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Jeśli miejsce docelowe kopii jest jednym z magazynów danych obsługiwanych przez polecenie `.export` i jeśli żadna z funkcji działania kopiowania nie jest istotna dla Twoich potrzeb, wybierz polecenie `.export`.

### <a name="copying-data-to-azure-data-explorer"></a>Kopiowanie danych do usługi Azure Eksplorator danych

Dane można kopiować do platformy Azure Eksplorator danych za pomocą działania kopiowania lub poleceń pozyskiwania, takich jak pozyskiwanie [z zapytań](/azure/kusto/management/data-ingestion/ingest-from-query) (`.set-or-append`, `.set-or-replace`, `.set`, `.replace)`i pozyskiwania [z magazynu](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`). 

W poniższej tabeli przedstawiono porównanie działania kopiowania i poleceń pozyskiwania do kopiowania danych do platformy Azure Eksplorator danych.

| | Działanie kopiowania | Pozyskiwanie z zapytania<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Pozyskiwanie z magazynu <br> `.ingest` |
|---|---|---|---|
| **Opis przepływu** | Funkcja ADF pobiera dane z magazynu danych źródłowych, konwertuje je na format tabelaryczny i wymaga zmian w mapowaniu schematu. ADF następnie przekazuje dane do obiektów blob platformy Azure, dzieli je na fragmenty, a następnie pobiera obiekty blob w celu pozyskiwania ich w tabeli ADX. <br> (**Źródłowy magazyn danych > ADF > Azure blob > ADX**) | Te polecenia mogą wykonać zapytanie lub polecenie `.show` i pozyskać wyniki zapytania w tabeli (**ADX > ADX**). | To polecenie pozyskuje dane do tabeli przez "ściąganie" danych z co najmniej jednego artefaktu magazynu w chmurze. |
| **Obsługiwane źródłowe magazyny danych** |  [różne opcje](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL (przy użyciu wtyczki sql_request), Cosmos (przy użyciu wtyczki cosmosdb_sql_request) i innych magazynów danych, które udostępniają interfejsy API protokołu HTTP lub Python. | System plików, Azure Blob Storage, ADLS Gen 1, ADLS Gen 2 |
| **Wydajność** | Pozyskiwania są umieszczane w kolejce i zarządzane, co zapewnia pozyskiwanie małych rozmiarów i gwarantuje wysoką dostępność poprzez zapewnienie równoważenia obciążenia, ponownych prób i obsługi błędów. | <ul><li>Te polecenia nie zostały zaprojektowane do importowania danych o dużym nasileniu.</li><li>Działa zgodnie z oczekiwaniami i tańsze. Jednak w przypadku scenariuszy produkcyjnych i szybkości ruchu i rozmiarów danych należy użyć działania kopiowania.</li></ul> |
| **Limity serwera** | <ul><li>Brak limitu rozmiaru.</li><li>Maksymalny limit czasu: 1 godzina na pozyskiwany obiekt BLOB. |<ul><li>Część zapytania zawiera tylko limit rozmiaru, który można pominąć, określając `noTruncation=true`.</li><li>Maksymalny limit czasu: 1 godzina.</li></ul> | <ul><li>Brak limitu rozmiaru.</li><li>Maksymalny limit czasu: 1 godzina.</li></ul>|

> [!TIP]
> * Podczas kopiowania danych z podajnika APD na platformę Azure Eksplorator danych używać poleceń `ingest from query`.  
> * W przypadku dużych zestawów danych (> 1 GB) Użyj działania kopiowania.  

## <a name="required-permissions"></a>Wymagane uprawnienia

W poniższej tabeli wymieniono uprawnienia wymagane do różnych kroków integracji z Azure Data Factory.

| Czynność | Operacja | Minimalny poziom uprawnień | Uwagi |
|---|---|---|---|
| **Tworzenie połączonej usługi** | Nawigacja w bazie danych | *Przeglądarka bazy danych* <br>Zalogowany użytkownik korzystający z funkcji ADF powinien mieć autoryzację do odczytu metadanych bazy danych. | Użytkownik może ręcznie podać nazwę bazy danych. |
| | Testuj połączenie | *monitor bazy danych* lub pozyskiwanie *tabel* <br>Jednostka usługi powinna mieć autoryzację do wykonywania poleceń `.show` poziomu bazy danych lub pozyskiwania poziomów tabeli. | <ul><li>TestConnection weryfikuje połączenie z klastrem, a nie z bazą danych. Może się to powieść, nawet jeśli baza danych nie istnieje.</li><li>Uprawnienia administratora tabeli nie są wystarczające.</li></ul>|
| **Tworzenie zestawu danych** | Nawigacja w tabeli | *Monitor bazy danych* <br>Zalogowany użytkownik korzystający z funkcji ADF musi mieć uprawnienia do wykonywania poleceń `.show` na poziomie bazy danych. | Użytkownik może podać nazwę tabeli ręcznie.|
| **Tworzenie działania zestawu danych** lub **kopiowania** | Podgląd danych | *Przeglądarka bazy danych* <br>Aby można było odczytać metadane bazy danych, jednostka usługi musi mieć autoryzację. | | 
|   | Importuj schemat | *Przeglądarka bazy danych* <br>Aby można było odczytać metadane bazy danych, jednostka usługi musi mieć autoryzację. | Gdy ADX jest źródłem kopii typu tabelarycznego, funkcja ADF automatycznie zaimportuje schemat, nawet jeśli użytkownik nie zaimportował schematu jawnie. |
| **ADX jako ujścia** | Tworzenie mapowania kolumn według nazwy | *Monitor bazy danych* <br>Jednostka usługi musi mieć autoryzację do wykonywania poleceń `.show` na poziomie bazy danych. | <ul><li>Wszystkie operacje wymagane będą współdziałać z pozyskiwaniem *tabeli*.</li><li> Niektóre operacje opcjonalne mogą zakończyć się niepowodzeniem.</li></ul> |
|   | <ul><li>Tworzenie mapowania woluminów CSV w tabeli</li><li>Porzuć mapowanie</li></ul>| pozyskiwanie *tabeli* lub *administrator bazy danych* <br>Aby można było wprowadzać zmiany w tabeli, jednostka usługi musi mieć autoryzację. | |
|   | Pobieranie danych | pozyskiwanie *tabeli* lub *administrator bazy danych* <br>Aby można było wprowadzać zmiany w tabeli, jednostka usługi musi mieć autoryzację. | | 
| **ADX jako źródło** | Wykonaj zapytanie | *Przeglądarka bazy danych* <br>Aby można było odczytać metadane bazy danych, jednostka usługi musi mieć autoryzację. | |
| **Kusto — polecenie** | | Zgodnie z poziomem uprawnień każdego polecenia. |

## <a name="performance"></a>Wydajność 

Jeśli usługa Azure Eksplorator danych jest źródłem i używasz działania Lookup, Copy lub Command, które zawiera zapytanie, w którym zapoznaj się z [najlepszymi rozwiązaniami](/azure/kusto/query/best-practices) dotyczącymi wydajności i [dokumentacją ADF dla działania kopiowania](/azure/data-factory/copy-activity-performance).
  
Ta sekcja dotyczy korzystania z działania kopiowania w przypadku, gdy usługa Azure Eksplorator danych to ujścia. Szacowana przepływność dla usługi Azure Eksplorator danych sink to 11-13 MB/s. Poniższa tabela zawiera szczegółowe informacje o parametrach wpływających na wydajność usługi Azure Eksplorator danych sink.

| Parametr | Uwagi |
|---|---|
| **Elementy sąsiedztwo geograficzne** | Umieść wszystkie składniki w tym samym regionie:<ul><li>magazyny danych źródłowych i ujścia.</li><li>Środowisko Integration Runtime.</li><li>Klaster ADX.</li></ul>Upewnij się, że co najmniej środowisko uruchomieniowe Integration znajduje się w tym samym regionie co klaster ADX. |
| **Liczba DIUs** | 1 maszyna wirtualna na każdy 4 DIUs używany przez ADF. <br>Zwiększenie DIUs będzie przydatne tylko wtedy, gdy źródłem jest magazyn oparty na plikach z wieloma plikami. Każda maszyna wirtualna będzie przetwarzać inny plik równolegle. W związku z tym kopiowanie pojedynczego dużego pliku będzie miało wyższy czas oczekiwania niż kopiowanie wielu mniejszych plików.|
|**Ilość i jednostka SKU klastra ADX** | Duża liczba węzłów ADX zwiększy czas przetwarzania pozyskiwania.|
| **Równoległości** | Aby skopiować bardzo dużą ilość danych z bazy danych, Podziel dane na partycje, a następnie użyj pętli ForEach, która kopiuje każdą partycję równolegle, lub Użyj [kopii zbiorczej z bazy danych do szablonu usługi Azure Eksplorator danych](data-factory-template.md). Uwaga: **ustawienia** > **stopień równoległości** działania kopiowania nie mają znaczenia dla ADX. |
| **Złożoność przetwarzania danych** | Opóźnienie zależy od formatu pliku źródłowego, mapowania kolumn i kompresji.|
| **Maszyna wirtualna, na której działa środowisko Integration Runtime** | <ul><li>W przypadku kopii na platformie Azure nie można zmieniać maszyn wirtualnych i jednostek SKU komputera.</li><li> W przypadku funkcji premium na platformie Azure należy określić, że maszyna wirtualna obsługująca własne środowisko IR jest wystarczająco mocna.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Porady i typowe pułapek

### <a name="monitor-activity-progress"></a>Monitorowanie postępu aktywności

* Podczas monitorowania postępu działania Właściwość *zapisywana dane* może być znacznie większa niż Właściwość *odczytywania danych* , ponieważ *Odczyt danych* jest obliczany zgodnie z rozmiarem pliku binarnego, podczas gdy *zapisywane dane* są obliczane zgodnie z rozmiarem w pamięci, po deserializacji i dekompresji danych.

* Podczas monitorowania postępu działania można zobaczyć, że dane są zapisywane w usłudze Azure Eksplorator danych sink. Podczas wykonywania zapytania dotyczącego tabeli Eksplorator danych platformy Azure zobaczysz, że dane nie dotarły. Wynika to z faktu, że podczas kopiowania na platformę Azure Eksplorator danych istnieją dwa etapy. 
    * Pierwszy etap odczytuje dane źródłowe, dzieli je na fragmenty 900-MB i przekazuje każdy fragment do obiektu blob platformy Azure. Pierwszy etap jest widoczny w widoku postępu działania dotyczącego ADF. 
    * Drugi etap rozpoczyna się po przesłaniu wszystkich danych do obiektów blob platformy Azure. Węzły aparatu usługi Azure Eksplorator danych pobierają obiekty blob i pozyskają dane do tabeli sink. Dane są następnie widoczne w tabeli Eksplorator danych platformy Azure.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Nie można pozyskać plików CSV z powodu nieprawidłowego ucieczki

Usługa Azure Eksplorator danych oczekuje, że pliki CSV mają być wyrównane ze [specyfikacją RFC 4180](https://www.ietf.org/rfc/rfc4180.txt).
Oczekuje:
* Pola, które zawierają znaki wymagające ucieczki (takie jak "i nowe linie), powinny zaczynać i kończyć się znakiem **"** , bez odstępów. Wszystkie **znaki** *w* polu są wyprowadzane przy użyciu podwójnego znaku **"** **" (""** ). Na przykład _"Witaj," World "" "_ jest prawidłowym plikiem CSV z pojedynczym rekordem zawierającym pojedynczą kolumnę lub pole z zawartością _Hello" World "_ .
* Wszystkie rekordy w pliku muszą mieć taką samą liczbę kolumn i pól.

Azure Data Factory umożliwia znak ukośnika odwrotnego (ucieczki). Jeśli wygenerujesz plik CSV z ukośnikiem odwrotnym przy użyciu Azure Data Factory, pozyskiwanie pliku do usługi Azure Eksplorator danych zakończy się niepowodzeniem.

#### <a name="example"></a>Przykład

Następujące wartości tekstowe: Hello, "World"<br/>
DOMYŚLNA ABC<br/>
"ABC\D" EF<br/>
"ABC DEF<br/>

Powinien pojawić się w prawidłowym pliku CSV w następujący sposób: "Witaj," World "" "<br/>
"ABC DEF"<br/>
"" "WARTOŚĆ ABC DEF"<br/>
"" "ABC\D" "EF"<br/>

Używając domyślnego znaku ucieczki (ukośnik odwrotny), następujący wolumin CSV nie będzie działał z platformą Azure Eksplorator danych: "Hello, \"świecie\""<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Zagnieżdżone obiekty JSON

Podczas kopiowania pliku JSON do usługi Azure Eksplorator danych należy pamiętać, że:
* Tablice nie są obsługiwane.
* Jeśli struktura JSON zawiera typy danych obiektów, Azure Data Factory spłaszcza elementy podrzędne obiektu i spróbuje zmapować każdy element podrzędny do innej kolumny w tabeli Eksplorator danych platformy Azure. Jeśli chcesz, aby cały obiekt obiektu był mapowany do pojedynczej kolumny w usłudze Azure Eksplorator danych:
    * Pozyskanie całego wiersza JSON w jednej kolumnie dynamicznej na platformie Azure Eksplorator danych.
    * Ręcznie Edytuj definicję potoku przy użyciu edytora JSON Azure Data Factory. W **mapowaniu**
       * Usuń wiele mapowań, które zostały utworzone dla każdego elementu podrzędnego, i Dodaj pojedyncze mapowanie, które mapuje typ obiektu do kolumny tabeli.
       * Po zamykającym nawiasie kwadratowym Dodaj przecinek, po którym następuje:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Określ AdditionalProperties podczas kopiowania na platformę Azure Eksplorator danych

> [!NOTE]
> Ta funkcja jest obecnie dostępna przez ręczną edycję ładunku JSON. 

Dodaj pojedynczy wiersz w sekcji "ujścia" działania kopiowania w następujący sposób:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Anulowanie wartości może być kłopotliwe. Użyj poniższego fragmentu kodu jako odwołania:

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

* Dowiedz się [, jak kopiować dane do usługi Azure Eksplorator danych przy użyciu Azure Data Factory](data-factory-load-data.md).
* Dowiedz się więcej o używaniu [szablonu Azure Data Factory do kopiowania zbiorczego z bazy danych na platformę Azure Eksplorator danych](data-factory-template.md).
* Dowiedz się więcej na temat używania [Azure Data Factory działania polecenia do uruchamiania poleceń sterowania platformy Azure Eksplorator danych](data-factory-command-activity.md).
* Dowiedz się więcej na temat [zapytań dotyczących usługi Azure Eksplorator danych](/azure/data-explorer/web-query-data) na potrzeby zapytań dotyczących danych.



