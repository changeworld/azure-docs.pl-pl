---
title: Zbieranie niestandardowych dzienników w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Usługa Azure Monitor może zbierać zdarzenia z plików tekstowych na komputerach z systemem Windows i Linux.  W tym artykule opisano sposób definiowania nowego dziennika niestandardowego i szczegółów rekordów, które tworzą w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/26/2019
ms.openlocfilehash: 1e889aaef7cd01cd743e8063a8a1dd5138ba9d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670597"
---
# <a name="custom-logs-in-azure-monitor"></a>Dzienniki niestandardowe w usłudze Azure Monitor

Źródło danych dzienników niestandardowych w usłudze Azure Monitor umożliwia zbieranie zdarzeń z plików tekstowych na komputerach z systemem Windows i Linux. Wiele aplikacji rejestruje informacje do plików tekstowych zamiast standardowych usług rejestrowania, takich jak dziennik zdarzeń systemu Windows lub Syslog. Po zebraniu można przeanalizować dane w poszczególnych polach w kwerendach lub wyodrębnić dane podczas zbierania do poszczególnych pól.

![Niestandardowa kolekcja dzienników](media/data-sources-custom-logs/overview.png)

Pliki dziennika, które mają być zbierane, muszą spełniać następujące kryteria.

- Dziennik musi mieć jeden wpis w wierszu lub użyć sygnatury czasowej pasującej do jednego z następujących formatów na początku każdego wpisu.

    YYYY-MM-DD HH:MM:SS<br>M/D/YYYY HH:MM:SS AM/PM<br>Pon DD, YYYY HH:MM:SS<br />yyMmdd HH:mm:ss<br />ddMMyy HH:mm:ss<br />MMM d hh:mm:ss<br />dd/MMM/yyyy:HH:mm:ss zzz<br />yyyy-MM-ddTHH:mm:ssK

- Plik dziennika nie może zezwalać na rejestrowanie cykliczne lub obrót dziennika, gdzie plik jest zastępowany nowymi wpisami.
- Plik dziennika musi używać kodowania ASCII lub UTF-8.  Inne formaty, takie jak UTF-16, nie są obsługiwane.

>[!NOTE]
> Jeśli w pliku dziennika znajdują się zduplikowane wpisy, usługa Azure Monitor będzie je zbierać. Jednak wyniki kwerendy będą niespójne, gdy wyniki filtru pokaż więcej zdarzeń niż liczba wyników. Ważne będzie, aby sprawdzić poprawność dziennika, aby ustalić, czy aplikacja, która go tworzy, powoduje to zachowanie i adres, jeśli to możliwe przed utworzeniem definicji kolekcji dziennika niestandardowego.  
>

>[!NOTE]
> Obszar roboczy usługi Log Analytics obsługuje następujące limity:
> 
> * Można utworzyć tylko 500 dzienników niestandardowych.
> * Tabela obsługuje tylko maksymalnie 500 kolumn. 
> * Maksymalna liczba znaków dla nazwy kolumny wynosi 500. 
>

>[!IMPORTANT]
>Kolekcja dziennika niestandardowego wymaga, aby aplikacja zapisująca plik dziennika okresowo opróżnia zawartość dziennika na dysku. Dzieje się tak, ponieważ kolekcja dziennika niestandardowego opiera się na powiadomieniach o zmianie systemu plików dla śledzonego pliku dziennika.

## <a name="defining-a-custom-log"></a>Definiowanie dziennika niestandardowego
Poniższa procedura służy do definiowania niestandardowego pliku dziennika.  Przewiń do końca tego artykułu, aby uzyskać instruktaż przykładu dodawania dziennika niestandardowego.

### <a name="step-1-open-the-custom-log-wizard"></a>Krok 1. Otwórz Kreatora dzienników niestandardowych
Kreator dziennika niestandardowego jest uruchamiany w witrynie Azure portal i umożliwia zdefiniowanie nowego dziennika niestandardowego do zbierania.

