---
title: Zrozumieć dane wyjściowe z usługi Azure Stream Analytics
description: W tym artykule opisano danych wyjściowych opcje dostępne w Azure Stream Analytics, w tym usługi Power BI dla wyników analizy.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: 10d7b5d3670bd7a5f289a6f9f2754ecc6aa18795
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Zrozumieć dane wyjściowe z usługi Azure Stream Analytics
W tym artykule opisano różne typy danych wyjściowych dostępne zadania usługi analiza strumienia Azure. Dane wyjściowe umożliwiają przechowywanie i Zapisz wyniki zadania usługi analiza strumienia. Za pomocą danych wyjściowych, można wykonać dalszego analiz biznesowych i danych magazynu danych. 

Podczas projektowania zapytania Stream Analytics odwoływać się do nazwy w danych wyjściowych przy użyciu [w klauzuli](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Można użyć pojedynczego wyjścia na zadanie lub wielu wyjść na zadania przesyłania strumieniowego, jeśli potrzebujesz zapewniając wielu klauzule INTO w zapytaniu.

Do tworzenia, edytowania i przetestować zadanie usługi analiza strumienia danych wyjściowych, można użyć [portalu Azure](stream-analytics-quick-create-portal.md#configure-output-to-the-job), [programu Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.Net interfejsu API](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [interfejsu API REST](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-output), i [programu Visual Studio](stream-analytics-tools-for-visual-studio.md).

Niektóre dane wyjściowe typy obsługi [partycjonowania](#partitioning), i [output rozmiarów partii](#output-batch-size) różnią się w celu zoptymalizowania przepływności.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Strumienia Analytics obsługuje [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store to repozytorium w hiperskali obsługujące całe przedsiębiorstwo na potrzeby obciążeń analizy dużych ilości danych (big data). Data Lake Store można przechowywać dane dowolnego rozmiar, typ i wprowadzanie szybkość analiz operacyjnych i poznawczych. Analiza strumienia musi mieć uprawnienie dostępu do usługi Data Lake Store.

### <a name="authorize-an-azure-data-lake-store-account"></a>Autoryzuj konto usługi Azure Data Lake Store

1. Po wybraniu jako dane wyjściowe w portalu Azure Data Lake Storage monit autoryzować połączenia do istniejącej usługi Data Lake Store.  

   ![Autoryzowanie usługi Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

2. Jeśli masz już dostępu do usługi Data Lake Store, wybierz **autoryzować teraz** i wyskakującej wskazujący stronę **przekierowywanie do autoryzacji**. Po autoryzacji zakończy się powodzeniem, jest wyświetlana strona, która pozwala na skonfigurowanie danych wyjściowych usługi Data Lake Store.

3. Po utworzeniu konta usługi Data Lake Store uwierzytelnionego, można skonfigurować właściwości dla danych wyjściowych usługi Data Lake Store. W poniższej tabeli jest listą nazw właściwości i ich opis, aby skonfigurować dane wyjściowe usługi Data Lake Store.

   ![Autoryzowanie usługi Data Lake Store](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

| Nazwa właściwości | Opis | 
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używane w zapytaniach w celu przekierowania wyników zapytania do tej usługi Data Lake Store. | 
| Nazwa konta | Nazwa konta usługi Data Lake Storage, gdzie wysyłania danych wyjściowych. Jest wyświetlana z listy rozwijanej, kont usługi Data Lake Store, które są dostępne w Twojej subskrypcji. |
| Wzorzec prefiksu ścieżki | Ścieżka do pliku używany do zapisywania plików w ramach określonego konta magazynu usługi Data Lake. Można określić co najmniej jedno wystąpienie elementu {date} i {time} zmiennych.</br><ul><li>Przykład 1: folder1/dzienniki / {date} / {time}</li><li>Przykład 2: folder1/dzienniki / {date}</li></ul>Jeśli wzorzec ścieżki plików nie zawiera końcowe "/", ostatni wzorca w ścieżce pliku jest traktowany jako prefiksu nazwy pliku. </br></br>W takiej sytuacji są tworzone nowe pliki:<ul><li>Zmiany w schemacie danych wyjściowych</li><li>Zewnętrznym lub wewnętrznym ponownego uruchomienia zadania.</li></ul> |
| Format daty | Opcjonalny. Jeśli token daty jest używany w ścieżce prefiks, można wybrać format daty, w którym pliki są organizowane. Przykład: RRRR/MM/dd. |
|Format godziny | Opcjonalny. Jeśli czas nie jest używany w ścieżce prefiks, określ format czasu, w którym pliki są organizowane. Obecnie jedyna obsługiwana wartość to HH. |
| Format serializacji zdarzeń | Format serializacji w danych wyjściowych. JSON, CSV i Avro są obsługiwane.| 
| Encoding | Jeśli przy użyciu formatu CSV lub JSON, należy określić kodowania. UTF-8 to jedyny obsługiwany obecnie format kodowania.|
| Ogranicznik | Dotyczy tylko serializacji woluminów CSV. Analiza strumienia obsługuje różne ograniczniki dla serializacji danych CSV. Obsługiwane wartości to przecinek, średnik, miejsca, karta i pionowy pasek.|
| Format | Dotyczy tylko serializacji JSON. Rozdzielone Określa, czy wynik jest formatowany przez poszczególne obiekty JSON rozdzielone znakiem nowego wiersza. Tablica Określa, czy dane wyjściowe są sformatowane jako tablica obiektów JSON. Ta tablica jest zamknięty, tylko wtedy, gdy zadanie zostanie zatrzymane lub Stream Analytics została przeniesiona następne okno czasu. Ogólnie rzecz biorąc, zaleca się używania wiersza oddzielone JSON, ponieważ nie wymaga żadnej specjalnej obsługi, gdy wciąż trwa zapisywanie pliku wyjściowego do.|

### <a name="renew-data-lake-store-authorization"></a>Odnów autoryzacji usługi Data Lake Store
Musisz ponownie uwierzytelniać konta usługi Data Lake Store, jeśli jego hasło uległ zmianie od czasu utworzenia lub ostatniej uwierzytelniony zadania. Jeśli nie ponownego uwierzytelnienia, zadania nie dostarczyło wyników danych wyjściowych i zawiera błąd wskazujący potrzebę ponowna autoryzacja w dzienników operacji. Obecnie jest to ograniczenie gdzie token uwierzytelniania musi być ręcznie odświeżyć co 90 dni, dla wszystkich zadań usługi Data Lake Store w danych wyjściowych. 

Odnowić autoryzacji, **zatrzymać** zadania > Przejdź do usługi Data Lake Store dane wyjściowe > kliknij **odnowić autoryzacji** łączenie i krótki czas strony będzie wyskakujące wskazujący **przekierowywanie do autoryzacji...** . Strona automatycznie zamyka i w razie powodzenia oznacza **autoryzacji został pomyślnie odnowiony**. Następnie należy kliknąć pozycję **zapisać** w dolnej części strony i są przetwarzane przez ponowne uruchomienie zadania z **czas ostatniego zatrzymania** w celu uniknięcia utraty danych.

![Autoryzowanie usługi Data Lake Store](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>SQL Database
[Baza danych SQL Azure](https://azure.microsoft.com/services/sql-database/) może służyć jako dane wyjściowe, w charakterze relacyjnym lub aplikacje zależne od hostowanych w relacyjnej bazie danych. Zadania usługi analiza strumienia zapisu do istniejącej tabeli w bazie danych SQL Azure.  Schemat tabeli musi dokładnie odpowiadać pola i ich typy są dane wyjściowe z zadania. [Magazyn danych SQL Azure](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) można również określić jako dane wyjściowe za pośrednictwem także opcji output bazy danych SQL. W poniższej tabeli wymieniono nazwy właściwości i ich opis tworzenie wyjścia bazy danych SQL.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używane w zapytaniach do kierowania wyników zapytania do tej bazy danych. |
| Database (Baza danych) | Nazwa bazy danych, w którym wysyłania danych wyjściowych. |
| Nazwa serwera | Nazwa serwera bazy danych SQL. |
| Nazwa użytkownika | Nazwa użytkownika, który ma dostęp do zapisu baza danych. |
| Hasło | Hasło, aby połączyć databas.e |
| Tabela | Nazwa tabeli, w którym dane wyjściowe są zapisywane. Nazwa tabeli jest rozróżniana wielkość liter i schemat tabeli powinien być w pełni zgodny liczba pól i ich typy generowane przez dane wyjściowe zadania. |

> [!NOTE]
> Obecnie oferty bazy danych SQL Azure jest obsługiwana dla danych wyjściowych zadania w Stream Analytics. Jednak maszyny wirtualnej platformy Azure, programem SQL Server z bazą danych dołączona nie jest obsługiwane. To może ulec zmianie w przyszłych wersjach.
> 

## <a name="blob-storage"></a>Blob Storage
Magazyn obiektów blob oferuje ekonomiczne i skalowalne rozwiązanie do przechowywania dużych ilości danych bez struktury w chmurze.  Aby obejrzeć wprowadzenie magazynu obiektów Blob platformy Azure i jego użycia, zobacz dokumentację w [sposób użycia obiekty BLOB](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

W poniższej tabeli wymieniono nazwy właściwości i ich opis tworzenia wyjściowego obiektu blob.

| Nazwa właściwości | Opis | 
| --- | --- |
| Alias wyjściowy | Przyjazna nazwa używane w zapytaniach w celu przekierowania wyników zapytania do tego magazynu obiektów blob. |
| Konto magazynu | Nazwa konta magazynu, w którym wysyłania danych wyjściowych. |
| Klucz konta magazynu | Klucz tajny, skojarzone z kontem magazynu. |
| Kontener magazynu | Kontenery umożliwiają logiczne grupowanie dla obiektów blob przechowywanych w usłudze Microsoft Azure Blob. Przekazywanie obiektu blob do usługi Blob, należy określić kontener dla tego obiektu blob. |
| Wzorzec ścieżki | Opcjonalny. Wzorzec ścieżki pliku używany do zapisywania obiektów blob w określonym kontenerze. </br> We wzorcu ścieżkę można umożliwia określenie częstotliwości, które obiekty BLOB są zapisywane w co najmniej jedno wystąpienie następujących zmiennych 2: </br> {date} {time} </br> Przykład 1: Klaster1/dzienniki / {date} / {time} </br> Przykład 2: Klaster1/dzienniki / {date} <BR> <BR> Następującej konwencji nazewnictwa plików są następujące: </br> {Ścieżka prefiks Pattern}/schemaHashcode_Guid_Number.extension </br></br> Przykład danych wyjściowych plików: </br>Myoutput/20170901/00/45434_gguid_1.csv </br> Myoutput/20170901/01/45434_gguid_1.csv |
| Format daty | Opcjonalny. Jeśli token daty jest używany w ścieżce prefiks, można wybrać format daty, w którym pliki są organizowane. Przykład: RRRR/MM/dd. |
| Format godziny | Opcjonalny. Jeśli czas nie jest używany w ścieżce prefiks, określ format czasu, w którym pliki są organizowane. Obecnie jedyna obsługiwana wartość to HH. |
| Format serializacji zdarzeń | Format serializacji w danych wyjściowych.  JSON, CSV i Avro są obsługiwane.
| Encoding | Jeśli przy użyciu formatu CSV lub JSON, należy określić kodowania. UTF-8 to jedyny obsługiwany obecnie format kodowania. |
| Ogranicznik | Dotyczy tylko serializacji woluminów CSV. Analiza strumienia obsługuje różne ograniczniki dla serializacji danych CSV. Obsługiwane wartości to przecinek, średnik, miejsca, karta i pionowy pasek. |
| Format | Dotyczy tylko serializacji JSON. Rozdzielone Określa, czy wynik jest formatowany przez poszczególne obiekty JSON rozdzielone znakiem nowego wiersza. Tablica Określa, czy dane wyjściowe są sformatowane jako tablica obiektów JSON. Ta tablica jest zamknięty, tylko wtedy, gdy zadanie zostanie zatrzymane lub Stream Analytics została przeniesiona następne okno czasu. Ogólnie rzecz biorąc, zaleca się używania wiersza oddzielone JSON, ponieważ nie wymaga żadnej specjalnej obsługi, gdy wciąż trwa zapisywanie pliku wyjściowego do. |

Korzystając z magazynu obiektów blob jako dane wyjściowe, nowy plik jest tworzony w obiekcie blob w następujących przypadkach:

* Jeśli plik przekracza maksymalną liczbę dozwolonych bloków. Maksymalną dozwoloną liczbę bloków może osiągnąć bez osiągnięcia rozmiar maksymalny dozwolony obiektu blob. Na przykład w przypadku wysoki współczynnik danych wyjściowych widać więcej bajtów na blok i rozmiar pliku jest większy. Jeśli szybkość, z danych wyjściowych jest niska, każdy blok ma mniejszą ilość danych, a rozmiar pliku jest mniejsza.
* Jeśli zmiany schematu w danych wyjściowych i format wyjściowy wymaga stałego schematu (CSV i Avro).  
* Jeśli zadanie zostanie ponownie uruchomiony albo zewnętrznie lub wewnętrzny ponownego uruchomienia zadania.  
* Jeśli zapytanie pełni jest podzielona na partycje, tworzony jest nowy plik dla każdej partycji w danych wyjściowych.  
* Jeśli plik lub kontenera konta magazynu jest usunięty przez użytkownika.  
* Jeśli dane wyjściowe po raz partycjonowanego wzorca prefiks ścieżki, nowy obiekt blob jest używany podczas kwerenda przechodzi do następnej godziny.

## <a name="event-hub"></a>Centrum zdarzeń
[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) usługa jest wysoce skalowalna publikowania / subskrypcji systemem zbierania zdarzeń. Może on zbierać miliony zdarzeń na sekundę. Jeden korzystanie z Centrum zdarzeń jako dane wyjściowe jest, gdy dane wejściowe zadania przesyłania strumieniowego innego staje się dane wyjściowe zadania usługi analiza strumienia.

Istnieje kilka parametrów, które są potrzebne do skonfigurowania strumieni danych w Centrum zdarzeń jako dane wyjściowe.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używane w zapytaniach do kierowania wyników zapytania do tego Centrum zdarzeń. |
| Przestrzeń nazw centrum zdarzeń |Przestrzeń nazw Centrum zdarzeń to kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń, utworzono Centrum zdarzeń przestrzeni nazw. |
| Nazwa centrum zdarzeń | Nazwa danych wyjściowych Centrum zdarzeń. |
| Nazwa zasad centrum zdarzeń | Zasady dostępu współdzielonego, można utworzyć na karcie Konfigurowanie Centrum zdarzeń. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia ustawić i klucze dostępu. |
| Klucz zasad centrum zdarzeń | Klucz dostępu współużytkowanego używany do uwierzytelniania dostępu do przestrzeni nazw Centrum zdarzeń. |
| Kolumna klucza partycji [opcjonalnie] | Ta kolumna zawiera klucz partycji dla danych wyjściowych Centrum zdarzeń. |
| Format serializacji zdarzeń | Format serializacji w danych wyjściowych.  JSON, CSV i Avro są obsługiwane. |
| Encoding | Dla woluminu CSV i JSON UTF-8 to jedyny obsługiwany format kodowania w tej chwili. |
| Ogranicznik | Dotyczy tylko serializacji woluminów CSV. Usługa Stream Analytics obsługuje różne ograniczniki dla serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, miejsca, karta i pionowy pasek. |
| Format | Dotyczy tylko serializacji JSON. Rozdzielone Określa, czy wynik jest formatowany przez poszczególne obiekty JSON rozdzielone znakiem nowego wiersza. Tablica Określa, czy dane wyjściowe są sformatowane jako tablica obiektów JSON. Ta tablica jest zamknięty, tylko wtedy, gdy zadanie zostanie zatrzymane lub Stream Analytics została przeniesiona następne okno czasu. Ogólnie rzecz biorąc, zaleca się używania wiersza oddzielone JSON, ponieważ nie wymaga żadnej specjalnej obsługi, gdy wciąż trwa zapisywanie pliku wyjściowego do. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) może służyć jako dane wyjściowe zadania usługi analiza strumienia do zapewnienia środowisko sformatowanego wizualizacja wyników analizy. Ta możliwość może służyć do operacyjnej pulpity nawigacyjne, Generowanie raportu i metryki na zgłoszenie.

### <a name="authorize-a-power-bi-account"></a>Autoryzuj konto usługi Power BI
1. Po wybraniu usługi Power BI jako dane wyjściowe w portalu Azure zostanie wyświetlony monit, do autoryzacji istniejącego użytkownika Power BI lub utworzyć nowe konto usługi Power BI.  
   
   ![Autoryzowanie Power BI użytkownika](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2. Utwórz nowe konto, jeśli nie zostały jeszcze mieć jeden, kliknij przycisk Autoryzuj teraz.  Przedstawiono to następującą stronę:
   
   ![Azure Account Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3. W tym kroku podaj konto służbowe autoryzacji wyjście usługi Power BI. Jeśli użytkownik nie jest już zarejestrowany na usługę Power BI, wybierz logowania teraz. Konto służbowe, którego używasz usługi Power BI może się różnić od konta subskrypcji platformy Azure, które są obecnie zalogowani przy użyciu.

### <a name="configure-the-power-bi-output-properties"></a>Skonfiguruj właściwości wyjście usługi Power BI
Po utworzeniu konta usługi Power BI uwierzytelniony, można skonfigurować właściwości dla danych wyjściowych usługi Power BI. W poniższej tabeli jest listę nazw właściwości i ich opis, aby skonfigurować dane wyjściowe usługi Power BI.

| Nazwa właściwości | description |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używane w zapytaniach do kierowania wyników zapytania do tego raportu usługi Power BI. |
| Obszar roboczy grupy |Aby włączyć udostępniania danych innym użytkownikom usługi Power BI można wybrać grupy wewnątrz koncie usługi Power BI lub wybierz opcję "Mój obszar roboczy", jeśli nie chcesz zapisać do grupy.  Aktualizowanie istniejącej grupy wymaga ponowne uwierzytelnianie usługi Power BI. |
| Nazwa zestawu danych |Podaj nazwę zestawu danych żądanej dla danych wyjściowych usługi Power BI do użycia |
| Nazwa tabeli |Podaj nazwę tabeli w obszarze zestawu danych wyjściowych usługi Power BI. Obecnie usługa Power BI dane wyjściowe zadania usługi analiza strumienia może mieć tylko jedną tabelę w zestawie danych |

Przewodnik konfigurowania usługi Power BI w danych wyjściowych i pulpitu nawigacyjnego, można wyświetlić [Azure Stream Analytics & usługi Power BI](stream-analytics-power-bi-dashboard.md) artykułu.

> [!NOTE]
> Nie należy jawnie tworzyć zestawu danych i tabeli na pulpicie nawigacyjnym usługi Power BI. Zestaw danych i tabeli jest automatycznie wypełniane podczas rozpoczęcia zadania i dane wyjściowe pompującego rozpoczęcia zadania w usłudze Power BI. Należy pamiętać, że jeśli zapytanie zadania nie generuje żadnych wyników, zestaw danych, a tabela nie jest utworzona. Należy zwrócić uwagę, jeśli usługi Power BI ma już zestaw danych i tabeli o takiej samej nazwie co podane w tym zadaniu Stream Analytics, istniejące dane te zostaną zastąpione.
> 

### <a name="schema-creation"></a>Tworzenie schematu
Usługa Azure Stream Analytics tworzy zestaw danych usługi Power BI i tabeli w imieniu użytkownika, jeśli już nie istnieje. We wszystkich innych przypadkach tabela jest aktualizowana nowymi wartościami. Obecnie istnieje ograniczenie tylko jednej tabeli może istnieć w elemencie dataset.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Konwersja typu danych z usługi Stream Analytics z usługą Power BI
Usługa Azure Stream Analytics aktualizacji modelu danych dynamicznie w czasie wykonywania w przypadku zmiany schematu danych wyjściowych. Zmiany nazwy kolumny, zmian w kolumnach typu i dodanie lub usunięcie kolumny są wszystkie śledzone.

W tej tabeli podano konwersje typów danych z [Stream Analytics typy danych](https://msdn.microsoft.com/library/azure/dn835065.aspx) do zasilania BIs [typy modelu danych jednostki (EDM)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) do usługi POWER BI zestawu danych i tabeli nie istnieją.

Z usługi Stream Analytics | To Power BI
-----|-----|------------
bigint | Int64
nvarchar(max) | Ciąg
datetime | Data/godzina
Float | Podwójnej precyzji
Tablica rekordu | Ciąg typu, wartości stałej "IRecord" lub "IArray"

### <a name="schema-update"></a>Aktualizacja schematu
Analiza strumienia wnioskuje schemat modelu danych, na podstawie pierwszego zestawu zdarzeń w danych wyjściowych. Później w razie potrzeby schematu modelu danych zostało zaktualizowane do uwzględnienia zdarzeń przychodzących, które mogą nie pasować do oryginalnego schematu.

`SELECT *` Zapytania należy unikać zapobiec aktualizacji dynamicznej schematu w wierszach. Oprócz potencjalnego wpływu na wydajność również może powodować niedokładność czas poświęcony na wyniki. Należy wybrać dokładnie pola, które muszą być wyświetlane na pulpicie nawigacyjnym usługi Power BI. Ponadto wartości danych powinny być zgodne z wybranym typem danych.


Poprzednie/bieżącym | Int64 | Ciąg | Data/godzina | Podwójnej precyzji
-----------------|-------|--------|----------|-------
Int64 | Int64 | Ciąg | Ciąg | Podwójnej precyzji
Podwójnej precyzji | Podwójnej precyzji | Ciąg | Ciąg | Podwójnej precyzji
Ciąg | Ciąg | Ciąg | Ciąg |  | Ciąg | 
Data/godzina | Ciąg | Ciąg |  Data/godzina | Ciąg


### <a name="renew-power-bi-authorization"></a>Odnów Power BI autoryzacji
Hasło konta usługi Power BI zmieni się po utworzenia lub ostatniej uwierzytelniony zadania Stream Analytics, należy ponownie uwierzytelniać Stream Analytics. Jeśli uwierzytelnianie wieloskładnikowe (MFA) jest skonfigurowany w dzierżawie usługi Azure Active Directory (AAD), należy odnowić autoryzacji usługi Power BI co dwa tygodnie. Objawem tego problemu jest Brak danych wyjściowych zadania i "uwierzytelnić użytkownika błąd" w dzienniki operacji:

  ![Błąd tokenu odświeżania Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Aby rozwiązać ten problem, Zatrzymaj uruchomione zadania i przejdź do usługi Power BI dane wyjściowe.  Wybierz **odnowić autoryzacji** połączyć, a następnie uruchom ponownie zadanie z **czas ostatniego zatrzymania** w celu uniknięcia utraty danych.

  ![Usługa Power BI odnawia autoryzacji](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Table Storage
[Magazyn tabel Azure](../storage/common/storage-introduction.md) oferuje magazynu wysokiej dostępności, skalowalna na ogromną skalę, dzięki czemu aplikacja może automatycznie skalować, aby spełnić wymagania użytkownika. Magazyn tabel jest magazynem kluczy/atrybutów NoSQL firmy Microsoft, które z nich mogą korzystać z danych strukturalnych o mniejszej liczbie ograniczeń w schemacie. Magazyn tabel Azure może służyć do przechowywania danych na potrzeby trwałości i efektywnego odzyskiwania.

W poniższej tabeli wymieniono nazwy właściwości i ich opis tworzenia tabeli danych wyjściowych.

| Nazwa właściwości | description |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używane w zapytaniach do kierowania wyników zapytania do magazynu w tej tabeli. |
| Konto magazynu |Nazwa konta magazynu, w którym wysyłania danych wyjściowych. |
| Klucz konta magazynu |Klucz dostępu skojarzone z kontem magazynu. |
| Nazwa tabeli |Nazwa tabeli. Pobiera utworzyć tabeli, jeśli nie istnieje. |
| Klucz partycji |Nazwa kolumny wyjściowej zawierającej klucz partycji. Klucz partycji to unikatowy identyfikator partycji w danej tabeli, który wchodzi w skład pierwszej części klucza podstawowego jednostki. Jest to wartość ciągu o maksymalnym rozmiarze 1 KB. |
| Klucz wiersza |Nazwa kolumny wyjściowej zawierającej klucz wiersza. Klucz wiersza to unikatowy identyfikator jednostki w danej partycji. Wchodzi w skład drugiej części klucza podstawowego jednostki. Klucz wiersza to wartość ciągu o maksymalnym rozmiarze 1 KB. |
| Rozmiar partii |Liczba rekordów dla operacji zbiorczej. Wartość domyślna (w 100) jest wystarczająca dla większości zadań. Zapoznaj się [specyfikacji operacji zbiorczej tabeli](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) Aby uzyskać więcej informacji na temat modyfikowania to ustawienie. |
 
## <a name="service-bus-queues"></a>Kolejki usługi Service Bus
[Kolejek usługi Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) oferują pierwszy na wejściu — pierwszy na wyjściu (FIFO) dostarczanie komunikatów dla co najmniej jeden konkurujących konsumentów. Zazwyczaj wiadomości powinny były odbierane i przetwarzane przez odbiorców w kolejności, w której zostały dodane do kolejki, a każdy komunikat jest odbierany i przetwarzany przez tylko jednego odbiorcę komunikatów.

W poniższej tabeli wymieniono nazwy właściwości i ich opis tworzenia kolejki dane wyjściowe.

| Nazwa właściwości | description |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używane w zapytaniach do kierowania wyników zapytania do tej kolejki magistrali usług. |
| Przestrzeń nazw magistrali usług |Przestrzeń nazw magistrali usług to kontener dla zestawu jednostek do obsługi komunikatów. |
| Nazwa kolejki |Nazwa kolejki magistrali usług. |
| Nazwa zasad kolejki |Podczas tworzenia kolejki można też utworzyć zasady dostępu współużytkowanego na karcie Konfigurowanie kolejki. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia ustawić i klucze dostępu. |
| Klucz zasad kolejki |Dostęp współdzielony klucz używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus |
| Format serializacji zdarzeń |Format serializacji w danych wyjściowych.  JSON, CSV i Avro są obsługiwane. |
| Encoding |Dla woluminu CSV i JSON UTF-8 to jedyny obsługiwany format kodowania w tej chwili |
| Ogranicznik |Dotyczy tylko serializacji woluminów CSV. Usługa Stream Analytics obsługuje różne ograniczniki dla serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, miejsca, karta i pionowy pasek. |
| Format |Dotyczy tylko typu JSON. Rozdzielone Określa, czy wynik jest formatowany przez poszczególne obiekty JSON rozdzielone znakiem nowego wiersza. Tablica Określa, czy dane wyjściowe są sformatowane jako tablica obiektów JSON. |

Liczba partycji jest [na podstawie SKU magistrali usługi i rozmiar](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji to unikatowa wartość dla każdej partycji.

## <a name="service-bus-topics"></a>Tematy dotyczące usługi Service Bus
Gdy kolejek usługi Service Bus udostępnia metody komunikacji jeden-do-jednego od nadawcy do odbiornika, [tematów usługi Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) Podaj jeden do wielu formę komunikacji.

W poniższej tabeli wymieniono nazwy właściwości i ich opis tworzenia tabeli danych wyjściowych.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używane w zapytaniach do kierowania wyników zapytania w tym temacie magistrali usługi. |
| Przestrzeń nazw magistrali usług |Przestrzeń nazw magistrali usług to kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń, utworzono przestrzeni nazw usługi Service Bus |
| Nazwa tematu |Tematy są obsługi komunikatów przypominające centra zdarzeń i kolejki, jednostek. Służą one do zbierania strumieni zdarzeń z różnych urządzeń i usług. Podczas tworzenia tematu podano również określonej nazwy. Komunikaty wysłane do tematu nie jest dostępna, chyba że subskrypcja jest tworzony, upewnij się, więc ma jedną lub więcej subskrypcji w temacie |
| Nazwa zasad tematu |Podczas tworzenia tematu można też utworzyć zasady dostępu współużytkowanego na karcie Konfigurowanie tematu. Wszystkie zasady dostępu współdzielonego ma nazwę, uprawnienia ustawić i klucze dostępu |
| Klucz zasad tematu |Dostęp współdzielony klucz używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus |
| Format serializacji zdarzeń |Format serializacji w danych wyjściowych.  JSON, CSV i Avro są obsługiwane. |
| Encoding |Jeśli przy użyciu formatu CSV lub JSON, należy określić kodowania. UTF-8 w tej chwili jest obsługiwany tylko format kodowania |
| Ogranicznik |Dotyczy tylko serializacji woluminów CSV. Usługa Stream Analytics obsługuje różne ograniczniki dla serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, miejsca, karta i pionowy pasek. |

Liczba partycji jest [na podstawie SKU magistrali usługi i rozmiar](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji to unikatowa wartość dla każdej partycji.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure DB rozwiązania Cosmos](https://azure.microsoft.com/services/documentdb/) jest globalnie rozproszone i wiele modeli bazy danych usługi zagwarantowanie oferty nieograniczona elastycznego skalowania wokół globu, zaawansowane zapytania i automatycznego indeksowania za pośrednictwem modeli danych pochodzącego od dowolnego schematu, małe opóźnienia i branży kompleksowe umów SLA. Aby dowiedzieć się więcej o opcjach kolekcji DB rozwiązania Cosmos Stream Analytics, zapoznaj się [analiza strumienia rozwiązania Cosmos DB jako dane wyjściowe](stream-analytics-documentdb-output.md) artykułu.

> [!Note]
> W tej chwili Azure Stream Analytics obsługuje tylko połączenia za pomocą CosmosDB **interfejsu API SQL**.
> Innych interfejsów API Azure rozwiązania Cosmos bazy danych nie są jeszcze obsługiwane. Jeśli punkt Azure Stream Analytics do kont Azure DB rozwiązania Cosmos tworzone za pomocą innych interfejsów API, dane mogą nie być poprawnie przechowywane. 

W poniższej tabeli opisano właściwości do utworzenia bazy danych Azure rozwiązania Cosmos danych wyjściowych.
| Nazwa właściwości | description |
| --- | --- |
| Alias danych wyjściowych | Odwoływanie się aliasu to dane wyjściowe w kwerendzie Stream Analytics. |
| Ujście | Cosmos DB |
| Opcja importu | Wybierz opcję "Wybierz DB rozwiązania Cosmos z subskrypcji", lub "Ustawienia rozwiązania Cosmos Podaj DB ręcznie".
| Identyfikator konta | Nazwa lub identyfikator URI konta DB rozwiązania Cosmos punktu końcowego. |
| Klucz konta | Klucz dostępu współdzielonego dla konta DB rozwiązania Cosmos. |
| Database (Baza danych) | Nazwa bazy danych DB rozwiązania Cosmos. |
| Wzorzec nazw kolekcji | Nazwa kolekcji lub ich wzorca kolekcji do użycia. <br/>Format nazw kolekcji można skonstruować przy użyciu tokenu opcjonalne {partition}, gdzie partycje zaczynają się od 0. Dwa przykłady:  <br/>1. _MyCollection_ — jedną kolekcję o nazwie "MyCollection" musi istnieć.  <br/>2. _MyCollection {partition}_ — na podstawie kolumny partycjonowania. <br/>Musi istnieć partycjonowania kolekcji kolumny — "MyCollection0", "MyCollection1", "MyCollection2" itd. |
| Klucz partycji | Opcjonalny. Jest to potrzebne tylko, jeśli używasz tokenu {partition} we wzorcu nazwy Twojej kolekcji.<br/> Klucz partycji to nazwa pola w zdarzeniach wyjściowych służąca do określenia klucza do partycjonowania danych wyjściowych na kolekcje.<br/> Dla danych wyjściowych jednej kolekcji można użyć dowolnej kolumny dowolne dane wyjściowe. Na przykład PartitionId. |
| Identyfikator dokumentu |Opcjonalny. Nazwa pola w zdarzeniach wyjściowych służąca do określenia klucza podstawowego, na które insert lub update bazują operacje.  

## <a name="azure-functions"></a>Azure Functions
Azure Functions to bezserwerowa usługa obliczeniowa, która umożliwia uruchamianie kodu na żądanie bez konieczności jawnego przydzielania infrastruktury ani zarządzania tą infrastrukturą. Umożliwia ona implementowania kodu wyzwalane przez zdarzenia występujące w Azure lub usług innych firm.  Tę możliwość usługi Azure Functions odpowiedzieć wyzwalaczy ułatwia fizycznych dane wyjściowe do usługi Azure Stream Analytics. Ta karta danych wyjściowych umożliwia użytkownikom Stream Analytics nawiązać połączenia z usługi Azure Functions, a następnie uruchom skrypt lub fragmentu kodu w odpowiedzi na szereg zdarzeń.

Usługa Azure Stream Analytics wywołuje usługi Azure Functions za pośrednictwem protokołu HTTP wyzwalaczy. Nowa karta danych wyjściowych funkcji Azure jest dostępna w programie można skonfigurować następujące właściwości:

| Nazwa właściwości | description |
| --- | --- |
| Aplikacja funkcji |Nazwa aplikacji Azure Functions |
| Funkcja |Nazwa funkcji w funkcje aplikacji Azure |
| Klucz |Jeśli chcesz użyć funkcji platformy Azure z inną subskrypcję, możesz to zrobić, podając klucza, który ma dostęp do funkcji |
| Maksymalny rozmiar partii |Tej właściwości można ustawić maksymalny rozmiar poszczególnych partii danych wyjściowych, wysyłany do funkcji platformy Azure. Domyślnie ta wartość jest 256 KB |
| Maksymalna liczba partii  |Jak wskazuje nazwa, ta właściwość pozwala określić maksymalną liczbę zdarzeń w każdej z partii, które są wysyłane do usługi Azure Functions. Wartość domyślna partii maksymalna liczba to 100 |

Gdy Azure Stream Analytics odbiera 413 wyjątek (http żądania jednostki zbyt duży) z funkcji platformy Azure, zmniejsza rozmiar partii, który wysyła do usługi Azure Functions. W kodzie funkcji platformy Azure Użyj tego wyjątku aby upewnić się, że usługi Azure Stream Analytics nie wysyła partie zbyt duży. Ponadto upewnij się, że wartości liczby i rozmiaru partii max używany w funkcji są zgodne z wartości wprowadzone w portalu usługi analiza strumienia. 

Ponadto w sytuacji, gdy nie ma żadnego zdarzenia, kierowanych do przedział czasu, jest generowane żadne dane wyjściowe. W związku z tym funkcja computeResult nie jest wywoływana. To zachowanie jest zgodne z wbudowanych funkcji agregujących w oknie.

## <a name="partitioning"></a>Partycjonowanie

W poniższej tabeli przedstawiono obsługę partycji i liczby modułów zapisywania danych wyjściowych dla każdego typu danych wyjściowych:

| Typ wyjścia | Partycjonowanie pomocy technicznej | Klucz partycji  | Liczba modułów zapisywania danych wyjściowych | 
| --- | --- | --- | --- |
| Azure Data Lake Store | Yes | Użyj {date} i {time} tokeny we wzorcu prefiks ścieżki. Wybierz format daty, takie jak RRRR/MM/dd. DD/MM/RRRR, MM-DD-RRRR. HH służy do formatu godziny. | Sposób wprowadzania partycjonowania dla [pełni działania równoległego zapytania](stream-analytics-scale-jobs.md). | 
| Azure SQL Database | Nie | Brak | Nie dotyczy. | 
| Azure Blob Storage | Yes | Użyj {date} i {time} tokenów w wzorzec ścieżki. Wybierz format daty, takie jak RRRR/MM/dd. DD/MM/RRRR, MM-DD-RRRR. HH służy do formatu godziny. | Sposób wprowadzania partycjonowania dla [pełni działania równoległego zapytania](stream-analytics-scale-jobs.md). | 
| Azure Event Hub | Yes | Yes | Może być różna w zależności od wyrównania partycji.</br> Po klucz partycji jest równie wyrównane nadrzędnego (poprzedni) kroku zapytania, liczba składników zapisywania Centrum zdarzeń jest taka sama liczba dane wyjściowe output partycji Centrum zdarzeń. Każdy moduł zapisujący używa w EventHub [klasy EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) do wysyłania zdarzeń do określonej partycji. </br> Gdy jest Centrum zdarzeń klucza partycji nie jest wyrównana z nadrzędnego (poprzedni) kroku zapytania, liczba modułów zapisywania danych wyjściowych taka sama jak liczba partycji w tym poprzedniego kroku. Każdy moduł zapisujący używa EventHubClient [klasy SendBatchAsync](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) do wysyłania zdarzeń do wszystkich partycji danych wyjściowych. |
| Power BI | Nie | Brak | Nie dotyczy. | 
| Azure Table Storage | Yes | Wszystkie kolumny wyjściowej.  | Sposób wprowadzania partycjonowania dla [pełni sparaliżowanych zapytania](stream-analytics-scale-jobs.md). | 
| Azure Service Bus tematu | Yes | Wybrana automatycznie. Liczba partycji jest oparta na [SKU magistrali usługi i rozmiar](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji to unikatowa wartość dla każdej partycji.| Taka sama jak liczba partycji w temacie danych wyjściowych.  |
| Kolejki magistrali usług Azure | Yes | Wybrana automatycznie. Liczba partycji jest oparta na [SKU magistrali usługi i rozmiar](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji to unikatowa wartość dla każdej partycji.| Taka sama jak liczba partycji w kolejki wyjściowej. |
| Azure Cosmos DB | Yes | Użyj tokenu {partition} we wzorcu nazwy kolekcji. wartość {partition} jest oparta na klauzuli PARTITION BY w zapytaniu. | Sposób wprowadzania partycjonowania dla [pełni sparaliżowanych zapytania](stream-analytics-scale-jobs.md). |
| Azure Functions | Nie | Brak | Nie dotyczy. | 

## <a name="output-batch-size"></a>Rozmiar partii danych wyjściowych
Usługa Azure Stream Analytics używa zmiennej rozmiar partii do przetwarzania zdarzeń i zapisywania danych wyjściowych. Zwykle aparat Stream Analytics nie zapisuje jeden komunikat w czasie, a używa partie w celu zwiększenia wydajności. Gdy zarówno przychodzącego i wychodzącego częstotliwość zdarzenia jest wysoka, używa większe partie. W przypadku małej szybkości wyjście używa mniejszych partie do zachowania niskim opóźnieniem. 

W poniższej tabeli opisano niektóre zagadnienia dotyczące danych wyjściowych przetwarzania wsadowego:

| Typ wyjścia | Maksymalny rozmiar wiadomości | Optymalizacji rozmiaru partii |
| :--- | :--- | :--- | 
| Azure Data Lake Store | Zobacz [ogranicza usługi Data Lake Storage](../azure-subscription-service-limits.md#data-lake-store-limits) | Dla operacji zapisu do 4 MB |
| Azure SQL Database | 10 000 maksymalna liczba wierszy przypadających na jednego zbiorczego wstawiania</br>100 minimalna liczba wierszy przypadających na wstawiania zbiorczego pojedynczego </br>Zobacz też [ogranicza Azure SQL](../sql-database/sql-database-resource-limits.md) |  Każdej partii jest początkowo zbiorczo wstawić z maksymalny rozmiar partii i może podzielone partii połowy oparte na błędy powtarzający operację z bazy danych SQL (do minimalny rozmiar partii). |
| Azure Blob Storage | Zobacz [ogranicza usługi Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Maksymalny rozmiar blokowych obiektów Blob to 4 MB</br>Maksymalna liczba bock obiektu Blob jest 50000 |
| Azure Event Hub   | 256 KB na jeden komunikat. </br>Zobacz też [ogranicza usługi Event Hubs](../event-hubs/event-hubs-quotas.md) |    Podczas partycjonowania wyjścia nie były wyrównane, każde zdarzenie jest indywidualnie pakowane w EventData i wysłane partii zgodnie z rozmiarem maksymalny komunikatu (1 MB do warstwy Premium). </br></br>  Podczas operacji wejścia-wyjścia Partycjonowanie jest wyrównywany wiele zdarzeń są pakowane w jednym EventData maksymalnie rozmiar maksymalny komunikatu i wysyłane.    |
| Power BI | Zobacz [ogranicza API Rest usługi Power BI](https://msdn.microsoft.com/library/dn950053.aspx) |
| Azure Table Storage | Zobacz [ogranicza usługi Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Domyślny jest 100 jednostek na jednej transakcji i można skonfigurować na wartość mniejszą zgodnie z potrzebami. |
| Azure kolejki usługi Service Bus   | 256 KB na jeden komunikat.</br> Zobacz też [ogranicza usługi Service Bus](../service-bus-messaging/service-bus-quotas.md) | Pojedyncze zdarzenie na jeden komunikat. |
| Azure tematu usługi Service Bus | 256 KB na jeden komunikat.</br> Zobacz też [ogranicza usługi Service Bus](../service-bus-messaging/service-bus-quotas.md) | Pojedyncze zdarzenie na jeden komunikat. |
| Azure Cosmos DB   | Zobacz [ogranicza bazy danych Azure rozwiązania Cosmos](../azure-subscription-service-limits.md#azure-cosmos-db-limits) | Wsadowe rozmiaru i zapisać dostosowane dynamicznie na podstawie odpowiedzi CosmosDB jest częstotliwość. </br> Brak ograniczeń ustalonej z usługi Stream Analytics. |
| Azure Functions   | | Domyślny rozmiar partii to 246 KB. </br> Domyślna liczba zdarzeń na partię to 100. </br> Rozmiar partii to konfigurowalne zwiększyć lub zmniejszyć w Stream Analytics i [opcje wyjściowe](#azure-functions). 

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie zadania usługi analiza strumienia za pomocą portalu Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
