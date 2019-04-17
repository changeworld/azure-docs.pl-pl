---
title: Zrozumieć dane wyjściowe z usługi Azure Stream Analytics
description: W tym artykule opisano opcje danych wyjściowych danych dostępnych w usłudze Azure Stream Analytics, w tym usługi Power BI dla wyników analizy.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/25/2019
ms.custom: seodec18
ms.openlocfilehash: 03871c3f3627e85cc2af2f05a5fba38bd8069a15
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609493"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Zrozumieć dane wyjściowe z usługi Azure Stream Analytics
W tym artykule opisano typy danych wyjściowych jest dostępny dla zadania usługi Azure Stream Analytics. Dane wyjściowe pozwalają na przechowywanie i zapisać wyniki zadania usługi Stream Analytics. Korzystając z danych wyjściowych, możesz zrobić dalszych analiz biznesowych i danych magazynu danych.

Podczas projektowania zapytania usługi Stream Analytics, zapoznaj się nazwę wyjściowego przy użyciu [Klauzula INTO](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Można użyć pojedynczego wyjścia poszczególne zadania lub wielu wyjść na zadanie przesyłania strumieniowego (w razie potrzeby), zapewniając wiele klauzul INTO w zapytaniu.

Do tworzenia, edytowania i przetestować zadanie usługi Stream Analytics danych wyjściowych, możesz użyć [witryny Azure portal](stream-analytics-quick-create-portal.md#configure-job-output), [programu Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [interfejsu API platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [interfejsu API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), i [programu Visual Studio](stream-analytics-quick-create-vs.md).

Obsługa typów niektóre dane wyjściowe [partycjonowanie](#partitioning). [Dane wyjściowe rozmiary partii](#output-batch-size) różnią się w celu zoptymalizowania przepływności.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics obsługuje [usługi Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store to repozytorium w hiperskali dla przedsiębiorstw na potrzeby obciążeń analizy danych big data. Data Lake Store służy do przechowywania danych o dowolnym rozmiarze, typie i szybkości pozyskiwania analizy operacyjnej i rozpoznawczej. Stream Analytics musi mieć możliwość dostępu Data Lake Store.

Wyjście usługi Azure Data Lake Store z usługi Stream Analytics nie jest obecnie dostępna w regionach platformy Azure w Niemczech (T-Systems International) i Azure — Chiny (21Vianet).

### <a name="authorize-an-azure-data-lake-store-account"></a>Autoryzacja konta usługi Azure Data Lake Store

1. Po wybraniu Data Lake Store jako dane wyjściowe w witrynie Azure portal, zostanie wyświetlony monit autoryzacji połączenia do istniejącego wystąpienia programu Data Lake Store.

   ![Autoryzuj połączenie, aby Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Jeśli masz już dostęp do programu Data Lake Store, wybierz opcję **autoryzować teraz**. Strona pojawia się co oznacza **przekierowywanie do autoryzacji**. Po autoryzacji zakończy się pomyślnie, zobaczysz stronę, która pozwala na konfigurowanie danych wyjściowych Data Lake Store.

3. Po utworzeniu konta Data Lake Store uwierzytelnionego, można skonfigurować właściwości dla danych wyjściowych usługi Data Lake Store.

   ![Data Lake Store jest definiowana jako dane wyjściowe usługi Stream Analytics](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

W poniższej tabeli wymieniono nazwy właściwości i ich opisy, aby skonfigurować dane wyjściowe usługi Data Lake Store.   

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych kwerendy Data Lake Store. |
| Nazwa konta | Nazwa konta Data Lake Store, w której są wysyłane dane wyjściowe. Zobaczysz listę rozwijaną listę kont Data Lake Store, które są dostępne w Twojej subskrypcji. |
| Wzorzec prefiksu ścieżki | Ścieżka pliku, który jest używany do zapisywania plików w ramach określonego konta Data Lake Store. Można określić co najmniej jedno wystąpienie {date} i {time} zmiennych:<br /><ul><li>Przykład 1: folder1/dzienniki / {date} / {time}</li><li>Przykład 2: folder1/dzienniki / {date}</li></ul><br />Sygnatura czasowa struktury utworzony folder jest zgodna UTC, a nie czas lokalny.<br /><br />Jeśli wzorzec ścieżki plików nie zawiera ukośnika (/), ostatni wzorca w polu Ścieżka pliku jest traktowany jako prefiksu nazwy pliku. <br /><br />W takiej sytuacji zostaną utworzone nowe pliki:<ul><li>Zmiany w schemacie danych wyjściowych</li><li>Zewnętrznego lub wewnętrznego ponownego uruchomienia zadania</li></ul> |
| Format daty | Opcjonalny. Jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w której pliki są uporządkowane. Przykład: RRRR/MM/DD |
|Format godziny | Opcjonalny. Jeśli token czasu jest używany w ścieżce prefiksu, należy określić format czasu, w której pliki są uporządkowane. Obecnie jedyna obsługiwana wartość to HH. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. JSON, CSV i format Avro są obsługiwane.|
| Kodowanie | Jeśli używasz formatu CSV lub JSON, należy określić kodowania. UTF-8 to jedyny obsługiwany obecnie format kodowania.|
| Ogranicznik | Dotyczy tylko serializacji woluminów CSV. Stream Analytics obsługuje różne ograniczniki dla serializacji danych CSV. Obsługiwane wartości to przecinek, średnik, miejsca, kartę i pionowy pasek.|
| Format | Dotyczy tylko serializacji JSON. **Rozdzielone** Określa, że dane wyjściowe są formatowane przez każdy obiekt JSON, oddzielone znakiem nowego wiersza. **Tablica** Określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. Ta tablica jest zamknięty, tylko wtedy, gdy zadanie zostanie zatrzymane lub usługi Stream Analytics została przeniesiona następny przedział czasu. Ogólnie rzecz biorąc zaleca się przy użyciu formatu JSON rozdzielonych wiersza, ponieważ nie wymaga żadnej specjalnej obsługi, gdy nadal trwa zapisywanie pliku wyjściowego do.|

### <a name="renew-data-lake-store-authorization"></a>Odnów autoryzację Data Lake Store
Musisz ponownie uwierzytelniać konta Data Lake Store, jeśli jego hasło zmieniła się od zadania utworzenia lub ostatniego uwierzytelnienia. Jeśli nie ponownego uwierzytelnienia, zadanie nie dawać wyników danych wyjściowych i pokazuje błąd, który wskazuje na konieczność ponownej w dziennikach operacji. 

Obecnie token uwierzytelniania wymaga ręcznego odświeżenia co 90 dni, dla wszystkich zadań z danymi wyjściowymi Data Lake Store. To ograniczenie, można rozwiązać [uwierzytelniania za pomocą zarządzanych tożsamości (wersja zapoznawcza)](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls).

Aby odnowić autoryzacji:

1. Wybierz **zatrzymać** można zatrzymać zadania.
1. Przejdź do usługi Data Lake Store danych wyjściowych, a następnie wybierz pozycję **Odnów autoryzację** łącza.

   Przez krótki czas wskazuje stronę wyskakującego **przekierowywanie do autoryzacji**. Jeśli autoryzacja zakończy się pomyślnie, wskazuje stronę **autoryzacja została pomyślnie odnowiona** i automatycznie zamknięty. 
   
1. Wybierz **Zapisz** w dolnej części strony. Następnie uruchom ponownie zadanie z **ostatniego zatrzymana** na uniknięcie utraty danych.

![Odnów autoryzację Data Lake Store w danych wyjściowych](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>SQL Database
Możesz użyć [usługi Azure SQL Database](https://azure.microsoft.com/services/sql-database/) jako dane wyjściowe dla danych relacyjnych z natury lub aplikacje, które są zależne od zawartości jest hostowana w relacyjnej bazie danych. Zadania usługi Stream Analytics zapisu do istniejącej tabeli w bazie danych SQL. Schemat tabeli musi dokładnie odpowiadać, pola i ich typy w danych wyjściowych zadania. Można również określić [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) jako dane wyjściowe za pośrednictwem bazy danych SQL, dane wyjściowe opcji. Aby uzyskać informacje dotyczące sposobów na ulepszenie przepływność zapisu, zobacz [usługi Stream Analytics z usługą Azure SQL Database jako dane wyjściowe](stream-analytics-sql-output-perf.md) artykułu. 

W poniższej tabeli wymieniono nazwy właściwości i ich opisy tworzenia wyjście bazy danych SQL.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych kwerendy do tej bazy danych. |
| Database (Baza danych) | Nazwa bazy danych, w której są wysyłane dane wyjściowe. |
| Nazwa serwera | Nazwa serwera bazy danych SQL. |
| Nazwa użytkownika | Nazwa użytkownika, który ma uprawnienia do zapisu w bazie danych. Stream Analytics obsługuje tylko uwierzytelnianie SQL. |
| Hasło | Hasło służące do połączenia z bazą danych. |
| Tabela | Nazwa tabeli, w którym plik wyjściowy zostanie zapisany. Nazwa tabeli jest rozróżniana wielkość liter. Schemat tabeli musi być dokładnie dopasowany numer pola i ich typy, które generuje dane wyjściowe zadania. |
|Dziedziczą schemat partycji| Opcja dziedziczenia schematu partycjonowania poprzedniego kroku zapytania umożliwiające topologia pełni równolegle z wielu składników zapisywania do tabeli. Aby uzyskać więcej informacji, zobacz [dane wyjściowe usługi Azure Stream Analytics, do usługi Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Liczba partii dopasowania| Zalecany limit liczby rekordów wysyłane z każdego zbiorczego Wstaw transakcję.|

> [!NOTE]
> Aktualnie obsługiwana jest oferty usługi Azure SQL Database, dane wyjściowe zadania w usłudze Stream Analytics. Maszynę wirtualną platformy Azure, programem SQL Server z bazą danych dołączone nie jest obsługiwana. To może ulec zmianie w przyszłych wersjach.
>

## <a name="blob-storage"></a>Blob Storage
Usługa Azure storage Blob oferuje ekonomiczne i skalowalne rozwiązanie do przechowywania dużych ilości danych bez struktury w chmurze. Aby zapoznać się z wprowadzeniem magazynu obiektów Blob i jego użycia, zobacz [jak obiekty BLOB są odpowiednie](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

W poniższej tabeli wymieniono nazwy właściwości i ich opisów tworzenia wyjściowego obiektu blob.

| Nazwa właściwości       | Opis                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias danych wyjściowych        | Przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania do tego magazynu obiektów blob. |
| Konto magazynu     | Nazwa konta magazynu, w której są wysyłane dane wyjściowe.               |
| Klucz konta magazynu | Klucz tajny skojarzony z kontem magazynu.                              |
| Kontener magazynu   | Logiczne grupowanie obiektów blob przechowywanych w usłudze Azure Blob. Podczas przekazywania obiektu blob do usługi obiektów Blob, należy określić kontener dla tego obiektu blob. |
| Wzorzec ścieżki | Opcjonalny. Wzorzec ścieżki pliku, który jest używany do zapisywania obiektów blob w określonym kontenerze. <br /><br /> We wzorcu ścieżkę można określić częstotliwość, z którą obiekty BLOB są zapisywane przy użyciu co najmniej jedno wystąpienie zmiennych daty i godziny: <br /> {date}, {time} <br /><br />Partycjonowanie niestandardowych obiektów blob umożliwia określić jedną nazwę niestandardowego {pola} z Twoich danych zdarzenia do partycji obiektów blob. Nazwa pola to znak alfanumeryczny oraz może zawierać spacji, łączniki i podkreślenia. Następujące ograniczenia dotyczące pól niestandardowych: <ul><li>Nazwy pól nie jest rozróżniana wielkość liter. Na przykład usługi nie można odróżnić kolumny "ID" i kolumnie "id".</li><li>Zagnieżdżone pola nie są dozwolone. Zamiast tego należy użyć aliasu w zapytaniu zadania do pola "spłaszczenia".</li><li>Nie można użyć wyrażenia jako nazwę pola.</li></ul> <br />Ta funkcja umożliwia korzystanie z konfiguracji w specyfikator formatu niestandardowego daty/godziny w ścieżce. Niestandardowa data i godzina formaty musi być określona pojedynczo, ujęta w {daty/godziny:\<specyfikator >} — słowo kluczowe. Dozwolone danych wejściowych dla \<specyfikator > to rrrr, MM, M, dd, d, HH, H, mm, m, ss lub s. {Daty/godziny:\<specyfikator >} — słowo kluczowe może służyć wielokrotnie w ścieżce do utworzenia konfiguracji niestandardowej daty/godziny. <br /><br />Przykłady: <ul><li>Przykład 1: Klaster1/dzienniki / {date} / {time}</li><li>Przykład 2: Klaster1/dzienniki / {date}</li><li>Przykład 3: Klaster1 / {client_id} / {date} / {time}</li><li>Przykład 4: Klaster1 / {datetime:ss} / {myField} gdzie to zapytanie: Wybierz data.myField jako myField z dane wejściowe</li><li>Przykład 5: Klaster1/rok = {datetime:yyyy} / miesiąc = {datetime:MM} / dzień = {datetime:dd}</ul><br />Sygnatura czasowa struktury utworzony folder jest zgodna UTC, a nie czas lokalny.<br /><br />Nazywanie pliku używa następującej konwencji: <br /><br />{Ścieżka prefiksu Pattern}/schemaHashcode_Guid_Number.extension<br /><br />Przykładowe pliki danych wyjściowych:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Aby uzyskać więcej informacji na temat tej funkcji, zobacz [blob niestandardowych usługi Azure Stream Analytics, partycjonowanie danych wyjściowych](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Format daty | Opcjonalny. Jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w której pliki są uporządkowane. Przykład: RRRR/MM/DD |
| Format godziny | Opcjonalny. Jeśli token czasu jest używany w ścieżce prefiksu, należy określić format czasu, w której pliki są uporządkowane. Obecnie jedyna obsługiwana wartość to HH. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. JSON, CSV i format Avro są obsługiwane. |
| Kodowanie    | Jeśli używasz formatu CSV lub JSON, należy określić kodowania. UTF-8 to jedyny obsługiwany obecnie format kodowania. |
| Ogranicznik   | Dotyczy tylko serializacji woluminów CSV. Stream Analytics obsługuje różne ograniczniki dla serializacji danych CSV. Obsługiwane wartości to przecinek, średnik, miejsca, kartę i pionowy pasek. |
| Format      | Dotyczy tylko serializacji JSON. **Rozdzielone** Określa, że dane wyjściowe są formatowane przez każdy obiekt JSON, oddzielone znakiem nowego wiersza. **Tablica** Określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. Ta tablica jest zamknięty, tylko wtedy, gdy zadanie zostanie zatrzymane lub usługi Stream Analytics została przeniesiona następny przedział czasu. Ogólnie rzecz biorąc zaleca się przy użyciu formatu JSON rozdzielonych wiersza, ponieważ nie wymaga żadnej specjalnej obsługi, gdy nadal trwa zapisywanie pliku wyjściowego do. |

Podczas korzystania z usługi Blob storage jako dane wyjściowe, tworzony jest nowy plik w obiekcie blob w następujących przypadkach:

* Jeśli plik przekracza maksymalną liczbę dozwolonych bloków (obecnie 50 000). Może osiągnąć maksymalną dozwoloną liczbę bloków, docierając rozmiar maksymalny dozwolony obiektu blob. Na przykład jeśli częstotliwość danych wyjściowych jest duża, możesz zobaczyć większą liczbę bajtów na blok, a rozmiar pliku jest większy. Jeśli szybkość, z danych wyjściowych jest niska, każdy blok ma mniejszą ilość danych, a rozmiar pliku jest mniejszy.
* W przypadku zmiany schematu w danych wyjściowych i format danych wyjściowych wymaga stałego schematu (pliku CSV i format Avro).
* Jeśli zadanie zostanie ponownie uruchomiony, zewnętrznie przez użytkownika, zatrzymując ją, a następnie uruchomiona lub wewnętrznie konserwacji lub błąd podczas odzyskiwania systemu.
* Jeśli zapytanie jest w pełni podzielona na partycje, a nowy plik zostanie utworzony dla każdej partycji w danych wyjściowych.
* Jeśli użytkownik usunie pliku lub kontener konta magazynu.
* Jeśli wynik to czas na partycje za pomocą wzorzec prefiksu ścieżki, a nowy obiekt blob jest używany, gdy kwerenda przechodzi do następnej pełnej godziny.
* Jeśli dane wyjściowe jest podzielona na partycje według pola niestandardowe, a nowy obiekt blob jest tworzony na partycję kluczy Jeśli on nie istnieje.
* Jeśli dane wyjściowe jest podzielona na partycje pole niestandardowe, w którym partycję kluczy Kardynalność przekracza 8000, a nowy obiekt blob jest tworzony na klucz partycji.

## <a name="event-hubs"></a>Event Hubs
[Usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) service to wysoce skalowalna publikowania/subskrybowania o dużych możliwościach skalowania zdarzeń. Może ona zbierać miliony zdarzeń na sekundę. Jednym z zastosowań Centrum zdarzeń jako dane wyjściowe jest, gdy dane wyjściowe zadania usługi Stream Analytics staje się dane wejściowe zadania przesyłania strumieniowego innego.

Konieczne jest kilku parametrów, aby skonfigurować strumienie danych z usługi event hubs jako dane wyjściowe.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania w tym Centrum zdarzeń. |
| Przestrzeń nazw centrum zdarzeń |Kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń, utworzono przestrzeń nazw Centrum zdarzeń. |
| Nazwa centrum zdarzeń | Nazwa danych wyjściowych Centrum zdarzeń. |
| Nazwa zasad Centrum zdarzeń | Zasady dostępu współdzielonego, który można utworzyć w tym Centrum zdarzeń **Konfiguruj** kartę. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. |
| Klucz zasad Centrum zdarzeń | Klucz dostępu współdzielonego, który jest używany do uwierzytelniania dostępu do przestrzeni nazw Centrum zdarzeń. |
| Kolumna klucza partycji | Opcjonalny. Kolumna, która zawiera klucz partycji dla danych wyjściowych Centrum zdarzeń. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. JSON, CSV i format Avro są obsługiwane. |
| Kodowanie | Dla woluminu CSV i JSON UTF-8 jest obsługiwany tylko format kodowania w tej chwili. |
| Ogranicznik | Dotyczy tylko serializacji woluminów CSV. Usługa Stream Analytics obsługuje różne ograniczniki dla serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, miejsca, kartę i pionowy pasek. |
| Format | Dotyczy tylko serializacji JSON. **Rozdzielone** Określa, że dane wyjściowe są formatowane przez każdy obiekt JSON, oddzielone znakiem nowego wiersza. **Tablica** Określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. Ta tablica jest zamknięty, tylko wtedy, gdy zadanie zostanie zatrzymane lub usługi Stream Analytics została przeniesiona następny przedział czasu. Ogólnie rzecz biorąc zaleca się przy użyciu formatu JSON rozdzielonych wiersza, ponieważ nie wymaga żadnej specjalnej obsługi, gdy nadal trwa zapisywanie pliku wyjściowego do. |
| Kolumny właściwości | Opcjonalny. Kolumny rozdzielonych przecinkami, które musi zostać dołączony jako użytkownika właściwości komunikatu wychodzącego zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [niestandardowe właściwości metadanych dla danych wyjściowych](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI
Możesz użyć [usługi Power BI](https://powerbi.microsoft.com/) jako dane wyjściowe zadania usługi Stream Analytics, zapewnienie rozbudowanego środowiska wizualizacji wyników analiz. Ta funkcja służy do operacyjne pulpity nawigacyjne, generowania raportu i raportowanie oparte na metrykę.

Wyjście usługi Power BI z usługą Stream Analytics nie jest obecnie dostępna w regionach platformy Azure w Niemczech (T-Systems International) i Azure — Chiny (21Vianet).

### <a name="authorize-a-power-bi-account"></a>Autoryzacja konta usługi Power BI
1. Po wybraniu usługi Power BI jako dane wyjściowe w witrynie Azure portal, zostanie wyświetlony monit, aby zezwolić istniejącym użytkownikiem usługi Power BI lub Utwórz nowe konto usługi Power BI.
   
   ![Autoryzacja użytkownika usługi Power BI, aby skonfigurować dane wyjściowe](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Utwórz nowe konto, jeśli nie jeszcze masz, a następnie wybierz pozycję **autoryzować teraz**. Zostanie wyświetlona następująca strona:
   
   ![Uwierzytelnianiu w usłudze Power BI z kontem platformy Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. Podaj konto służbowe lub szkolne do autoryzowania danych wyjściowych usługi Power BI. Jeśli jeszcze nie masz konta usługi Power BI, wybierz opcję **Zarejestruj się teraz**. Konto służbowe lub szkolne, którego używasz dla usługi Power BI może się różnić od konta subskrypcji platformy Azure, na którym użytkownik jest obecnie zalogowany.

### <a name="configure-the-power-bi-output-properties"></a>Konfigurowanie właściwości danych wyjściowych usługi Power BI
Po utworzeniu konta usługi Power BI uwierzytelniony, można skonfigurować właściwości dla danych wyjściowych usługi Power BI. W poniższej tabeli wymieniono nazwy właściwości i ich opisy, aby skonfigurować dane wyjściowe usługi Power BI.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Podaj przyjazną nazwę, która jest używane w zapytaniach do kierowania wyników zapytania do tych danych wyjściowych usługi Power BI. |
| Obszar roboczy grupy |Aby umożliwić udostępnianie danych innym użytkownikom usługi Power BI, możesz wybrać grupy wewnątrz konta usługi Power BI lub wybierz **Mój obszar roboczy** Jeśli nie chcesz zapisać do grupy. Aktualizowanie istniejącej grupy wymaga, ponowne uwierzytelnianie w usłudze Power BI. |
| Nazwa zestawu danych |Podaj nazwę zestawu danych, który chcesz, aby użyć danych wyjściowych usługi Power BI. |
| Nazwa tabeli |Podaj nazwę tabeli w zestawie danych, danych wyjściowych usługi Power BI. Obecnie usługa Power BI dane wyjściowe zadania usługi Stream Analytics może mieć tylko jedną tabelę w zestawie danych. |

Aby zapoznać się z przewodnikiem konfigurowania danych wyjściowych usługi Power BI i pulpitów nawigacyjnych, zobacz [usługi Azure Stream Analytics i usługą Power BI](stream-analytics-power-bi-dashboard.md) artykułu.

> [!NOTE]
> Nie tworzą jawnie zestaw danych i tabeli na pulpicie nawigacyjnym usługi Power BI. Zestaw danych i tabeli są wypełniane automatycznie podczas uruchamiania zadania i zadania, uruchamia przekazujący dane wyjściowe do usługi Power BI. Jeśli zapytanie zadania nie generuje żadnych wyników, nie tworzy zestaw danych i tabeli. Jeśli usługa Power BI ma już zestaw danych i tabeli o takiej samej nazwie, jak podano w ramach tego zadania usługi Stream Analytics, istniejące dane są zastępowane.
>

### <a name="create-a-schema"></a>Utwórz schemat
Usługa Azure Stream Analytics tworzy schemat zestawu danych i tabeli usługi Power BI dla użytkownika, jeśli jeszcze nie istnieją. We wszystkich innych przypadkach tabela jest aktualizowana z nowymi wartościami. Obecnie tylko w jednej tabeli może istnieć w zestawie danych. 

Usługa Power BI używa pierwszego in, first-out (FIFO) zasad przechowywania. Dane będą zbierane w tabeli, dopóki trafienia 200 000 wierszy.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Konwersję typu danych z usługi Stream Analytics w usłudze Power BI
Usługa Azure Stream Analytics aktualizacji modelu danych dynamicznie w czasie wykonywania, jeśli zmieni się schemat danych wyjściowych. Zmiany nazw kolumn, zmiana typu kolumny oraz dodawanie i usuwanie kolumn są wszystkie śledzone.

W tej tabeli opisano konwersje typów danych z [typów danych usługi Stream Analytics](https://msdn.microsoft.com/library/azure/dn835065.aspx) do usługi Power BI [typy Entity Data Model (EDM) struktury](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/entity-data-model), jeśli zestaw danych usługi Power BI i tabela nie istnieje.

Z usługi Stream Analytics | To Power BI
-----|-----
bigint | Int64
nvarchar(max) | Ciąg
datetime | Data/godzina
float | Podwójne
Tablica rekordu | Ciąg typu wartości stałej "IRecord" lub "IArray"

### <a name="update-the-schema"></a>Aktualizacja schematu
Stream Analytics wnioskuje schemat modelu danych, na podstawie pierwszego zestawu zdarzenia w danych wyjściowych. Później Jeśli to konieczne, do obsługi zdarzeń przychodzących, które może się nie zmieścić w oryginalnym schematem jest aktualizowana schematu modelu danych.

Należy unikać `SELECT *` zapytanie, aby zapobiec aktualizacja schematu dynamicznego w wierszach. Oprócz potencjalny wpływ na wydajność może to spowodować niepewność co do czasu potrzebnego dla wyników. Zaznacz dokładnie pola, które muszą być wyświetlane na pulpicie nawigacyjnym usługi Power BI. Ponadto wartości danych powinny być zgodne z wybranym typem danych.


Poprzednie/bieżąca | Int64 | Ciąg | Data/godzina | Podwójne
-----------------|-------|--------|----------|-------
Int64 | Int64 | Ciąg | Ciąg | Podwójne
Podwójne | Podwójne | Ciąg | Ciąg | Podwójne
Ciąg | Ciąg | Ciąg | Ciąg | Ciąg 
Data/godzina | Ciąg | Ciąg |  Data/godzina | Ciąg


### <a name="renew-power-bi-authorization"></a>Odnów autoryzację BI zasilania
Jeśli hasło konta usługi Power BI zmieni się po utworzenia lub ostatniego uwierzytelnienia zadania usługi Stream Analytics, musisz ponownie uwierzytelniać usługi Stream Analytics. Jeśli usługi Azure Multi-Factor Authentication jest skonfigurowany w dzierżawie usługi Azure Active Directory (Azure AD), należy odnowić autoryzację usługi Power BI, co dwa tygodnie. Objawem tego problemu jest nie danych wyjściowych zadań i "Uwierzytelnianie użytkownika error" w dziennikach operacji:

  ![Usługa Power BI uwierzytelniania błąd użytkownika](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Aby rozwiązać ten problem, Zatrzymaj uruchomione zadania, a następnie przejdź do usługi Power BI danych wyjściowych. Wybierz **Odnów autoryzację** połączyć, a następnie uruchom ponownie zadanie z **ostatniego zatrzymana** na uniknięcie utraty danych.

  ![Odnów autoryzację usługi Power BI dla danych wyjściowych](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Magazyn tabel
[Usługa Azure Table storage](../storage/common/storage-introduction.md) oferuje magazyn o wysokiej dostępności, skalowalności, dzięki czemu aplikacja może automatycznie skalować w celu spełnienia określonych wymagań użytkownika. Magazyn tabel to magazyn klucz atrybut NoSQL firmy Microsoft, który służy do ustrukturyzowanych danych za pomocą mniejszej liczby ograniczenia schematu. Usługa Azure Table storage może służyć do przechowywania danych dla stanu trwałego i efektywne pobieranie.

W poniższej tabeli wymieniono nazwy właściwości i ich opisów tworzenia tabeli danych wyjściowych.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania do magazynu w tej tabeli. |
| Konto magazynu |Nazwa konta magazynu, w której są wysyłane dane wyjściowe. |
| Klucz konta magazynu |Klucz dostępu skojarzone z kontem magazynu. |
| Nazwa tabeli |Nazwa tabeli. Jeśli nie istnieje, zostanie utworzona tabela. |
| Klucz partycji |Nazwa kolumny wyjściowej zawierającej klucz partycji. Klucz partycji to unikatowy identyfikator dla partycji w tabeli, który wchodzi w skład pierwszej części klucza podstawowego jednostki. Jest to wartość ciągu, która może być 1 KB rozmiaru. |
| Klucz wiersza |Nazwa kolumny wyjściowej zawierającej klucz wiersza. Klucz wiersza to unikatowy identyfikator dla jednostki w partycji. Wchodzi w skład drugiej części klucza podstawowego jednostki. Klucz wiersza jest wartość ciągu, która może być 1 KB rozmiaru. |
| Rozmiar partii |Liczba rekordów do operacji przetwarzania wsadowego. Wartość domyślna (100) jest wystarczające dla większości zadań. Zobacz [Specyfikacja operacji zbiorczej tabeli](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) Aby uzyskać więcej informacji na temat modyfikowania tego ustawienia. |

## <a name="service-bus-queues"></a>Kolejki usługi Service Bus
[Kolejki usługi Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) oferty do co najmniej jeden konkurujący odbiorcy dostarczania komunikatów FIFO. Zazwyczaj komunikaty są odbierane i przetwarzane przez odbiorców w kolejności, w jakiej zostały dodane do kolejki. Każdy komunikat jest odbierany i przetwarzany przez tylko jednego odbiorcę komunikatów.

W poniższej tabeli wymieniono nazwy właściwości i ich opisów tworzenia kolejki dane wyjściowe.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych kwerendy do tej kolejki usługi Service Bus. |
| Przestrzeń nazw magistrali usług |Kontener dla zestawu jednostek do obsługi komunikatów. |
| Nazwa kolejki |Nazwa kolejki usługi Service Bus. |
| Nazwa zasad kolejki |Podczas tworzenia kolejki można też utworzyć zasady dostępu współdzielonego, przez kolejkę **Konfiguruj** kartę. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. |
| Klucz zasad kolejki |Klucz dostępu współdzielonego, który jest używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus. |
| Format serializacji zdarzeń |Format serializacji danych wyjściowych. JSON, CSV i format Avro są obsługiwane. |
| Kodowanie |Dla woluminu CSV i JSON UTF-8 jest obsługiwany tylko format kodowania w tej chwili. |
| Ogranicznik |Dotyczy tylko serializacji woluminów CSV. Usługa Stream Analytics obsługuje różne ograniczniki dla serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, miejsca, kartę i pionowy pasek. |
| Format |Dotyczy tylko typu JSON. **Rozdzielone** Określa, że dane wyjściowe są formatowane przez każdy obiekt JSON, oddzielone znakiem nowego wiersza. **Tablica** Określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. |
| Kolumny właściwości | Opcjonalny. Kolumny rozdzielonych przecinkami, które musi zostać dołączony jako użytkownika właściwości komunikatu wychodzącego zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [niestandardowe właściwości metadanych dla danych wyjściowych](#custom-metadata-properties-for-output). |

Liczba partycji wynosi [na podstawie jednostki SKU magistrali usług i rozmiaru](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.

## <a name="service-bus-topics"></a>Tematy usługi Service Bus
Kolejki usługi Service Bus umożliwiają komunikację jeden od nadawcy do odbiorcy. [Tematy usługi Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) zapewniają formę komunikacji jeden do wielu.

W poniższej tabeli wymieniono nazwy właściwości i ich opisów dla tworzenie wyjścia tematu.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania do tego tematu usługi Service Bus. |
| Przestrzeń nazw magistrali usług |Kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń utworzonego również przestrzeni nazw usługi Service Bus. |
| Nazwa tematu |Tematy to jednostki przypominające centra zdarzeń i kolejki obsługi komunikatów. Służą one do zbierania strumieni zdarzeń z urządzeń i usług. Po utworzeniu tematu również dało określonej nazwy. Komunikaty wysyłane do tematu są niedostępne, chyba że zostanie utworzona subskrypcja, upewnij się, więc ma co najmniej jedną subskrypcję w ramach tematu. |
| Nazwa zasad tematu |Podczas tworzenia tematu można też utworzyć zasady dostępu współdzielonego, w tej dziedzinie **Konfiguruj** kartę. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. |
| Klucz zasad tematu |Klucz dostępu współdzielonego, który jest używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus. |
| Format serializacji zdarzeń |Format serializacji danych wyjściowych. JSON, CSV i format Avro są obsługiwane. |
| Kodowanie |Jeśli używasz formatu CSV lub JSON, należy określić kodowania. UTF-8 to jedyny obsługiwany obecnie format kodowania. |
| Ogranicznik |Dotyczy tylko serializacji woluminów CSV. Usługa Stream Analytics obsługuje różne ograniczniki dla serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, miejsca, kartę i pionowy pasek. |
| Kolumny właściwości | Opcjonalny. Kolumny rozdzielonych przecinkami, które musi zostać dołączony jako użytkownika właściwości komunikatu wychodzącego zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [niestandardowe właściwości metadanych dla danych wyjściowych](#custom-metadata-properties-for-output). |

Liczba partycji wynosi [na podstawie jednostki SKU magistrali usług i rozmiaru](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Usługa Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) to usługa globalnie rozproszonej bazy danych, która oferuje nieograniczone możliwości elastycznego skalowania na całym świecie, obsługą zaawansowanych zapytań i automatyczne indeksowanie w modele danych niezależnej od schematu. Aby dowiedzieć się więcej o opcjach kolekcji usługi Azure Cosmos DB dla usługi Stream Analytics, zobacz [usługi Stream Analytics z usługą Azure Cosmos DB jako dane wyjściowe](stream-analytics-documentdb-output.md) artykułu.

Usługa Azure Cosmos DB dane wyjściowe z usługi Stream Analytics nie jest obecnie dostępna w regionach platformy Azure w Niemczech (T-Systems International) i Azure — Chiny (21Vianet).

> [!Note]
> W tej chwili usługi Azure Stream Analytics obsługuje tylko połączenie do usługi Azure Cosmos DB przy użyciu interfejsu API SQL.
> Innych interfejsów API usługi Azure Cosmos DB nie są jeszcze obsługiwane. Jeśli punkt Azure Stream Analytics do kont usługi Azure Cosmos DB utworzone z innymi interfejsami API, dane mogą nie być prawidłowo przechowywane.

W poniższej tabeli opisano właściwości do utworzenia dane wyjściowe usługi Azure Cosmos DB.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Odwoływanie się aliasu to dane wyjściowe w zapytaniu usługi Stream Analytics. |
| Ujście | Azure Cosmos DB. |
| Opcja importu | Wybierz opcję **wybierz Cosmos DB z subskrypcji** lub **ustawienia usługi Cosmos DB zapewnia ręcznie**.
| Identyfikator konta | Nazwa lub identyfikator URI konta usługi Azure Cosmos DB punktu końcowego. |
| Klucz konta | Klucz dostępu współdzielonego dla konta usługi Azure Cosmos DB. |
| Database (Baza danych) | Nazwa bazy danych Azure Cosmos DB. |
| Wzorzec nazw kolekcji | Nazwa kolekcji lub wzorzec dla kolekcji, który ma być używany. <br />Format nazw kolekcji można skonstruować przy użyciu opcjonalnego tokenu {partition}, gdzie partycje zaczynają się od 0. Dwa przykłady:  <br /><ul><li> _MyCollection_: Musi istnieć jedna kolekcja o nazwie "MyCollection".</li>  <li> _MyCollection{partition}_: Oparte na kolumny partycjonowania.</li></ul> Partycjonowania kolekcji kolumn muszą być spełnione: "MyCollection0," "MyCollection1", "MyCollection2", i tak dalej. |
| Klucz partycji | Opcjonalny. Jest to potrzebne tylko wtedy, gdy używasz tokenu {partition} w Twojej wzorzec nazw kolekcji.<br /> Klucz partycji jest nazwą pola w zdarzeniach wyjściowych, który służy do określenia klucza do partycjonowania danych wyjściowych w kolekcjach.<br /> Dla danych wyjściowych w jedną kolekcję można użyć dowolnej kolumny dowolne dane wyjściowe. Przykładem jest identyfikatora partycji. |
| Identyfikator dokumentu |Opcjonalny. Nazwa pola w zdarzeniach wyjściowych, który jest używany do określenia klucza podstawowego, na które insert nebo update bazują operacje.

## <a name="azure-functions"></a>Azure Functions
Usługa Azure Functions to bezserwerowa usługa obliczeniowa, która umożliwia uruchamianie kodu na żądanie bez konieczności jawnego przydzielania infrastruktury ani zarządzania tą infrastrukturą. Umożliwia Implementowanie kodu wyzwalanego przez zdarzenia zachodzące w usługach platformy Azure lub partnera. Ta możliwość odpowiadania na Wyzwalacze usługi Azure Functions sprawia, że stanowi naturalne wyjście dla usługi Azure Stream Analytics. Ten adapter danych wyjściowych umożliwia użytkownikom łączenie Stream Analytics z usługi Azure Functions i uruchamiać skrypty lub fragment kodu w odpowiedzi na różne zdarzenia.

Azure wyjścia usługi Functions z usługi Stream Analytics nie jest obecnie dostępna w regionach platformy Azure w Niemczech (T-Systems International) i Azure — Chiny (21Vianet).

Usługa Azure Stream Analytics wywołuje usługi Azure Functions za pomocą wyzwalaczy protokołu HTTP. Adapter wyjścia usługi Functions na platformie Azure jest dostępna z poniższymi właściwościami można skonfigurować:

| Nazwa właściwości | Opis |
| --- | --- |
| Aplikacja funkcji |Nazwa aplikacji usługi Azure Functions. |
| Funkcja |Nazwa funkcji w aplikacji usługi Azure Functions. |
| Klucz |Aby użyć funkcji platformy Azure z innej subskrypcji, możesz to zrobić, podając klucz dostępu funkcji do. |
| Maksymalny rozmiar partii |Właściwość, która pozwala ustawić maksymalny rozmiar poszczególnych partii danych wyjściowych, które są wysyłane do funkcji platformy Azure. Jednostki danych wejściowych jest w bajtach. Domyślnie ta wartość jest 262 144 bajty (256 KB). |
| Maksymalna liczba partii  |Właściwość, pozwala określić maksymalną liczbę zdarzeń w każdej z partii, które są wysyłane do usługi Azure Functions. Wartość domyślna to 100. |

Gdy usługi Azure Stream Analytics odbierze 413 "http (żądanie podmiotu zbyt duże") wyjątku od funkcji platformy Azure, to zmniejszy rozmiar partii wysyłanych do usługi Azure Functions. W kodzie funkcji platformy Azure Użyj tego wyjątku, aby upewnić się, że usługi Azure Stream Analytics nie wysyła za dużych partii. Ponadto upewnij się, że maksymalna liczba i rozmiar wartości partii używany w funkcji są zgodne z wartościami podanymi w portalu usługi Stream Analytics.

Ponadto w sytuacji, gdy istnieje żadne zdarzenie kierowanych do przedział czasu, jest generowane żadne dane wyjściowe. W rezultacie **computeResult** funkcja nie jest wywoływana. To zachowanie jest zgodne z wbudowanych funkcji agregujących okna.

## <a name="custom-metadata-properties-for-output"></a>Właściwości niestandardowych metadanych dla danych wyjściowych 

Zapytanie kolumn jako właściwości użytkownika można dołączyć do wiadomości wychodzących. Kolumny te nie są kierowane do ładunku. Właściwości są obecne w formie słownika komunikatu wyjściowego. *Klucz* jest nazwą kolumny i *wartość* jest wartość kolumny ze słownika właściwości. Wszystkie typy danych usługi Stream Analytics są obsługiwane z wyjątkiem rekordu i tablicy.  

Obsługiwane dane wyjściowe: 
* Kolejka magistrali usług 
* Temat magistrali usług 
* Centrum zdarzeń 

W poniższym przykładzie dodamy dwa pola `DeviceId` i `DeviceStatus` metadanych. 
* Zapytanie: `select *, DeviceId, DeviceStatus from iotHubInput`
* Konfiguracja danych wyjściowych: `DeviceId,DeviceStatus`

![Kolumny właściwości](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe właściwości komunikatów inspekcji w Centrum zdarzeń za pośrednictwem [Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

![Niestandardowe właściwości zdarzenia](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Partycjonowanie

Poniższa tabela zawiera podsumowanie obsługi partycji i liczby modułów zapisywania danych wyjściowych, dla każdego typu danych wyjściowych:

| Typ wyjścia | Partycjonowanie pomocy technicznej | Klucz partycji  | Liczba modułów zapisywania danych wyjściowych |
| --- | --- | --- | --- |
| Azure Data Lake Store | Yes | Użyj {date} i {time} tokenów w wzorzec prefiksu ścieżki. Wybierz format daty — RRRR/MM/DD, DD/MM/RRRR lub MM-DD-RRRR. HH jest używany dla formatu godziny. | Następuje partycjonowania danych wejściowych dla [zapytań pełni równoległego](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Yes | Oparte na klauzuli PARTITION BY w zapytaniu. | Następuje partycjonowania danych wejściowych dla [zapytań pełni równoległego](stream-analytics-scale-jobs.md). Aby dowiedzieć się więcej na temat osiągania lepszej przepływności wydajność zapisu podczas ładowania danych do usługi Azure SQL Database, zobacz [dane wyjściowe usługi Azure Stream Analytics, do usługi Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Yes | Użyj {date} i {time} tokeny od pól zdarzeń we wzorcu ścieżki. Wybierz format daty — RRRR/MM/DD, DD/MM/RRRR lub MM-DD-RRRR. HH jest używany dla formatu godziny. Dane wyjściowe obiektu blob można podzielić na partycje atrybut pojedyncze zdarzenie niestandardowe {fieldname} lub {daty/godziny:\<specyfikator >}. | Następuje partycjonowania danych wejściowych dla [zapytań pełni równoległego](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Yes | Yes | Różni się w zależności od wyrównania partycji.<br /> Liczba modułów zapisujących klucza partycji dla danych wyjściowych Centrum zdarzeń jest równie powiązana z nadrzędnego krok zapytania (poprzednia wersja), jest taka sama jak liczba partycji w danych wyjściowych Centrum zdarzeń. Każdy moduł zapisujący używa [klasy EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) do wysyłania zdarzeń do określonej partycji. <br /> Liczba modułów zapisujących klucza partycji dla danych wyjściowych Centrum zdarzeń nie jest wyrównana z nadrzędnego krok zapytania (poprzednia wersja), jest taka sama jak liczba partycji w tym w poprzednim kroku. Każdy moduł zapisujący używa [klasy SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) w **EventHubClient** do wysyłania zdarzeń do wszystkich partycji danych wyjściowych. |
| Power BI | Nie | Brak | Nie dotyczy. |
| Azure Table Storage | Yes | Wszystkie kolumny wyjściowej.  | Następuje partycjonowania danych wejściowych dla [pełni zrównoleglona zapytania](stream-analytics-scale-jobs.md). |
| Usługa Azure tematu usługi Service Bus | Yes | Wybierane automatycznie. Liczba partycji jest oparty na [usługi Service Bus w jednostki SKU i rozmiar](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.| Taka sama jak liczba partycji w temacie dotyczącym danych wyjściowych.  |
| Kolejki usługi Service Bus platformy Azure | Yes | Wybierane automatycznie. Liczba partycji jest oparty na [usługi Service Bus w jednostki SKU i rozmiar](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.| Taka sama jak liczba partycji w kolejki wyjściowej. |
| Azure Cosmos DB | Yes | Użyj tokenu {partition} w wzorzec nazw kolekcji. Wartość {partition} opiera się na klauzuli PARTITION BY w zapytaniu. | Następuje partycjonowania danych wejściowych dla [pełni zrównoleglona zapytania](stream-analytics-scale-jobs.md). |
| Azure Functions | Nie | Brak | Nie dotyczy. |

Jeśli karty danych wyjściowych nie jest podzielona na partycje, Brak danych w jednej partycji danych wejściowych spowoduje opóźnienie maksymalnie późnego przybycia ilość czasu. W takich przypadkach dane wyjściowe są scalane do jednego elementu zapisującego, co może spowodować wąskie gardła w potoku. Aby dowiedzieć się więcej na temat późnego przybycia zasad, zobacz [zagadnienia dotyczące kolejności zdarzeń usługi Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Rozmiar partii danych wyjściowych
Usługa Azure Stream Analytics używa partii o zmiennym rozmiarze do przetwarzania zdarzeń i zapisywania danych wyjściowych. Zazwyczaj aparat usługi Stream Analytics nie zapisu jeden komunikat w danym momencie i używa partie w celu zwiększenia wydajności. Gdy liczba zdarzeń przychodzących i wychodzących jest wysoka, Stream Analytics korzysta z większych partii. Gdy współczynnik ruchu wychodzącego jest niskie, używa mniejsze segmenty do niskich opóźnień.

W poniższej tabeli opisano niektóre zagadnienia dotyczące dzielenia na partie danych wyjściowych:

| Typ wyjścia | Maksymalny rozmiar wiadomości | Optymalizacja rozmiaru partii |
| :--- | :--- | :--- |
| Azure Data Lake Store | Zobacz [limity usługi Data Lake Storage](../azure-subscription-service-limits.md#data-lake-store-limits). | Użyj maksymalnie 4 MB operacji zapisu. |
| Azure SQL Database | Wstaw 10 000 maksymalna liczba wierszy na pojedynczej zbiorczej.<br />Wstaw 100 wierszy minimalne pojedynczej zbiorczej. <br />Zobacz [Azure SQL ogranicza](../sql-database/sql-database-resource-limits.md). |  Każdej partii jest początkowo zbiorczo wstawić z maksymalny rozmiar partii. Można podzielić na pół (aż do rozmiaru partii minimalna) oparte na partiach na temat błędów powtarzający operację z bazy danych SQL. |
| Azure Blob Storage | Zobacz [limity usługi Azure Storage](../azure-subscription-service-limits.md#storage-limits). | Rozmiar bloku maksymalna obiektu blob to 4 MB.<br />Liczba bock maksymalna obiektów blob to 50 000. |
| Azure Event Hubs  | 256 KB na komunikat. <br />Zobacz [limitów usługi Event Hubs](../event-hubs/event-hubs-quotas.md). |  Podczas partycjonowania wejścia/wyjścia nie jest wyrównana, każde zdarzenie zawiera indywidualnie w **EventData** i wysłane w partii maksymalnie maksymalny rozmiar komunikatu (1 MB dla jednostki SKU Premium). <br /><br />  Podczas partycjonowania wejścia/wyjścia jest wyrównany, wiele zdarzeń są pakowane w pojedynczej **EventData** wystąpienia maksymalnie maksymalny rozmiar komunikatu i wysyłane.  |
| Power BI | Zobacz [limitów interfejsu API Rest usługi Power BI](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Zobacz [limity usługi Azure Storage](../azure-subscription-service-limits.md#storage-limits). | Wartość domyślna to 100 jednostek na jednej transakcji. Możesz skonfigurować tak, mniejszej wartości zgodnie z potrzebami. |
| Kolejki usługi Service Bus platformy Azure   | 256 KB na komunikat.<br /> Zobacz [limitów usługi Service Bus](../service-bus-messaging/service-bus-quotas.md). | Przy użyciu pojedynczego zdarzenia na wiadomość. |
| Usługa Azure tematu usługi Service Bus | 256 KB na komunikat.<br /> Zobacz [limitów usługi Service Bus](../service-bus-messaging/service-bus-quotas.md). | Przy użyciu pojedynczego zdarzenia na wiadomość. |
| Azure Cosmos DB   | Zobacz [limity usługi Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Wielkość partii i zapisu, częstotliwość są ustawiane dynamicznie na podstawie odpowiedzi w usłudze Azure Cosmos DB. <br /> Nie ma ograniczeń wcześniej z usługi Stream Analytics. |
| Azure Functions   | | Domyślny rozmiar wsadu wynosi 262 144 bajty (256 KB). <br /> Domyślna liczba zdarzeń na partię to 100. <br /> Rozmiar partii jest konfigurowalne i można zwiększyć lub zmniejszyć w usłudze Stream Analytics [opcje wyjściowe](#azure-functions).

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> 
> [Szybki start: Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
