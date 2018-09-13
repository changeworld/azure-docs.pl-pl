---
title: Szczegółowy przewodnik dotyczący sposobu użycia usługi Azure Machine Learning danych przygotowań | Dokumentacja firmy Microsoft
description: Ten dokument zawiera omówienie i szczegółowe informacje o tym, jak rozwiązać problemy związane z danymi za pomocą usługi Azure Machine Learning danych przygotowań
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: e4db8d70d2a96fe3ad24797d7a32d75e1a792e32
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647560"
---
# <a name="data-preparations-user-guide"></a>Podręcznik użytkownika przygotowywanie danych 
Środowisko Azure Machine Learning danych przygotowań udostępnia wiele funkcji zaawansowanych. W tym artykule opisano najgłębiej zagnieżdżoną części środowiska.

### <a name="step-execution-history-and-caching"></a>Wykonanie kroku, Historia i buforowanie 
Historii kroku czynności przygotowawcze danych obsługuje szereg pamięci podręcznych ze względu na wydajność. Jeśli zaznacz krok i trafienia pamięci podręcznej, więc nie wykonuje ponownie. Jeśli masz zapis bloku na końcu historii kroku i przerzucanie i z powrotem na temat kroków, ale nie wprowadzaj żadnych zmian, zapisu nie zostanie wyzwolony po raz pierwszy. Nowe zapisu występuje i zastępuje poprzednią wersję, jeśli użytkownik:

- Wprowadź zmiany w bloku zapisu.
- Dodaj nowy blok przekształcenie, a następnie przenieś powyżej bloku zapisu, która powoduje wygenerowanie unieważniania pamięci podręcznej.
- Zmień właściwości bloku powyżej bloku zapisu, która powoduje wygenerowanie unieważniania pamięci podręcznej.
- Wybierz opcję odświeżania, na przykład (tym samym unieważniając wszystkich pamięci podręcznych).

### <a name="error-values"></a>Wartości błędów

Przekształcenia danych może zakończyć się niepowodzeniem dla wartości wejściowej, ponieważ ta wartość nie może być odpowiednio obsługiwane. Na przykład w przypadku operacji wymuszenia typu, wymuszanie nie działa, jeśli wartość ciągu wejściowym nie można rzutować na typ określonego obiektu docelowego. Operacja wymuszenia typu może Konwertowanie kolumny typu string na typ liczbowa lub Boolean lub próby zduplikowania kolumn, który nie istnieje. (Ten błąd występuje w wyniku przenoszenie *Usuń kolumnę X* operację przed *zduplikowaną kolumnę X* operacji.)

W takich przypadkach przygotowywanie danych tworzy wartość błędu jako dane wyjściowe. Wartości błędów wskazują, że poprzednia operacja nie powiodła się dla danej wartości. Wewnętrznie są one traktowane jako typ wartości pierwszej klasy, ale ich obecność nie powoduje zmian podstawowy typ kolumny, nawet jeśli kolumna zawiera tylko wartości błędów.

Wartości błędów można łatwo zidentyfikować. Ich wyróżniony czerwonym kolorem i Odczyt "Błąd". Aby ustalić przyczynę tego błędu, umieść kursor nad wartością błędu, aby wyświetlić opis błędu.

Propagacja wartości błędów. Po wystąpieniu błędu pojawia się, propaguje w większości przypadków jako błąd przez większość operacji. Istnieją trzy sposoby Zamień lub usuń je:

* Replace
    -  Kliknij kolumnę prawym przyciskiem myszy, a następnie wybierz pozycję **Zastąp wartości błędów**. Następnie możesz wybrać wartość zastąpienia dla każdej wartości błędu, w kolumnie.

* Remove
    - Przygotowywanie danych zawiera filtry interakcyjne, aby zachować lub usunąć wartości błędów.
    - Kliknij kolumnę prawym przyciskiem myszy, a następnie wybierz pozycję **Filtruj kolumnę**. Aby zachować lub usunąć wartości błędów, należy utworzyć warunkowe z warunkiem *"jest błąd"* lub *"nie jest błąd."*

