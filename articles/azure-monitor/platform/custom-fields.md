---
title: Pola niestandardowe w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Funkcja pól niestandardowych usługi Azure Monitor pozwala na tworzenie własnego pola z możliwością wyszukiwania na podstawie rekordów w obszarze roboczym usługi Log Analytics, które dodać właściwości zebranych rekordu.  W tym artykule opisano proces tworzenia pola niestandardowego i zawiera szczegółowy przewodnik z zdarzenie próbkowania.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: bwren
ms.openlocfilehash: eebf3709657382eb403041e6637e32e5f5d43b15
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793349"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor"></a>Tworzenie niestandardowych pól w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor

> [!NOTE]
> W tym artykule opisano, jak analizować dane tekstowe, w obszarze roboczym usługi Log Analytics, zgodnie z ich zebraniu. Istnieją zalety łączenia podczas analizowania danych tekstowych w zapytaniu, po ich zebraniu zgodnie z opisem w [analizy danych tekstowych w usłudze Azure Monitor](../log-query/parse-text.md).

**Pól niestandardowych** funkcji usługi Azure Monitor umożliwia rozszerzanie istniejących rekordów, w obszarze roboczym usługi Log Analytics, dodając własny pola z możliwością wyszukiwania.  Niestandardowe pola są automatycznie wypełniane na podstawie danych wyodrębnionych z innych właściwości w ten sam rekord.

![Przegląd](media/custom-fields/overview.png)

Na przykład rekord przykładowe poniżej zawiera przydatne dane ukryty w opisie zdarzenia. Wyodrębnienie tych danych do oddzielnych właściwość udostępnia takie czynności, jak filtrowanie i sortowanie.

![Wyodrębnij próbki](media/custom-fields/sample-extract.png)

> [!NOTE]
> W wersji zapoznawczej jest ograniczona do 100 pól niestandardowych w obszarze roboczym.  Ten limit zostanie rozszerzona, gdy ta funkcja staje się ogólnie dostępna.

## <a name="creating-a-custom-field"></a>Tworzenie pola niestandardowego
Podczas tworzenia pola niestandardowego, usługi Log Analytics, należy zrozumieć których dane mają być używane do wypełnienia jego wartość.  Używa technologii przez firmę Microsoft Research o nazwie FlashExtract szybkie identyfikowanie tych danych.  Zamiast konieczności podać jawne instrukcje, dane, które mają zostać wyodrębnione z przykładów, które należy podać poznaje usługi Azure Monitor.

Poniższe sekcje zawierają procedury tworzenia pola niestandardowego.  W dolnej części tego artykułu znajduje się przewodnik wyodrębniania próbki.

> [!NOTE]
> Niestandardowe pole jest wypełniane, jak rekordy spełniające określone kryteria są dodawane do obszaru roboczego usługi Log Analytics, dzięki czemu będą wyświetlane tylko w rekordów zbieranych po utworzeniu pola niestandardowego.  Pole niestandardowe nie zostanie dodany do rekordów, które znajdują się już w magazynie danych, podczas jego tworzenia.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Krok 1: określenie rekordy, które będą miały pole niestandardowe
Pierwszym krokiem jest do identyfikowania rekordy, które umożliwią pola niestandardowego.  Początkowo [standardowym dzienniku zapytań](../log-query/log-query-overview.md) , a następnie zaznacz rekord do działania jako model, który przedstawiono z usługi Azure Monitor.  Po określeniu, które mają do wyodrębniania danych do pola niestandardowego, **Kreator wyodrębniania pól** jest otwarty, gdzie Zweryfikuj i zoptymalizuj kryteria.

1. Przejdź do **dzienniki** i użyj [zapytanie, aby pobrać te rekordy](../log-query/log-query-overview.md) będą mieć pola niestandardowego.
2. Wybierz rekord, który usługi Log Analytics będzie używany jako model do wyodrębniania danych, aby wypełnić pole niestandardowe.  Należy określić dane, które mają zostać wyodrębnione z tego rekordu, a usługi Log Analytics użyje tych informacji do określenia logiki, aby wypełnić pole niestandardowe dla wszystkich rekordów podobne.
3. Rozwiń właściwości rekordu, kliknij przycisk wielokropka na lewo od najważniejszych właściwości rekordu, a następnie wybierz **Wyodrębnij pola z**.
4. **Kreator wyodrębniania pól** jest otwarty, a rekord wybrany jest wyświetlany w **głównym przykładem** kolumny.  Pole niestandardowe zostaną określone dla tych rekordów o tej samej wartości właściwości, które są wybrane.  
5. Jeśli ustawienie to nie dokładnie to, czego potrzebujesz wybrać dodatkowe pola, aby zawęzić kryteria.  Aby zmienić wartości pól kryteriów, użytkownik musi anulować i wybierz inny rekord zgodne z kryteriami, które chcesz.

