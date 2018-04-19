---
title: "Szczegółowy przewodnik na temat korzystania z usługi Azure Machine Learning danych przygotowania | Dokumentacja firmy Microsoft"
description: "Ten dokument zawiera omówienie i szczegółowe informacje na temat rozwiązywania problemów danych z usługi Azure Machine Learning danych przygotowania"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 9fc0ae8929f12447123321a5c64ac89309c68dca
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="data-preparations-user-guide"></a>Podręcznik użytkownika przygotowań danych 
Środowisko usługi Azure Machine Learning danych przygotowania zawiera wiele zaawansowanych funkcji. W tym artykule omówiono najgłębszym części środowiska.

### <a name="step-execution-history-and-caching"></a>Wykonanie kroku, Historia i buforowanie 
Historia krok przygotowania danych obsługuje szereg pamięci podręcznej ze względu na wydajność. Zaznacz krok trafienia w pamięci podręcznej, nie ponownego wykonania. Jeśli masz zapis bloku na końcu historii kroku i przerzucanie i z powrotem na czynności, ale nie wprowadzisz zmian, zapis nie jest wyzwalane po raz pierwszy. Nowe zapisu występuje i spowoduje zastąpienie poprzedniej wersji, jeśli użytkownik:

- Zmiany zapis bloku.
- Dodaj nowy blok transformacji i przeniesienie go powyżej bloku zapisu, które generuje unieważniania pamięci podręcznej.
- Zmień właściwości bloku powyżej bloku zapisu, które generuje unieważniania pamięci podręcznej.
- Wybierz opcję odświeżania na podstawie próbki (w związku z tym unieważnienie wszystkich usług pamięć podręczna).

### <a name="error-values"></a>Wartości błędów

Przekształcenia danych może zakończyć się niepowodzeniem dla wartości wejściowej, ponieważ tej wartości nie mogą być obsługiwane odpowiednio. Na przykład w przypadku operacji koercja typu, koercja kończy się niepowodzeniem, jeśli wartość ciągu wejściowego nie można rzutować na typ docelowy określony. Operacja koercja typu może Konwertowanie kolumny typu string do typu liczbowego lub typu Boolean lub próby zduplikowane kolumny, która nie istnieje. (Ten błąd występuje w wyniku przenoszenia *usunąć kolumnę X* przed *zduplikowaną kolumnę X* operacji.)

W takich przypadkach przygotowań danych tworzy wartość błędu jako dane wyjściowe. Wartości błędów wskazują, że poprzednia operacja nie powiodła się dla podanej wartości. Wewnętrznie traktowane jako typ wartości pierwszej klasy, ale ich obecność nie powoduje zmian podstawowy typ kolumny, nawet jeśli kolumna zawiera tylko wartości błędów.

Wartości błędów są łatwo zidentyfikować. Są wyróżnione kolorem czerwonym i do odczytu "Error". Aby ustalić przyczynę błędu, umieść kursor nad wartość błędu, aby wyświetlić opis błędu.

Propagacja wartości błędów. Po wystąpieniu błędu pojawia się, propaguje w większości przypadków, jako błąd przez większość operacji. Istnieją trzy sposoby Zamień lub usuń je:

* Replace
    -  Kliknij prawym przyciskiem myszy kolumnę, a następnie wybierz **Zastąp wartości błędów**. Można wybrać wartość zastąpienia dla każdej wartości błędów w kolumnie.

* Remove
    - Przygotowań danych obejmuje filtry interakcyjne, aby zachować lub usunąć wartości błędów.
    - Kliknij prawym przyciskiem myszy kolumnę, a następnie wybierz **kolumny filtru**. Aby zachować lub usunąć błąd wartości, należy utworzyć warunkowo z warunkiem *"jest błąd"* lub *"nie jest błąd."*

