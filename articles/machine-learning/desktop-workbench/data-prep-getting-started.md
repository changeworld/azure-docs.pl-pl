---
title: "Wprowadzenie do przygotowania danych do usługi Azure Machine Learning | Dokumentacja firmy Microsoft"
description: Jest to pobierania przewodnika dla sekcji przygotowanie bazy danych AML workbench
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: b0fbb0af433cfad6693b022d7a00373dc39533aa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="getting-started-with-data-preparation"></a>Wprowadzenie do przygotowywania danych

Zapraszamy danych przygotowanie Podręcznik Wprowadzenie. 

Przygotowanie danych udostępnia zestaw narzędzi do eksplorowania wydajnie, opis i rozwiązywania problemów w danych. Umożliwia ona wykorzystują dane w wielu formularzach i przekształcania danych oczyszczony dane, które lepiej jest przeznaczone do użycia podrzędne.

Przygotowanie danych jest instalowany jako część środowiska Azure Machine Learning Workbench.  Użyj Przygotowanie danych lokalnie lub wdrożyć klastra docelowego i w chmurze jako środowisku środowiska uruchomieniowego lub wykonywania.

Środowisko uruchomieniowe czasu projektowania używa języka Python dla rozszerzeń i zależy od różnych bibliotek języka Python, takie jak Pandas. Zgodnie z innymi składnikami Workbench uczenie Maszynowe Azure nie jest konieczne do zainstalowania języka Python, jest instalowana automatycznie. Jednak jeśli musisz zainstalować dodatkowe biblioteki te biblioteki muszą być zainstalowane w katalogu usługi Azure ML Workbench Python nie zwykle katalogu języka Python. Więcej informacji na temat sposobu instalowania pakietów można znaleźć [tutaj](data-prep-python-extensibility-overview.md).

Projekt jest wymagana przed użyciem przygotowanie bazy danych, po utworzeniu projektu można przygotować danych. 

