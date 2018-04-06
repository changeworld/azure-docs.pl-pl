---
title: Zbieranie dzienników niestandardowych w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Analiza dzienników można zbierać zdarzenia z plików tekstowych na komputerach z systemami Windows i Linux.  W tym artykule opisano sposób definiowania nowy dziennik niestandardowy i szczegóły rekordów tworzonych w obszarze roboczym analizy dzienników.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: bwren
ms.openlocfilehash: bf9acd5d7130a5e35182271f07593adab19d448b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="custom-logs-in-log-analytics"></a>Niestandardowe dzienniki w analizy dzienników
Źródło danych niestandardowe dzienniki w analizy dzienników umożliwia zbieranie zdarzeń z plików tekstowych na komputerach z systemami Windows i Linux. Wiele aplikacji rejestrowania informacji w plikach tekstowych zamiast standardowych usług rejestrowania, takich jak dziennika zdarzeń systemu Windows lub Syslog.  Po zebraniu danych, można przeanalizować każdego rekordu podczas logowania do poszczególnych pól przy użyciu [pola niestandardowe](log-analytics-custom-fields.md) funkcji analizy dzienników.

![Zbieranie dzienników niestandardowych](media/log-analytics-data-sources-custom-logs/overview.png)

Pliki dziennika mają być zbierane musi odpowiadać następujących kryteriów.

- Dziennik musi mieć pojedynczy wpis wierszu albo użyj sygnatury czasowej dopasowania jedną z następujących formatów na początku każdego wpisu.

    HH: MM: RRRR MM-DD<br>M/D/RRRR GG: MM: SS AM/PM <br>MON DD, YYYY HH: mm:

- Plik dziennika nie może dopuszczać logowanie cykliczne lub rotacji dziennika, gdy plik jest zastępowany nowe wpisy.
- Plik dziennika, należy użyć kodowanie ASCII lub UTF-8.  Innych formatach, takich jak UTF-16 nie są obsługiwane.

>[!NOTE]
>Jeśli istnieją zduplikowane wpisy w pliku dziennika, analizy dzienników zapisze je.  Jednak wyniki wyszukiwania będą niespójne gdzie wyników filtrowania Pokaż więcej zdarzeń niż liczba wyników.  Będzie on potrzebny, sprawdzanie poprawności dziennik, aby określić, jeśli aplikacja, która tworzy jego przyczyną jest to zachowanie i rozwiąż go w miarę możliwości przed utworzeniem definicji kolekcji dziennik niestandardowy.  
>
  
## <a name="defining-a-custom-log"></a>Definiowanie niestandardowego dziennika
Poniższa procedura umożliwia zdefiniowanie niestandardowego pliku dziennika.  Przewiń do końca w tym artykule przewodnik próby dodania dziennik niestandardowy.

### <a name="step-1-open-the-custom-log-wizard"></a>Krok 1. Otwórz kreatora dziennik niestandardowy
Kreator niestandardowy dziennika jest uruchamiana w portalu Azure i można zdefiniować nowego dziennika niestandardowego do zbierania.

1. W portalu Azure wybierz **analizy dzienników** > obszar roboczy > **Zaawansowane ustawienia**.
2. Polecenie **danych** > **niestandardowe dzienniki**.
3. Domyślnie wszystkie zmiany konfiguracji są automatycznie przypisany do wszystkich agentów.  Dla agentów systemów Linux plik konfiguracji jest wysyłany do Fluentd modułów zbierających dane.  Jeśli chcesz zmodyfikować ten plik ręcznie na każdym agenta systemu Linux, usuń zaznaczenie pola *Zastosuj poniższą konfigurację na moich maszynach z systemem Linux*.
4. Kliknij przycisk **Dodaj +** aby otworzyć Kreatora dziennika niestandardowego.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Krok 2. Przekazywanie i przeanalizować przykładowy dziennik
Możesz uruchomić przekazywanie próbce dziennika niestandardowego.  Kreator przeanalizować i wyświetlane wpisy w tym pliku można sprawdzić poprawności.  Analiza dzienników użyje ogranicznik, który określisz do identyfikowania każdego rekordu.

**Nowy wiersz** jest domyślnym ogranicznikiem i używany dla plików dziennika, które mają pojedynczy wpis wierszu.  Jeśli wiersz rozpoczyna się od daty i godziny w jednym z formatów dostępne, a następnie można określić **sygnatury czasowej** ogranicznik obsługującej wpisów, obejmujące więcej niż jeden wiersz.

Jeśli jest używany ogranicznik sygnatury czasowej, właściwość TimeGenerated każdego rekordu przechowywane w analizy dzienników zostaną wypełnione z daty/godziny, określony dla tego wpisu w pliku dziennika.  Jeśli jest używany nowy ogranicznik wiersza, TimeGenerated jest wypełniane przy użyciu daty i godziny, aby analizy dzienników pobrane wpis.


