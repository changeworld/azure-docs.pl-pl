---
title: Pola niestandardowe w usłudze Azure Monitor (wersja zapoznawcza) | Dokumenty firmy Microsoft
description: Funkcja Pola niestandardowe usługi Azure Monitor umożliwia tworzenie własnych pól z możliwością wyszukiwania z rekordów w obszarze roboczym usługi Log Analytics, które dodają do właściwości zebranego rekordu.  W tym artykule opisano proces tworzenia pola niestandardowego i zawiera szczegółowe wskazówki z przykładowym zdarzeniem.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: bfb0a73631564c96a4af745fe9d7540a3a84f9c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655365"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Tworzenie pól niestandardowych w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor (Wersja zapoznawcza)

> [!NOTE]
> W tym artykule opisano sposób analizowania danych tekstowych w obszarze roboczym usługi Log Analytics podczas ich zbierania. Zalecamy analizowanie danych tekstowych w filtrze kwerend po zebraniu ich zgodnie ze [wskazówkami opisanymi w obszarze Analizowanie danych tekstowych w usłudze Azure Monitor.](../log-query/parse-text.md) Zapewnia kilka zalet w stosunku do korzystania z pól niestandardowych.

Funkcja **Pola niestandardowe** usługi Azure Monitor umożliwia rozszerzenie istniejących rekordów w obszarze roboczym usługi Log Analytics przez dodanie własnych pól z możliwością wyszukiwania.  Pola niestandardowe są automatycznie wypełniane z danych wyodrębnionych z innych właściwości w tym samym rekordzie.

![Omówienie](media/custom-fields/overview.png)

Na przykład poniższy przykładowy rekord ma przydatne dane pochowane w opisie zdarzenia. Wyodrębnianie tych danych do oddzielnej właściwości udostępnia go dla takich akcji, jak sortowanie i filtrowanie.

![Ekstrakt z próbki](media/custom-fields/sample-extract.png)

> [!NOTE]
> W wersji zapoznawczej w obszarze roboczym jest ograniczona liczba pól niestandardowych.  Ten limit zostanie rozszerzony, gdy ta funkcja osiągnie ogólną dostępność.

## <a name="creating-a-custom-field"></a>Tworzenie pola niestandardowego
Podczas tworzenia pola niestandardowego usługa Log Analytics musi zrozumieć, które dane mają być używane do wypełniania jego wartości.  Wykorzystuje technologię firmy Microsoft Research o nazwie FlashExtract, aby szybko zidentyfikować te dane.  Zamiast wymagać od ciebie podania jawnych instrukcji, usługa Azure Monitor dowiaduje się o danych, które chcesz wyodrębnić z przykładów, które podasz.

Poniższe sekcje zawierają procedurę tworzenia pola niestandardowego.  Na dole tego artykułu znajduje się instruktaż ekstrakcji próbki.

> [!NOTE]
> Pole niestandardowe jest wypełniane, ponieważ rekordy spełniające określone kryteria są dodawane do obszaru roboczego usługi Log Analytics, więc będą wyświetlane tylko w rekordach zebranych po utworzeniu pola niestandardowego.  Pole niestandardowe nie zostanie dodane do rekordów, które znajdują się już w magazynie danych podczas jego tworzenia.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Krok 1 — identyfikowanie rekordów, które będą miały pole niestandardowe
Pierwszym krokiem jest zidentyfikowanie rekordów, które otrzymają pole niestandardowe.  Zacznij od [standardowej kwerendy dziennika,](../log-query/log-query-overview.md) a następnie wybierz rekord, aby działać jako model, z którego będzie się uczyć usługa Azure Monitor.  Po określeniu, że dane mają być wyodrębniane do pola niestandardowego, otwierany jest **Kreator wyodrębniania pól** w miejscu sprawdzania poprawności i udoskonalania kryteriów.

1. Przejdź do **dzienników** i użyj [kwerendy, aby pobrać rekordy,](../log-query/log-query-overview.md) które będą miały pole niestandardowe.
2. Wybierz rekord, który usługa Log Analytics będzie używana do działania jako model wyodrębniania danych w celu wypełnienia pola niestandardowego.  Zidentyfikujesz dane, które chcesz wyodrębnić z tego rekordu, a usługa Log Analytics użyje tych informacji do określenia logiki wypełniania pola niestandardowego dla wszystkich podobnych rekordów.
3. Rozwiń właściwości rekordu, kliknij elipsę po lewej stronie górnej właściwości rekordu, a następnie wybierz pozycję **Wyodrębnij pola z .**
4. Zostanie otwarty **Kreator wyodrębniania pól,** a wybrany rekord zostanie wyświetlony w kolumnie **Przykład główny.**  Pole niestandardowe zostanie zdefiniowane dla tych rekordów o tych samych wartościach we właściwościach, które są zaznaczone.  
5. Jeśli zaznaczenie nie jest dokładnie takie, jakie chcesz, wybierz dodatkowe pola, aby zawęzić kryteria.  Aby zmienić wartości pól dla kryteriów, należy anulować i wybrać inny rekord pasujący do żądanych kryteriów.

