---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183682"
---
Można również wyświetlić metryki, aby monitorować wydajność urządzenia, a w niektórych przypadkach rozwiązywania problemów z urządzeniem.

Aby utworzyć wykres dla wybranych metryk urządzenia, należy wykonać następujące kroki w witrynie Azure Portal.

1. Aby uzyskać zasób w witrynie Azure portal, przejdź do **monitorowania > metryki** i wybierz **pozycję Dodaj metrykę**.

    ![Dodawanie metryki](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Zasób jest wypełniany automatycznie.  

    ![Bieżący zasób](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Aby określić inny zasób, wybierz zasób. W obszarze Wybierz blok zasobu wybierz **subskrypcję,** grupę zasobów, typ zasobu i określony zasób, dla którego chcesz wyświetlić metryki, i wybierz pozycję **Zastosuj**.

    ![Wybieranie innego zasobu](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. Z listy rozwijanej wybierz metrykę, aby monitorować urządzenie. Metryki mogą być **metryki pojemności** lub **metryki transakcji**. Metryki pojemności są związane z pojemnością urządzenia. Metryki transakcji są powiązane z operacjami odczytu i zapisu w usłudze Azure Storage.

    |Metryki pojemności                     |Opis  |
    |-------------------------------------|-------------|
    |**Dostępna pojemność**               | Odnosi się do rozmiaru danych, które mogą być zapisywane na urządzeniu. Innymi słowy, jest to pojemność, która może być udostępniona na urządzeniu. <br></br>Możesz zwolnić pojemność urządzenia, usuwając lokalną kopię plików, które mają kopię zarówno na urządzeniu, jak i w chmurze.        |
    |**Całkowita pojemność**                   | Odnosi się do całkowitej liczby bajtów na urządzeniu, na które mają być zapisywane dane. Jest to również określane jako całkowity rozmiar lokalnej pamięci podręcznej. <br></br> Teraz można zwiększyć pojemność istniejącego urządzenia wirtualnego, dodając dysk danych. Dodaj dysk danych za pośrednictwem zarządzania hipernadzorcami dla maszyny Wirtualnej, a następnie uruchom ponownie maszynę wirtualną. Lokalna pula magazynu urządzenia bramy zostanie rozszerzona, aby pomieścić nowo dodany dysk danych. <br></br>Aby uzyskać więcej informacji, przejdź do [tematu Dodawanie dysku twardego dla maszyny wirtualnej funkcji Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Metryki transakcji              | Opis         |
    |-------------------------------------|---------|
    |**Przesłane bajty w chmurze (urządzenie)**    | Suma wszystkich bajtów przesłanych we wszystkich udziałach na urządzeniu        |
    |**Bajty w chmurze przesłane (udostępnianie)**     | Bajty przesłane na jedną akcję. Może to być: <br></br> Średnia, czyli (Suma wszystkich bajtów przesłanych na jedną akcję / Liczba udziałów),  <br></br>Maks. <br></br>Min, czyli minimalna liczba bajtów przesłanych z udziału      |
    |**Przepływność pobierania w chmurze (udostępnianie)**| Bajty pobrane na jedną akcję. Może to być: <br></br> Avg, czyli (Suma wszystkich bajtów przeczytanych lub pobranych na akcję / Liczba udziałów) <br></br> Max, czyli maksymalna liczba bajtów pobranych z udziału<br></br> i Min, co jest minimalną liczbą bajtów pobranych z  |
    |**Przepływność odczytu w chmurze**            | Suma wszystkich bajtów odczytanych z chmury we wszystkich udziałach na urządzeniu     |
    |**Przepływność przekazywania w chmurze**          | Suma wszystkich bajtów zapisanych w chmurze we wszystkich udziałach na urządzeniu     |
    |**Przepływność przekazywania w chmurze (udostępnianie)**  | Suma wszystkich bajtów zapisanych w chmurze z akcji/# udziałów to średnia, max i min na akcję      |
    |**Przepływność odczytu (sieć)**           | Zawiera przepustowość sieci systemowej dla wszystkich bajtów odczytanych z chmury. Ten widok może zawierać dane, które nie są ograniczone do udziałów. <br></br>Podział spowoduje wyświetlenie ruchu na wszystkich kartach sieciowych na urządzeniu. Obejmuje to karty, które nie są podłączone lub włączone.      |
    |**Przepływność zapisu (sieć)**       | Zawiera przepustowość sieci systemowej dla wszystkich bajtów zapisanych w chmurze. Ten widok może zawierać dane, które nie są ograniczone do udziałów. <br></br>Podział spowoduje wyświetlenie ruchu na wszystkich kartach sieciowych na urządzeniu. Obejmuje to karty, które nie są podłączone lub włączone.          |
    |**Obliczanie krawędzi — użycie pamięci**      | Ta metryka nie ma zastosowania do bramy pola danych i dlatego nie jest wypełniona.          |
    |**Obliczeń krawędziowych — procent procesora CPU**    | Ta metryka nie ma zastosowania do bramy pola danych i dlatego nie jest wypełniona.         |

4. Gdy metryka jest zaznaczona z listy rozwijanej, można również zdefiniować agregację. Agregacja odnosi się do rzeczywistej wartości zagregowane w określonym przedziale czasu. Zagregowane wartości mogą być wartością średnią, minimalną lub maksymalną. Wybierz agregację ze średnika, maks.

    ![Wyświetl wykres](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Jeśli wybrana metryka ma wiele wystąpień, opcja podziału jest dostępna. Wybierz **pozycję Zastosuj dzielenie,** a następnie wybierz wartość, według której chcesz wyświetlić podział.

    ![Stosowanie podziału](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Jeśli chcesz teraz zobaczyć podział tylko dla kilku wystąpień, możesz filtrować dane. Na przykład w takim przypadku, jeśli chcesz zobaczyć przepływność sieci tylko dla dwóch połączonych interfejsów sieciowych na urządzeniu, można filtrować te interfejsy. Wybierz **dodaj filtr** i określ nazwę interfejsu sieciowego do filtrowania.

    ![Dodaj filtr](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Można również przypiąć wykres do pulpitu nawigacyjnego, aby mieć do niego łatwy dostęp.

    ![Przypnij do pulpitu nawigacyjnego](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Aby wyeksportować dane wykresu do arkusza kalkulacyjnego programu Excel lub uzyskać łącze do wykresu, który można udostępnić, wybierz opcję udziału z paska poleceń.

    ![Eksportowanie danych](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)