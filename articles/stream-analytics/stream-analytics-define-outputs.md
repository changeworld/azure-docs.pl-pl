---
title: Opis produktów z usługi Azure Stream Analytics
description: W tym artykule opisano opcje danych wyjściowych dostępne w usłudze Azure Stream Analytics, w tym usługę Power BI do analizy wyników.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 4517f85fae278bd8bc15a9586d9dc0202e7dfe56
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475231"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Opis produktów z usługi Azure Stream Analytics

W tym artykule opisano typy danych wyjściowych dostępnych dla zadania usługi Azure Stream Analytics. Dane wyjściowe umożliwiają przechowywanie i zapisywanie wyników zadania usługi Stream Analytics. Korzystając z danych wyjściowych, można wykonać dalszą analizę biznesową i hurtownię danych.

Podczas projektowania kwerendy usługi Stream Analytics należy zapoznać się z nazwą danych wyjściowych przy użyciu [klauzuli INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Można użyć pojedynczego wyjścia na zadanie lub wiele wyjść na zadanie przesyłania strumieniowego (jeśli są potrzebne), podając wiele into klauzule w kwerendzie.

Aby utworzyć, edytować i przetestować dane wyjściowe usługi Stream Analytics, można użyć [witryny Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), REST [API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)i [Visual Studio](stream-analytics-quick-create-vs.md).

Niektóre typy wyjść obsługują [partycjonowanie.](#partitioning) [Wielkości partii wyjściowej](#output-batch-size) różnią się w celu optymalizacji przepływności.


## <a name="azure-data-lake-storage-gen-1"></a>Usługa Azure Data Lake Storage Gen 1

Usługa Stream Analytics obsługuje [usługę Azure Data Lake Storage Gen 1.](../data-lake-store/data-lake-store-overview.md) Usługa Azure Data Lake Storage to repozytorium hiperskalowe dla obciążeń analitycznych dużych zbiorów danych w całym przedsiębiorstwie. Magazyn usługi Data Lake umożliwia przechowywanie danych o dowolnym rozmiarze, typie i szybkości pozyskiwania w celu analizy operacyjnej i eksploracyjnej. Usługa Stream Analytics musi być autoryzowana do uzyskiwania dostępu do magazynu usługi Data Lake.

Dane usługi Azure Data Lake Storage dane pochodzą z usługi Stream Analytics nie jest obecnie dostępna w chinach 21Vianet platformy Azure i platformy Azure Niemcy (T-Systems International) regionów.

W poniższej tabeli wymieniono nazwy właściwości i ich opisy w celu skonfigurowania danych wyjściowych magazynu usługi Data Lake Storage gen 1.   

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używana w kwerendach do kierowania danych wyjściowych kwerendy do magazynu Usługi Data Lake. |
| Subskrypcja | Subskrypcja zawierająca konto usługi Azure Data Lake Storage. |
| Nazwa konta | Nazwa konta Data Lake Store, na którym wysyłasz dane wyjściowe. Zostanie wyświetlona lista rozwijana kont w sklepie Data Lake Store, które są dostępne w twojej subskrypcji. |
| Wzorzec prefiksu ścieżki | Ścieżka pliku, która jest używana do zapisywania plików w ramach określonego konta Usługi Data Lake Store. Można określić co najmniej jedno wystąpienie zmiennych {date} i {time}:<br /><ul><li>Przykład 1: folder1/logs/{date}/{time}</li><li>Przykład 2: folder1/logs/{date}</li></ul><br />Sygnatura czasowa utworzonej struktury folderu jest zgodna z czasem UTC, a nie czasem lokalnym.<br /><br />Jeśli wzorzec ścieżki pliku nie zawiera ukośnika końcowego (/), ostatni wzorzec w ścieżce pliku jest traktowany jako prefiks nazwy pliku. <br /><br />Nowe pliki są tworzone w następujących okolicznościach:<ul><li>Zmiana schematu wyjściowego</li><li>Zewnętrzne lub wewnętrzne ponowne uruchomienie zadania</li></ul> |
| Format daty | Element opcjonalny. Jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w którym pliki są zorganizowane. Przykład: YYYY/MM/DD |
|Format czasu | Element opcjonalny. Jeśli token czasu jest używany w ścieżce prefiksu, określ format czasu, w którym pliki są zorganizowane. Obecnie jedyną obsługiwaną wartością jest HH. |
| Format serializacji zdarzeń | Format serializacji dla danych wyjściowych. JSON, CSV i Avro są obsługiwane.|
| Kodowanie | Jeśli używasz formatu CSV lub JSON, należy określić kodowanie. UTF-8 jest jedynym obsługiwanym formatem kodowania w tej chwili.|
| Ogranicznik | Dotyczy tylko serializacji CSV. Usługa Stream Analytics obsługuje wiele typowych ograniczników do serializowania danych CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek.|
| Format | Dotyczy tylko serializacji JSON. **Linia oddzielona** określa, że dane wyjściowe są formatowane przez każdy obiekt JSON oddzielony nowym wierszem. W przypadku **wybrania opcji Linia oddzielona**, JSON odczytuje jeden obiekt naraz. Cała zawartość sama w sobie nie byłaby prawidłowym JSON.  **Tablica** określa, że dane wyjściowe są sformatowane jako tablica obiektów JSON. Ta tablica jest zamykana tylko wtedy, gdy zadanie zostanie zatrzymane lub usługa Stream Analytics zostanie przeniesiona do następnego przedziału czasu. Ogólnie rzecz biorąc zaleca się używanie json oddzielone linii, ponieważ nie wymaga żadnej specjalnej obsługi, podczas gdy plik wyjściowy jest nadal zapisywany.|
| Tryb uwierzytelniania | Możesz autoryzować dostęp do konta Data Lake Storage przy użyciu [tokenu Zarządzanej tożsamości](stream-analytics-managed-identities-adls.md) lub użytkownika. Po udzieleniu dostępu można odwołać dostęp, zmieniając hasło konta użytkownika, usuwając dane wyjściowe magazynu usługi Dla tego zadania lub usuwając zadanie usługi Stream Analytics. |

## <a name="sql-database"></a>Baza danych SQL

Usługi [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) można używać jako danych wyjściowych dla danych o charakterze relacyjnego lub aplikacji, które zależą od zawartości hostowanej w relacyjnej bazie danych. Zadania usługi Stream Analytics zapisują do istniejącej tabeli w bazie danych SQL. Schemat tabeli musi dokładnie odpowiadać pola i ich typy w danych wyjściowych zadania. Można również określić [usługi Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) jako dane wyjściowe za pomocą opcji danych wyjściowych bazy danych SQL. Aby dowiedzieć się więcej o sposobach poprawy przepływności zapisu, zobacz stream analytics z usługi Azure SQL Database jako artykuł [wyjściowy.](stream-analytics-sql-output-perf.md)

Można również użyć [wystąpienia zarządzanego usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) jako danych wyjściowych. Musisz [skonfigurować publiczny punkt końcowy w wystąpieniu zarządzanym bazy danych SQL platformy Azure,](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) a następnie ręcznie skonfigurować następujące ustawienia w usłudze Azure Stream Analytics. Maszyna wirtualna platformy Azure z uruchomionym programem SQL Server z dołączoną bazą danych jest również obsługiwana przez ręczne konfigurowanie poniższych ustawień.

W poniższej tabeli wymieniono nazwy właściwości i ich opis do tworzenia danych wyjściowych bazy danych SQL.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w kwerendach do kierowania danych wyjściowych kwerendy do tej bazy danych. |
| baza danych | Nazwa bazy danych, w której wysyłasz dane wyjściowe. |
| Nazwa serwera | Nazwa serwera bazy danych SQL. W przypadku wystąpienia zarządzanego usługi Azure SQL Database wymagane jest określenie portu 3342. Na przykład *plik sampleserver.public.database.windows.net,3342* |
| Nazwa użytkownika | Nazwa użytkownika, która ma dostęp do zapisu do bazy danych. Usługa Stream Analytics obsługuje tylko uwierzytelnianie SQL. |
| Hasło | Hasło do łączenia się z bazą danych. |
| Tabela | Nazwa tabeli, w której jest zapisywane dane wyjściowe. W nazwie tabeli jest rozróżniana wielkość liter. Schemat tej tabeli powinien dokładnie odpowiadać liczbie pól i ich typom generowanym przez dane wyjściowe zadania. |
|Dziedzicz schemat partycji| Opcja dziedziczenia schematu partycjonowania poprzedniego kroku kwerendy, aby włączyć w pełni równoległą topologię z wieloma modułami zapisującymi do tabeli. Aby uzyskać więcej informacji, zobacz [dane wyjściowe usługi Azure Stream Analytics do usługi Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Maksymalna liczba partii| Zalecany górny limit liczby rekordów wysłanych z każdą transakcją wstawiania zbiorczego.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Magazyn obiektów blob i usługa Azure Data Lake Gen2

Usługa Data Lake Storage Gen2 sprawia, że usługa Azure Storage stanowi podstawę do tworzenia jezior danych przedsiębiorstwa na platformie Azure. Zaprojektowany od początku do obsługi wielu petabajtów informacji przy jednoczesnym utrzymaniu setek gigabitów przepustowości, Data Lake Storage Gen2 pozwala łatwo zarządzać ogromnymi ilościami danych. Podstawową częścią usługi Data Lake Storage Gen2 jest dodanie hierarchicznego obszaru nazw do magazynu obiektów Blob.

Usługa Azure Blob storage oferuje ekonomiczne i skalowalne rozwiązanie do przechowywania dużych ilości nieustrukturyzowanych danych w chmurze. Aby uzyskać wprowadzenie do magazynu obiektów Blob i jego użycia, zobacz [Przekazywanie, pobieranie i lista obiektów blob za pomocą witryny Azure portal.](../storage/blobs/storage-quickstart-blobs-portal.md)

W poniższej tabeli wymieniono nazwy właściwości i ich opisy do tworzenia obiektu blob lub danych wyjściowych ADLS Gen2.

| Nazwa właściwości       | Opis                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias danych wyjściowych        | Przyjazna nazwa używana w kwerendach do kierowania danych wyjściowych kwerendy do tego magazynu obiektów blob. |
| Konto magazynu     | Nazwa konta magazynu, na którym wysyłasz dane wyjściowe.               |
| Klucz konta magazynu | Klucz tajny skojarzony z kontem magazynu.                              |
| Kontener magazynowy   | Logiczne grupowanie obiektów blob przechowywanych w usłudze Azure Blob. Podczas przekazywania obiektu blob do usługi obiektów Blob, należy określić kontener dla tego obiektu blob. |
| Wzorzec ścieżki | Element opcjonalny. Wzorzec ścieżki pliku, który jest używany do zapisywania obiektów blob w określonym kontenerze. <br /><br /> We wzorcu ścieżki można użyć jednego lub więcej wystąpień zmiennych daty i godziny, aby określić częstotliwość zapisu obiektów blob: <br /> {date}, {time} <br /><br />Niestandardowe partycjonowanie obiektów blob służy do określania jednej niestandardowej nazwy {field} z danych zdarzeń do partycjonowania obiektów blob. Nazwa pola jest alfanumeryczna i może zawierać spacje, łączniki i podkreślenia. Ograniczenia dotyczące pól niestandardowych są następujące: <ul><li>W nazwach pól nie rozróżnia się wielkość liter. Na przykład usługa nie może rozróżnić kolumny "ID" i kolumny "id".</li><li>Pola zagnieżdżone nie są dozwolone. Zamiast tego użyj aliasu w kwerendzie zadania, aby "spłaszczyć" pole.</li><li>Wyrażenia nie mogą być używane jako nazwa pola.</li></ul> <br />Ta funkcja umożliwia korzystanie z niestandardowych konfiguracji specyfikatora formatu daty/godziny w ścieżce. Niestandardowe formaty daty i godziny muszą być określone po jednym\<w czasie, ujęte w {datetime: specyfikator>} słowa kluczowego. Dopuszczalne wejścia \<dla specyfikatora> to rrrr, MM, M, dd, d, HH, H, mm, m, ss lub s. Słowo kluczowe {datetime:\<specifier>} może być używane wiele razy w ścieżce do tworzenia niestandardowych konfiguracji daty/godziny. <br /><br />Przykłady: <ul><li>Przykład 1: cluster1/logs/{date}/{time}</li><li>Przykład 2: cluster1/logs/{date}</li><li>Przykład 3: cluster1/{client_id}/{date}/{time}</li><li>Przykład 4: cluster1/{datetime:ss}/{myField}, gdzie kwerenda brzmi: SELECT data.myField AS myField FROM Input;</li><li>Przykład 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />Sygnatura czasowa utworzonej struktury folderu jest zgodna z czasem UTC, a nie czasem lokalnym.<br /><br />Nazewnictwo plików używa następującej konwencji: <br /><br />{Wzorzec prefiksu ścieżki}/schemaHashcode_Guid_Number.extension<br /><br />Przykładowe pliki wyjściowe:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Partycjonowanie danych wyjściowych niestandardowych obiektów blob usługi Azure Stream Analytics.](stream-analytics-custom-path-patterns-blob-storage-output.md) |
| Format daty | Element opcjonalny. Jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w którym pliki są zorganizowane. Przykład: YYYY/MM/DD |
| Format czasu | Element opcjonalny. Jeśli token czasu jest używany w ścieżce prefiksu, określ format czasu, w którym pliki są zorganizowane. Obecnie jedyną obsługiwaną wartością jest HH. |
| Format serializacji zdarzeń | Format serializacji dla danych wyjściowych. JSON, CSV, Avro i Parkiet są obsługiwane. |
|Minimalne rzędy (tylko parkiet)|Liczba minimalnych wierszy na partię. W przypadku parkietu każda partia utworzy nowy plik. Bieżąca wartość domyślna to 2000 wierszy, a dozwolona maksymalna to 10 000 wierszy.|
|Maksymalny czas (tylko Parkiet)|Maksymalny czas oczekiwania na partię. Po tym czasie partia zostanie zapisana na wyjściu, nawet jeśli minimalne wymaganie wierszy nie jest spełnione. Bieżąca wartość domyślna to 1 minuta, a dozwolona maksymalna to 2 godziny. Jeśli dane wyjściowe obiektu blob ma częstotliwość wzorca ścieżki, czas oczekiwania nie może być wyższy niż zakres czasu partycji.|
| Kodowanie    | Jeśli używasz formatu CSV lub JSON, należy określić kodowanie. UTF-8 jest jedynym obsługiwanym formatem kodowania w tej chwili. |
| Ogranicznik   | Dotyczy tylko serializacji CSV. Usługa Stream Analytics obsługuje wiele typowych ograniczników do serializowania danych CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek. |
| Format      | Dotyczy tylko serializacji JSON. **Linia oddzielona** określa, że dane wyjściowe są formatowane przez każdy obiekt JSON oddzielony nowym wierszem. W przypadku **wybrania opcji Linia oddzielona**, JSON odczytuje jeden obiekt naraz. Cała zawartość sama w sobie nie byłaby prawidłowym JSON. **Tablica** określa, że dane wyjściowe są sformatowane jako tablica obiektów JSON. Ta tablica jest zamykana tylko wtedy, gdy zadanie zostanie zatrzymane lub usługa Stream Analytics zostanie przeniesiona do następnego przedziału czasu. Ogólnie rzecz biorąc zaleca się używanie json oddzielone linii, ponieważ nie wymaga żadnej specjalnej obsługi, podczas gdy plik wyjściowy jest nadal zapisywany. |

Gdy używasz magazynu obiektów Blob jako danych wyjściowych, nowy plik jest tworzony w obiekcie blob w następujących przypadkach:

* Jeśli plik przekracza maksymalną liczbę dozwolonych bloków (obecnie 50 000). Można osiągnąć maksymalną dozwoloną liczbę bloków bez osiągnięcia maksymalnego dozwolonego rozmiaru obiektu blob. Na przykład jeśli szybkość wyjściowa jest wysoka, można zobaczyć więcej bajtów na blok, a rozmiar pliku jest większy. Jeśli szybkość wyjściowa jest niska, każdy blok ma mniej danych, a rozmiar pliku jest mniejszy.
* Jeśli istnieje zmiana schematu w danych wyjściowych, a format wyjściowy wymaga stałego schematu (CSV i Avro).
* Jeśli zadanie zostanie ponownie uruchomione, zewnętrznie przez użytkownika zatrzymując go i uruchamiając go lub wewnętrznie do konserwacji systemu lub odzyskiwania błędów.
* Jeśli kwerenda jest w pełni podzielony na partycje i tworzony jest nowy plik dla każdej partycji wyjściowej.
* Jeśli użytkownik usunie plik lub kontener konta magazynu.
* Jeśli dane wyjściowe jest czas podzielony na partycje przy użyciu wzorca prefiksu ścieżki, a nowy obiekt blob jest używany, gdy kwerenda przenosi się do następnej godziny.
* Jeśli dane wyjściowe są podzielone na partycje przez pole niestandardowe, a nowy obiekt blob jest tworzony na klucz partycji, jeśli nie istnieje.
* Jeśli dane wyjściowe są podzielone na partycje przez pole niestandardowe, w którym kardynalność klucza partycji przekracza 8000, a nowy obiekt blob jest tworzony na klucz partycji.

## <a name="event-hubs"></a>Event Hubs

[Usługa Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) jest wysoce skalowalne publikowania subskrybowania zdarzenia ingestor. Może zbierać miliony zdarzeń na sekundę. Jednym z zastosowań centrum zdarzeń jako dane wyjściowe jest, gdy dane wyjściowe zadania usługi Stream Analytics staje się dane wejściowe innego zadania przesyłania strumieniowego. Aby uzyskać informacje na temat maksymalnego rozmiaru wiadomości i optymalizacji rozmiaru partii, zobacz sekcję [rozmiar partii wyjściowej.](#output-batch-size)

Potrzebujesz kilku parametrów, aby skonfigurować strumienie danych z centrów zdarzeń jako dane wyjściowe.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używana w kwerendach do kierowania danych wyjściowych kwerendy do tego centrum zdarzeń. |
| Przestrzeń nazw centrum zdarzeń | Kontener dla zestawu jednostek obsługi wiadomości. Podczas tworzenia nowego centrum zdarzeń utworzono również obszar nazw centrum zdarzeń. |
| Nazwa centrum zdarzeń | Nazwa danych wyjściowych centrum zdarzeń. |
| Nazwa zasad Centrum zdarzeń | Zasady dostępu współdzielonego, które można utworzyć na karcie **Konfiguruj** centrum zdarzeń. Każda zasada dostępu współdzielonego ma ustawioną nazwę, uprawnienia i klucze dostępu. |
| Klucz zasad centrum zdarzeń | Klucz dostępu współdzielonego, który służy do uwierzytelniania dostępu do obszaru nazw centrum zdarzeń. |
| Kolumna klucza partycji | Element opcjonalny. Kolumna zawierająca klucz partycji dla danych wyjściowych centrum zdarzeń. |
| Format serializacji zdarzeń | Format serializacji dla danych wyjściowych. JSON, CSV i Avro są obsługiwane. |
| Kodowanie | Dla CSV i JSON UTF-8 jest jedynym obsługiwanym formatem kodowania w tej chwili. |
| Ogranicznik | Dotyczy tylko serializacji CSV. Usługa Stream Analytics obsługuje wiele typowych ograniczników do serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek. |
| Format | Dotyczy tylko serializacji JSON. **Linia oddzielona** określa, że dane wyjściowe są formatowane przez każdy obiekt JSON oddzielony nowym wierszem. W przypadku **wybrania opcji Linia oddzielona**, JSON odczytuje jeden obiekt naraz. Cała zawartość sama w sobie nie byłaby prawidłowym JSON. **Tablica** określa, że dane wyjściowe są sformatowane jako tablica obiektów JSON.  |
| Kolumny właściwości | Element opcjonalny. Kolumny oddzielone przecinkami, które muszą być dołączone jako właściwości użytkownika wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [Niestandardowe właściwości metadanych dla danych wyjściowych](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

Usługi [Power BI](https://powerbi.microsoft.com/) można używać jako danych wyjściowych dla zadania usługi Stream Analytics, aby zapewnić bogate środowisko wizualizacji wyników analizy. Tej funkcji można używać do operacyjnych pulpitów nawigacyjnych, generowania raportów i raportowania opartego na metrykach.

Dane wyjściowe usługi Power BI z usługi Stream Analytics nie są obecnie dostępne w regionach usługi Azure China 21Vianet i Azure Germany (T-Systems International).

W poniższej tabeli wymieniono nazwy właściwości i ich opisy w celu skonfigurowania danych wyjściowych usługi Power BI.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Podaj przyjazną nazwę używaną w kwerendach do kierowania danych wyjściowych zapytania do tego wyjścia usługi Power BI. |
| Grupowanie obszaru roboczego |Aby włączyć udostępnianie danych innym użytkownikom usługi Power BI, możesz wybrać grupy wewnątrz konta usługi Power BI lub wybrać **pozycję Mój obszar roboczy,** jeśli nie chcesz pisać w grupie. Aktualizowanie istniejącej grupy wymaga odnowienia uwierzytelniania usługi Power BI. |
| Nazwa zestawu danych |Podaj nazwę zestawu danych, której chcesz używać danych wyjściowych usługi Power BI. |
| Nazwa tabeli |Podaj nazwę tabeli pod zestawem danych danych wyjściowych usługi Power BI. Obecnie dane wyjściowe usługi Power BI z zadań usługi Stream Analytics mogą mieć tylko jedną tabelę w zestawie danych. |
| Autoryzowanie połączenia | Aby skonfigurować ustawienia danych wyjściowych, musisz autoryzować za pomocą usługi Power BI. Po udzieleniu tego dostępu wyjściowego do pulpitu nawigacyjnego usługi Power BI można odwołać dostęp, zmieniając hasło konta użytkownika, usuwając dane wyjściowe zadania lub usuwając zadanie usługi Stream Analytics. | 

Aby zapoznać się z omówieniem konfigurowania danych wyjściowych i pulpitu nawigacyjnego usługi Power BI, zobacz samouczek [usługi Azure Stream Analytics i power bi.](stream-analytics-power-bi-dashboard.md)

> [!NOTE]
> Nie należy jawnie tworzyć zestawu danych i tabeli na pulpicie nawigacyjnym usługi Power BI. Zestaw danych i tabela są automatycznie wypełniane po uruchomieniu zadania i rozpoczęciu pompowania danych wyjściowych do usługi Power BI. Jeśli kwerenda zadania nie generuje żadnych wyników, zestaw danych i tabela nie są tworzone. Jeśli usługa Power BI miała już zestaw danych i tabelę o takiej samej nazwie jak ta podana w tym zadaniu usługi Stream Analytics, istniejące dane są zastępowane.
>

### <a name="create-a-schema"></a>Tworzenie schematu
Usługa Azure Stream Analytics tworzy zestaw danych usługi Power BI i schemat tabeli dla użytkownika, jeśli jeszcze nie istnieją. We wszystkich innych przypadkach tabela jest aktualizowana o nowe wartości. Obecnie w zestawie danych może istnieć tylko jedna tabela. 

Usługa Power BI korzysta z zasad przechowywania pierwszego w pierwszym, pierwszego wyjścia (FIFO). Dane będą zbierane w tabeli, dopóki nie osiągnie 200 000 wierszy.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Konwertowanie typu danych z usługi Stream Analytics na usługi Power BI
Usługa Azure Stream Analytics aktualizuje model danych dynamicznie w czasie wykonywania, jeśli zmieni się schemat wyjściowy. Zmiany nazwy kolumny, zmiany typu kolumny i dodawanie lub usuwanie kolumn są śledzone.

Ta tabela obejmuje konwersje typów danych z [typów danych usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) do typów [EDM (Power](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)BI Entity Data Model), jeśli zestaw danych i tabela usługi Power BI nie istnieją.

Od Stream Analytics | Do usługi Power BI
-----|-----
bigint | Int64
Nvarchar(max) | Ciąg
datetime | Datetime (data/godzina)
float | Double
Tablica rekordów | Typ ciągu, stała wartość "IRecord" lub "IArray"

### <a name="update-the-schema"></a>Aktualizowanie schematu
Usługa Stream Analytics wnioskuje schemat modelu danych na podstawie pierwszego zestawu zdarzeń w danych wyjściowych. Później, jeśli to konieczne, schemat modelu danych jest aktualizowany w celu uwzględnienia zdarzeń przychodzących, które mogą nie pasować do oryginalnego schematu.

Unikaj `SELECT *` kwerendy, aby zapobiec dynamicznej aktualizacji schematu w wierszach. Oprócz potencjalnych skutków dla wydajności może to spowodować niepewność czasu poświęconego na wyniki. Wybierz dokładne pola, które muszą być wyświetlane na pulpicie nawigacyjnym usługi Power BI. Ponadto wartości danych powinny być zgodne z wybranym typem danych.


Poprzedni/bieżący | Int64 | Ciąg | Datetime (data/godzina) | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Ciąg | Ciąg | Double
Double | Double | Ciąg | Ciąg | Double
Ciąg | Ciąg | Ciąg | Ciąg | Ciąg 
Datetime (data/godzina) | Ciąg | Ciąg |  Datetime (data/godzina) | Ciąg

## <a name="table-storage"></a>Magazyn tabel

[Usługa Azure Table Storage](../storage/common/storage-introduction.md) oferuje wysoce dostępne, masowo skalowalne magazyny, dzięki czemu aplikacja może automatycznie skalować się w celu zaspokojenia zapotrzebowania użytkowników. Magazyn tabel to magazyn kluczy/atrybutów NoSQL firmy Microsoft, którego można używać w przypadku danych strukturalnych z mniejszą liczbą ograniczeń schematu. Usługa Azure Table Storage może służyć do przechowywania danych dla trwałości i wydajnego pobierania.

W poniższej tabeli wymieniono nazwy właściwości i ich opisy do tworzenia danych wyjściowych tabeli.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w kwerendach do kierowania danych wyjściowych kwerendy do tego magazynu tabeli. |
| Konto magazynu |Nazwa konta magazynu, na którym wysyłasz dane wyjściowe. |
| Klucz konta magazynu |Klucz dostępu skojarzony z kontem magazynu. |
| Nazwa tabeli |Nazwa tabeli. Tabela zostanie utworzona, jeśli nie istnieje. |
| Klucz partycji |Nazwa kolumny danych wyjściowych zawierającej klucz partycji. Klucz partycji jest unikatowym identyfikatorem partycji w tabeli, która stanowi pierwszą część klucza podstawowego jednostki. Jest to wartość ciągu, który może mieć rozmiar do 1 KB. |
| Klucz wiersza |Nazwa kolumny danych wyjściowych zawierającej klucz wiersza. Klucz wiersza jest unikatowym identyfikatorem jednostki w obrębie partycji. Stanowi drugą część klucza podstawowego jednostki. Klucz wiersza jest wartością ciągu, która może mieć rozmiar do 1 KB. |
| Rozmiar partii |Liczba rekordów dla operacji wsadowej. Wartość domyślna (100) jest wystarczająca dla większości zadań. Zobacz [specyfikację operacji partii tabeli, aby](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) uzyskać więcej informacji na temat modyfikowania tego ustawienia. |

## <a name="service-bus-queues"></a>Kolejki usługi Service Bus

[Kolejki usługi Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) oferują dostarczanie wiadomości FIFO do jednego lub więcej konkurujących konsumentów. Zazwyczaj wiadomości są odbierane i przetwarzane przez odbiorniki w kolejności czasowej, w której zostały dodane do kolejki. Każda wiadomość jest odbierana i przetwarzana przez tylko jednego konsumenta wiadomości.

W [poziomie zgodności 1.2](stream-analytics-compatibility-level.md)usługa Azure Stream Analytics używa protokołu obsługi protokołu [windowsopatów (Advanced Message Queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) do zapisywania w kolejkach i tematach usługi Service Bus. Protokół AMQP umożliwia tworzenie wieloplatformowych, hybrydowych aplikacji przy użyciu otwartego standardowego protokołu.

W poniższej tabeli wymieniono nazwy właściwości i ich opisy do tworzenia danych wyjściowych kolejki.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w kwerendach do kierowania danych wyjściowych kwerendy do tej kolejki usługi Service Bus. |
| Obszar nazw usługi Service Bus |Kontener dla zestawu jednostek obsługi wiadomości. |
| Nazwa kolejki |Nazwa kolejki usługi Service Bus. |
| Nazwa zasad kolejki |Podczas tworzenia kolejki można również utworzyć zasady dostępu współdzielonego na karcie **Konfigurowanie** kolejki. Każda zasada dostępu współdzielonego ma ustawioną nazwę, uprawnienia i klucze dostępu. |
| Klucz zasad kolejki |Klucz dostępu współdzielonego, który służy do uwierzytelniania dostępu do obszaru nazw usługi Service Bus. |
| Format serializacji zdarzeń |Format serializacji dla danych wyjściowych. JSON, CSV i Avro są obsługiwane. |
| Kodowanie |Dla CSV i JSON UTF-8 jest jedynym obsługiwanym formatem kodowania w tej chwili. |
| Ogranicznik |Dotyczy tylko serializacji CSV. Usługa Stream Analytics obsługuje wiele typowych ograniczników do serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek. |
| Format |Dotyczy tylko typu JSON. **Linia oddzielona** określa, że dane wyjściowe są formatowane przez każdy obiekt JSON oddzielony nowym wierszem. W przypadku **wybrania opcji Linia oddzielona**, JSON odczytuje jeden obiekt naraz. Cała zawartość sama w sobie nie byłaby prawidłowym JSON. **Tablica** określa, że dane wyjściowe są sformatowane jako tablica obiektów JSON. |
| Kolumny właściwości | Element opcjonalny. Kolumny oddzielone przecinkami, które muszą być dołączone jako właściwości użytkownika wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [Niestandardowe właściwości metadanych dla danych wyjściowych](#custom-metadata-properties-for-output). |
| Kolumny Właściwości systemu | Element opcjonalny. Pary wartości klucza właściwości systemu i odpowiadające im nazwy kolumn, które muszą być dołączone do wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [Właściwości systemu dla wyjść kolejkowania usługi Service Bus i tematu](#system-properties-for-service-bus-queue-and-topic-outputs)  |

Liczba partycji jest [oparta na jednostce SKU i rozmiarze magistrali usług.](../service-bus-messaging/service-bus-partitioning.md) Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.

## <a name="service-bus-topics"></a>Tematy dotyczące usługi Service Bus
Kolejki usługi Service Bus zapewniają metodę komunikacji jeden do jednego od nadawcy do odbiorcy. [Tematy usługi Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) zapewniają jedną do wielu form komunikacji.

W poniższej tabeli wymieniono nazwy właściwości i ich opisy do tworzenia danych wyjściowych tematu usługi Service Bus.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w kwerendach do kierowania danych wyjściowych kwerendy do tego tematu usługi Service Bus. |
| Obszar nazw usługi Service Bus |Kontener dla zestawu jednostek obsługi wiadomości. Podczas tworzenia nowego centrum zdarzeń utworzono również obszar nazw usługi Service Bus. |
| Nazwa tematu |Tematy są jednostki obsługi wiadomości, podobne do centrów zdarzeń i kolejek. Są one przeznaczone do zbierania strumieni zdarzeń z urządzeń i usług. Po utworzeniu tematu nadana jest również określona nazwa. Wiadomości wysyłane do tematu nie są dostępne, chyba że zostanie utworzona subskrypcja, więc upewnij się, że istnieje co najmniej jedna subskrypcja w temacie. |
| Nazwa zasad tematu |Podczas tworzenia tematu usługi Service Bus można również utworzyć zasady dostępu udostępnionego na karcie **Konfigurowanie** tematu. Każda zasada dostępu współdzielonego ma ustawioną nazwę, uprawnienia i klucze dostępu. |
| Klucz zasad tematu |Klucz dostępu współdzielonego, który służy do uwierzytelniania dostępu do obszaru nazw usługi Service Bus. |
| Format serializacji zdarzeń |Format serializacji dla danych wyjściowych. JSON, CSV i Avro są obsługiwane. |
| Kodowanie |Jeśli używasz formatu CSV lub JSON, należy określić kodowanie. UTF-8 jest jedynym obsługiwanym formatem kodowania w tej chwili. |
| Ogranicznik |Dotyczy tylko serializacji CSV. Usługa Stream Analytics obsługuje wiele typowych ograniczników do serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek. |
| Kolumny właściwości | Element opcjonalny. Kolumny oddzielone przecinkami, które muszą być dołączone jako właściwości użytkownika wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [Niestandardowe właściwości metadanych dla danych wyjściowych](#custom-metadata-properties-for-output). |
| Kolumny Właściwości systemu | Element opcjonalny. Pary wartości klucza właściwości systemu i odpowiadające im nazwy kolumn, które muszą być dołączone do wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [Właściwości systemu dla wyjść kolejkowania usługi Service Bus i tematu](#system-properties-for-service-bus-queue-and-topic-outputs) |

Liczba partycji jest [oparta na jednostce SKU i rozmiarze magistrali usług.](../service-bus-messaging/service-bus-partitioning.md) Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Usługa Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) to globalnie rozproszona usługa baz danych, która oferuje nieograniczoną skalę elastyczną na całym świecie, rozbudowane zapytania i automatyczne indeksowanie za pomocą modeli danych niezależnie od schematu. Aby dowiedzieć się więcej o opcjach kontenerów usługi Azure Cosmos DB dla usługi Stream Analytics, zobacz [artykuł usługi Stream Analytics z usługą Azure Cosmos DB jako artykuł wyjściowy.](stream-analytics-documentdb-output.md)

Dane wyjściowe usługi Azure Cosmos DB z usługi Stream Analytics nie są obecnie dostępne w regionach usługi Azure China 21Vianet i Azure Germany (T-Systems International).

> [!Note]
> W tej chwili usługa Azure Stream Analytics obsługuje tylko połączenie z usługą Azure Cosmos DB przy użyciu interfejsu API SQL.
> Inne interfejsy API bazy danych usługi Azure Cosmos nie są jeszcze obsługiwane. Jeśli wskażesz usługę Azure Stream Analytics na konta usługi Azure Cosmos DB utworzone za pomocą innych interfejsów API, dane mogą nie być poprawnie przechowywane.

W poniższej tabeli opisano właściwości tworzenia danych wyjściowych usługi Azure Cosmos DB.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Alias, aby odwołać się do tego danych wyjściowych w zapytaniu usługi Stream Analytics. |
| Ujście | Azure Cosmos DB. |
| Opcja importu | Wybierz pozycję **Wybierz bazę danych Cosmos z subskrypcji** lub ręcznie **podaj ustawienia usługi Cosmos DB**.
| Identyfikator konta | Nazwa lub identyfikator URI punktu końcowego konta usługi Azure Cosmos DB. |
| Klucz konta | Klucz dostępu współdzielonego dla konta usługi Azure Cosmos DB. |
| baza danych | Nazwa bazy danych usługi Azure Cosmos DB. |
| Nazwa kontenera | Nazwa kontenera, który ma być używany, który musi istnieć w usłudze Cosmos DB. Przykład:  <br /><ul><li> _MyContainer:_ Kontener o nazwie "MyContainer" musi istnieć.</li>|
| Identyfikator dokumentu |Element opcjonalny. Nazwa pola w zdarzeniach wyjściowych, które są używane do określenia klucza podstawowego, na którym są oparte operacje wstawiania lub aktualizacji.

## <a name="azure-functions"></a>Azure Functions
Usługa Azure Functions to usługa obliczeniowa bez użycia serwera, której można używać do uruchamiania kodu na żądanie bez konieczności jawnego aprowizowania infrastruktury lub zarządzania nią. Umożliwia implementowanie kodu, który jest wyzwalany przez zdarzenia występujące na platformie Azure lub usług partnerskich. Ta możliwość usługi Azure Functions do reagowania na wyzwalacze sprawia, że jest to naturalne wyjście dla usługi Azure Stream Analytics. Ta karta wyjściowa umożliwia użytkownikom łączenie usługi Stream Analytics z usługami Azure Functions i uruchamianie skryptu lub fragmentu kodu w odpowiedzi na różne zdarzenia.

Dane wyjściowe usługi Azure Functions z usługi Stream Analytics nie są obecnie dostępne w regionach usługi Azure China 21Vianet i Azure Germany (T-Systems International).

Usługa Azure Stream Analytics wywołuje usługi Azure Functions za pośrednictwem wyzwalaczy HTTP. Karta wyjściowa usługi Azure Functions jest dostępna z następującymi konfigurowalnymi właściwościami:

| Nazwa właściwości | Opis |
| --- | --- |
| Aplikacja funkcji |Nazwa aplikacji usługi Azure Functions. |
| Funkcja |Nazwa funkcji w aplikacji usługi Azure Functions. |
| Klucz |Jeśli chcesz użyć funkcji platformy Azure z innej subskrypcji, możesz to zrobić, podając klucz, aby uzyskać dostęp do funkcji. |
| Maksymalny rozmiar partii |Właściwość, która umożliwia ustawienie maksymalnego rozmiaru dla każdej partii wyjściowej, która jest wysyłana do funkcji platformy Azure. Jednostka wejściowa znajduje się w bajtach. Domyślnie ta wartość wynosi 262 144 bajtów (256 KB). |
| Maksymalna liczba partii  |Właściwość, która umożliwia określenie maksymalnej liczby zdarzeń w każdej partii, która jest wysyłana do usługi Azure Functions. Wartość domyślna to 100. |

Usługa Azure Stream Analytics oczekuje stanu HTTP 200 z aplikacji Functions dla partii, które zostały pomyślnie przetworzone.

Gdy usługa Azure Stream Analytics odbiera wyjątek 413 ("http Request Entity Too Large") z funkcji platformy Azure, zmniejsza rozmiar partii wysyłanych do usługi Azure Functions. W kodzie funkcji platformy Azure użyj tego wyjątku, aby upewnić się, że usługa Azure Stream Analytics nie wysyła dużych partii. Upewnij się również, że maksymalna liczba partii i wartości rozmiaru używane w funkcji są zgodne z wartościami wprowadzonymi w portalu usługi Stream Analytics.

> [!NOTE]
> Podczas połączenia testowego usługa Stream Analytics wysyła pustą partię do usługi Azure Functions w celu sprawdzenia, czy połączenie między tymi dwoma działa. Upewnij się, że aplikacja Functions obsługuje puste żądania wsadowe, aby upewnić się, że połączenie testowe przebiega pomyślnie.

Ponadto w sytuacji, gdy nie ma lądowania zdarzenia w oknie czasu, nie jest generowany żadnych danych wyjściowych. W rezultacie **computeResult** funkcja nie jest wywoływana. To zachowanie jest zgodne z wbudowanymi funkcjami agregacji okiennych.

## <a name="custom-metadata-properties-for-output"></a>Niestandardowe właściwości metadanych dla danych wyjściowych 

Kolumny kwerend można dołączać jako właściwości użytkownika do wiadomości wychodzących. Te kolumny nie idą do ładunku. Właściwości są obecne w postaci słownika na komunikat wyjściowy. *Klucz* jest nazwą kolumny, a *wartość* jest wartością kolumny w słowniku właściwości. Wszystkie typy danych usługi Stream Analytics są obsługiwane z wyjątkiem rekordów i macierzy.  

Obsługiwane wyjścia: 
* Kolejka usługi Service Bus 
* Temat usługi Service Bus 
* Centrum zdarzeń 

W poniższym przykładzie dodajemy `DeviceId` `DeviceStatus` dwa pola i metadane. 
* Kwerendy:`select *, DeviceId, DeviceStatus from iotHubInput`
* Konfiguracja wyjściowa:`DeviceId,DeviceStatus`

![Kolumny właściwości](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Poniższy zrzut ekranu przedstawia właściwości komunikatów wyjściowych sprawdzone w usłudze EventHub za pośrednictwem [Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

![Właściwości niestandardowe zdarzenia](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Właściwości systemu dla wyjść kolejkowania usługi Service Bus i tematu 
Kolumny kwerend można dołączać jako właściwości systemu do [komunikatów](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) kolejki magistrali usług wychodzących lub tematów. Te kolumny nie przejść do ładunku zamiast odpowiedniej [właściwości systemu](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) BrokeredMessage jest wypełniona wartościami kolumny kwerendy.
Te właściwości systemu są `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`obsługiwane - .
Wartości ciągu tych kolumn są analizowane jako odpowiedni typ wartości właściwości systemu, a wszelkie błędy analizy są traktowane jako błędy danych.
To pole jest dostarczane w formacie obiektu JSON. Szczegóły dotyczące tego formatu są następujące -
* Otoczony kręconymi {}szelkami .
* Napisane w parach klucz/wartość.
* Klucze i wartości muszą być ciągami.
* Klucz jest nazwą właściwości systemu i wartość jest nazwa kolumny kwerendy.
* Klucze i wartości są oddzielone dwukropek.
* Każda para klucz/wartość jest oddzielona przecinkiem.

To pokazuje, jak korzystać z tej właściwości —

* Kwerendy:`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Kolumny właściwości systemu:`{ "MessageId": "column1", "PartitionKey": "column2"}`

Spowoduje to `MessageId` ustawienie komunikatów `column1`kolejki magistrali usługi w `column2`usłudze z wartościami 's i PartitionKey jest ustawiona z wartościami 's.

## <a name="partitioning"></a>Partycjonowanie

W poniższej tabeli podsumowano obsługę partycji i liczbę modułów zapisu danych wyjściowych dla każdego typu danych wyjściowych:

| Typ wyjścia | Obsługa partycjonowania | Klucz partycji  | Liczba modułów zapisu produkcji |
| --- | --- | --- | --- |
| Azure Data Lake Store | Tak | Użyj tokenów {date} i {time} we wzorcu prefiksu ścieżki. Wybierz format daty, taki jak YYYY/MM/DD, DD/MM/YYYY lub MM-DD-YYYY. HH jest używany dla formatu czasu. | Następuje partycjonowanie danych wejściowych dla [w pełni równoległych zapytań](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Tak, musi być włączona. | Na podstawie klauzuli PARTITION BY w kwerendzie. | Gdy opcja Dziedzicz partycjonowanie jest włączona, następuje partycjonowanie danych wejściowych dla [w pełni równoległych zapytań](stream-analytics-scale-jobs.md). Aby dowiedzieć się więcej o osiąganiu lepszej wydajności przepływności zapisu podczas ładowania danych do bazy danych SQL usługi Azure, zobacz [dane wyjściowe usługi Azure Stream Analytics do bazy danych SQL Azure.](stream-analytics-sql-output-perf.md) |
| Azure Blob Storage | Tak | Użyj tokenów {date} i {time} z pól zdarzeń we wzorcu ścieżki. Wybierz format daty, taki jak YYYY/MM/DD, DD/MM/YYYY lub MM-DD-YYYY. HH jest używany dla formatu czasu. Dane wyjściowe obiektu blob mogą być podzielone na partycje przez pojedynczy\<atrybut zdarzenia niestandardowego {fieldname} lub {datetime: specyfikator>}. | Następuje partycjonowanie danych wejściowych dla [w pełni równoległych zapytań](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Tak | Tak | Różni się w zależności od wyrównania partycji.<br /> Gdy klucz partycji dla danych wyjściowych centrum zdarzeń jest jednakowo wyrównany z krokiem zapytania nadrzędnego (poprzedni), liczba modułów zapisu jest taka sama jak liczba partycji w danych wyjściowych centrum zdarzeń. Każdy moduł zapisujący używa [EventHubSender klasy](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) do wysyłania zdarzeń do określonej partycji. <br /> Gdy klucz partycji dla danych wyjściowych centrum zdarzeń nie jest wyrównany z krokiem zapytania nadrzędnego (poprzedni), liczba modułów zapisu jest taka sama jak liczba partycji w tym poprzednim kroku. Każdy moduł zapisujący używa [SendBatchAsync klasy](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) w **EventHubClient** do wysyłania zdarzeń do wszystkich partycji wyjściowych. |
| Power BI | Nie | Brak | Nie dotyczy. |
| Azure Table Storage | Tak | Dowolna kolumna wyjściowa.  | Następuje partycjonowanie wejściowe dla [w pełni równoległych zapytań](stream-analytics-scale-jobs.md). |
| Temat usługi Azure Service Bus | Tak | Automatycznie wybrany. Liczba partycji jest oparta na [jednostce SKU i rozmiarze magistrali usług.](../service-bus-messaging/service-bus-partitioning.md) Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.| Taka sama jak liczba partycji w temacie danych wyjściowych.  |
| Kolejka usługi Azure Service Bus | Tak | Automatycznie wybrany. Liczba partycji jest oparta na [jednostce SKU i rozmiarze magistrali usług.](../service-bus-messaging/service-bus-partitioning.md) Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.| Taka sama jak liczba partycji w kolejce wyjściowej. |
| Azure Cosmos DB | Tak | Na podstawie klauzuli PARTITION BY w kwerendzie. | Następuje partycjonowanie wejściowe dla [w pełni równoległych zapytań](stream-analytics-scale-jobs.md). |
| Azure Functions | Tak | Na podstawie klauzuli PARTITION BY w kwerendzie. | Następuje partycjonowanie wejściowe dla [w pełni równoległych zapytań](stream-analytics-scale-jobs.md). |

Liczba modułów zapisu danych `INTO <partition count>` wyjściowych można również kontrolować za pomocą (zobacz [INTO)](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)klauzuli w zapytaniu, które mogą być pomocne w osiągnięciu pożądanej topologii zadania. Jeśli karta wyjściowa nie jest podzielony na partycje, brak danych w jednej partycji wejściowej spowoduje opóźnienie do późnego przybycia. W takich przypadkach dane wyjściowe są scalane z pojedynczym modułem zapisu, co może spowodować wąskie gardła w potoku. Aby dowiedzieć się więcej na temat zasad późnego przylotu, zobacz [Zagadnienia dotyczące zamówień zdarzeń usługi Azure Stream Analytics.](stream-analytics-out-of-order-and-late-events.md)

## <a name="output-batch-size"></a>Wielkość partii wyjściowej
Usługa Azure Stream Analytics używa partii o zmiennym rozmiarze do przetwarzania zdarzeń i zapisu w danych wyjściowych. Zazwyczaj aparat usługi Stream Analytics nie pisze jeden komunikat naraz i używa partii dla wydajności. Gdy szybkość zdarzeń przychodzących i wychodzących jest wysoka, usługa Stream Analytics używa większych partii. Gdy szybkość ruchu wychodzącego jest niska, używa mniejszych partii, aby utrzymać niskie opóźnienie.

W poniższej tabeli wyjaśniono niektóre zagadnienia dotyczące przetwarzania wsadowego:

| Typ wyjścia |    Maksymalny rozmiar wiadomości | Optymalizacja rozmiaru partii |
| :--- | :--- | :--- |
| Azure Data Lake Store | Zobacz [limity magazynowania w jeziorze danych](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Użyj do 4 MB na operację zapisu. |
| Azure SQL Database | Konfigurowalne przy użyciu maksymalnej liczby partii. Domyślnie 10 000 wierszy i 100 wierszy minimalnych na pojedynczą wstawianie zbiorcze.<br />Zobacz [limity SQL platformy Azure](../sql-database/sql-database-resource-limits.md). |  Każda partia jest początkowo wstawiana zbiorczo z maksymalną liczbą partii. Partia jest podzielona na pół (do minimalnej liczby partii) na podstawie błędów ponawialnych z SQL. |
| Azure Blob Storage | Zobacz [limity usługi Azure Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Maksymalny rozmiar bloku obiektu blob wynosi 4 MB.<br />Maksymalna liczba obiektów blob bock wynosi 50 000. |
| Azure Event Hubs    | 256 KB lub 1 MB na wiadomość. <br />Zobacz [limity centrów zdarzeń](../event-hubs/event-hubs-quotas.md). |    Gdy partycjonowanie danych wejściowych/wyjściowych nie jest wyrównane, każde zdarzenie jest pakowane pojedynczo `EventData` i wysyłane w partii do maksymalnego rozmiaru wiadomości. Dzieje się tak również wtedy, gdy używane są [niestandardowe właściwości metadanych.](#custom-metadata-properties-for-output) <br /><br />  Gdy partycjonowanie danych wejściowych/wyjściowych jest wyrównane, wiele zdarzeń jest pakowanych w jedno `EventData` wystąpienie, do maksymalnego rozmiaru wiadomości i wysyłanych.    |
| Power BI | Zobacz [Limity interfejsu API odpoczynku usługi Power BI](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Zobacz [limity usługi Azure Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Wartość domyślna to 100 jednostek na pojedynczą transakcję. W razie potrzeby można skonfigurować go do mniejszej wartości. |
| Kolejka usługi Azure Service Bus    | 256 KB na wiadomość dla warstwy standardowej, 1 MB dla warstwy Premium.<br /> Zobacz [Limity usługi Service Bus](../service-bus-messaging/service-bus-quotas.md). | Użyj jednego zdarzenia na wiadomość. |
| Temat usługi Azure Service Bus | 256 KB na wiadomość dla warstwy standardowej, 1 MB dla warstwy Premium.<br /> Zobacz [Limity usługi Service Bus](../service-bus-messaging/service-bus-quotas.md). | Użyj jednego zdarzenia na wiadomość. |
| Azure Cosmos DB    | Zobacz [limity usługi Azure Cosmos DB](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | Rozmiar partii i częstotliwość zapisu są dostosowywane dynamicznie na podstawie odpowiedzi usługi Azure Cosmos DB. <br /> Nie ma żadnych z góry określonych ograniczeń z usługi Stream Analytics. |
| Azure Functions    | | Domyślny rozmiar partii to 262 144 bajtów (256 KB). <br /> Domyślna liczba zdarzeń na partię wynosi 100. <br /> Rozmiar partii można konfigurować i można go zwiększyć lub zmniejszyć w [opcjach wyjściowych](#azure-functions)usługi Stream Analytics .

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> 
> [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