1. Kliknij przycisk **Przeglądaj** i przejdź do przykładowy plik.  Należy pamiętać, że może to przycisk może być oznaczony jako **wybierz plik** w niektórych przeglądarkach.
2. Kliknij przycisk **Dalej**.
3. Kreator dziennika niestandardowego przekazać plik i listę wszystkich rekordów, które identyfikuje go.
4. Zmień ogranicznik, który służy do identyfikowania nowy rekord i wybierz ogranicznik najlepiej identyfikujący rekordy w pliku dziennika.
5. Kliknij przycisk **Dalej**.

### <a name="step-3-add-log-collection-paths"></a>Krok 3. Dodaj ścieżki zbierania dzienników
Należy zdefiniować co najmniej jedną ścieżkę na agencie, gdzie można znaleźć dziennika niestandardowego.  Można podać albo określoną ścieżkę i nazwę pliku dziennika lub z symbolem wieloznacznym dla nazwy można określić ścieżkę.  Obejmuje to obsługę aplikacji, które tworzą nowy plik, każdego dnia lub gdy jeden plik osiągnie określony rozmiar.  Można też podać wiele ścieżek dla jednego pliku dziennika.

Na przykład aplikacja może utworzyć pliku dziennika każdego dnia z datą uwzględniony w nazwie, jak log20100316.txt. Szablon dla takich dziennika może być *dziennika\*.txt* która powinna zostać zastosowana do dowolnego pliku dziennika, po zastosowaniu do nazw schematu.

Poniższa tabela zawiera przykłady prawidłowych do określenia różnych plikach dziennika.

| Opis | Ścieżka |
|:--- |:--- |
| Wszystkie pliki w *C:\Logs* z rozszerzeniem txt agenta systemu Windows |C:\Logs\\\*txt |
| Wszystkie pliki w *C:\Logs* o nazwie rozpoczynającej się od dziennika i rozszerzeniem txt agenta systemu Windows |C:\Logs\log\*.txt |
| Wszystkie pliki w */var/log/audit* z rozszerzeniem txt na agenta systemu Linux |/var/log/audit/*.txt |
| Wszystkie pliki w */var/log/audit* o nazwie rozpoczynającej się od dziennika i rozszerzeniem txt na agenta systemu Linux |/var/log/audit/log\*.txt |

1. Wybierz systemu Windows lub Linux, aby określić format ścieżki, które dodajesz.
2. Wpisz ścieżkę i kliknij przycisk **+** przycisku.
3. Należy powtórzyć dla żadnych dodatkowych ścieżek.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Krok 4. Podaj nazwę i opis dziennika
Określona nazwa będzie służyć do typu dziennika, jak opisano powyżej.  Zawsze zakończy się z _CL odróżniający go jako dziennik niestandardowy.

1. Wpisz nazwę dziennika.   **\_CL** sufiks jest teraz udostępniana automatycznie.
2. Dodaj opcjonalny **opis**.
3. Kliknij przycisk **dalej** można zapisać definicji dziennik niestandardowy.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Krok 5. Zweryfikuj, że są zbierane dzienników niestandardowych
Może potrwać do godziny początkowe dane z nowego dziennika niestandardowego pojawią się w analizy dzienników.  Rozpocznie się zbieranie wpisy z dzienników odnaleziona w ścieżce określonej od momentu zdefiniowania dziennik niestandardowy.  Wpisy przekazane podczas tworzenia niestandardowego dziennika nie zostaną zachowane, ale będzie zbierać już istniejących wpisów w plikach dziennika, które klient zlokalizuje.

Po uruchomieniu analizy dzienników zbierane z dziennika niestandardowego swoje rekordy będą dostępne z wyszukiwania dziennika.  Użyj nazwy, który udostępnił dziennik niestandardowy jako **typu** w zapytaniu.

> [!NOTE]
> Jeśli brakuje właściwości RawData wyszukiwania, może być konieczne Zamknij i otwórz ponownie przeglądarkę.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>Krok 6. Przeanalizować wpisów dziennika niestandardowego
Wpis dziennika całego będą przechowywane w jedną właściwość o nazwie **RawData**.  Prawdopodobnie należy rozdzielić różne informacje zawarte w każdej pozycji do poszczególnych właściwości przechowywanych w rekordzie.  Można to zrobić przy użyciu [pola niestandardowe](log-analytics-custom-fields.md) funkcji analizy dzienników.

Szczegółowe informacje na temat analizowania wpis dziennika niestandardowego nie znajdują się w tym miejscu.  Zapoznaj się z [pola niestandardowe](log-analytics-custom-fields.md) tych informacji w dokumentacji.

## <a name="removing-a-custom-log"></a>Usuwanie dziennik niestandardowy
Należy wykonać poniższe czynności w portalu Azure, aby usunąć dziennik niestandardowy, który wcześniej zdefiniowany.

1. Z **danych** w menu **Zaawansowane ustawienia** obszaru roboczego wybierz **dzienników niestandardowych** Aby wyświetlić listę wszystkich dzienników niestandardowych.
2. Kliknij przycisk **Usuń** obok dziennik niestandardowy do usunięcia.


