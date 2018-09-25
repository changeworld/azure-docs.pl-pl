---
title: Wprowadzenie do przygotowywania danych do usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jest to przewodnik Wprowadzenie dla sekcji przygotowywania danych aplikacji workbench AML
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ROBOTS: NOINDEX
ms.openlocfilehash: a3f50e8fbaa396d9ca5a0d14fe9f54c30bc24f41
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996285"
---
# <a name="getting-started-with-data-preparation"></a>Wprowadzenie do przygotowywania danych

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Zapraszamy do danych przygotowania przewodnik Wprowadzenie dla. 

Przygotowywanie danych zawiera zestaw funkcji wspomagających efektywnie Eksplorowanie, wykrywania i rozwiązywania problemów w danych. Umożliwia korzystanie z rejestrowanych danych w wielu formularzach i przekształcający te dane oczyszczonych dane, które jest lepiej dopasowane do użycia podrzędnego.

Przygotowywanie danych jest instalowany jako część środowisko Azure Machine Learning Workbench.  Użyj operacji przygotowania danych lokalnie lub wdrożenie w klastrze docelowym i w chmurze jako środowisko wykonywania lub wykonywanie.

Środowiska uruchomieniowego w czasie projektowania używa języka Python dla rozszerzalności i zależy od różnych bibliotek języka Python, takich jak Pandas. Zgodnie z innymi składnikami aplikacji Azure ML Workbench nie ma potrzeby instalowania języka Python, jest instalowana automatycznie. Jednakże jeśli musisz zainstalować dodatkowe biblioteki tych bibliotek muszą być zainstalowane w katalogu usługi Azure ML Workbench w języku Python nie zwykle katalogu języka Python. Można znaleźć szczegółowe informacje na temat sposobu instalowania pakietów [tutaj](data-prep-python-extensibility-overview.md).

Projekt jest wymagane przed użyciem przygotowywanie danych, po utworzeniu projektu może przygotowywania danych. 

