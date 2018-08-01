---
title: Zbieranie dzienników niestandardowych w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Usługa log Analytics może zbierać zdarzenia z plików tekstowych na komputerach z systemami Windows i Linux.  W tym artykule opisano sposób definiowania nowy dziennik niestandardowy i szczegóły rekordów, które tworzą w obszarze roboczym usługi Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/27/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 831b52a27a1ccfc349b9b54f8c3d874e41ddc322
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363147"
---
# <a name="custom-logs-in-log-analytics"></a>Dzienniki niestandardowe w usłudze Log Analytics
Źródło danych dzienników niestandardowych w usłudze Log Analytics można zbierać zdarzenia z plików tekstowych na komputerach z systemami Windows i Linux. Wiele aplikacji rejestrować informacje w plikach tekstowych zamiast standardowymi usługami rejestrowania, takie jak dziennik zdarzeń Windows lub Syslog.  Po zebraniu można analizować każdy rekord w Zaloguj się do poszczególnych pól za pomocą [pól niestandardowych](log-analytics-custom-fields.md) funkcji usługi Log Analytics.

![Zbieranie dzienników niestandardowych](media/log-analytics-data-sources-custom-logs/overview.png)

Pliki dziennika mają być zbierane musi spełniać następujące kryteria.

- Dziennika musi mieć pojedynczy wpis dla każdego wiersza albo użyj sygnatury czasowej dopasowane do jednej z następujących formatów na początku każdego wpisu.

    RRRR MM-DD HH: MM:<br>M/D/RRRR GG: MM: SS AM/PM<br>MON DD rrrr hh: mm:<br />rrmmdd: mm: ss<br />ddMMyy: mm: ss<br />MMM d hh: mm:<br />dd/mm/yyyy zzz<br />RRRR-MM-ddTHH:mm:ssK

- Plik dziennika nie może dopuszczać logowanie cykliczne lub rotacji dziennika, gdy plik jest zastępowany przy użyciu nowych wpisów.
- Plik dziennika, należy użyć kodowanie ASCII lub UTF-8.  Innych formatach, takich jak UTF-16 nie są obsługiwane.

>[!NOTE]
>Jeśli istnieją zduplikowane wpisy w pliku dziennika, usługi Log Analytics będzie zbierać je.  Jednak wyniki wyszukiwania będą niespójne gdzie wyników filtrowania Pokaż więcej zdarzeń niż liczba wyników.  Ważne jest, sprawdź poprawność dziennika, aby określić, jeśli aplikacja, która tworzy on jest przyczyną tego zachowania i rozwiązać problem, jeśli jest to możliwe, przed utworzeniem definicji kolekcji dzienników niestandardowych.  
>
  
## <a name="defining-a-custom-log"></a>Definiowanie dziennika niestandardowego
Poniższa procedura umożliwia zdefiniowanie niestandardowego pliku dziennika.  Przewiń do końca tego artykułu przewodnik przykładowej Dodawanie dziennika niestandardowego.

### <a name="step-1-open-the-custom-log-wizard"></a>Krok 1. Otwórz kreatora dziennika niestandardowego
Niestandardowego kreatora dziennika jest uruchamiany w witrynie Azure portal i pozwala na zdefiniowanie nowego dziennika niestandardowego do zbierania.

1. W witrynie Azure portal wybierz **usługi Log Analytics** > obszar roboczy > **Zaawansowane ustawienia**.
2. Kliknij pozycję **danych** > **niestandardowe dzienniki**.
3. Domyślnie wszystkie zmiany konfiguracji są automatycznie przekazywane do wszystkich agentów.  Agenci dla systemu Linux, aby uzyskać plik konfiguracji są wysyłane do Fluentd modułów zbierających dane.  Jeśli chcesz zmodyfikować ten plik ręcznie na każdym agencie systemu Linux, usuń zaznaczenie pola *Zastosuj poniższą konfigurację do moich maszyn z systemem Linux*.
4. Kliknij przycisk **Dodaj +** aby otworzyć Kreatora dziennika niestandardowego.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Krok 2. Przekazywanie i analizowanie przykładowy dziennik
Możesz uruchomić przykład dzienników niestandardowych.  Kreator przeanalizować i wyświetla wpisy w tym pliku dla Ciebie do sprawdzania poprawności.  Usługa log Analytics będzie używać ogranicznik, którego należy określić do identyfikowania każdego rekordu.