### <a name="step-2---perform-initial-extract"></a>Krok 2 — wykonaj początkową wyodrębniania.
Po zidentyfikowaniu rekordy, które będą mieć pole niestandardowe, należy zidentyfikować dane, które mają zostać wyodrębnione.  Informacje te są wykorzystywane do identyfikacji podobnych wzorców w podobne rekordy usługi log Analytics.  W kroku po tym będzie mógł zweryfikować wyniki i dostarczyć więcej szczegółów dla usługi Log Analytics do użycia w ich analizy.

1. Wyróżnij tekst w rekordzie próbki, które chcesz wypełnić pole niestandardowe.  Następnie zobaczysz z okna dialogowego podaj nazwę i typ danych dla pola i wykonywanie początkowej wyodrębniania.  Znaki  **\_CF** zostanie dodane automatycznie.
2. Kliknij przycisk **wyodrębnić** przeprowadzać analizę zebranych rekordów.  
3. **Podsumowanie** i **wyniki wyszukiwania** sekcjach są wyświetlane wyniki extract, dzięki czemu można sprawdzić jego dokładności.  **Podsumowanie** Wyświetla kryteria używane do identyfikowania rekordów i liczbą dla każdego z wartościami danych zidentyfikowane.  **Wyniki wyszukiwania** zawiera szczegółową listę rekordów pasujących do kryteriów.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Krok 3 — Sprawdź dokładność wyodrębniania i utworzyć pole niestandardowe
Po wykonaniu początkowej wyodrębniania usługi Log Analytics wyświetli wyniki na podstawie danych, które już zostały zebrane.  Spojrzeć dokładne wyniki można utworzyć pole niestandardowe z nie dalszej pracy.  Jeśli nie, następnie uściślić wyniki, aby usługi Log Analytics może zwiększyć swojej logiki.

1. Jeśli wszystkie wartości w początkowej wyodrębniania nie są poprawne, kliknij przycisk **Edytuj** ikonę obok nieprawidłowych rekordów i wybierz pozycję **Modyfikuj to zaznaczenie** Aby zmodyfikować zaznaczenie.
2. Wpis jest kopiowany do **dodatkowe przykłady** sekcji poniżej **głównym przykładem**.  Wyróżnienie w tym miejscu można dostosować pomagające zrozumieć powinien wprowadzone przez zaznaczenie w usłudze Log Analytics.
3. Kliknij przycisk **wyodrębnić** do tego nowe informacje służą do oceny istniejących rekordów.  Wyniki mogą być zmieniane dla rekordów niż ten, który właśnie został zmodyfikowany oparte na tym nowe informacje analityczne.
4. Kontynuuj dodawanie poprawek, dopóki wszystkie rekordy w extract poprawnie zidentyfikować dane, aby wypełnić pole niestandardowe.
5. Kliknij przycisk **Zapisz wyodrębnienie** gdy jesteś zadowolony z wyników.  Pole niestandardowe teraz jest zdefiniowany, ale nie będzie można dodać go do żadnych rekordów jeszcze.
6. Poczekaj, aż nowe rekordy odpowiadającego określonym kryteriom gromadzone i następnie ponownie uruchomić wyszukiwanie w dzienniku. Nowe rekordy mogą mieć pola niestandardowego.
7. Użyj pole niestandardowe, takie jak wszystkich innych właściwości rekordu.  Można go użyć do agregacji i grupy danych i nawet używać, aby utworzyć nowe szczegółowe informacje.

## <a name="viewing-custom-fields"></a>Wyświetlanie pola niestandardowe
Możesz wyświetlić listę wszystkich pól niestandardowych w grupie zarządzania z **Zaawansowane ustawienia** menu obszaru roboczego usługi Log Analytics w witrynie Azure portal.  Wybierz **danych** i następnie **pól niestandardowych** listę wszystkich pól niestandardowych w obszarze roboczym.  

