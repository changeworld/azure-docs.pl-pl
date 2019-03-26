---
title: Zrozumieć dane wyjściowe z usługi Azure Stream Analytics
description: W tym artykule opisano opcje danych wyjściowych danych dostępnych w usłudze Azure Stream Analytics, w tym usługi Power BI dla wyników analizy.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/21/2018
ms.custom: seodec18
ms.openlocfilehash: c22b82dcd3438a8175457aa0963d52e84d582abf
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438503"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Zrozumieć dane wyjściowe z usługi Azure Stream Analytics
W tym artykule opisano różne typy danych wyjściowych jest dostępny dla zadania usługi Azure Stream Analytics. Dane wyjściowe pozwalają na przechowywanie i zapisać wyniki zadania usługi Stream Analytics. Można wykonać, korzystając z danych wyjściowych, dalszych analiz biznesowych i danych magazynu danych.

Podczas projektowania zapytania usługi Stream Analytics można znaleźć nazwy wyjściowego przy użyciu [Klauzula INTO](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Można użyć pojedynczego wyjścia poszczególne zadania lub wielu wyjść na zadanie przesyłania strumieniowego, jeśli potrzebujesz, zapewniając wiele klauzul INTO w zapytaniu.

Do tworzenia, edytowania i przetestować zadanie usługi Stream Analytics danych wyjściowych, możesz użyć [witryny Azure portal](stream-analytics-quick-create-portal.md#configure-job-output), [programu Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [interfejsu API platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [interfejsu API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), i [programu Visual Studio](stream-analytics-quick-create-vs.md).

Obsługa typów niektóre dane wyjściowe [partycjonowanie](#partitioning), i [danych wyjściowych rozmiary partii](#output-batch-size) różnią się w celu zoptymalizowania przepływności.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics obsługuje [usługi Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store to repozytorium w hiperskali obsługujące całe przedsiębiorstwo na potrzeby obciążeń analizy dużych ilości danych (big data). Data Lake Store można przechowywać dane dowolnego rozmiaru, typu i szybkości wprowadzania dla analizy operacyjnej i rozpoznawczej. Stream Analytics musi mieć możliwość dostępu Data Lake Store.

Wyjście usługi Azure Data Lake Store z usługi Stream Analytics nie jest obecnie dostępna w regionach platformy Azure w Niemczech (T-Systems International) i Azure — Chiny (21Vianet).

### <a name="authorize-an-azure-data-lake-store-account"></a>Autoryzacja konta usługi Azure Data Lake Store

1. Po wybraniu usługi Data Lake Storage jako dane wyjściowe w witrynie Azure portal monit autoryzować połączenie istniejących w Data Lake Store.

   ![Autoryzuj połączenie, aby Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Jeśli masz już dostęp do programu Data Lake Store, wybierz opcję **autoryzować teraz** i strony pojawi się wskazujący **przekierowywanie do autoryzacji**. Po autoryzacji zakończy się powodzeniem, zostanie wyświetlona strona, która pozwala na konfigurowanie danych wyjściowych Data Lake Store.

3. Po utworzeniu konta Data Lake Store uwierzytelnionego, można skonfigurować właściwości dla danych wyjściowych usługi Data Lake Store. W poniższej tabeli jest listę nazw właściwości i ich opisy, aby skonfigurować dane wyjściowe usługi Data Lake Store.

   ![Data Lake Store jest definiowana jako dane wyjściowe usługi Stream Analytics](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania to Data Lake Store. |
| Nazwa konta | Nazwa konta usługi Data Lake Storage, gdzie w przypadku wysyłania danych wyjściowych. Zostanie wyświetlona lista rozwijana lista konta Data Lake Store, które są dostępne w Twojej subskrypcji. |
| Wzorzec prefiksu ścieżki | Ścieżka pliku używany do zapisywania plików w ramach określonego konta Data Lake Store. Można określić co najmniej jedno wystąpienie {date} i {time} zmiennych.<br /><ul><li>Przykład 1: folder1/dzienniki / {date} / {time}</li><li>Przykład 2: folder1/dzienniki / {date}</li></ul><br />Sygnatura czasowa strukturę folderów, utworzony następuje UTC, a nie czas lokalny.<br /><br />Jeśli wzorzec ścieżki plików nie zawiera końcowe "/", ostatni wzorca w polu Ścieżka pliku jest traktowany jako prefiksu nazwy pliku. <br /><br />W takiej sytuacji zostaną utworzone nowe pliki:<ul><li>Zmiany w schemacie danych wyjściowych</li><li>Zewnętrznego lub wewnętrznego ponownie zadanie.</li></ul> |
| Format daty | Opcjonalny. Jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w której pliki są uporządkowane. Przykład: RRRR/MM/DD |
|Format godziny | Opcjonalny. Jeśli token czasu jest używany w ścieżce prefiksu, należy określić format czasu, w której pliki są uporządkowane. Obecnie jedyna obsługiwana wartość to HH. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. JSON, CSV i format Avro są obsługiwane.|
| Kodowanie | Jeśli przy użyciu formatu CSV lub JSON, należy określić kodowania. UTF-8 to jedyny obsługiwany obecnie format kodowania.|
| Ogranicznik | Stosuje się tylko do serializacji woluminów CSV. Stream Analytics obsługuje różne ograniczniki dla serializacji danych CSV. Obsługiwane wartości to przecinek, średnik, miejsce, tab i pionowy pasek.|
| Format | Dotyczy tylko serializacji JSON. Rozdzielone Określa, że dane wyjściowe są formatowane przez każdy obiekt JSON, oddzielone znakiem nowego wiersza. Tablica Określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. Ta tablica jest zamknięty, tylko wtedy, gdy zadanie zostanie zatrzymane lub usługi Stream Analytics została przeniesiona następny przedział czasu. Ogólnie rzecz biorąc, lepiej jest używać wiersza rozdzielonych JSON, ponieważ nie wymaga żadnej specjalnej obsługi, gdy nadal trwa zapisywanie pliku wyjściowego do.|

### <a name="renew-data-lake-store-authorization"></a>Odnów autoryzację Data Lake Store
Musisz ponownie uwierzytelniać konta Data Lake Store, jeśli jego hasło zmieniła się od zadania utworzenia lub ostatniego uwierzytelnienia. Jeśli nie ponownego uwierzytelnienia, zadanie nie dawać wyników danych wyjściowych i pokazuje komunikat o błędzie wskazujący potrzebę ponownej w dziennikach operacji. Obecnie ma ograniczenia gdzie token uwierzytelniania wymaga ręcznego odświeżenia co 90 dni, dla wszystkich zadań z danymi wyjściowymi Data Lake Store. Jednak można rozwiązać tego ograniczenia, [uwierzytelniania za pomocą zarządzanych tożsamości (wersja zapoznawcza)](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls).

Aby odnowić autoryzację, **zatrzymać** zadania > Przejdź do usługi Data Lake Store dane wyjściowe > kliknij **Odnów autoryzację** połączyć, a przez krótki czas strony wyświetli wskazujący **przekierowywanie do Autoryzacja...** . Na stronie automatycznie zamyka i wskazuje, w przypadku powodzenia **autoryzacja została pomyślnie odnowiona**. Następnie należy kliknąć przycisk **Zapisz** w dolnej części strony i przejść przez ponowne uruchomienie zadania z **ostatniego zatrzymana** na uniknięcie utraty danych.

![Odnów autoryzację Data Lake Store w danych wyjściowych](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>SQL Database
[Usługa Azure SQL Database](https://azure.microsoft.com/services/sql-database/) mogą być używane jako dane wyjściowe dla danych relacyjnych z natury lub aplikacje, które są zależne od zawartości jest hostowana w relacyjnej bazie danych. Zadania usługi Stream Analytics zapisu do istniejącej tabeli w bazie danych SQL Azure. Schemat tabeli musi dokładnie odpowiadać, pola i ich typy, które są dane wyjściowe zadania. [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) można również określić jako dane wyjściowe za pośrednictwem także opcji output bazy danych SQL. Aby uzyskać informacje dotyczące sposobów na ulepszenie przepływność zapisu, zapoznaj się [usługi Stream Analytics z bazą danych SQL Azure jako dane wyjściowe](stream-analytics-sql-output-perf.md) artykułu. Poniższa tabela zawiera listę nazw właściwości i ich opisy tworzenia wyjście bazy danych SQL.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych kwerendy do tej bazy danych. |
| Database (Baza danych) | Nazwa bazy danych, w których w przypadku wysyłania danych wyjściowych. |
| Nazwa serwera | Nazwa serwera bazy danych SQL. |
| Nazwa użytkownika | Nazwa użytkownika, który ma dostęp do zapisu w bazie danych. Stream Analytics obsługuje tylko uwierzytelnianie SQL. |
| Hasło | Hasło służące do połączenia z bazą danych. |
| Tabela | Nazwa tabeli, w którym plik wyjściowy zostanie zapisany. Nazwa tabeli jest rozróżniana wielkość liter i schematu w tej tabeli powinna być zgodna dokładnie z numerem pola i ich typy, które są generowane przez zadanie danych wyjściowych. |
|Dziedziczą schemat partycji| Dzięki temu można dziedziczyć schematu partycjonowania z poprzedniego kroku zapytania umożliwiające topologia pełni równolegle z wielu składników zapisywania do tabeli. Aby uzyskać więcej informacji, zobacz [dane wyjściowe usługi Azure Stream Analytics, do usługi Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Liczba partii dopasowania| Zalecane górny limit liczbę rekordów wysyłane z każdego zbiorczego Wstaw transakcję.|

> [!NOTE]
> Aktualnie obsługiwana jest oferty usługi Azure SQL Database, dane wyjściowe zadania w usłudze Stream Analytics. Maszynę wirtualną platformy Azure z programem SQL Server z bazą danych dołączone nie jest obsługiwana. To może ulec zmianie w przyszłych wersjach.
>

## <a name="blob-storage"></a>Blob Storage
Magazyn obiektów blob oferuje ekonomiczne i skalowalne rozwiązanie do przechowywania dużych ilości danych bez struktury w chmurze. Aby zapoznać się w usłudze Azure Blob storage i jego użycia, zobacz dokumentację w [jak obiekty BLOB są odpowiednie](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

Poniższa tabela zawiera listę nazw właściwości i ich opisy tworzenia wyjściowego obiektu blob.

| Nazwa właściwości       | Opis                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias danych wyjściowych        | Przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania do tego magazynu obiektów blob. |
| Konto magazynu     | Nazwa konta magazynu, w których w przypadku wysyłania danych wyjściowych.               |
| Klucz konta magazynu | Klucz tajny skojarzony z kontem magazynu.                              |
| Kontener magazynu   | Kontenery umożliwiają logiczne grupowanie obiektów blob przechowywanych w usłudze Microsoft Azure Blob. Podczas przekazywania obiektu blob do usługi obiektów Blob, należy określić kontener dla tego obiektu blob. |
| Wzorzec ścieżki | Opcjonalny. Wzorzec ścieżki pliku używany do zapisywania obiektów blob w określonym kontenerze. <br /><br /> We wzorcu ścieżki mają możliwość Użyj co najmniej jednego wystąpienia daty zmiennych czasu, aby określić częstotliwość, z którą są zapisywane obiekty BLOB: <br /> {date}, {time} <br /><br />Partycjonowanie niestandardowych obiektów blob umożliwia określić jedną nazwę niestandardowego {pola} z Twoich danych zdarzenia do partycji obiektów blob. Nazwa pola to znak alfanumeryczny oraz może zawierać spacji, łączniki i podkreślenia. Następujące ograniczenia dotyczące pól niestandardowych: <ul><li>Zamierzone, Zapisz niedostatecznej (nie można odróżnić kolumny "ID" i kolumnie "id")</li><li>Zagnieżdżone pola nie są dozwolone (zamiast tego użyj aliasu w zapytaniu zadania do pola "spłaszczenia")</li><li>Nie można użyć wyrażenia jako nazwę pola.</li></ul> <br /><br /> Ta funkcja umożliwia korzystanie z konfiguracji w specyfikator formatu niestandardowego daty/godziny w ścieżce. Niestandardowa data i godzina formaty musi być określona pojedynczo, ujęta w {daty/godziny:\<specyfikator >} — słowo kluczowe. Dozwolone danych wejściowych \<specyfikator > to rrrr, MM, M, dd, d, HH, H, mm, m, ss lub s. {Daty/godziny:\<specyfikator >} — słowo kluczowe może wielokrotnie w ścieżce można użyć w celu utworzenia konfiguracji niestandardowej daty/godziny. <br /><br />Przykłady: <ul><li>Przykład 1: Klaster1/dzienniki / {date} / {time}</li><li>Przykład 2: Klaster1/dzienniki / {date}</li><li>Przykład 3: Klaster1 / {client_id} / {date} / {time}</li><li>Przykład 4: Klaster1 / {datetime:ss} / {myField} gdzie to zapytanie: Wybierz data.myField jako myField z dane wejściowe</li><li>Przykład 5: Klaster1/rok = {datetime:yyyy} / miesiąc = {datetime:MM} / dzień = {datetime:dd}</ul><br /><br />Sygnatura czasowa strukturę folderów, utworzony następuje UTC, a nie czas lokalny.<br /><br />Następująca Konwencja nazewnictwa plików są następujące: <br /><br />{Ścieżka prefiksu Pattern}/schemaHashcode_Guid_Number.extension<br /><br />Przykładowe pliki danych wyjściowych:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br /><br /> Aby uzyskać więcej informacji na temat tej funkcji, odwiedź stronę [blob niestandardowych usługi Azure Stream Analytics, partycjonowanie danych wyjściowych](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Format daty | Opcjonalny. Jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w której pliki są uporządkowane. Przykład: RRRR/MM/DD |
| Format godziny | Opcjonalny. Jeśli token czasu jest używany w ścieżce prefiksu, należy określić format czasu, w której pliki są uporządkowane. Obecnie jedyna obsługiwana wartość to HH. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. JSON, CSV i format Avro są obsługiwane. |
| Kodowanie    | Jeśli przy użyciu formatu CSV lub JSON, należy określić kodowania. UTF-8 to jedyny obsługiwany obecnie format kodowania. |
| Ogranicznik   | Stosuje się tylko do serializacji woluminów CSV. Stream Analytics obsługuje różne ograniczniki dla serializacji danych CSV. Obsługiwane wartości to przecinek, średnik, miejsca, kartę i pionowy pasek. |
| Format      | Dotyczy tylko serializacji JSON. Rozdzielone Określa, że dane wyjściowe są formatowane przez każdy obiekt JSON, oddzielone znakiem nowego wiersza. Tablica Określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. Ta tablica jest zamknięty, tylko wtedy, gdy zadanie zostanie zatrzymane lub usługi Stream Analytics została przeniesiona następny przedział czasu. Ogólnie rzecz biorąc, lepiej jest używać wiersza rozdzielonych JSON, ponieważ nie wymaga żadnej specjalnej obsługi, gdy nadal trwa zapisywanie pliku wyjściowego do. |

Podczas korzystania z usługi blob storage jako dane wyjściowe nowy plik jest tworzony w obiekcie blob w następujących przypadkach:

* Jeśli plik przekracza maksymalną liczbę dozwolonych bloków (obecnie 50 000). Może być można osiągnąć maksymalną dozwoloną liczbę bloków docierając rozmiar maksymalny dozwolony obiektu blob. Na przykład jeśli częstotliwość danych wyjściowych jest duża, możesz zobaczyć większą liczbę bajtów na blok, a rozmiar pliku jest większy. Jeśli szybkość, z danych wyjściowych jest niska, każdy blok ma mniejszą ilość danych, a rozmiar pliku jest mniejszy.
* Jeśli nastąpiła zmiana schematu w danych wyjściowych i format danych wyjściowych wymaga stałego schematu (pliku CSV i format Avro).
* Jeśli zadanie zostanie ponownie uruchomiony, zewnętrznie przez użytkownika, zatrzymując ją, a następnie uruchomiona lub wewnętrznie konserwacji lub błąd podczas odzyskiwania systemu.
* Jeśli zapytanie pełni jest podzielona na partycje, tworzony jest nowy plik dla każdej partycji danych wyjściowych.
* Usunięcie pliku lub kontener konta magazynu przez użytkownika.
* Jeśli dane wyjściowe to czas na partycje za pomocą wzorzec prefiksu ścieżki, nowy obiekt blob jest używany, gdy kwerenda przechodzi do następnej pełnej godziny.
* Jeśli dane wyjściowe jest podzielona na partycje według pola niestandardowe, nowy obiekt blob jest tworzony na klucz partycji, jeśli nie istnieje.
* Jeśli dane wyjściowe jest podzielona na partycje według pola niestandardowe, jeśli kardynalność klucza partycji przekracza 8000, nowy obiekt blob może zostać utworzony na klucz partycji.

## <a name="event-hub"></a>Centrum zdarzeń
[Usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) service to wysoce skalowalna publikowania/subskrybowania o dużych możliwościach skalowania zdarzeń. Może ona zbierać miliony zdarzeń na sekundę. Jednym z zastosowań Centrum zdarzeń jako dane wyjściowe jest, gdy dane wyjściowe zadania usługi Stream Analytics staje się dane wejściowe zadania przesyłania strumieniowego innego.

Istnieje kilka parametrów, które są wymagane do skonfigurowania strumieni danych w Centrum zdarzeń jako dane wyjściowe.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania w tym Centrum zdarzeń. |
| Przestrzeń nazw centrum zdarzeń |Przestrzeń nazw Centrum zdarzeń to kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń, utworzono przestrzeń nazw Centrum zdarzeń. |
| Nazwa centrum zdarzeń | Nazwa Centrum zdarzeń dane wyjściowe. |
| Nazwa zasad centrum zdarzeń | Zasady dostępu współdzielonego można utworzyć na karcie Konfigurowanie Centrum zdarzeń. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. |
| Klucz zasad centrum zdarzeń | Klucz dostępu współdzielonego, używany do uwierzytelniania dostępu do przestrzeni nazw Centrum zdarzeń. |
| Kolumna klucza partycji [opcjonalnie] | Ta kolumna zawiera klucz partycji dla danych wyjściowych Centrum zdarzeń. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. JSON, CSV i format Avro są obsługiwane. |
| Kodowanie | Dla woluminu CSV i JSON UTF-8 jest obsługiwany tylko format kodowania w tej chwili. |
| Ogranicznik | Stosuje się tylko do serializacji woluminów CSV. Usługa Stream Analytics obsługuje różne ograniczniki dla serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, miejsca, kartę i pionowy pasek. |
| Format | Dotyczy tylko serializacji JSON. Rozdzielone Określa, że dane wyjściowe są formatowane przez każdy obiekt JSON, oddzielone znakiem nowego wiersza. Tablica Określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. Ta tablica jest zamknięty, tylko wtedy, gdy zadanie zostanie zatrzymane lub usługi Stream Analytics została przeniesiona następny przedział czasu. Ogólnie rzecz biorąc, lepiej jest używać wiersza rozdzielonych JSON, ponieważ nie wymaga żadnej specjalnej obsługi, gdy nadal trwa zapisywanie pliku wyjściowego do. |
| Kolumny właściwości [opcjonalnie] | Kolumny, które muszą być dołączane jako użytkownika właściwości komunikatu wychodzącego zamiast ładunku rozdzielone przecinkami. Więcej informacji na temat tej funkcji w sekcji "Niestandardowe właściwości metadanych dla danych wyjściowych" |

## <a name="power-bi"></a>Power BI
[Usługa Power BI](https://powerbi.microsoft.com/) może służyć jako dane wyjściowe zadania usługi Stream Analytics zapewnienie rozbudowanego środowiska wizualizacji wyników analiz. Ta funkcja może służyć do operacyjne pulpity nawigacyjne, generowania raportu i metryk opartych na zgłoszenie.

Wyjście usługi Power BI z usługą Stream Analytics nie jest obecnie dostępna w regionach platformy Azure w Niemczech (T-Systems International) i Azure — Chiny (21Vianet).

### <a name="authorize-a-power-bi-account"></a>Autoryzacja konta usługi Power BI
1. Po wybraniu usługi Power BI jako dane wyjściowe w witrynie Azure portal, zostanie wyświetlony monit, aby zezwolić istniejącym użytkownikiem usługi Power BI lub Utwórz nowe konto usługi Power BI.
   
   ![Autoryzacja użytkownika usługi Power BI, aby skonfigurować dane wyjściowe](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Utwórz nowe konto, jeśli nie jeszcze masz, kliknij przycisk Autoryzuj teraz. Jest wyświetlana następująca strona:
   
   ![Uwierzytelnianiu w usłudze Power BI z konta platformy Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. W tym kroku należy podać konto służbowe lub szkolne autoryzowanie danych wyjściowych usługi Power BI. Jeśli nie już wcześniej zalogowano w usłudze Power BI, wybierz opcję Zarejestruj teraz. Konto służbowe lub szkolne, którego używasz dla usługi Power BI może różnić się od konta subskrypcji platformy Azure, które jest aktualnie zalogowany przy użyciu.

### <a name="configure-the-power-bi-output-properties"></a>Konfigurowanie właściwości danych wyjściowych usługi Power BI
Po utworzeniu konta usługi Power BI uwierzytelniony, można skonfigurować właściwości dla danych wyjściowych usługi Power BI. W poniższej tabeli jest listę nazw właściwości i ich opisy, aby skonfigurować dane wyjściowe usługi Power BI.

| Nazwa właściwości | description |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania do tych danych wyjściowych usługi Power BI. |
| Obszar roboczy grupy |Aby umożliwić udostępnianie danych innym użytkownikom usługi Power BI możesz wybierz grupy w ramach konta usługi Power BI lub wybierz pozycję "Mój obszar roboczy", jeśli nie chcesz zapisać do grupy. Aktualizowanie istniejącej grupy wymaga, ponowne uwierzytelnianie w usłudze Power BI. |
| Nazwa zestawu danych |Podaj nazwę zestawu danych żądanej dla danych wyjściowych usługi Power BI do użycia |
| Nazwa tabeli |Podaj nazwę tabeli w zestawie danych, danych wyjściowych usługi Power BI. Obecnie usługa Power BI dane wyjściowe zadania usługi Stream Analytics może mieć tylko jedną tabelę w zestawie danych |

Omówienie konfigurowania danych wyjściowych usługi Power BI i pulpitów nawigacyjnych, zobacz [usługi Azure Stream Analytics i Power BI](stream-analytics-power-bi-dashboard.md) artykułu.

> [!NOTE]
> Nie należy jawnie tworzyć zestaw danych i tabeli na pulpicie nawigacyjnym usługi Power BI. Zestaw danych i tabeli jest automatycznie wypełniane podczas uruchamiania zadania i zadania, uruchamia przekazujący dane wyjściowe do usługi Power BI. Należy pamiętać, że jeśli zapytanie zadania nie generuje żadnych wyników, zestawu danych i tabeli nie jest tworzony. Należy pamiętać, że jeśli w usłudze Power BI był już zestaw danych i tabelę z taką samą nazwę jak dostarczonego w ramach tego zadania usługi Stream Analytics, zastąpione istniejących danych.
>

### <a name="schema-creation"></a>Tworzenie schematu
Usługa Azure Stream Analytics tworzy zestaw danych usługi Power BI i tabeli w imieniu użytkownika, jeśli już nie istnieje. We wszystkich innych przypadkach tabela jest aktualizowana z nowymi wartościami. Obecnie ma ograniczenia tego tylko w jednej tabeli może istnieć w zestawie danych. Usługa Power BI używa FIFO zasady przechowywania. Po włączeniu dane będą zbierane w tabeli, dopóki trafienia 200 000 wierszy.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Konwersja typu danych z usługi Stream Analytics w usłudze Power BI
Usługa Azure Stream Analytics aktualizacji modelu danych dynamicznie w czasie wykonywania, jeśli zmieni się schemat danych wyjściowych. Zmiany nazw kolumn, zmiana typu kolumny oraz dodawanie i usuwanie kolumn są wszystkie śledzone.

W tej tabeli opisano konwersje typów danych z [typów danych usługi Stream Analytics](https://msdn.microsoft.com/library/azure/dn835065.aspx) do zasilania BIs [typy Entity Data Model (EDM) struktury](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) zestawu danych usługi POWER BI i tabeli nie istnieją.

Z usługi Stream Analytics | To Power BI
-----|-----
bigint | Int64
nvarchar(max) | Ciąg
datetime | Data/godzina
float | Podwójne
Tablica rekordu | Ciąg typu wartości stałej "IRecord" lub "IArray"

### <a name="schema-update"></a>Aktualizacja schematu
Stream Analytics wnioskuje schemat modelu danych, na podstawie pierwszego zestawu zdarzenia w danych wyjściowych. Później Jeśli to konieczne, do obsługi zdarzeń przychodzących, które mogą nie pasować do oryginalnego schematu jest aktualizowana schematu modelu danych.

`SELECT *` Zapytania należy unikać zapobiegające aktualizacja schematu dynamicznego w wierszach. Oprócz potencjalny wpływ na wydajność może to również spowodować niepewność co do czasu potrzebnego dla wyników. Należy wybrać dokładnie pola, które muszą być wyświetlane na pulpicie nawigacyjnym usługi Power BI. Ponadto wartości danych powinny być zgodne z wybranym typem danych.


Poprzednie/bieżąca | Int64 | Ciąg | Data/godzina | Podwójne
-----------------|-------|--------|----------|-------
Int64 | Int64 | Ciąg | Ciąg | Podwójne
Podwójne | Podwójne | Ciąg | Ciąg | Podwójne
Ciąg | Ciąg | Ciąg | Ciąg | Ciąg 
Data/godzina | Ciąg | Ciąg |  Data/godzina | Ciąg


### <a name="renew-power-bi-authorization"></a>Odnów autoryzację BI zasilania
Jeśli hasło konta usługi Power BI zmieni się po utworzenia lub ostatniego uwierzytelnienia zadania usługi Stream Analytics, musisz ponownie uwierzytelniać usługi Stream Analytics. Jeśli uwierzytelnianie wieloskładnikowe (MFA) jest skonfigurowany w dzierżawie usługi Azure Active Directory (AAD), należy odnowić autoryzację usługi Power BI, co dwa tygodnie. Objawem tego problemu jest nie danych wyjściowych zadań i "Uwierzytelnianie użytkownika error" w dziennikach operacji:

  ![Usługa Power BI uwierzytelniania błąd użytkownika](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Aby rozwiązać ten problem, Zatrzymaj uruchomione zadania, a następnie przejdź do usługi Power BI danych wyjściowych. Wybierz **Odnów autoryzację** połączyć, a następnie uruchom ponownie zadanie z **ostatniego zatrzymana** na uniknięcie utraty danych.

  ![Odnów autoryzację usługi Power BI dla danych wyjściowych](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Table Storage
[Usługa Azure Table storage](../storage/common/storage-introduction.md) oferuje magazyn o wysokiej dostępności, skalowalności, dzięki czemu aplikacja może automatycznie skalować w celu spełnienia określonych wymagań użytkownika. Magazyn tabel to magazyn klucz atrybut NoSQL firmy Microsoft, który z nich mogą korzystać z danych strukturalnych z mniejszą liczbą ograniczeniami w schemacie. Usługa Azure Table storage może służyć do przechowywania danych dla stanu trwałego i efektywne pobieranie.

Poniższa tabela zawiera listę nazw właściwości i ich opisy, tworzenia danych wyjściowych tabeli.

| Nazwa właściwości | description |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania do magazynu w tej tabeli. |
| Konto magazynu |Nazwa konta magazynu, w których w przypadku wysyłania danych wyjściowych. |
| Klucz konta magazynu |Klucz dostępu skojarzone z kontem magazynu. |
| Nazwa tabeli |Nazwa tabeli. Tabela zostanie utworzona, jeśli nie istnieje. |
| Klucz partycji |Nazwa kolumny wyjściowej zawierającej klucz partycji. Klucz partycji to unikatowy identyfikator dla partycji w danej tabeli, który wchodzi w skład pierwszej części klucza podstawowego jednostki. Jest to wartość ciągu, który może być 1 KB rozmiaru. |
| Klucz wiersza |Nazwa kolumny wyjściowej zawierającej klucz wiersza. Klucz wiersza to unikatowy identyfikator jednostki w danej partycji. Wchodzi w skład drugiej części klucza podstawowego jednostki. Klucz wiersza jest wartość ciągu, który może być 1 KB rozmiaru. |
| Rozmiar partii |Liczba rekordów do operacji przetwarzania wsadowego. Wartość domyślna (100) jest wystarczające dla większości zadań. Zapoznaj się [Specyfikacja operacji zbiorczej tabeli](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) Aby uzyskać więcej informacji na temat modyfikowania tego ustawienia. |

## <a name="service-bus-queues"></a>Kolejki usługi Service Bus
[Kolejki usługi Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) oferują pierwszy na wejściu — pierwszy na wyjściu (FIFO) dostarczanie komunikatów dla co najmniej jeden konkurujących konsumentów. Komunikaty są zwykle odbierane i przetwarzane przez odbiorców w kolejności, w którym zostały one dodane do kolejki, a każdy komunikat jest odbierany i przetwarzany przez tylko jednego odbiorcę komunikatów.

Poniższa tabela zawiera listę nazw właściwości i ich opisy tworzenia kolejki dane wyjściowe.

| Nazwa właściwości | description |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych kwerendy do tej kolejki usługi Service Bus. |
| Przestrzeń nazw magistrali usług |Przestrzeń nazw usługi Service Bus to kontener dla zestawu jednostek do obsługi komunikatów. |
| Nazwa kolejki |Nazwa kolejki usługi Service Bus. |
| Nazwa zasad kolejki |Podczas tworzenia kolejki można też utworzyć zasady dostępu współdzielonego, na karcie Konfigurowanie kolejki. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. |
| Klucz zasad kolejki |Dostęp współdzielony klucz używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus |
| Format serializacji zdarzeń |Format serializacji danych wyjściowych. JSON, CSV i format Avro są obsługiwane. |
| Kodowanie |Dla woluminu CSV i JSON UTF-8 jest obsługiwany tylko format kodowania w tej chwili |
| Ogranicznik |Stosuje się tylko do serializacji woluminów CSV. Usługa Stream Analytics obsługuje różne ograniczniki dla serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, miejsca, kartę i pionowy pasek. |
| Format |Dotyczy tylko typu JSON. Rozdzielone Określa, że dane wyjściowe są formatowane przez każdy obiekt JSON, oddzielone znakiem nowego wiersza. Tablica Określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. |
| Kolumny właściwości [opcjonalnie] | Kolumny, które muszą być dołączane jako użytkownika właściwości komunikatu wychodzącego zamiast ładunku rozdzielone przecinkami. Więcej informacji na temat tej funkcji w sekcji "Niestandardowe właściwości metadanych dla danych wyjściowych" |

Liczba partycji wynosi [na podstawie jednostki SKU magistrali usług i rozmiaru](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.

## <a name="service-bus-topics"></a>Tematy dotyczące usługi Service Bus
Gdy kolejek usługi Service Bus umożliwiają komunikację jeden-do-jednego od nadawcy do odbiorcy, [tematów usługi Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) zapewniają formę komunikacji jeden do wielu.

Poniższa tabela zawiera listę nazw właściwości i ich opisy, tworzenia danych wyjściowych tabeli.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania do tego tematu usługi Service Bus. |
| Przestrzeń nazw magistrali usług |Przestrzeń nazw usługi Service Bus to kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń jest tworzona przestrzeni nazw usługi Service Bus |
| Nazwa tematu |Tematy to jednostki przypominające centra zdarzeń i kolejki obsługi komunikatów. Służą one do zbierania strumieni zdarzeń z różnych urządzeń i usług. Po utworzeniu tematu jest również podana określonej nazwy. Komunikaty wysyłane do tematu nie jest dostępny, chyba że zostanie utworzona subskrypcja, upewnij się, więc istnieją co najmniej jedną subskrypcję w ramach tematu |
| Nazwa zasad tematu |Podczas tworzenia tematu można też utworzyć zasady dostępu współdzielonego, na karcie Konfigurowanie tematu. Wszystkie zasady dostępu współdzielonego ma nazwę, uprawnienia, ustaw i klawisze dostępu |
| Klucz zasad tematu |Dostęp współdzielony klucz używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus |
| Format serializacji zdarzeń |Format serializacji danych wyjściowych. JSON, CSV i format Avro są obsługiwane. |
| Kodowanie |Jeśli przy użyciu formatu CSV lub JSON, należy określić kodowania. UTF-8 jest obsługiwany tylko format kodowania w tej chwili |
| Ogranicznik |Stosuje się tylko do serializacji woluminów CSV. Usługa Stream Analytics obsługuje różne ograniczniki dla serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, miejsca, kartę i pionowy pasek. |
| Kolumny właściwości [opcjonalnie] | [Opcjonalnie] Kolumny, które muszą być dołączane jako użytkownika właściwości komunikatu wychodzącego zamiast ładunku rozdzielone przecinkami. Więcej informacji na temat tej funkcji w sekcji "Niestandardowe właściwości metadanych dla danych wyjściowych" |

Liczba partycji wynosi [na podstawie jednostki SKU magistrali usług i rozmiaru](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Usługa Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) to globalnie dystrybuowana, wielomodelowa baza danych usługi, że oferty nieograniczone możliwości elastycznego skalowania w całym świecie, obsługą zaawansowanych zapytań i automatyczne indeksowanie w modelach danych niezależnej od schematu gwarantowane małe opóźnienia i wiodącą w branży kompleksowe umowy SLA. Aby dowiedzieć się więcej o opcjach kolekcję usługi Cosmos DB dla usługi Stream Analytics, zobacz [usługi Stream Analytics z usługą Cosmos DB jako dane wyjściowe](stream-analytics-documentdb-output.md) artykułu.

Usługa Azure Cosmos DB dane wyjściowe z usługi Stream Analytics nie jest obecnie dostępna w regionach platformy Azure w Niemczech (T-Systems International) i Azure — Chiny (21Vianet).

> [!Note]
> W tej chwili usługi Azure Stream Analytics obsługuje tylko połączenia do bazy danych cosmos DB przy użyciu **interfejsu API SQL**.
> Innych interfejsów API usługi Azure Cosmos DB nie są jeszcze obsługiwane. Jeśli punkt Azure Stream Analytics do kont usługi Azure Cosmos DB utworzone z innymi interfejsami API, dane mogą nie być prawidłowo przechowywane.

W poniższej tabeli opisano właściwości do utworzenia dane wyjściowe usługi Azure Cosmos DB.

| Nazwa właściwości | description |
| --- | --- |
| Alias danych wyjściowych | Odwoływanie się aliasu to dane wyjściowe w zapytaniu usługi Stream Analytics. |
| Ujście | Cosmos DB |
| Opcja importu | Wybierz opcję "Wybierz usługę Cosmos DB z subskrypcji", lub "Ustawienia Podaj usługi Cosmos DB ręcznie".
| Identyfikator konta | Nazwa lub identyfikator URI konta usługi Cosmos DB punktu końcowego. |
| Klucz konta | Klucz dostępu współdzielonego dla konta usługi Cosmos DB. |
| Database (Baza danych) | Nazwa bazy danych Cosmos DB. |
| Wzorzec nazw kolekcji | Nazwa kolekcji lub ich wzorzec dla kolekcji, które ma być używany. <br />Format nazw kolekcji można skonstruować przy użyciu opcjonalnego tokenu {partition}, gdzie partycje zaczynają się od 0. Dwa przykłady:  <br />1. _MyCollection_ — jedna kolekcja o nazwie "MyCollection" musi istnieć.  <br />2. _MyCollection {partition}_ — na podstawie kolumny partycjonowania. <br />Musi istnieć partycjonowania kolekcji kolumny — "MyCollection0", "MyCollection1", "MyCollection2" i tak dalej. |
| Klucz partycji | Opcjonalny. Jest to potrzebne tylko wtedy, jeśli używasz tokenu {partition} w Twojej wzorzec nazw kolekcji.<br /> Klucz partycji to nazwa pola w zdarzeniach wyjściowych służąca do określenia klucza do partycjonowania danych wyjściowych w kolekcjach.<br /> Jedną kolekcję danych wyjściowych można użyć dowolnej kolumny dowolne dane wyjściowe. Na przykład, identyfikator partycji. |
| Identyfikator dokumentu |Opcjonalny. Nazwa pola w zdarzeniach wyjściowych służąca do określenia klucza podstawowego, na które insert nebo update bazują operacje.

## <a name="azure-functions"></a>Azure Functions
Azure Functions to bezserwerowa usługa obliczeniowa, która umożliwia uruchamianie kodu na żądanie bez konieczności jawnego przydzielania infrastruktury ani zarządzania tą infrastrukturą. Umożliwia Implementowanie kodu wyzwalanego przez zdarzenia występujące na platformie Azure lub usług innych firm. Ta możliwość odpowiadania na Wyzwalacze usługi Azure Functions sprawia, że stanowi naturalne wyjście dla usługi Azure Stream Analytics. Ta karta danych wyjściowych pozwala użytkownikom na łączenie Stream Analytics z usługi Azure Functions i uruchamiać skrypty lub fragment kodu w odpowiedzi na szereg zdarzeń.

Azure wyjścia usługi Functions z usługi Stream Analytics nie jest obecnie dostępna w regionach platformy Azure w Niemczech (T-Systems International) i Azure — Chiny (21Vianet).

Usługa Azure Stream Analytics wywołuje usługi Azure Functions za pomocą wyzwalaczy protokołu HTTP. Nowa karta dane wyjściowe funkcji platformy Azure jest dostępna z poniższymi właściwościami można skonfigurować:

| Nazwa właściwości | description |
| --- | --- |
| Aplikacja funkcji |Nazwa aplikacji usługi Azure Functions |
| Funkcja |Nazwa funkcji w aplikacji usługi Azure Functions |
| Klucz |Jeśli chcesz użyć funkcji platformy Azure z innej subskrypcji, możesz to zrobić, podając klawisz, aby dostęp do funkcji |
| Maksymalny rozmiar partii |Ta właściwość może służyć do ustawiania maksymalnego rozmiaru poszczególnych partii danych wyjściowych, które są wysyłane do funkcji platformy Azure. Jednostki danych wejściowych jest w bajtach. Domyślnie ta wartość jest 262 144 bajty (256 KB) |
| Maksymalna liczba partii  |Jak wskazuje nazwa, ta właściwość pozwala określić maksymalną liczbę zdarzeń w każdej z partii, który jest wysyłany do usługi Azure Functions. Wartość domyślna partii maksymalna liczba wynosi 100 |

W przypadku usługi Azure Stream Analytics otrzymuje 413 (żądanie podmiotu zbyt duże protokołu http) wyjątku od funkcji platformy Azure, to zmniejszy rozmiar partii wysyłanych do usługi Azure Functions. W kodzie funkcji platformy Azure Użyj tego wyjątku, aby upewnić się, że usługi Azure Stream Analytics nie wysyła za dużych partii. Ponadto upewnij się, że wartości rozmiaru i liczby max batch używany w funkcji są zgodne z wartościami podanymi w portalu usługi Stream Analytics.

Ponadto w sytuacji, gdy istnieje żadne zdarzenie kierowanych do przedział czasu, jest generowane żadne dane wyjściowe. W rezultacie nie jest wywoływana funkcja computeResult. To zachowanie jest zgodne z wbudowanych funkcji agregujących okna.

## <a name="custom-metadata-properties-for-output"></a>Właściwości niestandardowych metadanych dla danych wyjściowych 

Ta funkcja umożliwia dołączenie kolumn kwerendy jako właściwości użytkownika do wiadomości wychodzących. Te kolumny, które nie przejdą w ładunku. Te właściwości są obecne w formie słownika komunikatu wyjściowego. Klucz jest nazwa kolumny, a wartość jest wartością kolumny ze słownika właściwości. Wszystkie typy danych usługi Stream Analytics są obsługiwane z wyjątkiem rekordu i tablicy.  

Obsługiwane dane wyjściowe: 
* Kolejki usługi Service Bus 
* Tematy dotyczące usługi Service Bus 
* Centrum zdarzeń 

Przykład: W poniższym przykładzie dodamy 2 pola DeviceId i DeviceStatus metadanych. 
* Zapytanie: `select *, DeviceId, DeviceStatus from iotHubInput` .
* Konfiguracja danych wyjściowych: `DeviceId,DeviceStatus`.

![Kolumny właściwości](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Dane wyjściowe właściwości komunikatów inspekcji w Centrum zdarzeń za pomocą [Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

   ![Niestandardowe właściwości zdarzenia](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Partycjonowanie

Poniższa tabela zawiera podsumowanie obsługi partycji i liczby modułów zapisywania danych wyjściowych, dla każdego typu danych wyjściowych:

| Typ wyjścia | Partycjonowanie pomocy technicznej | Klucz partycji  | Liczba modułów zapisywania danych wyjściowych |
| --- | --- | --- | --- |
| Azure Data Lake Store | Yes | Użyj {date} i {time} tokenów w wzorzec prefiksu ścieżki. Wybierz format daty, takich jak RRRR/MM/DD, DD/MM/RRRR, MM-DD-RRRR. HH jest używany dla formatu godziny. | Następuje partycjonowania danych wejściowych dla [zapytań pełni równoległego](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Yes | Oparte na klauzuli PARTITION BY w zapytaniu | Następuje partycjonowania danych wejściowych dla [zapytań pełni równoległego](stream-analytics-scale-jobs.md). Aby dowiedzieć się więcej na temat osiągania lepiej przepływności wydajność zapisu podczas ładowania danych do bazy danych SQL Azure, odwiedź stronę [dane wyjściowe usługi Azure Stream Analytics, do usługi Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Yes | Użyj {date} i {time} tokeny od pól zdarzeń we wzorcu ścieżki. Wybierz format daty, takich jak RRRR/MM/DD, DD/MM/RRRR, MM-DD-RRRR. HH jest używany dla formatu godziny. Dane wyjściowe obiektu blob można podzielić na partycje atrybut pojedyncze zdarzenie niestandardowe {fieldname} lub {daty/godziny:\<specyfikator >}. | Następuje partycjonowania danych wejściowych dla [zapytań pełni równoległego](stream-analytics-scale-jobs.md). |
| Centrum zdarzeń Azure | Yes | Yes | Różni się w zależności od wyrównania partycji.<br /> Gdy dane wyjściowe, które Centrum zdarzeń klucza partycji jest równie powiązana z nadrzędnego (poprzednia wersja) krok zapytania, liczba składników zapisywania jest taka sama liczba danych wyjściowych partycji Centrum zdarzeń. Każdy moduł zapisujący korzysta z Centrum EventHub w [klasy EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) do wysyłania zdarzeń do określonej partycji. <br /> Gdy danych wyjściowych Centrum zdarzeń, klucz partycji nie jest wyrównana z nadrzędnego (poprzednia wersja) krok zapytania, liczba składników zapisywania jest taka sama jak liczba partycji w tym w poprzednim kroku. Każdy moduł zapisujący używa EventHubClient [klasy SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) do wysyłania zdarzeń do wszystkich partycji danych wyjściowych. |
| Power BI | Nie | Brak | Nie dotyczy. |
| Azure Table Storage | Yes | Wszystkie kolumny wyjściowej.  | Następuje partycjonowania danych wejściowych dla [pełni zrównoleglona zapytania](stream-analytics-scale-jobs.md). |
| Temat usługi Azure Service Bus | Yes | Wybierane automatycznie. Liczba partycji jest oparty na [usługi Service Bus w jednostki SKU i rozmiar](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.| Taka sama jak liczba partycji w temacie dotyczącym danych wyjściowych.  |
| Kolejki usługi Azure Service Bus | Yes | Wybierane automatycznie. Liczba partycji jest oparty na [usługi Service Bus w jednostki SKU i rozmiar](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.| Taka sama jak liczba partycji w kolejki wyjściowej. |
| Azure Cosmos DB | Yes | Użyj tokenu {partition} w wzorzec nazw kolekcji. wartość {partition} opiera się na klauzuli PARTITION BY w zapytaniu. | Następuje partycjonowania danych wejściowych dla [pełni zrównoleglona zapytania](stream-analytics-scale-jobs.md). |
| Azure Functions | Nie | Brak | Nie dotyczy. |

Jeśli karty danych wyjściowych nie jest podzielona na partycje, Brak danych w jednej partycji danych wejściowych spowoduje opóźnienie maksymalnie późnego przybycia ilość czasu.  W takich przypadkach dane wyjściowe są scalane w jednym składniku zapisywania, co może spowodować wąskie gardła w potoku. Aby dowiedzieć się więcej na temat późnego przybycia zasad, odwiedź stronę [zagadnienia dotyczące kolejności zdarzeń usługi Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Rozmiar partii danych wyjściowych
Usługa Azure Stream Analytics używa partii o zmiennym rozmiarze do przetwarzania zdarzeń i zapisywania danych wyjściowych. Zazwyczaj aparat usługi Stream Analytics nie zapisuje jeden komunikat w danym momencie i używa partie w celu zwiększenia wydajności. Gdy przychodzącego i wychodzącego częstotliwość zdarzeń jest duża, używa większych partii. Gdy współczynnik ruchu wychodzącego jest niskie, używa mniejsze segmenty do niskich opóźnień.

W poniższej tabeli opisano niektóre zagadnienia, dane wyjściowe przetwarzania wsadowego:

| Typ wyjścia | Maksymalny rozmiar wiadomości | Optymalizacja rozmiaru partii |
| :--- | :--- | :--- |
| Azure Data Lake Store | Zobacz [limity usługi Data Lake Storage](../azure-subscription-service-limits.md#data-lake-store-limits) | Maksymalnie 4 MB operacji zapisu |
| Azure SQL Database | Wstaw 10 000 maksymalna liczba wierszy na pojedynczej zbiorczej<br />Minimum 100 wierszy wstawiania zbiorczego pojedynczego <br />Zobacz też [limity usługi Azure SQL](../sql-database/sql-database-resource-limits.md) |  Każdej partii jest początkowo zbiorczo wstawić z maksymalny rozmiar partii i może podzielić partii połowę (aż do minimalny rozmiar partii) oparte na błędy powtarzający operację z bazy danych SQL. |
| Azure Blob Storage | Zobacz [limity usługi Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Maksymalny rozmiar blokowych obiektów Blob to 4 MB<br />Maksymalna liczba bock obiektów Blob jest 50000 |
| Centrum zdarzeń Azure   | 256 KB na komunikat <br />Zobacz też [limitów usługi Event Hubs](../event-hubs/event-hubs-quotas.md) |   Podczas partycjonowania Firmware nie wyrównać, każde zdarzenie jest spakowany indywidualnie w EventData i wysłane w partii o rozmiarze maksymalny komunikatu (1 MB w wersji Premium). <br /><br />  Podczas partycjonowania operacje We / Wy jest wyrównany, wiele zdarzeń są pakowane w jednym EventData maksymalny komunikatu o rozmiarze i wysyłane.  |
| Power BI | Zobacz [limitów interfejsu API Rest usługi Power BI](https://msdn.microsoft.com/library/dn950053.aspx) |
| Azure Table Storage | Zobacz [limity usługi Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Domyślny jest 100 jednostek na pojedynczej transakcji i mogą być skonfigurowane na mniejszą wartość zgodnie z potrzebami. |
| Kolejki usługi Service Bus platformy Azure   | 256 KB na komunikat<br /> Zobacz też [limitów usługi Service Bus](../service-bus-messaging/service-bus-quotas.md) | Pojedyncze zdarzenie dla komunikatu |
| Usługa Azure tematu usługi Service Bus | 256 KB na komunikat<br /> Zobacz też [limitów usługi Service Bus](../service-bus-messaging/service-bus-quotas.md) | Pojedyncze zdarzenie dla komunikatu |
| Azure Cosmos DB   | Zobacz [limity usługi Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits) | Batch size i zapisu, częstotliwość to skorygowany dynamicznie na podstawie odpowiedzi bazy danych cosmos DB. <br /> Brak ograniczeń wcześniej z usługi Stream Analytics. |
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
