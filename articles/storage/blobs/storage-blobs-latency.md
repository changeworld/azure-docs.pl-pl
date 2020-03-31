---
title: Opóźnienie w magazynie obiektów Blob — usługa Azure Storage
description: Zrozumienie i mierzenie opóźnień dla operacji magazynu obiektów Blob i dowiedz się, jak zaprojektować aplikacje magazynu obiektów Blob z małym opóźnieniem.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75749728"
---
# <a name="latency-in-blob-storage"></a>Opóźnienia w magazynie obiektów blob

Opóźnienie, czasami odwołuje się jako czas odpowiedzi, jest czas, który aplikacja musi czekać na żądanie, aby zakończyć. Opóźnienie może bezpośrednio wpłynąć na wydajność aplikacji. Małe opóźnienia są często ważne w przypadku scenariuszy z ludźmi w pętli, takich jak przeprowadzanie transakcji kartą kredytową lub ładowanie stron internetowych. Systemy, które muszą przetwarzać zdarzenia przychodzące z wysokimi szybkościami, takie jak rejestrowanie danych telemetrycznych lub zdarzenia IoT, również wymagają małych opóźnień. W tym artykule opisano, jak zrozumieć i zmierzyć opóźnienie operacji na blokowych obiektach blob i jak zaprojektować aplikacje dla małych opóźnień.

Usługa Azure Storage oferuje dwie różne opcje wydajności dla bloków obiektów blob: premium i standard. Obiekty BLOB bloków premium oferują znacznie mniejsze i bardziej spójne opóźnienia niż standardowe blokowe obiekty blob za pośrednictwem dysków SSD o wysokiej wydajności. Aby uzyskać więcej informacji, zobacz **Magazyn obiektów blob bloku wydajności premium** w magazynie obiektów [Blob platformy Azure: warstwy dostępu gorąca, chłodna i archiwizowana.](storage-blob-storage-tiers.md)

## <a name="about-azure-storage-latency"></a>Opóźnienia usługi Azure Storage – informacje

Opóźnienie usługi Azure Storage jest związane ze stawkami żądań dla operacji usługi Azure Storage. Stawki żądań są również nazywane operacjami wejścia/wyjścia na sekundę (IOPS).

Aby obliczyć stawkę żądania, najpierw określ czas potrzebny do ukończenia każdego żądania, a następnie oblicz, ile żądań można przetwarzać na sekundę. Załóżmy na przykład, że żądanie trwa 50 milisekund (ms) do wykonania. Aplikacja używająca jednego wątku z jedną zaległą operacją odczytu lub zapisu powinna osiągnąć 20 operacji We/Wy (1 sekunda lub 1000 ms / 50 ms na żądanie). Teoretycznie, jeśli liczba wątków jest podwojona do dwóch, aplikacja powinna być w stanie osiągnąć 40 IOPS. Jeśli nierozstrzygnięte asynchroniczne operacje odczytu lub zapisu dla każdego wątku są podwojone do dwóch, aplikacja powinna być w stanie osiągnąć 80 Operacji We/Wy.

W praktyce stawki żądań nie zawsze skalują się tak liniowo, ze względu na obciążenie klienta z planowania zadań, przełączania kontekstu i tak dalej. Po stronie usługi może istnieć zmienność opóźnienia z powodu nacisku na system usługi Azure Storage, różnice w nośniku magazynu używane, hałas z innych obciążeń, zadania konserwacji i inne czynniki. Na koniec połączenie sieciowe między klientem a serwerem może mieć wpływ na opóźnienie usługi Azure Storage z powodu przeciążenia, przekierowania lub innych zakłóceń.

Przepustowość usługi Azure Storage, nazywana również przepływnością, jest powiązana z szybkością żądania i może być obliczana przez pomnożenie szybkości żądania (IOPS) przez rozmiar żądania. Na przykład przy założeniu 160 żądań na sekundę, każdy 256 KiB danych powoduje przepustowość 40 960 KiB na sekundę lub 40 MiB na sekundę.

## <a name="latency-metrics-for-block-blobs"></a>Metryki opóźnienia dla bloków blob

Usługa Azure Storage udostępnia dwie metryki opóźnienia dla bloków obiektów blob. Te metryki można wyświetlić w witrynie Azure portal:

- **Opóźnienie end-to-end (E2E)** mierzy interwał od momentu odebraniem pierwszego pakietu żądania przez usługę Azure Storage, dopóki usługa Azure Storage nie otrzyma potwierdzenia klienta w ostatnim pakiecie odpowiedzi.

- **Opóźnienie serwera** mierzy interwał od momentu odebrania ostatniego pakietu żądania przez usługę Azure Storage do momentu zwrócenia pierwszego pakietu odpowiedzi z usługi Azure Storage.