## <a name="data-collection"></a>Zbieranie danych
Analiza dzienników zbierze nowe wpisy z każdego dziennik niestandardowy co 5 minut.  Agent zarejestruje jego miejsce w każdym pliku dziennika, który zbiera z.  Agent przejdzie do trybu offline w danym okresie czasu, następnie Log Analytics będzie gromadzić wpisów z którym ostatnio przerwał, nawet jeśli te wpisy zostały utworzone podczas agent jest w trybie offline.

Cała zawartość wpis dziennika są zapisywane w jedną właściwość o nazwie **RawData**.  Można to przeanalizować na wiele właściwości, które mogą być przeanalizowane i przeszukiwać osobno, definiując [pól niestandardowych](log-analytics-custom-fields.md) po utworzeniu dziennik niestandardowy.

## <a name="custom-log-record-properties"></a>Właściwości rekordu dziennika niestandardowego
Rekordy dziennika niestandardowego mają typ o nazwie dziennika, który podasz i właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| TimeGenerated |Data i godzina rekord zebrane przez analizy dzienników.  Jeśli dziennik używa ogranicznik na podstawie czasu to czas zebrane z wpisu. |
| SourceSystem |Typ agenta rekordu zostały zebrane. <br> Połącz OpsManager — agent systemu Windows, bądź bezpośrednio lub System Center Operations Manager <br> Linux — wszystkich agentów systemu Linux |
| RawData |Pełny tekst zebranych wpisu. |
| ManagementGroupName |Nazwa grupy zarządzania agenci zarządzania programu System Center Operations.  W przypadku innych agentów jest AOI -\<identyfikator obszaru roboczego\> |

## <a name="log-searches-with-custom-log-records"></a>Dziennik wyszukiwania z rekordów dziennika niestandardowego
Rekordy z dzienników niestandardowych są przechowywane w obszarze roboczym analizy dzienników, podobnie jak rekordy z innego źródła danych.  Dysponują typu zgodnego z nazwą podane podczas definiowania dziennika, dzięki czemu można używać właściwości typu podczas wyszukiwania w celu pobrania rekordów zbierane z określonego dziennika.

Poniższa tabela zawiera różne przykłady wyszukiwania dziennika, które pobieranie rekordów z dzienników niestandardowych.

| Zapytanie | Opis |
|:--- |:--- |
| MyApp_CL |Wszystkie zdarzenia z niestandardowego dziennika o nazwie MyApp_CL. |
| MyApp_CL &#124; gdzie Severity_CF == "error" |Wszystkie zdarzenia z niestandardowego dziennika o nazwie MyApp_CL o wartości *błąd* w pole niestandardowe o nazwie *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Przykładowe wskazówki dodawania dziennik niestandardowy
Poniższej sekcji przedstawiono przykład tworzenia dziennik niestandardowy.  Przykładowy dziennik zbieranych ma pojedynczy wpis w każdym wierszu, począwszy od daty i czasu, a następnie rozdzielana przecinkami pól dla kodu, stanu i komunikatu.  Poniżej przedstawiono kilka przykładowych wpisów.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Przekazywanie i przeanalizować przykładowy dziennik
Firma Microsoft Podaj jeden z plików dziennika i można przeglądać zdarzenia, które będzie zbierać.  W takim przypadku nowy wiersz jest wystarczające ogranicznika.  Jeśli pojedynczy wpis dziennika można jednak podzielony na wiele wierszy, a następnie ogranicznik sygnatury czasowej potrzebny do użycia.

![Przekazywanie i przeanalizować przykładowy dziennik](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Dodaj ścieżki zbierania dzienników
Pliki dziennika będą znajdować się w *C:\MyApp\Logs*.  Nowy plik będzie można utworzyć każdego dnia o nazwie zawierającej daty we wzorcu *appYYYYMMDD.log*.  Będą wystarczające wzorca dla tego dziennika *C:\MyApp\Logs\\\*log*.

![Ścieżka zbierania dzienników](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Podaj nazwę i opis dziennika
Używamy nazwę *MyApp_CL* i wpisz **opis**.

![Nazwa dziennika](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Zweryfikuj, że są zbierane dzienników niestandardowych
Używamy zapytania *typu = MyApp_CL* aby zwrócić wszystkie rekordy z zebranych dziennika.

![Dziennik zapytań bez pól niestandardowych](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Przeanalizować wpisów dziennika niestandardowego
Możemy użyć pola niestandardowe, aby zdefiniować *EventTime*, *kod*, *stan*, i *komunikat* pól i firma Microsoft mogą przeglądać różnica w rekordach które są zwracane przez zapytanie.

![Kwerenda dziennika z polami niestandardowymi](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Kolejne kroki
* Użyj [pól niestandardowych](log-analytics-custom-fields.md) przeanalizować wpisy w niestandardowych logowanie do poszczególnych pól.
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) analizować dane zebrane ze źródeł danych i rozwiązania.