### <a name="step-2---perform-initial-extract"></a>Krok 2 - Wykonaj ekstrakt początkowy.
Po zidentyfikowaniu rekordów, które będą miały pole niestandardowe, można zidentyfikować dane, które chcesz wyodrębnić.  Usługa Log Analytics użyje tych informacji do identyfikowania podobnych wzorców w podobnych rekordach.  W kroku po tym będzie można sprawdzić poprawność wyników i podać dalsze szczegóły dla usługi Log Analytics do użycia w swojej analizie.

1. Wyróżnij tekst w przykładowym rekordzie, który chcesz wypełnić pole niestandardowe.  Następnie zostanie wyświetlone okno dialogowe, aby podać nazwę i typ danych dla pola i wykonać początkowy wyciąg.  Znaki ** \_CF** zostaną automatycznie dołączone.
2. Kliknij **przycisk Wyodrębnij,** aby przeprowadzić analizę zebranych rekordów.  
3. W sekcjach **Podsumowanie** i **Wyniki wyszukiwania** wyświetlane są wyniki wyciągu, dzięki czemu można sprawdzić jego dokładność.  **Podsumowanie** wyświetla kryteria używane do identyfikowania rekordów i liczbę dla każdej z zidentyfikowanych wartości danych.  **Wyniki wyszukiwania** zawiera szczegółową listę rekordów spełniających kryteria.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Krok 3 - Sprawdź dokładność wyodrębnienia i utwórz pole niestandardowe
Po wykonaniu początkowego wyodrębnienia usługa Log Analytics wyświetli jego wyniki na podstawie danych, które zostały już zebrane.  Jeśli wyniki wyglądają dokładnie, można utworzyć pole niestandardowe bez dalszej pracy.  Jeśli nie, następnie można zawęzić wyniki, dzięki czemu usługa Log Analytics może poprawić jego logikę.

1. Jeśli jakieś wartości w początkowym wyciągu nie są poprawne, kliknij ikonę **Edytuj** obok niedokładnego rekordu i wybierz pozycję **Zmodyfikuj to wyróżnienie,** aby zmodyfikować zaznaczenie.
2. Wpis jest kopiowany do sekcji **Dodatkowe przykłady** poniżej **przykładu głównego**.  Wyróżnienie można dostosować tutaj, aby ułatwić usłudze Log Analytics zrozumienie wyboru, którego powinien dokonać.
3. Kliknij **przycisk Wyodrębnij,** aby użyć tych nowych informacji do oceny wszystkich istniejących rekordów.  Wyniki mogą być modyfikowane dla rekordów innych niż ten, który właśnie został zmodyfikowany na podstawie tej nowej inteligencji.
4. Kontynuuj dodawanie poprawek, aż wszystkie rekordy w ekstrakcie poprawnie zidentyfikują dane, aby wypełnić nowe pole niestandardowe.
5. Kliknij **przycisk Zapisz wyodrębnić,** gdy wyniki są zadowalające.  Pole niestandardowe jest teraz zdefiniowane, ale nie zostanie jeszcze dodane do żadnych rekordów.
6. Poczekaj na nowe rekordy pasujące do określonych kryteriów do zebrania, a następnie uruchom wyszukiwanie dziennika ponownie. Nowe rekordy powinny mieć pole niestandardowe.
7. Użyj pola niestandardowego, jak każdą inną właściwość rekordu.  Można go używać do agregowania i grupowania danych, a nawet do tworzenia nowych szczegółowych informacji.

## <a name="viewing-custom-fields"></a>Wyświetlanie pól niestandardowych
Listę wszystkich pól niestandardowych w grupie zarządzania można wyświetlić w menu **Ustawienia zaawansowane** obszaru roboczego usługi Log Analytics w witrynie Azure portal.  Wybierz **pozycję Dane,** a następnie **pola niestandardowe** dla listy wszystkich pól niestandardowych w obszarze roboczym.  

