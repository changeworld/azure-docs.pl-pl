---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183682"
---
Można również wyświetlić metryki do monitorowania wydajności urządzenia i w niektórych przypadkach, aby informacje o rozwiązywaniu problemów z urządzeniami.

Wykonaj następujące kroki w witrynie Azure portal, aby utworzyć wykres metryk wybranego urządzenia.

1. Dla zasobu w witrynie Azure portal, przejdź do **monitorowanie > metryki** i wybierz **Dodaj metrykę**.

    ![Dodawanie metryki](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Zasób jest wypełniane automatycznie.  

    ![Bieżący zasób](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Aby określić inny zasób, wybierz zasób. Na **wybierz zasób** bloku, wybierz subskrypcję, grupy zasobów, typ zasobu i określonego zasobu, dla którego chcesz wyświetlić metryki, a następnie wybierz pozycję **Zastosuj**.

    ![Wybierz inny zasób](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. Z listy rozwijanej wybierz metrykę, aby monitorować urządzenie. Metryki może być **metryki pojemności** lub **metryk transakcji**. Pojemność urządzenia są powiązane metryki pojemności. Metryki transakcji odnoszą się do odczytu i zapisu do usługi Azure Storage.

    |Metryki wydajności                     |Opis  |
    |-------------------------------------|-------------|
    |**Dostępna pojemność**               | Odnosi się do rozmiaru danych, które mogą być zapisywane na urządzeniu. Innymi słowy to wydajności, które mogą być udostępniane na urządzeniu. <br></br>Możesz zwolnić pojemność urządzenia przez usunięcie lokalną kopię plików, które mają kopii na zarówno urządzenie, jak i chmury.        |
    |**Całkowita pojemność**                   | Odnosi się do całkowita liczba bajtów na urządzeniu, aby zapisywać danych. To również jest określane jako całkowity rozmiar lokalnej pamięci podręcznej. <br></br> Teraz można zwiększyć pojemność istniejącego urządzenia wirtualnego, Dodawanie dysku danych. Dodaj dysk danych za pośrednictwem zarządzania funkcji hypervisor dla maszyny Wirtualnej, a następnie uruchom ponownie maszynę Wirtualną. Pula magazynu lokalnego urządzenia bramy rozszerzy się do uwzględnienia nowo dodane dane dysku. <br></br>Aby uzyskać więcej informacji, przejdź do [Dodaj dysk twardy dla maszyny wirtualnej funkcji Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Metryki transakcji              | Opis         |
    |-------------------------------------|---------|
    |**Chmury przekazanych bajtów (urządzenia)**    | Sumie wszystkich bajtów przypadająca na wszystkie udziały na urządzeniu z systemem        |
    |**Przekazane bajty chmurze (udział)**     | Bajty są przekazywane na jedną akcję. Może to być: <br></br> Średnia liczba, która jest (sumie wszystkich bajtów przekazany na jedną akcję / numer udziałów),  <br></br>Maksymalna liczba, która jest maksymalną liczbę bajtów przekazany z udziału <br></br>Wartość minimalna, czyli minimalną liczbę bajtów przekazany z udziału      |
    |**Przepływność pobierania chmurze (udział)**| Bajty pobrane na jedną akcję. Może to być: <br></br> Średnia liczba, która jest (sumie wszystkich bajtów odczytu lub pobrany do udziału / numer akcji) <br></br> Maksymalna liczba, która jest maksymalną liczbę bajtów pobrane z udziału<br></br> a wartość minimalna, czyli minimalną liczbę bajtów pobrane z udziału  |
    |**Przepływność zapisu w chmurze**            | Sumie wszystkich bajtów odczytu z chmury dla wszystkich udziałów na urządzeniu z systemem     |
    |**Przepustowość przekazywania w chmurze**          | Sumie wszystkich bajtów, które są zapisywane do chmury we wszystkich akcji na urządzeniu z systemem     |
    |**Przepustowość przekazywania w chmurze (udział)**  | Sumie wszystkich bajtów zapisywane w chmurze z udziału / # akcji jest średnią, maksimum i minimum na udział      |
    |**Przepływność odczytu (sieć)**           | Zawiera przepływność sieci systemu dla wszystkich bajtów odczytanych z chmury. W tym widoku mogą obejmować dane, które nie są ograniczone do udziałów. <br></br>Dzielenie pokaże ruch za pośrednictwem wszystkich kart sieciowych na urządzeniu. W tym kart, które nie są połączone lub włączone.      |
    |**Zapis przepływności (sieć)**       | Zawiera przepływność sieci systemu dla wszystkich bajtów, które są zapisywane w chmurze. W tym widoku mogą obejmować dane, które nie są ograniczone do udziałów. <br></br>Dzielenie pokaże ruch za pośrednictwem wszystkich kart sieciowych na urządzeniu. W tym kart, które nie są połączone lub włączone.          |
    |**Obliczeń brzegowych — użycie pamięci**      | Ta metryka nie jest odpowiednie dla bramy pola danych i dlatego nie jest wypełnione.          |
    |**Krawędź compute - procentowe użycie procesora CPU**    | Ta metryka nie jest odpowiednie dla bramy pola danych i dlatego nie jest wypełnione.         |

4. Gdy jest wybrana Metryka z listy rozwijanej, można także definiować agregacji. Agregacja odnosi się do rzeczywistej wartości agregowane przez określony przedział czasu. Zagregowane wartości może być średnią, minimalną lub maksymalną wartość. Wybierz agregację Avg, minimalnej lub maksymalnej.

    ![Widok wykresu](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Jeśli wybrane metryki ma wiele wystąpień, podziału opcja jest dostępna. Wybierz **zastosować podział** a następnie wybierz wartość, według której chcesz zobaczyć podział.

    ![Zastosuj rozdzielenie](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Jeśli teraz chcesz zobaczyć podział tylko w przypadku kilku wystąpień, można filtrować dane. Na przykład w tym przypadku, jeśli chcesz zobaczyć przepływność sieci tylko w przypadku dwóch interfejsów sieciowych podłączonych na twoim urządzeniu, można filtrować te interfejsy. Wybierz **Dodaj filtr** i określ nazwę interfejsu sieciowego w celu filtrowania.

    ![Dodaj filtr](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Można również przypiąć wykres do pulpitu nawigacyjnego, aby mieć łatwy dostęp.

    ![Przypnij do pulpitu nawigacyjnego](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Eksportuj dane wykresu do arkusza kalkulacyjnego programu Excel lub Utwórz link pozwalający wykres, który można udostępniać, wybierz opcję udostępniania na pasku poleceń.

    ![Eksportowanie danych](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)