**Nowy wiersz** domyślnym ogranicznikiem i są używane dla plików dziennika, które mają pojedynczy wpis dla każdego wiersza.  Jeśli wiersz zaczyna się od daty i godziny w jednej z dostępnych formatów, a następnie możesz określić **sygnatura czasowa** ogranicznik, który obsługuje wpisy, które rozciągają się więcej niż jeden wiersz.

Jeśli jest używany ogranicznik sygnatury czasowej, właściwość TimeGenerated poszczególnych rekordów przechowywanych w usłudze Log Analytics zostaną wypełnione przy użyciu daty/godziny określone dla tego wpisu w pliku dziennika.  Jeśli jest używany nowy ogranicznik wiersza, TimeGenerated jest wypełniana z datą i godziną, że usługi Log Analytics zebrany wpis.


1. Kliknij przycisk **Przeglądaj** i przejdź do przykładowego pliku.  Należy zauważyć, że ten przycisk, może być oznaczony jako **wybierz plik** w niektórych przeglądarkach.
2. Kliknij przycisk **Dalej**.
3. Kreator dziennika niestandardowego przekazać plik i wyświetlić listę rekordów, które identyfikuje go.
4. Zmień ogranicznik, który służy do identyfikowania nowy rekord i wybierz ogranicznik, która najlepiej identyfikuje rekordy w pliku dziennika.
5. Kliknij przycisk **Dalej**.

### <a name="step-3-add-log-collection-paths"></a>Krok 3. Dodaj ścieżki zbierania dzienników
Należy zdefiniować jedną lub więcej ścieżek na agencie, gdzie można znaleźć dziennika niestandardowego.  Możesz albo podać określoną ścieżkę i nazwę pliku dziennika, lub można określić ścieżkę symbolem wieloznacznym dla nazwy.  To obsługuje aplikacje, które dziennie, lub gdy jeden plik osiągnie określony rozmiar, Utwórz nowy plik.  Możesz również podać wiele ścieżek dla jednego pliku dziennika.

Na przykład aplikacja może utworzyć pliku dziennika każdego dnia z datą uwzględniony w nazwie, jak log20100316.txt. Wzorzec dla takich dziennika może być *dziennika\*.txt* która będzie stosowana dla każdego pliku dziennika, po zastosowaniu nazewnictwa schematu.

Poniższa tabela zawiera przykłady prawidłowych do określenia różnych plikach dziennika.

| Opis | Ścieżka |
|:--- |:--- |
| Wszystkie pliki w *C:\Logs* z rozszerzeniem txt na agencie Windows |C:\Logs\\\*txt |
| Wszystkie pliki w *C:\Logs* o nazwie rozpoczynającej się od dzienników i rozszerzenie .txt na agencie Windows |C:\Logs\log\*.txt |
| Wszystkie pliki w */var/log/audit* z rozszerzeniem txt agenta systemu Linux |/var/log/audit/*.txt |
| Wszystkie pliki w */var/log/audit* o nazwie rozpoczynającej się od dzienników i rozszerzenie .txt agenta systemu Linux |/var/log/audit/log\*.txt |

1. Wybierz pozycję Windows lub Linux, aby określić format ścieżki, które dodajesz.
2. Wpisz ścieżkę i kliknij przycisk **+** przycisku.
3. Powtórz te czynności dla wszelkich dodatkowych ścieżek.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Krok 4. Podaj nazwę i opis dziennika
Typ dziennika umożliwi podanej nazwie zgodnie z powyższym opisem.  Zawsze zakończy się za pomocą _CL odróżniający go jako dziennik niestandardowy.