1. W witrynie Azure portal wybierz **obszary robocze usługi Log Analytics** > obszarze roboczym > ustawienia **zaawansowane**.
2. Kliknij **Data** > **dzienniki niestandardowe**danych .
3. Domyślnie wszystkie zmiany konfiguracji są automatycznie wypychane do wszystkich agentów. W przypadku agentów systemu Linux plik konfiguracyjny jest wysyłany do modułu zbierającego dane Fluentd.
4. Kliknij **przycisk Dodaj +,** aby otworzyć Kreatora dzienników niestandardowych.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Krok 2. Przekazywanie i analizowanie przykładowego dziennika
Możesz rozpocząć od przesłania próbki dziennika niestandardowego.  Kreator będzie analizować i wyświetlać wpisy w tym pliku, aby można było sprawdzić poprawność.  Usługa Azure Monitor użyje ogranicznika, który określisz, aby zidentyfikować każdy rekord.

**Nowy wiersz** jest domyślnym ogranicznikiem i jest używany dla plików dziennika, które mają jeden wpis w wierszu.  Jeśli wiersz rozpoczyna się od daty i godziny w jednym z dostępnych formatów, można określić ogranicznik **sygnatury czasowej,** który obsługuje wpisy obejmujące więcej niż jeden wiersz.

Jeśli ogranicznik sygnatury czasowej jest używany, a następnie TimeGenerated właściwość każdego rekordu przechowywanego w usłudze Azure Monitor zostaną wypełnione datą/godziną określoną dla tego wpisu w pliku dziennika.  Jeśli używany jest nowy ogranicznik wiersza, timegenerated jest wypełniany datą i godziną, którą usługa Azure Monitor zebrała wpis.

1. Kliknij **pozycję Przeglądaj** i przejdź do przykładowego pliku.  Należy pamiętać, że ten przycisk może być oznaczony jako **Wybierz plik** w niektórych przeglądarkach.
2. Kliknij przycisk **alej**.
3. Kreator dziennika niestandardowego przekaże plik i wyświetli listę rekordów, które identyfikuje.
4. Zmień ogranicznik, który jest używany do identyfikowania nowego rekordu i wybierz ogranicznik, który najlepiej identyfikuje rekordy w pliku dziennika.
5. Kliknij przycisk **alej**.

### <a name="step-3-add-log-collection-paths"></a>Krok 3. Dodawanie ścieżek kolekcji dzienników
Należy zdefiniować jedną lub więcej ścieżek na agenta, gdzie można zlokalizować dziennik niestandardowy.  Można podać określoną ścieżkę i nazwę pliku dziennika lub można określić ścieżkę z symbolem wieloznacznym dla nazwy. Obsługuje to aplikacje, które tworzą nowy plik każdego dnia lub gdy jeden plik osiągnie określony rozmiar. Można również podać wiele ścieżek dla pojedynczego pliku dziennika.

Na przykład aplikacja może utworzyć plik dziennika każdego dnia z datą zawartą w nazwie, jak w log20100316.txt. Wzorzec dla takiego dziennika może być *log\*.txt,* który miałby zastosowanie do dowolnego pliku dziennika po schemat nazewnictwa aplikacji.

Poniższa tabela zawiera przykłady prawidłowych wzorców, aby określić różne pliki dziennika.

| Opis | Ścieżka |
|:--- |:--- |
| Wszystkie pliki w *języku C:\Logs* z rozszerzeniem txt w agencie Windows |C:\Loguje\\\*.txt |
| Wszystkie pliki w *języku C:\Logs* o nazwie zaczynasz od dziennika i rozszerzeniu txt w agencie Windows |C:\Logs\log\*.txt |
| Wszystkie pliki w */var/log/audit* z rozszerzeniem .txt w agencie Linux |/var/log/audit/*.txt |
| Wszystkie pliki w */var/log/audit* o nazwie zaczyna ną od dziennika i rozszerzeniu txt w agencie Linux |/var/log/audit/log\*.txt |

