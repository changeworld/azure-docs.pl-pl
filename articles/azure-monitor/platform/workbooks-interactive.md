---
title: Skoroszyty usługi Azure Monitor z parametrami niestandardowymi
description: Uprość składanie złożonych raportów dzięki wstępnie utworzonym i niestandardowych sparametryzowanym skoroszytom
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658273"
---
# <a name="interactive-workbooks"></a>Interakcyjne skoroszyty

Skoroszyty umożliwiają autorom tworzenie interaktywnych raportów i środowisk dla swoich odbiorców. Interaktywność jest obsługiwana na wiele sposobów.

## <a name="parameter-changes"></a>Zmiany parametrów
Gdy użytkownik skoroszytu aktualizuje parametr, każdy formant, który używa parametru automatycznie odświeża i ponownie rysuje, aby odzwierciedlić nowy stan. W ten sposób większość raportów witryny Azure portal obsługuje interaktywność. Skoroszyty zapewniają to w bardzo prosty sposób, przy minimalnym wysiłku użytkownika.

Dowiedz się więcej o [parametrach w skoroszytach](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Kliknięcia wiersza siatki
Skoroszyty umożliwiają autorom konstruowanie scenariuszy, w których kliknięcie wiersza w siatce aktualizuje kolejne wykresy na podstawie zawartości wiersza. 

Na przykład użytkownik może mieć siatkę, która pokazuje listę żądań i niektóre statystyki, takie jak liczba błędów. Mogą skonfigurować go w taki sposób, że kliknięcie wiersza odpowiadającego żądaniu spowoduje szczegółowe wykresy poniżej aktualizacji, aby filtrować tylko to żądanie.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Konfigurowanie interaktywności na kliknięciu wiersza siatki
1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edycja._
2. Użyj łącza _Dodaj kwerendę,_ aby dodać kontrolkę kwerendy dziennika do skoroszytu. 
3. Wybierz typ kwerendy jako _Dziennik,_ typ zasobu (na przykład Usługa Application Insights) i zasoby docelowe.
4. Wprowadź KQL do analizy za pomocą edytora zapytań
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`, aby zobaczyć wyniki
6. Kliknij ikonę _Ustawienia zaawansowane_ na stopce zapytania (ikona wygląda jak bieg). Spowoduje to otwarcie okienka ustawień zaawansowanych 
7. Sprawdź ustawienie:`When an item is selected, export a parameter`
    1. Pole do wyeksportowania:`Request`
    2. Nazwa parametru:`SelectedRequest`
    3. Wartość domyślna:`All requests`
    
    ![Obraz przedstawiający zaawansowany edytor z ustawieniami eksportowania pól jako parametrów](./media/workbooks-interactive/advanced-settings.png)

8. Kliknij pozycję `Done Editing` (Dalej).
9. Dodaj inną kontrolę kwerendy, wykonując kroki 2 i 3.
10. Wprowadź KQL do analizy za pomocą edytora zapytań
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`, aby zobaczyć wyniki.
12. Zmień _wizualizację_ na`Area chart`
12. Kliknij wiersz w pierwszej siatce. Zwróć uwagę, jak poniższy wykres warstwowy filtruje do wybranego żądania.

Wynikowy raport wygląda następująco w trybie edycji:

![Obraz przedstawiający tworzenie interaktywnego środowiska za pomocą kliknięć wiersza siatki](./media/workbooks-interactive//grid-click-create.png)

Poniższy obraz ekspozycyjny przedstawia bardziej skomplikowany interaktywny raport w trybie odczytu oparty na tych samych zasadach. Raport używa kliknięć siatki do eksportowania parametrów , co z kolei jest używane na dwóch wykresach i bloku tekstu.

![Obraz przedstawiający tworzenie interaktywnego środowiska za pomocą kliknięć wiersza siatki](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Eksportowanie zawartości całego wiersza
Czasami pożądane jest wyeksportowanie całej zawartości zaznaczonego wiersza zamiast tylko określonej kolumny. W takich przypadkach `Field to export` pozostaw właściwość unset w kroku 7.1 powyżej. Skoroszyty wyeksportują całą zawartość wiersza jako json do parametru. 

W przypadku odwoływania się do `todynamic` formantu KQL użyj funkcji, aby przeanalizować json i uzyskać dostęp do poszczególnych kolumn.

 ## <a name="grid-cell-clicks"></a>Kliknięcia komórki siatki
Skoroszyty umożliwiają autorom dodawanie interaktywności za pomocą specjalnego `link renderer`typu modułu renderowania kolumn siatki o nazwie . Moduł renderowania łączy konwertuje komórkę siatki na hiperłącze na podstawie zawartości komórki. Skoroszyty obsługują wiele rodzajów modułów renderowania łączy — w tym te, które umożliwiają otwieranie bloków przeglądu zasobów, przeglądarki worków właściwości, wyszukiwanie aplikacji App Insights, użycie, śledzenie transakcji itp.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Konfigurowanie interaktywności przy użyciu kliknięć komórek siatki
1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edycja._
2. Użyj łącza _Dodaj kwerendę,_ aby dodać kontrolkę kwerendy dziennika do skoroszytu. 
3. Wybierz typ kwerendy jako _Dziennik,_ typ zasobu (na przykład Usługa Application Insights) i zasoby docelowe.
4. Wprowadź KQL do analizy za pomocą edytora zapytań
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`, aby zobaczyć wyniki
6. Kliknij _pozycję Ustawienia kolumn,_ aby otworzyć okienko ustawień.
7. W sekcji _Kolumny_ ustaw:
    1. _Przykład —_ moduł `Link`renderowania kolumn: `Cell Details`, Widok do otwarcia: , Etykieta łącza:`Sample`
    2. _Count_ - Moduł `Bar`renderowania kolumn: `Blue`, Paleta kolorów: , Minimalna wartość:`0`
    3. _Żądanie_ — moduł renderowania kolumn:`Automatic`
    4. Kliknij _przycisk Zapisz i zamknij,_ aby zastosować zmiany
8. Kliknij jeden z `Sample` linków w siatce. Spowoduje to otwarcie okienka właściwości ze szczegółami próbkowania żądania.

    ![Obraz przedstawiający tworzenie interaktywnego środowiska za pomocą kliknięć komórek siatki](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Akcje renderowania łączy
| Akcja linku | Akcja po kliknięciu |
|:------------- |:-------------|
| `Generic Details` | Pokazuje wartości wierszy w bloku kontekstu siatki właściwości |
| `Cell Details` | Pokazuje wartość komórki w bloku kontekstu siatki właściwości. Przydatne, gdy komórka zawiera typ dynamiczny z informacjami (na przykład json z właściwościami żądania, takimi jak lokalizacja, wystąpienie roli itp.). |
| `Cell Details` | Pokazuje wartość komórki w bloku kontekstu siatki właściwości. Przydatne, gdy komórka zawiera typ dynamiczny z informacjami (na przykład json z właściwościami żądania, takimi jak lokalizacja, wystąpienie roli itp.). |
| `Custom Event Details` | Otwiera szczegóły wyszukiwania usługi Application Insights z niestandardowym identyfikatorem zdarzenia (itemId) w komórce |
| `* Details` | Podobnie jak szczegóły zdarzenia niestandardowego, z wyjątkiem zależności, wyjątków, widoków strony, żądań i śladów. |
| `Custom Event User Flows` | Otwiera środowisko przepływów użytkowników usługi Application Insights przestawne na niestandardowej nazwie zdarzenia w komórce |
| `* User Flows` | Podobne do niestandardowych przepływów użytkowników zdarzeń, z wyjątkiem wyjątków, widoków strony i żądań |
| `User Timeline` | Otwiera oś czasu użytkownika z identyfikatorem użytkownika (user_Id) w komórce |
| `Session Timeline` | Otwiera środowisko wyszukiwania usługi Application Insights dla wartości w komórce (na przykład wyszukaj tekst "abc", w którym abc jest wartością w komórce) |
| `Resource overview` | Otwieranie przeglądu zasobu w portalu na podstawie wartości identyfikatora zasobu w komórce |

## <a name="conditional-visibility"></a>Widoczność warunkowa
Skoroszyt umożliwia użytkownikom wyświetlenie lub zniknięcie niektórych formantów na podstawie wartości parametrów. Dzięki temu autorzy mają raporty wyglądają inaczej na podstawie danych wejściowych użytkownika lub stanu telemetrii. Przykład pokazuje konsumentom tylko podsumowanie, gdy rzeczy są dobre, ale pokazać pełne szczegóły, gdy coś jest nie tak.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Konfigurowanie interaktywności przy użyciu widoczności warunkowej
1. Wykonaj kroki opisane `Setting up interactivity on grid row click` w sekcji, aby skonfigurować dwa interaktywne kontrolki.
2. Dodaj nowy parametr u góry:
    1. Nazwa:`ShowDetails`
    2. Typ parametru:`Drop down`
    3. Wymagane:`checked`
    4. Pobierz dane z:`JSON`
    5. Wejście JSON:`["Yes", "No"]`
    6. Zapisz, aby zatwierdzić zmiany.
3. Ustawianie wartości parametru na`Yes`
4. W formancie kwerendy z wykresem warstwowym kliknij ikonę _Ustawienia zaawansowane_ (ikona koła zębatego)
5. Sprawdź ustawienie`Make this item conditionally visible`
    1. Ten element jest `ShowDetails` widoczny, `equals` jeśli wartość parametru`Yes`
6. Kliknij _pozycję Gotowe edytowanie,_ aby zatwierdzić zmiany.
7. Kliknij _pozycję Gotowe edytowanie_ na pasku narzędzia skoroszytu, aby przejść do trybu odczytu.
8. Przełącz wartość `ShowDetails` parametru na `No`. Zauważ, że poniższy wykres znika.

Poniższy obraz ekspozytury `ShowDetails` przedstawia widoczną obudowę, w której`Yes`

![Obraz przedstawiający widoczność warunkową, na której wykres jest widoczny](./media/workbooks-interactive/conditional-visibility.png)

Poniższy obraz ekspozytury przedstawia ukrytą obudowę, w której `ShowDetails``No`

![Obraz przedstawiający widoczność warunkową, w której wykres jest ukryty](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Następne kroki


* [Rozpocznij](workbooks-visualizations.md) naukę o skoroszytach wiele rozbudowanych opcji wizualizacji.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