Przejdź do sekcji danych projektu, wybierając ikonę danych ![ikona źródła danych](media/data-prep-getting-started/data-source-icon.png) po lewej stronie ekranu.  Kliknij przycisk "+" ponownie **Dodaj źródło danych**. Należy uruchomić Kreatora źródła danych i dodaje **źródła danych** (.dsource) pliku do projektu po ukończeniu kreatora. Domyślnie widok danych jest siatki. Powyżej siatki jest również można wybrać metryki widoku. W widoku metryki przedstawiono podsumowanie statystyk.  Po przejrzeniu podsumowania statystyk, kliknij **Prepare** w górnej części ekranu. [Więcej informacji na temat Kreatora źródła danych](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Bloki konstrukcyjne Przygotowanie danych ##
### <a name="the-package"></a>Pakiet ###

Pakiet jest podstawowym kontenera do pracy. Pakiet jest artefaktu, który jest zapisywana i ładowanych z dysku. Podczas pracy w kliencie, pakiet jest stale automatycznie zapisany w tle. 

Wynik/pakietu można zbadane, Python lub za pomocą notesu Jupyter.

Pakiet może być wykonywane przez wiele środowisk uruchomieniowych w tym lokalne Python, Spark (w tym w Docker) i usługi HDInsight.

Pakiet zawiera co najmniej jeden przepływy danych, które są kroki i transformacji zastosowanych do danych.

Pakiet może używać inny pakiet jako źródła danych (określone jako odwołanie przepływu danych).

### <a name="the-dataflow"></a>Biblioteka przepływu danych ###
Biblioteka przepływu danych ma źródło i przekształca opcjonalne, które są ustawione za pośrednictwem serii kroków i opcjonalnie miejsc docelowych. Ponowne kliknięcie krok wykonuje wszystkich źródeł i transformacje przed, w tym kroku wybrane.  Przekształcone dane za pośrednictwem tego kroku jest wyświetlany w siatce. Kroki można dodać, przenieść i usuwane w ramach przepływu danych za pośrednictwem listy kroku.

Na liście krok po prawej stronie klienta można otworzyć i zamknięte w celu zapewnienia więcej miejsca na ekranie.

Przepływy danych wielu może istnieć w interfejsie użytkownika w czasie, każdego przepływu danych jest reprezentowany jako karty w interfejsie użytkownika.

### <a name="the-source"></a>Źródło
Gdy dane pochodzą z i format jest jest źródło. Pakiet przygotowanie bazy danych zawsze źródeł danych z innego Flow(Data Source) danych. Jest to odwołanie przepływu danych, który zawiera informacje. Każde źródło ma środowisko innego użytkownika, aby umożliwić jego skonfigurowanie. Źródło tworzy "prostokątna" / tabelaryczny widok danych. Jeśli źródło danych ma pierwotnie "bez wyrównania do prawej", następnie struktury są znormalizowane jako "prostokątna". [Dodatek 2 zawiera bieżącą listę obsługiwanych źródeł](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>Przekształcenia ###
Transformacje wykorzystują dane w danym formacie, wykonać pewne operacje na danych (np. zmiana typu danych) i następnie wyprodukować danych do nowego formatu. Przekształcanie poszczególnych ma własnego interfejsu użytkownika i behavior(s). Tworzenie łańcuchów kilka przekształceń ze sobą za pomocą kroków przepływu danych jest podstawową funkcjonalność Przygotowanie danych. [Dodatek 3 zawiera bieżącą listę obsługiwanych transformacje](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>Inspektor ###

Inspektorzy są wizualizacje danych i są dostępne do lepszego zrozumienia danych.  Opis danych i danych jakości wystawia ułatwia podjęcie decyzji, należy podjąć odpowiednie działania, które (transformacji). Niektóre inspektorzy obsługuje akcje, które generowania przekształceń. Na przykład inspektora liczba wartości umożliwia wybierz wartość, a następnie zastosować filtr, aby dołączyć tę wartość lub do wykluczenia z tej wartości. Inspektorzy oferuje również kontekstu transformacji. Na przykład wybranie co najmniej jedną kolumnę zmienia przekształcenia to możliwe, które mogą być stosowane.

Kolumna może mieć wielu inspektorzy w dowolnym momencie w czasie (na przykład statystyk kolumny i Histogram). Można także wystąpień inspektora w wielu kolumnach. Na przykład wszystkie kolumny liczbowe może mieć histogramów w tym samym czasie.

Inspektorzy są wyświetlane w profilowania również w dolnej części ekranu.  Zmaksymalizuj inspektorzy większe w obszarze zawartości głównej je wyświetlić. Siatki danych należy traktować jako inspektora domyślne. Wszelkie Inspektora można rozszerzyć do głównego obszaru zawartości. Inspektorzy w obszarze zawartości głównej zminimalizować do profilowania również. Dostosowywanie inspektora, klikając ikonę ołówka w kontroler. Inspektorzy zmiany kolejności w również przy użyciu przeciągnij i upuść.

Niektóre inspektorzy obsługuje tryb "Halo". Ten tryb zawiera wartość lub stan sprzed zastosowania ostatniego transformacji. Stara wartość jest wyświetlane w kolorze szarym z bieżącą wartością na pierwszym planie i pokazuje wpływ transformacji. [Dodatek 4 zawiera bieżącą listę obsługiwanych inspektorzy](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>Miejsce docelowe
 Miejsce docelowe jest gdzie możesz zapisu/Eksportuj dane do po przygotowaniu w przepływu danych. Danego przepływu danych może mieć wielu miejsc docelowych. Każdej lokalizacji docelowej ma środowisko innego użytkownika, aby umożliwić jego skonfigurowanie. Miejsce docelowe zużywa dane w formacie "prostokątna" / tabelarycznych i zapisuje do określonej lokalizacji w danym formacie. [Dodatek 5 zawiera bieżącą listę obsługiwanych miejsc docelowych](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Przy użyciu Przygotowanie danych ###
Przygotowanie danych zakłada podstawowe pięciu krokach metodologii/podejście do przygotowania danych.

#### <a name="step-1-ingestion"></a>Krok 1: wprowadzanie ####
Importowanie danych w celu przygotowania danych przy użyciu **Dodaj źródło danych** opcji w widoku projektu.  Wszystkie początkowej wprowadzanie danych jest obsługiwana za pomocą Kreatora źródła danych.

#### <a name="step-2-understandprofile-the-data"></a>Krok 2: Zrozumienie/profilu danych ####

Najpierw sprawdź pasek jakości danych na początku każdej kolumny. Zielony oznacza wierszy, które mają wartości. Szary reprezentuje wiersze, dla których brakuje wartości null, itp. Czerwony oznacza wartości błędów. Umieść kursor nad paskiem uzyskanie etykietka narzędzia, która dokładnie numery wierszy w każdej z trzech zasobników. Używa danych jakości paska skali logarytmicznej zawsze Sprawdź wartości rzeczywistych można uzyskać pewne pojęcie nierównej ilość danych brakuje.

![kolumny](media/data-prep-getting-started/columns.png)

Następnie użyj kombinacji innych inspektorzy plus siatki, aby lepiej zrozumieć właściwości danych.  Uruchom formułowania hipotez informacje o przygotowaniu danych wymagane do dalszej analizy. Większość inspektorzy działają w jednej kolumnie lub mniejszą liczbą kolumn.  

Istnieje prawdopodobieństwo, że kilka inspektorzy kilku kolumn są potrzebne do zrozumienia danych. Możesz przewijać różnych inspektorzy w profilowania również. W magazynie można również przenosić inspektorzy nagłówek listy, aby można było je wyświetlać w obszarze od razu widoczne.

![Inspektorzy](media/data-prep-getting-started/inspectors.PNG)

Inspektorzy różnych są dostępne dla ciągłej vs podzielone na kategorie zmienne/kolumny. Menu inspektora włącza i wyłącza opcje w zależności od typu zmienne/kolumn, których masz.

Podczas pracy z szeroką zestawów danych, który ma wiele kolumn, zalecane jest podejście pragmatyczne pracy z podzestawy. Ta metoda obejmuje koncentrujących się na niewielkiej liczby kolumn (na przykład 5 – 10), ich przygotowywanie i następnie pracuje nad pozostałych kolumnach. Inspektor siatki obsługuje partycjonowanie pionowe kolumn i, jeśli masz więcej niż 300 kolumny, a następnie trzeba "page" za pośrednictwem ich.
 

#### <a name="step-3-transform-the-data"></a>Krok 3: Przekształcania danych ####
Transformacje zmiany danych i umożliwia wykonanie danych do obsługi bieżącego hipoteza pracy. Przekształceń są wyświetlane jako czynności na liście krok po prawej stronie. Istnieje możliwość "czas podróży" za pośrednictwem listy krok, klikając w żadnym punkcie dowolnego na liście kroku.

Zielona ikona na lewo od danego kroku wskazuje, że ma i wykonania przekształcenia odzwierciedla dane. Pionowy pasek po lewej kroku wskazuje bieżący stan danych w inspektorzy.

![kroki](media/data-prep-getting-started/steps.PNG)

Spróbuj poprowadzić małych częstych zmian do danych i do sprawdzania poprawności (krok 4) po każdej zmianie jako hipotezę rozwoju środowisko.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>Krok 4: Sprawdzić ich oddziaływanie transformacji. 
Zdecyduj, czy hipoteza była poprawna. Jeśli są poprawne, opracowywanie hipoteza dalej i powtórz kroki 2 – 3 nowego. Jeśli jest nieprawidłowy, następnie Cofnij ostatniego przetworzenia opracowanie nowych hipoteza i powtórz kroki od 2 do 3.

Podstawowy można określić, jeśli przekształcenie miało wpływ prawo jest Użyj inspektorzy. Użyj istniejącego. Użyj inspektorzy z Halo efekt włączona lub uruchamianie wielu inspektorzy do wyświetlania danych w podane punkty w czasie.

![Inspektor Halo](media/data-prep-getting-started/halo1.PNG) ![Inspektor Halo](media/data-prep-getting-started/halo2.PNG)

Aby cofnąć transformację, przejdź na liście kroki po prawej stronie interfejsu użytkownika. (Panelu listę kroków może być konieczne zostać zdjęte ze stosu wstecz wychodzących. Aby go otworzyć, kliknij przycisk wskazanie ostrokątny po lewej stronie). W panelu wybierz przekształcania, które zostało wykonane, które chcesz wycofać. Wybierz z listy rozwijanej po prawej stronie bloku interfejsu użytkownika. Wybierz opcję **Edytuj** do wprowadzania zmian lub **usunąć** do usunięcia z listy kroków i przepływu danych transformacji.

#### <a name="step-5-output"></a>Krok 5: dane wyjściowe 
Po zakończeniu przygotowania danych, może zapisać przepływu danych wyjściowych. Biblioteka przepływu danych może mieć wielu wyjść. Z menu transformacje można wybrać wyjściowy, który ma zestaw danych do zapisania jako. Można również wybrać miejsce docelowe danych wyjściowych. 

## <a name="list-of-appendices"></a>Lista dodatki 
[Dodatek 2 - obsługiwanych źródeł danych](data-prep-appendix2-supported-data-sources.md)  
[Przekształca dodatku 3 - obsługiwane](data-prep-appendix3-supported-transforms.md)  
[Inspektorzy obsługiwany dodatek 4.](data-prep-appendix4-supported-inspectors.md)  
[Dodatek 5 - obsługiwane miejsc docelowych](data-prep-appendix5-supported-destinations.md)  
[Dodatek 6 - przykładowe wyrażenia filtru w języku Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Dodatek 7 - przykładowe przekształcenie wyrażeń przepływu danych w języku Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Dodatek 8 - źródła danych przykładowych w języku Python](data-prep-appendix8-sample-source-connections-python.md)  
[Dodatek 9 — przykładowy docelowy połączenia w języku Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Dodatek 10 - kolumny próbki przekształca w języku Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Zobacz też

[Zaawansowane danych przygotowania samouczka](tutorial-bikeshare-dataprep.md)