1. Wpisz nazwę dziennika.  **\_CL** sufiks jest dostarczana automatycznie.
2. Dodaj opcjonalny **opis**.
3. Kliknij przycisk **dalej** można zapisać definicji dzienników niestandardowych.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Krok 5. Zweryfikuj, że niestandardowe dzienniki są zbierane
Może potrwać do godziny początkowe dane z nowego dziennika niestandardowego pojawią się w usłudze Log Analytics.  Rozpocznie się zbieranie wpisy z dzienników odnaleziona w ścieżce określonej z punktu zdefiniowane dziennika niestandardowego.  Wpisy, które przekazane podczas tworzenia niestandardowego dziennika nie zostaną zachowane, ale już istniejącymi zapisów w plikach dziennika, które klient zlokalizuje będą zbierane.

Po uruchomieniu usługi Log Analytics zbieranie z dziennika niestandardowego jego rekordów będzie dostępna przy użyciu przeszukiwania dzienników.  Użyj nazwy, która udostępniła dziennika niestandardowego jako **typu** w zapytaniu.

> [!NOTE]
> Jeśli brakuje właściwości RawData wyszukiwania, może być konieczne zamknięcie i ponowne otwarcie przeglądarki.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>Krok 6. Analizowanie wpisy dziennika niestandardowego
Wpis dziennika całego będą przechowywane w pojedynczej właściwości o nazwie **RawData**.  Prawdopodobnie należy oddzielić różnych rodzajów informacji w każdej pozycji w poszczególnych właściwości przechowywane w rekordzie.  Możesz to zrobić za pomocą [pól niestandardowych](log-analytics-custom-fields.md) funkcji usługi Log Analytics.

Szczegółowy opis kroków do analizowania wpis dziennika niestandardowego nie znajdują się w tym miejscu.  Zapoznaj się [pól niestandardowych](log-analytics-custom-fields.md) dokumentacji dotyczącej tych informacji.

## <a name="removing-a-custom-log"></a>Usuwanie dziennika niestandardowego
Użyj następującego procesu w witrynie Azure portal, można usunąć dziennika niestandardowego, który wcześniej zdefiniowany.

1. Z **danych** menu w **Zaawansowane ustawienia** dla Twojego obszaru roboczego wybierz **niestandardowe dzienniki** Aby wyświetlić listę wszystkich dzienników niestandardowych.
2. Kliknij przycisk **Usuń** obok dziennika niestandardowego do usunięcia.


## <a name="data-collection"></a>Zbieranie danych
Usługa log Analytics będzie zbierać nowe wpisy z każdego dziennika niestandardowego co 5 minut.  Agent zarejestruje jej miejscu w każdym pliku dziennika, które są zbierane z.  Jeśli agent przejdzie do trybu offline w okresie czasu, następnie usługi Log Analytics będzie zbierać wpisów z tam, gdzie ją ostatnia przerwaliśmy, nawet jeśli te wpisy zostały utworzone, gdy agent był w trybie offline.

Całą zawartość wpis dziennika są zapisywane w pojedynczej właściwości o nazwie **RawData**.  Może to przeanalizować na wiele właściwości, które mogą być analizowane i przeszukiwać osobno, definiując [pól niestandardowych](log-analytics-custom-fields.md) po utworzeniu dziennika niestandardowego.