1. Wybierz system Windows lub Linux, aby określić format ścieżki, który jest dodany.
2. Wpisz ścieżkę i **+** kliknij przycisk.
3. Powtórz proces dla dodatkowych ścieżek.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Krok 4. Podaj nazwę i opis dziennika
Nazwa, która określisz będzie używana dla typu dziennika, jak opisano powyżej.  Zawsze będzie to _CL, aby odróżnić go jako dziennik niestandardowy.

1. Wpisz nazwę dziennika.  Sufiks ** \_CL** jest dostarczany automatycznie.
2. Dodaj opcjonalny **opis**.
3. Kliknij **przycisk Dalej,** aby zapisać definicję dziennika niestandardowego.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Krok 5. Sprawdzanie, czy dzienniki niestandardowe są zbierane
Może upłynąć do godziny, aby początkowe dane z nowego dziennika niestandardowego były wyświetlane w usłudze Azure Monitor.  Rozpocznie zbieranie wpisów z dzienników znalezionych w ścieżce określonej od punktu, który został zdefiniowany dziennik niestandardowy.  Nie zachowa wpisów, które zostały przekazane podczas tworzenia dziennika niestandardowego, ale będzie zbierać już istniejące wpisy w plikach dziennika, które lokalizuje.

Gdy usługa Azure Monitor rozpocznie zbieranie z dziennika niestandardowego, jego rekordy będą dostępne z kwerendą dziennika.  Użyj nazwy, która nadała dziennik niestandardowy jako **typ** w kwerendzie.

> [!NOTE]
> Jeśli w zapytaniu brakuje właściwości RawData, może być konieczne zamknięcie i ponowne otwarcie przeglądarki.

### <a name="step-6-parse-the-custom-log-entries"></a>Krok 6. Analizowanie niestandardowych wpisów dziennika
Cały wpis dziennika będzie przechowywany w jednej właściwości o nazwie **RawData**.  Najprawdopodobniej chcesz oddzielić różne informacje w każdym wpisie do poszczególnych właściwości dla każdego rekordu. Zapoznaj się [z analizuj dane tekstowe w usłudze Azure Monitor, aby](../log-query/parse-text.md) uzyskać opcje analizowania **danych RawData** na wiele właściwości.

## <a name="removing-a-custom-log"></a>Usuwanie dziennika niestandardowego
Użyj następującego procesu w witrynie Azure portal, aby usunąć dziennik niestandardowy, który został wcześniej zdefiniowany.

1. Z menu **Dane** w **obszarze roboczym wybierz** polecenie **Dzienniki niestandardowe,** aby wyświetlić listę wszystkich dzienników niestandardowych.
2. Kliknij **przycisk Usuń** obok dziennika niestandardowego, aby usunąć.

## <a name="data-collection"></a>Zbieranie danych
Usługa Azure Monitor będzie zbierać nowe wpisy z każdego dziennika niestandardowego co około 5 minut.  Agent zarejestruje swoje miejsce w każdym pliku dziennika, z których zbiera.  Jeśli agent przejdzie w tryb offline przez pewien czas, następnie usługa Azure Monitor będzie zbierać wpisy, z których ostatnio zostało przerwane, nawet jeśli te wpisy zostały utworzone, gdy agent był w trybie offline.

Cała zawartość wpisu dziennika są zapisywane w jednej właściwości o nazwie **RawData**.  Zobacz [analizowanie danych tekstowych w usłudze Azure Monitor,](../log-query/parse-text.md) aby uzyskać metody analizowania każdego zaimportowanego wpisu dziennika w wielu właściwościach.