Na poniższej ilustracji przedstawiono **opóźnienie średniego sukcesu E2E** i średnie `Get Blob` opóźnienie serwera **sukcesu** dla przykładowego obciążenia, które wywołuje operację:

![Zrzut ekranu przedstawiający metryki opóźnienia dla operacji Pobierz obiekt blob](media/storage-blobs-latency/latency-metrics-get-blob.png)

W normalnych warunkach istnieje niewielka różnica między opóźnieniem end-to-end i opóźnienie serwera, co jest, co obraz pokazuje dla obciążenia próbki.

Jeśli przejrzysz metryki opóźnienia end-to-end i serwera i okaże się, że opóźnienie end-to-end jest znacznie wyższa niż opóźnienie serwera, a następnie zbadać i rozwiązać źródło dodatkowego opóźnienia.

Jeśli opóźnienie end-to-end i serwera są podobne, ale wymagają mniejszego opóźnienia, należy rozważyć migrację do magazynu bloków obiektów blob w wersji premium.

## <a name="factors-influencing-latency"></a>Czynniki wpływające na opóźnienie

Głównym czynnikiem wpływającym na opóźnienie jest rozmiar operacji. Trwa dłużej, aby zakończyć większe operacje, ze względu na ilość danych przesyłanych za pośrednictwem sieci i przetwarzane przez usługę Azure Storage.

Na poniższym diagramie przedstawiono całkowity czas operacji o różnych rozmiarach. W przypadku małych ilości danych interwał opóźnienia jest głównie wydawany na obsługę żądania, a nie na przesyłanie danych. Interwał opóźnienia zwiększa się tylko nieznacznie wraz ze wzrostem rozmiaru operacji (oznaczonym 1 na poniższym diagramie). Wraz ze wzrostem rozmiaru operacji więcej czasu poświęcają na przesyłanie danych, dzięki czemu całkowity interwał opóźnienia jest dzielony między obsługę żądań i transfer danych (oznaczony 2 na poniższym diagramie). W przypadku większych rozmiarów operacji interwał opóźnienia jest prawie wyłącznie wydawany na przesyłanie danych, a obsługa żądań jest w dużej mierze nieistotna (oznaczona 3 na poniższym diagramie).

![Zrzut ekranu przedstawiający całkowity czas pracy według rozmiaru operacji](media/storage-blobs-latency/operation-time-size-chart.png)

Czynniki konfiguracji klienta, takie jak współbieżność i wątki również wpływają na opóźnienie. Ogólna przepływność zależy od liczby żądań magazynu są w locie w danym momencie i jak aplikacja obsługuje wątki. Zasoby klienta, w tym procesora CPU, pamięci, magazynu lokalnego i interfejsów sieciowych może również mieć wpływ na opóźnienie.

Przetwarzanie żądań usługi Azure Storage wymaga zasobów procesora CPU i pamięci klienta. Jeśli klient znajduje się pod presją z powodu niedostateczonej maszyny wirtualnej lub niektórych proces ucieczki w systemie, istnieje mniej zasobów dostępnych do przetwarzania żądań usługi Azure Storage. Wszelkie rywalizacji lub brak zasobów klienta spowoduje wzrost opóźnienia end-to-end bez zwiększenia opóźnienia serwera, zwiększając lukę między tymi dwoma metrykami.

Równie ważne jest interfejs sieciowy i potok sieciowy między klientem a usługą Azure Storage. Sama odległość fizyczna może być istotnym czynnikiem, na przykład jeśli maszyna wirtualna klienta znajduje się w innym regionie platformy Azure lub lokalnie. Inne czynniki, takie jak przeskoki sieciowe, routing usługodawcy internetowego i stan internetu, mogą mieć wpływ na ogólne opóźnienie magazynu.

Aby ocenić opóźnienie, należy najpierw ustalić metryki linii bazowej dla scenariusza. Metryki planu bazowego zapewniają oczekiwane opóźnienie end-to-end i serwera w kontekście środowiska aplikacji, w zależności od profilu obciążenia, ustawień konfiguracji aplikacji, zasobów klienta, potoku sieciowego i innych czynników. Jeśli masz metryki linii bazowej, można łatwiej zidentyfikować nieprawidłowe w porównaniu z normalnymi warunkami. Metryki linii bazowej umożliwiają również obserwowanie efektów zmienionych parametrów, takich jak konfiguracja aplikacji lub rozmiary maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

- [Cele dotyczące skalowalności i wydajności pamięci masowej obiektów Blob](scalability-targets.md)
- [Lista kontrolna wydajności i skalowalności magazynu obiektów Blob](storage-performance-checklist.md)