![Niestandardowe pola](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Usuwanie pola niestandardowego
Istnieją dwa sposoby, aby usunąć pole niestandardowe.  Pierwsza to **Usuń** opcji dla każdego pola, podczas wyświetlania pełną listę, zgodnie z powyższym opisem.  Inna metoda jest pobrać rekordu, a następnie kliknij przycisk po lewej stronie pola.  Menu będzie mieć opcję, aby usunąć pole niestandardowe.

## <a name="sample-walkthrough"></a>Przewodnik po przykładzie
Poniższej sekcji przedstawiono pełny przykład tworzenia pola niestandardowego.  W tym przykładzie pobiera nazwę usługi w Windows zdarzenia wskazujące zmianę stanu usługi.  To zależy od zdarzenia utworzone przez Menedżera sterowania usługami w logowania systemu Windows, komputerów.  Jeśli chcesz wykonać w tym przykładzie, musi być [zbierania informacji zdarzeń dziennika systemu](data-sources-windows-events.md).

Firma Microsoft wprowadź następujące zapytanie, aby zwrócić wszystkich zdarzeń z Menedżera kontroli usług mających Identyfikatorem zdarzenia 7036, który jest zdarzenie wskazujące uruchomienie lub zatrzymanie usługi.

![Zapytanie](media/custom-fields/query.png)

Firma Microsoft wybierz i rozwiń każdy rekord ze zdarzeniem 7036 identyfikator.

![Rekord źródła](media/custom-fields/source-record.png)

Definiujemy pola niestandardowe, klikając wielokropek obok najważniejsze właściwości.

![Wyodrębnienie pól](media/custom-fields/extract-fields.png)

**Kreator wyodrębniania pól** jest otwarty i **EventLog** i **EventID** wybrane pola w **głównym przykładem** kolumny.  Oznacza to, że pole niestandardowe zostanie zdefiniowana dla zdarzenia w dzienniku systemu identyfikator zdarzenia 7036.  Jest to wystarczające, więc nie potrzebujemy wybrać inne pola.

![Główny przykład](media/custom-fields/main-example.png)

Firma Microsoft wyróżnij nazwę usługi w **RenderedDescription** właściwość i użyj **usługi** do identyfikowania nazwy usługi.  Pole niestandardowe, zostanie wywołana **Service_CF**. Typ pola w tym przypadku jest ciągiem, dzięki czemu można pole pozostanie bez zmian.

![Tytuł pola](media/custom-fields/field-title.png)

Widzimy, że nazwa usługi jest identyfikowany prawidłowo niektóre rekordy, ale nie dla innych użytkowników.   **Wyniki wyszukiwania** Pokaż nazwę dla tej części **Karta wydajności WMI** nie został wybrany.  **Podsumowanie** pokazuje jednego rekordu zidentyfikowane **Instalator modułów** zamiast **Instalator modułów Windows**.  

![Wyniki wyszukiwania](media/custom-fields/search-results-01.png)

Rozpoczniemy pracę **Karta wydajności WMI** rekordu.  Firma Microsoft kliknij ikonę edycji i następnie **Modyfikuj to zaznaczenie**.  

![Modyfikowanie wyróżnienia](media/custom-fields/modify-highlight.png)

Możemy zwiększyć wyróżnienia, aby uwzględnić słowa **WMI** , a następnie uruchom ponownie wyodrębniania.  

![Dodatkowe przykład](media/custom-fields/additional-example-01.png)

Widać, że wpisy dla **Karta wydajności WMI** zostały poprawione, a usługi Log Analytics umożliwia również te informacje rozwiązać rekordy dla **Instalator modułu Windows**.

![Wyniki wyszukiwania](media/custom-fields/search-results-02.png)

Można teraz uruchomić kwerendę sprawdzającą **Service_CF** zostanie utworzony, ale nie została jeszcze dodana do żadnych rekordów. To, ponieważ pole niestandardowe nie działa dla istniejących rekordów, dlatego musimy poczekaj, aż nowe rekordy, które mają być zbierane.

![Liczba początkowa](media/custom-fields/initial-count.png)

Po upływie trochę czasu więc nowe zdarzenia są zbierane, widać, że **Service_CF** pola teraz jest dodawany do rekordy, które spełniają nasze kryteria.

![Wyniki końcowe](media/custom-fields/final-results.png)

Teraz możemy użyć pole niestandardowe, takie jak wszystkich innych właściwości rekordu.  Na przykład możemy utworzyć kwerendę, która grupuje przez nowy **Service_CF** pola, aby sprawdzić, które usługi są najbardziej aktywne.

![Grupuj według kwerendy](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md) tworzenie zapytań przy użyciu pól niestandardowych kryteriów.
* Monitor [pliki dziennika niestandardowego](data-sources-custom-logs.md) , analizy przy użyciu pól niestandardowych.