![Niestandardowe pola](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Usuwanie pola niestandardowego
Istnieją dwa sposoby usuwania pola niestandardowego.  Pierwsza to opcja **Usuń** dla każdego pola podczas wyświetlania pełnej listy, jak opisano powyżej.  Inną metodą jest pobranie rekordu i kliknięcie przycisku po lewej stronie pola.  Menu będzie miało możliwość usunięcia pola niestandardowego.

## <a name="sample-walkthrough"></a>Przewodnik po przykładzie
W poniższej sekcji przedstawiono pełny przykład tworzenia pola niestandardowego.  W tym przykładzie wyodrębnia nazwę usługi w zdarzeniach systemu Windows, które wskazują na zmianę stanu usługi.  Opiera się to na zdarzeniach utworzonych przez Menedżera sterowania usługami podczas uruchamiania systemu na komputerach z systemem Windows.  Jeśli chcesz wykonać ten przykład, musisz [zbierać zdarzenia informacji dla dziennika systemu](data-sources-windows-events.md).

Wprowadź następującą kwerendę, aby zwrócić wszystkie zdarzenia z Menedżera sterowania usługami, które mają identyfikator zdarzenia 7036, który jest zdarzeniem wskazującym uruchamianie lub zatrzymywanie usługi.

![Zapytanie](media/custom-fields/query.png)

Następnie wybieramy i rozwijamy dowolny rekord o identyfikatorze zdarzenia 7036.

![Rekord źródłowy](media/custom-fields/source-record.png)

Definiujemy pola niestandardowe, klikając elipsę obok górnej właściwości.

![Wyodrębnianie pól](media/custom-fields/extract-fields.png)

Zostanie otwarty **Kreator wyodrębniania pól,** a pola **EventLog** i **EventID** zostaną wybrane w kolumnie **Przykład główny.**  Oznacza to, że pole niestandardowe zostanie zdefiniowane dla zdarzeń z dziennika systemu o identyfikatorze zdarzenia 7036.  Jest to wystarczające, więc nie musimy wybierać żadnych innych pól.

![Główny przykład](media/custom-fields/main-example.png)

Wyróżniamy nazwę usługi we właściwisce **RenderedDescription** i używamy **usługi** do identyfikowania nazwy usługi.  Pole niestandardowe będzie nazywane **Service_CF**. Typ pola w tym przypadku jest ciągiem, więc możemy pozostawić to bez zmian.

![Tytuł pola](media/custom-fields/field-title.png)

Widzimy, że nazwa usługi jest prawidłowo identyfikowana dla niektórych rekordów, ale nie dla innych.   **Wyniki wyszukiwania** pokazują, że część nazwy karty **wydajności WMI** nie została wybrana.  **Podsumowanie** pokazuje, że jeden rekord zidentyfikowane **Moduły Instalatora** zamiast **Instalatora modułów systemu Windows**.  

![Wyniki wyszukiwania](media/custom-fields/search-results-01.png)

Zaczynamy od rekordu **karty wydajności WMI.**  Klikamy jego ikonę edycji, a następnie **modyfikujemy to wyróżnienie**.  

![Modyfikowanie podświetlenia](media/custom-fields/modify-highlight.png)

Zwiększamy wyróżnienie, aby uwzględnić słowo **WMI,** a następnie ponownie uruchomić ekstrakt.  

![Dodatkowy przykład](media/custom-fields/additional-example-01.png)

Widzimy, że wpisy **karty wydajności WMI** zostały poprawione, a usługa Log Analytics użyła tych informacji do poprawienia rekordów **instalatora modułu systemu Windows**.

![Wyniki wyszukiwania](media/custom-fields/search-results-02.png)

Możemy teraz uruchomić kwerendę, która **sprawdza, Service_CF** jest tworzony, ale nie jest jeszcze dodawany do żadnych rekordów. Dzieje się tak dlatego, że pole niestandardowe nie działa w stosunku do istniejących rekordów, więc musimy poczekać na pobranie nowych rekordów.

![Początkowa liczba](media/custom-fields/initial-count.png)

Po pewnym czasie minęło tak nowe wydarzenia są zbierane, widzimy, że **pole Service_CF** jest teraz dodawane do rekordów, które odpowiadają naszym kryteriom.

![Wyniki końcowe](media/custom-fields/final-results.png)

Możemy teraz użyć pola niestandardowego, jak każdą inną właściwość rekordu.  Aby to zilustrować, tworzymy kwerendę, która grupuje według nowego pola **Service_CF,** aby sprawdzić, które usługi są najbardziej aktywne.

![Grupowanie według kwerendy](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [kwerendach dziennika](../log-query/log-query-overview.md) do tworzenia kwerend przy użyciu pól niestandardowych dla kryteriów.
* [Monitoruj niestandardowe pliki dziennika,](data-sources-custom-logs.md) które analizujesz przy użyciu pól niestandardowych.