## <a name="custom-log-record-properties"></a>Właściwości niestandardowego rekordu dziennika
Rekordy dziennika niestandardowego mają typ o nazwie dziennika, które podasz i właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| TimeGenerated |Data i godzina, że rekord został zebrany przez usługę Azure Monitor.  Jeśli dziennik używa ogranicznika opartego na czasie, jest to czas zebrany z wpisu. |
| SourceSystem |Typ agenta, z został zebrany. <br> OpsManager — agent systemu Windows, bezpośrednie połączenie lub Menedżer operacyjny centrum systemowego <br> Linux – Wszyscy agenci Linuksa |
| Dane raw |Pełny tekst zebranego wpisu. Najprawdopodobniej chcesz [przeanalizować te dane w poszczególnych właściwościach](../log-query/parse-text.md). |
| ManagementGroupName |Nazwa grupy zarządzania agentami zarządzania operacjami w centrum systemu.  W przypadku innych agentów jest\<to identyfikator obszaru roboczego AOI\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Przykładowe wskazówki dotyczące dodawania dziennika niestandardowego
W poniższej sekcji przedstawiono przykład tworzenia dziennika niestandardowego.  Pobierany dziennik przykładowy ma pojedynczy wpis w każdym wierszu, zaczynając od daty i godziny, a następnie pola rozdzielane przecinkami dla kodu, stanu i wiadomości.  Poniżej przedstawiono kilka przykładowych wpisów.

    2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2019-08-27 01:38:22 302,Error,Application could not connect to database
    2019-08-27 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Przekazywanie i analizowanie przykładowego dziennika
Udostępniamy jeden z plików dziennika i możemy zobaczyć zdarzenia, które będą zbierać.  W tym przypadku New Line jest wystarczającym ogranicznikiem.  Jeśli pojedynczy wpis w dzienniku może obejmować wiele wierszy, a następnie ogranicznik sygnatury czasowej musiałby być używany.

![Przekazywanie i analizowanie przykładowego dziennika](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Dodawanie ścieżek kolekcji dzienników
Pliki dziennika będą znajdować się w *folderze C:\MyApp\Logs*.  Nowy plik zostanie utworzony każdego dnia z nazwą, która zawiera datę w *appYYYYMMDD.log*wzorca .  Wystarczającym wzorcem dla tego dziennika będzie *C:\MyApp\Logs\\\*.log*.

![Ścieżka kolekcji dziennika](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Podaj nazwę i opis dziennika
Używamy nazwy *MyApp_CL* i **wpisujemy**opis .

![Nazwa dziennika](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Sprawdzanie, czy dzienniki niestandardowe są zbierane
Używamy prostego zapytania *MyApp_CL,* aby zwrócić wszystkie rekordy z zebranego dziennika.

![Kwerenda dziennika bez pól niestandardowych](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternatywy dla dzienników niestandardowych
Dzienniki niestandardowe są przydatne, jeśli dane są zgodne z wymienionymi kryteriami, ale istnieją następujące przypadki, w których potrzebna jest inna strategia:

- Dane nie pasują do wymaganej struktury, takiej jak sygnatura czasowa w innym formacie.
- Plik dziennika nie jest zgodny z wymaganiami, takimi jak kodowanie plików lub nieobsługiwała struktury folderów.
- Dane wymagają wstępnego przetwarzania lub filtrowania przed pobraniem. 

W przypadkach, gdy dane nie mogą być zbierane za pomocą dzienników niestandardowych, należy wziąć pod uwagę następujące alternatywne strategie:

- Użyj niestandardowego skryptu lub innej metody, aby zapisać dane [w usłudze Zdarzenia systemu Windows](data-sources-windows-events.md) lub [syslog,](data-sources-syslog.md) które są zbierane przez usługę Azure Monitor. 
- Wysyłaj dane bezpośrednio do usługi Azure Monitor przy użyciu [interfejsu API modułu zbierającego dane HTTP](data-collector-api.md). 

## <a name="next-steps"></a>Następne kroki
* Zobacz [analizowanie danych tekstowych w usłudze Azure Monitor,](../log-query/parse-text.md) aby uzyskać metody analizowania każdego zaimportowanego wpisu dziennika w wielu właściwościach.
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań.