Przejdź do sekcji danych projektu, wybierając ikonę danych ![ikona źródła danych](media/data-prep-getting-started/data-source-icon.png) po lewej stronie ekranu.  Kliknij przycisk "+" ponownie do **Dodaj źródło danych**. Należy uruchomić Kreatora źródła danych i dodaje **źródła danych** (.dsource) pliku do projektu po zakończeniu pracy kreatora. Domyślnie widok danych jest siatki. Powyżej siatki istnieje również możliwość wybierz widok metryk. W widoku metryki są wyświetlane statystyki podsumowujące.  Po przejrzeniu statystyki podsumowujące kliknij **przygotowanie** w górnej części ekranu. [Więcej informacji na temat Kreatora źródła danych](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Bloki konstrukcyjne przygotowywania danych ##
### <a name="the-package"></a>Pakiet ###

Pakiet jest podstawowym kontenerem na potrzeby swoją pracę. Pakiet jest artefakt, który jest zapisywana i załadowany z dysku. Podczas pracy w klienta, pakiet jest stale automatycznie zapisany w tle. 

W języku Python lub za pomocą notesu programu Jupyter, można eksplorować tylko wyjściowe/pakietu.

Pakiet można wykonywać na wielu modułów wykonawczych, takich jak lokalne języka Python, Spark (w tym Docker) i HDInsight.

Pakiet zawiera jeden lub więcej przepływów danych, które są kroki i transformacji zastosowanych do danych.

Pakiet może używać inny pakiet jako źródła danych (określone jako przepływ danych odwołania).

### <a name="the-dataflow"></a>Przepływ danych ###
Przepływ danych zawiera źródło i opcjonalnie przekształca, które są rozmieszczone przez szereg czynności i opcjonalnie miejsc docelowych. Ponowne kliknięcie kroku wykonuje wszystkich źródeł i przed przekształceń, łącznie z zaznaczonym krokiem.  Przekształcone dane za pośrednictwem tego kroku jest wyświetlany w obrębie siatki. Kroki mogą być dodawane, przeniesione lub usunięte w ramach przepływu za pośrednictwem listy kroku.

Na liście kroku po prawej stronie klienta, można go otworzyć i zamknięte w celu zapewnienia więcej miejsca na ekranie.

Wiele przepływów danych może znajdować się w Interfejsie użytkownika w czasie, każdy przepływ danych jest przedstawiana jako kartę w interfejsie użytkownika.

### <a name="the-source"></a>Źródło
Gdy dane pochodzą z i format jest jest źródło. Pakiet przygotowania bazy danych zawsze źródeł danych z innego Flow(Data Source) danych. Jest to odwołanie przepływu danych, który zawiera informacje. Każde źródło ma doświadczenie innego użytkownika, aby zezwalała na można skonfigurować. Źródło tworzy "prostokątna" / tabelaryczny widok danych. Jeśli źródło danych ma pierwotnie "bez wyrównania do prawej", następnie struktury jest znormalizować jako "prostokątna". [Dodatek 2 zawiera bieżącą listę obsługiwanych źródeł](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>Przekształcenia ###
Przekształcenia wykorzystywania danych w danym formacie, wykonać pewne operacje na danych (np. zmiana typu danych) i następnie generują dane do nowego formatu. Każdy przekształcenia ma swój własny interfejs użytkownika i behavior(s). Łączenie łańcuchowe wielu przekształceń za pośrednictwem czynności w przepływie danych stanowi podstawę funkcji przygotowywania danych. [Dodatku 3 zawiera bieżącą listę obsługiwane przekształcenia](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>Inspektor ###

Inspektorzy są wizualizacje danych i są dostępne do lepszego zrozumienia dane.  Informacje o danych i danych jakości problemów ułatwia podjęcie decyzji, powinny zostać podjęte akcje, które (przekształcenia). Niektóre inspektorzy obsługuje akcje, które generują przekształcenia. Na przykład Inspektor liczba wartości umożliwia wybierz wartość, a następnie zastosować filtr, aby uwzględnić tę wartość, lub do wykluczenia z tej wartości. Inspektorzy oferuje również kontekst transformacji. Na przykład wybranie co najmniej jedna kolumna zmienia możliwe przekształceń, które mogą być stosowane.

Kolumna może mieć wiele inspektorzy w dowolnym momencie w czasie (na przykład statystyki kolumny i Histogram). Można również istnieć wystąpień Inspektor w wielu kolumnach. Na przykład wszystkie kolumny liczbowe może mieć histogramów w tym samym czasie.

Inspektorzy są wyświetlane w profilowania dobrze w dolnej części ekranu.  Maksymalizuj inspektorzy widoczne większe w obrębie głównego obszaru zawartości. Siatka danych należy traktować jako inspektor domyślne. Wszelkie Inspector można rozszerzyć do głównego obszaru zawartości. Inspektorzy w obrębie głównego obszaru zawartości zminimalizowanie programu do profilowania dobrze. Inspektor można dostosować, klikając ikonę ołówka w panelu Inspektor. Inspektorzy zmiany kolejności w obrębie również za pomocą przeciąganie i upuszczanie.

Niektóre inspektorzy obsługuje tryb "Halo". W tym trybie pokazuje wartość lub stan sprzed zastosowania ostatniego transformacji. Stara wartość jest wyświetlana w kolorze szarym z bieżącą wartością na pierwszym planie i pokazuje wpływ transformacji. [Dodatek 4 zawiera bieżącą listę obsługiwani inspektorzy](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>Miejsce docelowe
 Miejsce docelowe jest gdzie możesz zapisu/export dane po przygotowaniu w przepływu danych. Dany przepływ danych może mieć wielu miejsc docelowych. Każdej lokalizacji docelowej zawiera środowisko innego użytkownika, aby zezwalała na można skonfigurować. Miejsce docelowe wykorzystuje dane w formacie "prostokątna" / tabelarycznym i zapisuje miejsce w danym formacie. [Dodatek 5 zawiera bieżącą listę obsługiwanych lokalizacji docelowych](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Za pomocą operacji przygotowania danych ###
Przygotowywanie danych zakłada podstawowe pięciu krokach metodologii/podejście do przygotowywania danych.

#### <a name="step-1-ingestion"></a>Krok 1: pozyskiwanie ####
Importowanie danych w celu przygotowania danych przy użyciu **Dodaj źródło danych** opcji w widoku projektu.  Wszystkie początkowej pozyskiwania danych odbywa się za pomocą Kreatora źródła danych.

#### <a name="step-2-understandprofile-the-data"></a>Krok 2: Omówienie/profilu danych ####

Po pierwsze Spójrz na pasku jakości danych, u góry każdej kolumny. Zielony reprezentuje wiersze, które mają wartości. Szary reprezentuje wiersze, dla których brakuje wartości ma wartość null, itp. Kolor czerwony oznacza wartości błędów. Umieść kursor nad paskiem można pobrać etykietki narzędzia dokładnie numery wierszy we wszystkich trzech zasobników. Używa pasku jakości danych skali logarytmicznej więc zawsze sprawdzić wartości rzeczywistych można uzyskać przybliżoną działania woluminu brakujące dane.

![Kolumny](media/data-prep-getting-started/columns.png)

Następnie użyj kombinacji innych inspektorzy oraz siatki, aby lepiej zrozumieć właściwości danych.  Rozpocznij formułowania hipotezy o przygotowywaniu danych wymagane w celu dalszej analizy. Większość inspektorzy działać względem pojedynczej kolumny lub niewielkiej liczby kolumn.  

Istnieje prawdopodobieństwo, że kilka inspektorzy kilku kolumn są potrzebne zrozumieć dane. Możesz przewijać różnych inspektorzy w profilowania dobrze. W ramach dobrze można również przenieść inspektorzy porównanie listy, aby można było je wyświetlić w obszarze od razu widoczne.

![Inspektorzy](media/data-prep-getting-started/inspectors.PNG)

Inspektorzy różnych są dostarczane dla ciągłej vs podzielone na kategorie zmienne/kolumny. Menu inspektora włącza i wyłącza opcje, w zależności od typu zmienne/kolumny, które masz.

Podczas pracy z szerokiego zestawów danych, które mają wiele kolumn, zalecane jest pragmatyczne podejście pracy z podzbiorów. Ta metoda obejmuje koncentrujących się na niewielką liczbę kolumn (na przykład 5 – 10), ich przygotowywanie i następnie pracy za pomocą pozostałych kolumnach. Inspektor siatki obsługuje partycjonowanie pionowe kolumn i dlatego jeśli masz więcej niż 300 kolumny, musisz "page" przetwarza je.
 

#### <a name="step-3-transform-the-data"></a>Krok 3: Przekształcanie danych ####
Przekształcenia zmiany danych i umożliwi wykonanie dane w celu obsługi bieżącego hipotezę pracy. Transformacje są traktowane jako czynności na liście kroku po prawej stronie. Istnieje możliwość "czas podróży" za pośrednictwem listy kroku, klikając na dowolnym dowolnego punktu na liście kroku.

Zieloną ikonę po lewej stronie w danym kroku wskazuje, że jego wykonaniu i danych odzwierciedla wykonania przekształcenia. Pionowy pasek po lewej stronie kroku wskazuje bieżący stan danych w inspektorzy.

![kroki](media/data-prep-getting-started/steps.PNG)

Spróbuj dokonać małych częste zmiany danych, a do sprawdzania poprawności (krok 4) po każdej zmianie w miarę rozwoju hipotezę.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>Krok 4: Weryfikacja wpływu transformacji. 
Zdecyduj, jeśli hipoteza była poprawna. Jeśli jest to poprawne, twórz dalej hipotezę i powtórz kroki 2 – 3 nowego. Niepoprawne, następnie cofanie przekształcania ostatniego i opracowywanie nowych hipotezę i powtórz kroki 2 – 3.

Jest podstawowym sposobem ustalenia, jeśli przekształcenie miało wpływ odpowiednie do użycia inspektorzy. Użyj istniejącej. Inspektorzy korzystanie z Halo efekt włączone lub uruchomienia inspektorami wielu do wyświetlania danych w danej punkty w czasie.

![Inspektor obwódki](media/data-prep-getting-started/halo1.PNG) ![Inspektor obwódki](media/data-prep-getting-started/halo2.PNG)

Aby cofnąć przekształcenie, przejdź na liście kroków po prawej stronie interfejsu użytkownika. (Panelu listę kroków może być konieczne zostać zdjęte ze stosu powrotem. Aby go otworzyć, kliknij przycisk wskazuje ostrokątny po lewej stronie). W panelu wybierz przekształcania, wykonywanego chcesz cofnąć. Wybierz listę rozwijaną po prawej stronie bloku interfejsu użytkownika. Wybierz opcję **Edytuj** zmian lub **Usuń** Usuwanie przekształcenia listę kroków i przepływu danych.

#### <a name="step-5-output"></a>Krok 5: dane wyjściowe 
Po zakończeniu Twojej przygotowywania danych, można napisać przepływu danych do pliku wyjściowego. Przepływ danych może mieć wiele wyjść. Z menu przekształcenia można wybrać, wyjściowy, który chcesz, aby zestaw danych do zapisania jako. Możesz również wybrać miejsce docelowe danych wyjściowych. 

## <a name="list-of-appendices"></a>Lista załączników 
[Dodatek 2 - obsługiwanych źródeł danych](data-prep-appendix2-supported-data-sources.md)  
[Dodatku 3 - obsługiwane przekształcenia](data-prep-appendix3-supported-transforms.md)  
[Dodatek 4 - obsługiwani inspektorzy](data-prep-appendix4-supported-inspectors.md)  
[Dodatek 5 - obsługiwane miejsca docelowe](data-prep-appendix5-supported-destinations.md)  
[Dodatek 6 - przykładowe wyrażenia filtru w języku Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Dodatek 7 — przykładowe Przekształcanie przepływu danych wyrażenia w języku Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Dodatek 8 - źródła danych przykładowych w języku Python](data-prep-appendix8-sample-source-connections-python.md)  
[Dodatek 9 - przykładowe połączenia miejsc docelowych w języku Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Dodatek 10 - kolumny przykładowe transformacje w języku Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Zobacz też

[Samouczek przygotowywania danych zaawansowane](tutorial-bikeshare-dataprep.md)