* Użycie wyrażenia języka Python do działania warunkowo na wartości błędów. Aby uzyskać więcej informacji, zobacz [sekcję na temat rozszerzeń języka Python](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>Próbkowanie
Plik źródła danych przyjmuje nieprzetworzone dane z jednego lub więcej źródeł, z lokalnego systemu plików lub lokalizacji zdalnej. Blok próbki pozwala na Określ, czy do pracy z podzbiór danych przez generowanie próbek. Działający na przykładowych danych, a nie dużego zestawu danych często prowadzi do poprawy wydajności podczas przeprowadzania operacji w kolejnych krokach.

Dla każdego źródła danych pliku wielu próbkach umożliwia generowanie i przechowywane. Jednak tylko jeden przykład można ustawić jako aktywnej próbce. Można utworzyć, edytować lub usunąć próbek w Kreatorze źródła danych lub edytując bloku próbki. Wszystkie pliki przygotowania danych, które odwołują się do źródła danych z założenia użyć przykładowego określone w pliku źródła danych.

Istnieją różne dostępne strategie, każdy z innymi parametrami można konfigurować.

#### <a name="top"></a>Najważniejsze
Ta strategia może odnosić się do plików lokalnych lub zdalnych. Trwa pierwsze N wierszy (określonego przez liczbę) do źródła danych.

#### <a name="random-n"></a>Losowe N 
Ta strategia może odnosić się tylko do plików lokalnych. Trwa losowe N wierszy (określonego przez liczbę) do źródła danych. Możesz podać określonych inicjatora, aby upewnić się, że zostanie wygenerowana próbka tego samego, pod warunkiem, że liczba również jest taka sama.

#### <a name="random-"></a>Losowe % 
Ta strategia może odnosić się do plików lokalnych lub zdalnych. W obu przypadkach prawdopodobieństwa i inicjator musi być podany, podobny do strategii N losowych.

Przykłady pliki zdalne, należy podać dodatkowe parametry:

- Generator próbki 
  - Wybierz klaster Spark lub Docker zdalnego obliczeniowe docelowy służący do generowania próbki. Element docelowy obliczeniowe musi zostać utworzony dla projektu wcześniej na jego ma na liście. Wykonaj kroki opisane w sekcji "Tworzenie nowego docelowego obliczeń" w [sposób użycia procesora GPU w usłudze Azure Machine Learning](how-to-use-gpu.md) do tworzenia celów obliczeń.
- Przykładowe magazynu 
  - Podaj lokalizację magazynu pośredniego do przechowywania przykładowych zdalnego. Ta ścieżka musi być inny katalog z lokalizacji pliku wejściowego.

#### <a name="full-file"></a>Całego pliku 
Ta strategia może odnosić się tylko do plików lokalnych, uwzględniając całego pliku źródła danych. Jeśli plik jest zbyt duży, ta opcja może spowolnić przyszłych operacji w aplikacji. Może być bardziej odpowiednie do użycia w strategii różnych próbkowania.


### <a name="fork-merge-and-append"></a>Rozwidlania, scalania i dołączania

Po zastosowaniu filtru za pośrednictwem zestawu danych, operacja dzieli dane na dwa zestawy wyników: rekordów, które pomyślnie w filtrze reprezentuje jeden zestaw i jest inny zestaw rekordów, które się nie powieść. W obu przypadkach użytkownik może wybrać zestaw które wyników do wyświetlenia. Użytkownik może odrzucić zestawu danych lub umieścić go w nowej przepływu danych. Tę druga opcję nazywa się rozwidlania.

Do rozwidlania: 
1. Wybierz kolumny, kliknij prawym przyciskiem myszy i wybierz **filtru** kolumny.

2. W obszarze **I mają do**, wybierz pozycję **Zachowaj wiersze** do wyświetlania zestawu wyników, który przekazuje filtru.

3. Wybierz **usuwanie wierszy** do wyświetlania zestawu nie powiodło się.

4. Po **warunki**, wybierz pozycję **Utwórz przepływu danych zawierająca filtrowane limit wiersze** do rozwidlania wyświetlania z systemem innym niż zestaw wyników na nowy przepływ danych.


Takie rozwiązanie jest często używane wyodrębnienie zestawu danych, która wymaga dodatkowych przygotowań. Po przygotowaniu rozwidlonych zestawu danych jest często scalania danych z zestawu wyników w oryginalnym przepływu danych. Do scalania (wstecznego operacji rozwidlenia), użyj jednej z następujących czynności:

- **Dołącz wiersze**. Scal dwa lub więcej przepływów danych w pionie (row-wise). 
- **Dołącz kolumny**. Scal poziomie co najmniej dwóch przepływów danych (column-wise).


>[!NOTE]
>Dołącz kolumny kończy się niepowodzeniem, jeśli występuje kolizja kolumny.


Po zakończeniu operacji scalania co najmniej jeden przepływów danych odwołuje się przepływ źródła danych. Dane przygotowania powiadamia powiadomienia w prawym dolnym rogu aplikacji, pod listą czynności.


Wszelkie operacje na przepływ danych występujące w odwołaniu wymaga przepływu danych nadrzędnej odświeżyć przykładowych używane z przepływu danych występujące w odwołaniu. W takim przypadku okno dialogowe potwierdzenia zastępuje powiadomienia odwołanie przepływu danych w prawym dolnym rogu. Tego okna dialogowego potwierdza, czy należy odświeżyć przepływ danych do synchronizacji z zmiany żadnych zależności przepływów danych.

### <a name="list-of-appendices"></a>Lista dodatki 
* [Obsługiwane źródła danych](data-prep-appendix2-supported-data-sources.md)  
* [Obsługiwane transformacji](data-prep-appendix3-supported-transforms.md)  
* [Inspektorzy obsługiwane](data-prep-appendix4-supported-inspectors.md)  
* [Obsługiwane miejsc docelowych](data-prep-appendix5-supported-destinations.md)  
* [Przykładowe wyrażenia filtru w języku Python](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Przykładowe przekształcania danych przepływu wyrażenia w języku Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Źródła danych przykładowych w języku Python](data-prep-appendix8-sample-source-connections-python.md)  
* [Przykładowe docelowy połączenia w języku Python](data-prep-appendix9-sample-destination-connections-python.md)  
* [Przekształca próbki kolumny w języku Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  
