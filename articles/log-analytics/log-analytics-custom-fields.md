---
title: Pola niestandardowe w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Funkcja pola niestandardowe analizy dzienników służy do tworzenia własnych pól z możliwością wyszukiwania z rekordów dziennika analizy, dodawanych do właściwości zebranych rekordu.  W tym artykule opisano proces tworzenia niestandardowego pola i udostępnia szczegółowy przewodnik zdarzenie próbkowania.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: f0f3311f35f954f81560cad21e7f0e3bc850a094
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
ms.locfileid: "28030710"
---
# <a name="custom-fields-in-log-analytics"></a>Pola niestandardowe w analizy dzienników
**Pola niestandardowe** funkcji analizy dzienników umożliwia rozszerzanie istniejących rekordów w analizy dzienników przez dodanie pola wyszukiwania.  Pola niestandardowe zostaną automatycznie wypełnione z danych wyodrębnionych z innych właściwości w tym samym rekordzie.

![Przegląd pól niestandardowych](media/log-analytics-custom-fields/overview.png)

Na przykład poniżej przykładowy rekord zawiera przydatne dane ukryty w opisie zdarzenia.  Wyodrębnianie te dane w oddzielnych właściwości udostępnia takie zadania jak sortowania i filtrowania.

![Przycisk wyszukiwania dziennika](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> W wersji zapoznawczej jest ograniczona do 100 pola niestandardowe w obszarze roboczym.  Ten limit zostaną rozwinięte, gdy ta funkcja osiągnie ogólnej dostępności.
> 
> 

## <a name="creating-a-custom-field"></a>Tworzenie niestandardowego pola
Podczas tworzenia niestandardowego pola analizy dzienników muszą zrozumieć dane do wypełnienia jej wartość.  Używa technologii Microsoft Research o nazwie FlashExtract szybką identyfikację tych danych.  Zamiast konieczności jawnego instrukcjami, analizy dzienników uzyskuje informacje o danych, które mają zostać wyodrębnione w przykładach podanych danych.

Poniższe sekcje zawierają procedury tworzenia niestandardowego pola.  W dolnej części tego artykułu jest przewodnik wyodrębniania próbki.

> [!NOTE]
> Pole niestandardowe jest wypełniana zgodnie z rekordów spełniających określone kryteria są dodawane do analizy dzienników, zostanie wyświetlony tylko na rekordy zebrane po utworzeniu pola niestandardowego.  Pole niestandardowe nie zostanie dodany do rekordów, które znajdują się już w magazynie danych po jego utworzeniu.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Krok 1: określenie rekordów, które będą miały pole niestandardowe
Pierwszym krokiem jest do identyfikowania rekordów, które otrzymają pola niestandardowego.  Rozpoczyna [wyszukiwania standardowe dziennika](log-analytics-log-searches.md) , a następnie zaznacz rekord do działania jako model, który będzie nauki analizy dzienników.  Po określeniu, czy zamierzasz wyodrębniania danych do pola niestandardowego, **Kreator wyodrębniania pola** jest otwarty, gdzie Zweryfikuj i zoptymalizuj kryteria.

1. Przejdź do **wyszukiwania dziennika** i użyj [zapytanie, aby pobrać te rekordy](log-analytics-log-searches.md) będą mieć pola niestandardowego.
2. Wybierz rekord dziennika Analytics będzie używała do działania jako model do wyodrębniania danych do wypełnienia pola niestandardowego.  Należy określić dane, które mają zostać wyodrębnione z tego rekordu i analizy dzienników użyje tych informacji do określenia logiki, aby wypełnić pole niestandardowe dla wszystkich rekordów podobne.
3. Kliknij przycisk z lewej strony żadnych właściwości text rekordu i wybierz **wyodrębniania pól z**.
4. **Zostanie otwarty Kreator wyodrębniania pola**, i rekord wybrano jest wyświetlany w **przykład Main** kolumny.  Pole niestandardowe zostaną określone dla tych rekordów o tej samej wartości właściwości, które są wybrane.  
5. Jeśli zaznaczenie jest dokładnie, co należy wybrać dodatkowe pola, aby zawęzić kryteria.  Aby zmienić wartości pól kryteriów, możesz Anuluj i wybierz inny rekord zgodne z kryteriami, które mają.

### <a name="step-2---perform-initial-extract"></a>Krok 2 — wykonywania początkowej wyodrębniania.
Po wyłaniają rekordów, które będą miały pole niestandardowe, należy zidentyfikować dane, które mają zostać wyodrębnione.  Analiza dzienników użyje tych informacji do identyfikacji wzorców podobne w rekordach podobne.  W kroku po tym będzie mógł zweryfikować wyniki i udostępniania dodatkowych szczegółów dla analizy dzienników do użycia w jego analizy.

1. Wyróżnianie tekstu w rekordzie próbki, który chcesz wypełnić pole niestandardowe.  Następnie zostanie wyświetlone okno dialogowe, aby podać nazwę pola i wykonywania początkowej wyodrębniania.  Znaki  **\_CF** zostaną automatycznie dołączone.
2. Kliknij przycisk **wyodrębnić** przeprowadzić analizę zebranych rekordów.  
3. **Podsumowanie** i **wyniki wyszukiwania** sekcjach są wyświetlane wyniki extract, możesz sprawdzić dokładność.  **Podsumowanie** Wyświetla kryteria używane do identyfikowania rekordów i liczba dla każdej wartości danych.  **Wyniki wyszukiwania** zawiera szczegółową listę rekordów pasujących do kryteriów.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Krok 3 — sprawdzić dokładność wyodrębniania i utworzyć niestandardowego pola
Po wykonaniu początkowego wyodrębniania analizy dzienników wyświetli wyniki na podstawie danych, które już zostały zebrane.  Jeśli wyniki wyglądają dokładne można utworzyć niestandardowego pola z żadne dodatkowe czynności.  Jeśli nie, następnie ograniczenia wyników, aby analizy dzienników można zwiększyć swojej logiki.

1. Jeśli wszystkie wartości w początkowej wyodrębniania nie są poprawne, kliknij przycisk **Edytuj** ikona obok niedokładne rekordów i wybierz pozycję **zmodyfikować tego wyróżnienia** Aby zmodyfikować zaznaczenie.
2. Wpis jest kopiowany do **dodatkowe przykłady** sekcji poniżej **przykład Main**.  Wyróżnienie w tym miejscu można dostosować pomagające zrozumieć powinien wprowadzone przez zaznaczenie analizy dzienników.
3. Kliknij przycisk **wyodrębnić** do tego nowe informacje służą do oceny istniejących rekordów.  Wyniki mogą zostać zmodyfikowane rekordów innego niż ten, wystarczy zmodyfikować oparte na tej nowej analizy.
4. Nadal dodawać poprawki, dopóki wszystkie rekordy w extract poprawnie zidentyfikować dane do wypełnienia nowego pola niestandardowego.
5. Kliknij przycisk **zapisać wyodrębnić** po zakończeniu z wynikami.  Pole niestandardowe jest teraz zdefiniowana, ale nie można dodać go do wszystkich rekordów jeszcze.
6. Poczekaj, aż nowych rekordów odpowiadającego określonym kryteriom były zbierane i ponownie uruchom wyszukiwania dziennika. Nowe rekordy powinny mieć pola niestandardowego.
7. Użyj pola niestandardowego, podobnie jak inne właściwości rekordu.  Można używać go do agregacji i grupy danych i nawet używać do tworzenia nowych danych.

## <a name="viewing-custom-fields"></a>Wyświetlanie pola niestandardowe
Można wyświetlić listę wszystkich pól niestandardowych w grupie zarządzania z **Zaawansowane ustawienia** menu obszaru roboczego analizy dzienników w portalu Azure.  Wybierz **danych** , a następnie **pola niestandardowe** listę wszystkich pól niestandardowych w obszarze roboczym.  

![Niestandardowe pola](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Usuwanie pola niestandardowego
Istnieją dwa sposoby usuwania niestandardowego pola.  Pierwsza to **Usuń** opcji dla każdego pola podczas wyświetlania pełną listę, zgodnie z powyższym opisem.  Inne metody jest pobrać rekordu i kliknij przycisk do lewej części pola.  Menu będzie mieć opcję, aby usunąć pola niestandardowego.

## <a name="sample-walkthrough"></a>Przykładowe wskazówki
Poniższa sekcja przeprowadzi Cię przez kompletnym przykładem tworzenia niestandardowego pola.  W tym przykładzie wyodrębnia nazwę usługi w zdarzeń systemu Windows, które wskazują zmiana stanu usługi.  Zależy to od zdarzenia tworzone przez Menedżera sterowania usługami w dzienniku systemu na komputerach z systemem Windows.  Jeśli chcesz wykonać w tym przykładzie, musi być [zbierania zdarzeń informacji w dzienniku systemu](log-analytics-data-sources-windows-events.md).

Firma Microsoft wpisz poniższe zapytanie do zwrócenia wszystkich zdarzeń z Menedżera kontroli usług mające Identyfikatorem zdarzenia 7036, który jest zdarzenie wskazujące uruchomienie lub zatrzymanie usługi.

![Zapytanie](media/log-analytics-custom-fields/query.png)

Następnie wybierz możemy żadnych rekordów z 7036 identyfikator zdarzenia.

![Rekord źródła](media/log-analytics-custom-fields/source-record.png)

Chcemy nazwę usługi, która jest wyświetlana w **RenderedDescription** właściwości, a następnie wybierz przycisk Dalej, aby ta właściwość.

![Wyodrębnianie pól](media/log-analytics-custom-fields/extract-fields.png)

**Kreator wyodrębniania pola** jest otwarty i **EventLog** i **EventID** wybrano pola w **przykład Main** kolumny.  Oznacza to, że pole niestandardowe są definiowane dla zdarzenia w dzienniku systemu identyfikator zdarzenia 7036.  To jest wystarczająca, dlatego nie należy wybrać inne pola.

![Przykład głównego](media/log-analytics-custom-fields/main-example.png)

Możemy wyróżnić nazwy usługi w **RenderedDescription** właściwości i użyj **usługi** Aby określić nazwę usługi.  Pole niestandardowe zostanie wywołana **Service_CF**.

![Pole tytułu](media/log-analytics-custom-fields/field-title.png)

Firma Microsoft Zobacz, czy nazwa usługi jest identyfikowane prawidłowo niektóre rekordy, ale nie do innych użytkowników.   **Wyniki wyszukiwania** Pokaż nazwę dla tej części **Karta wydajności WMI** nie został wybrany.  **Podsumowanie** pokazuje, które rejestruje cztery z **DPRMA** usługi nieprawidłowo włączone dodatkowe word i dwa rekordy określone **Instalator modułów** zamiast **Instalator modułów systemu Windows**.  

![Wyniki wyszukiwania](media/log-analytics-custom-fields/search-results-01.png)

Możemy zaczynać **Karta wydajności WMI** rekordu.  Firma Microsoft, kliknij ikonę edycji, a następnie **zmodyfikować tego wyróżnienia**.  

![Modyfikowanie wyróżnienia](media/log-analytics-custom-fields/modify-highlight.png)

Firma Microsoft zwiększyć wyróżnienie Dołącz słowo **WMI** , a następnie uruchom ponownie wyodrębniania.  

![Przykład dodatkowe](media/log-analytics-custom-fields/additional-example-01.png)

Zobaczysz, że wpisy **Karta wydajności WMI** zostały skorygowane, i analizy dzienników również użyć tych informacji do rozwiązać rekordy **Instalator modułu Windows**.  Zobaczysz w **Podsumowanie** sekcji jednak, że **DPMRA** jest nadal nie są określone poprawnie.

![Wyniki wyszukiwania](media/log-analytics-custom-fields/search-results-02.png)

Firma Microsoft przewiń rekord z usługi DPMRA i użyj tego samego procesu, aby naprawić tego rekordu.

![Przykład dodatkowe](media/log-analytics-custom-fields/additional-example-02.png)

 Uruchomienie ekstrakcja, widać, że wszystkie nasze wyniki teraz są poprawne.

![Wyniki wyszukiwania](media/log-analytics-custom-fields/search-results-03.png)

Zobaczysz, że **Service_CF** zostało utworzone, ale nie została jeszcze dodana do żadnych rekordów.

![Liczba początkowa](media/log-analytics-custom-fields/initial-count.png)

Po upływie chwilę, więc nowe zdarzenia są zbierane, zobaczysz, że który **Service_CF** pole jest teraz dodawane do rekordów, które spełniają kryteriów.

![Wyniki końcowe](media/log-analytics-custom-fields/final-results.png)

Teraz można używać niestandardowego pola, podobnie jak inne właściwości rekordu.  Na przykład utworzymy zapytania, który grupuje przez nowy **Service_CF** pola, aby sprawdzić, które usługi są najbardziej aktywni.

![Grupuj według zapytania](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) do kompilowania zapytań przy użyciu pól niestandardowych kryteriów.
* Monitor [pliki dziennika niestandardowego](log-analytics-data-sources-custom-logs.md) który przeanalizować, przy użyciu pól niestandardowych.