## <a name="custom-log-record-properties"></a>Właściwości rekordu dziennika niestandardowego
Rekordy dziennika niestandardowego mają typ o nazwie dziennika, przez Ciebie i właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| TimeGenerated |Data i godzina, który rekord został zebrany przez usługę Log Analytics.  Jeśli dziennik używa rozdzielnika oparte na czasie to czas, zebranych z wpisu. |
| SourceSystem |Typ agenta, które zostały zebrane rekordu. <br> Połącz OpsManager — Windows agent, bezpośrednio lub System Center Operations Manager <br> Linux — Wszyscy agenci systemu Linux |
| RawData |Pełny tekst wpisu zebrane. |
| ManagementGroupName |Nazwa grupy zarządzania agentów System Center Operations Manager.  Dla innych agentów jest to AOI -\<identyfikator obszaru roboczego\> |

## <a name="log-searches-with-custom-log-records"></a>Wyszukiwanie w dzienniku rekordy dziennika niestandardowego
Rekordy z dziennikami niestandardowymi są przechowywane w obszarze roboczym usługi Log Analytics, podobnie jak rekordów z dowolnego źródła danych.  Będzie miał typ pasujące do nazwy, zapewniające podczas definiowania dziennika, aby można było używać właściwości typu wyszukiwania, można pobrać rekordów zbieranych z określonego dziennika.

Poniższa tabela zawiera różne przykłady przeszukiwania dzienników, które pobierają rekordy z dziennikami niestandardowymi.

| Zapytanie | Opis |
|:--- |:--- |
| MyApp_CL |Wszystkie zdarzenia z niestandardowego dziennika o nazwie MyApp_CL. |
| MyApp_CL &#124; gdzie Severity_CF == "error" |Wszystkie zdarzenia z niestandardowego dziennika o nazwie MyApp_CL o wartości *błąd* w niestandardowe pole o nazwie *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Przewodnik po przykładzie Dodawanie dziennika niestandardowego
Poniższej sekcji przedstawiono przykład tworzenia dziennika niestandardowego.  Przykładowy dziennik zbieranych ma pojedynczy wpis w każdym wierszu, począwszy od daty i pól czasu i następnie rozdzielane przecinkami dla kodu, stanu i komunikatu.  Poniżej przedstawiono kilka przykładowych wpisów.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Przekazywanie i analizowanie przykładowy dziennik
Oferujemy jednego z plików dziennika i zobaczyć zdarzenia, które będzie zbierać.  W takim przypadku nowy wiersz jest wystarczające ogranicznika.  Jeśli pojedynczy wpis dziennika można jednak podzielony na wiele wierszy, a następnie należałoby ogranicznik sygnatury czasowej do użycia.

![Przekazywanie i analizowanie przykładowy dziennik](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Dodaj ścieżki zbierania dzienników
Pliki dziennika będą znajdować się w *C:\MyApp\Logs*.  Nowy plik zostanie utworzony każdego dnia o nazwie, która zawiera datę we wzorcu *appYYYYMMDD.log*.  Będą wystarczające wzorzec dla tego dziennika *C:\MyApp\Logs\\\*.log*.

![Ścieżka zbierania dzienników](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Podaj nazwę i opis dziennika
Używamy nazwy z *MyApp_CL* i wpisz w polu **opis**.

![Nazwa dziennika](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Zweryfikuj, że niestandardowe dzienniki są zbierane
Używamy zapytania *typu = MyApp_CL* do zwrócenia wszystkich rekordów z zebrany dziennik.

![Zapytanie dziennika bez pól niestandardowych](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Analizowanie wpisy dziennika niestandardowego
Używamy pola niestandardowe, aby zdefiniować *EventTime*, *kodu*, *stan*, i *komunikat* pola i możemy zobaczyć różnicę w rekordach które są zwracane przez zapytanie.

![Zapytanie dziennika z polami niestandardowymi](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Kolejne kroki
* Użyj [pól niestandardowych](log-analytics-custom-fields.md) przeanalizować wpisy w niestandardowych Zaloguj się do poszczególnych pól.
* Dowiedz się więcej o [dziennikach](log-analytics-log-searches.md) analizować dane zbierane z innych źródeł danych i rozwiązań.