* Aby warunkowo działają na wartości błędów, należy użyć wyrażenia języka Python. Aby uzyskać więcej informacji, zobacz [sekcji rozszerzeń Python](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>Próbkowanie
Plik źródła danych pobiera nieprzetworzone dane z jednego lub kilku źródeł, z lokalnego systemu plików lub lokalizacji zdalnej. Blok próbki pozwala określić, czy do pracy z podzbiorem danych, generując przykładów. Wykonywanie operacji na przykładowych danych, a nie duży zestaw danych często prowadzi do zapewnienia lepszej wydajności, podczas przeprowadzania operacji w kolejnych krokach.

Dla każdego źródła danych pliku wielu próbkach umożliwia generowanie i przechowywane. Jednak tylko jeden przykład można ustawić jako aktywne próbki. Można tworzyć, edytować lub usunąć przykładów w Kreatorze źródła danych lub przez edycję bloku próbki. Wszystkie pliki przygotowywanie danych, które standardowo odwoływać się do źródła danych skorzystaj z przykładu, określone w pliku źródła danych.

Istnieje kilka strategii próbkowania jest dostępna, każdego z różnymi parametrami, które można konfigurować.

#### <a name="top"></a>Góra
Ta strategia można zastosować do plików lokalnych lub zdalnych. Trwa N początkowych wierszy (określone przez liczbę) do źródła danych.

#### <a name="random-n"></a>Losowe N 
Ta strategia można stosować tylko do plików lokalnych. Trwa losowe N wierszy (określone przez liczbę) do źródła danych. Możesz podać określone inicjatora, aby zapewnić, że tej samej próbki jest generowany, pod warunkiem, że liczba również jest taki sam.

#### <a name="random-"></a>% Losowe 
Ta strategia można zastosować do plików lokalnych lub zdalnych. W obu przypadkach prawdopodobieństwa i inicjator musi być podany, podobny do strategii N losowych.

Przykłady pliki zdalne należy podać dodatkowe parametry:

- Generator próbki 
  - Wybierz klaster Spark lub zdalnego Docker obliczeniowego elementu docelowego służący do generowania próbki. Obliczeniowego elementu docelowego musi zostać utworzony dla projektu wcześniej będzie wyświetlana na tej liście. Postępuj zgodnie z instrukcjami w sekcji "Tworzenie nowemu celowi obliczenia" w [sposób użycia procesora GPU w usłudze Azure Machine Learning](how-to-use-gpu.md) utworzyć obliczeniowych elementów docelowych.
- Przykład magazynu 
  - Podaj lokalizację pośredni magazyn do przechowywania przykładowych zdalnego. Ta ścieżka musi być inny katalog z lokalizacji pliku wejściowego.

#### <a name="full-file"></a>Cały plik 
Ta strategia mogą dotyczyć tylko plików lokalnych i uwzględnieniu cały plik źródła danych. Jeśli plik jest zbyt duży, ta opcja może spowolnić przyszłych działań w aplikacji. Może okazać się bardziej odpowiednie użycie różnych próbek.


### <a name="fork-merge-and-append"></a>Utworzenie rozwidlenia, scalania i dołączania

Jeśli zastosujesz filtr w zestawie danych operacji dzieli dane na dwa zestawy wyników: jeden zestaw reprezentuje rekordy, które pomyślnie w filtrze, a inny zestaw dotyczy rekordy, które się nie powieść. W obu przypadkach użytkownik może wybrać zestaw które wyników do wyświetlenia. Użytkownik może odrzucić zestaw danych lub umieść go w nowego przepływu danych. Tę druga opcję nazywa się rozwidlenia.

Aby utworzyć rozwidlenie: 
1. Wybierz kolumnę, kliknij prawym przyciskiem myszy i wybierz **filtru** kolumny.

2. W obszarze **chcę**, wybierz opcję **Zachowaj wiersze** do wyświetlania zestawu wyników, który przekazuje filtru.

3. Wybierz **Usuń wiersze** do wyświetlania zestawu nie powiodło się.

4. Po **warunki**, wybierz opcję **Tworzenie przepływu danych, zawierająca filtrowane się wiersze** rozwidlić bez wyświetlania zestawu wyników do nowego przepływu danych.


To rozwiązanie jest często używane do oddzielenia zestaw danych, które wymagają dodatkowych przygotowań. Po przygotowaniu rozwidlone zestawu danych jest często scalania danych z zestawu wyników w oryginalnym przepływu danych. Do scalania (odwrotnej kolejności operacji rozwidlenia), użyj jednej z następujących czynności:

- **Dołącz wiersze**. Scal co najmniej dwóch przepływów danych w pionie (row-wise). 
- **Dołącz kolumny**. Scal co najmniej dwóch przepływów danych w poziomie (column-wise).


>[!NOTE]
>Dołącz kolumny kończy się niepowodzeniem, jeśli wystąpi kolizji kolumny.


Po zakończeniu operacji scalania co najmniej jeden przepływ danych odwołują się przepływ danych źródłowych. Przygotowywanie danych powiadamia powiadomienia w prawym dolnym rogu aplikacji, pod listą kroków.


Żadnych operacji na przepływ danych występujące w odwołaniu wymaga przepływu danych nadrzędnej odświeżyć przykładowy używany z przepływem danych występujące w odwołaniu. W takim przypadku okna dialogowego potwierdzenia zastępuje powiadomienia odwołanie przepływu danych w prawym dolnym rogu. To okno dialogowe potwierdza, trzeba odświeżyć przepływu danych do synchronizacji z zmiany do wszystkich przepływów danych zależności.

### <a name="list-of-appendices"></a>Lista załączników 
* [Obsługiwane źródła danych](data-prep-appendix2-supported-data-sources.md)  
* [Obsługiwane przekształcenia](data-prep-appendix3-supported-transforms.md)  
* [Obsługiwani inspektorzy](data-prep-appendix4-supported-inspectors.md)  
* [Obsługiwane miejsca docelowe](data-prep-appendix5-supported-destinations.md)  
* [Przykładowe wyrażenia filtru w języku Python](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Wyrażenia przepływu danych Przekształć przykładowy w języku Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Przykładowe źródła danych w języku Python](data-prep-appendix8-sample-source-connections-python.md)  
* [Przykładowe połączenia miejsc docelowych w języku Python](data-prep-appendix9-sample-destination-connections-python.md)  
* [Przykładowe kolumny przekształca się w języku Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  
