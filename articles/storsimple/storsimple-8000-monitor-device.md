---
title: Monitorowanie urządzenia serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania usługi Menedżer StorSimple urządzeń do monitorowania użycia, wydajności we/wy i wykorzystanie pojemności.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 679c1fc8775ad4481bc99c9aea79fe16e9bcac8f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2017
ms.locfileid: "23933144"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Użyj Menedżera urządzeń StorSimple usługę do monitorowania urządzenia StorSimple

## <a name="overview"></a>Omówienie
Usługę Menedżer StorSimple urządzenia służy do monitorowania określonych urządzeń w rozwiązaniu StorSimple. Można tworzyć niestandardowe wykresy na podstawie wydajności operacji We/Wy, wykorzystanie pojemności, przepływność sieci i metryki wydajności urządzenia i przypiąć je na pulpicie nawigacyjnym. Aby uzyskać więcej informacji, przejdź do [dostosowanie pulpitu nawigacyjnego portalu](../azure-portal/azure-portal-dashboards.md).

Aby wyświetlić dane monitorowania dla określonego urządzenia w portalu Azure, wybierz usługę Menedżer StorSimple urządzenia. Z listy urządzeń wybierz urządzenia, a następnie przejdź do **Monitor**. Możesz sprawdzić **pojemności**, **użycia**, i **wydajności** wykresów dla wybranego urządzenia.

## <a name="capacity"></a>Pojemność
**Pojemność** śledzi elastycznie i miejsca na urządzeniu. Pozostałe zasoby zebrane przypięty lokalnie lub do warstwy.

Pojemność elastycznie i pozostałe jest dalsze rozbiciu woluminów warstwowych i przypiętych lokalnie. Dla każdego woluminu jest wyświetlany elastycznie pojemności i pozostałe zasoby na urządzeniu.

![Wydajność We/Wy](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Sposób użycia
**Użycie** śledzi metryki dotyczące ilości miejsca do magazynowania danych, używany przez woluminy, kontenery woluminów lub urządzenia. Możesz utworzyć raporty na podstawie wykorzystania pojemności magazynu podstawowego, magazynu w chmurze lub pamięć masową urządzenia. Wykorzystanie pojemności może być zmierzony przy określonego woluminu, określony wolumin kontenera lub wszystkie kontenery woluminów.
Domyślnie jest zgłaszana użycie w ciągu ostatnich 24 godzin. Można edytować na wykresie, aby zmienić czas trwania, w którym użycia jest zgłaszany, wybierając z:
* Po 24 godzinach
* Ostatnie 7 dni
* Ostatnie 30 dni
* Ostatnie 90 dni
* Ciągu ostatniego roku

Dwa metrices klucza, wzrostu i zakres są zgłaszane w przypadku wykresów użycia. Zakres odwołuje się do maksymalnej wartości i wartości minimalnej użycia zgłoszonych przez wybrany czas trwania (wystąpienie fo, ostatnie 7 dni).

Rozwój odwołuje się do wzrost użycia od pierwszego dnia ostatni dzień przez wybrany czas trwania. 

Wzrost i zakres również może być reprezentowany przez następujące równania:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Chmury podstawowej, i lokalny magazyn używany można przedstawić w następujący sposób:

### <a name="primary-storage-usage"></a>Użycie magazynu głównego
Te na wykresach ilość danych zapisywane woluminów StorSimple przed danych jest deduplikowany i skompresowane. Można wyświetlić podstawowy magazyn używany przez wszystkie woluminy w kontenerze woluminów lub jednego woluminu. Podstawowy magazyn używany jest dalsze wyszczególnieniem podstawowego magazynu warstwowego używane i Magazyn używany przypięty lokalnie podstawowej.

Podstawowy magazyn używany dla urządzenia StorSimple przed i po chmurze migawki wykresach. Jak jest to po prostu danych woluminu, migawka w chmurze nie należy zmieniać magazynu głównego. Jak widać, wykres ten przedstawia różnicy w głównej magazynu warstwowego lub przypięty lokalnie używane wyniku migawki chmury. Migawka w chmurze rozpoczęty o godzinie około 11:50 pm na tym urządzeniu.

![Wykorzystanie pojemności głównej po migawka w chmurze](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Jeśli teraz uruchomić we/wy na hoście podłączony do urządzenia StorSimple, zobaczysz wzrost podstawowego magazynu warstwowego lub przypięty lokalnie podstawowy magazyn używany w zależności od woluminów (warstwowa lub przypięty lokalnie) zostanie zapisane dane. Poniżej przedstawiono wykresy użycia magazynu głównego dla urządzenia StorSimple. Na tym urządzeniu hosta StorSimple uruchomiona obsługująca zapisuje około godzinie 2:30 na wolumin warstwowy na urządzeniu. Widać szczytu w zapisu bajtów/s odpowiadający we/wy uruchomiona na hoście.

![Wydajność We/Wy z woluminami warstwowymi](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Jeśli przyjrzymy się podstawowego magazynu warstwowego używany, która stała się natomiast przypięty lokalnie podstawowej użycie pozostaje bez zmian, ponieważ nie ma żadnych zapisy udostępniane woluminów przypiętych lokalnie na urządzeniu.

![Wykorzystanie pojemności głównej uruchomionej we/wy na woluminach warstwowych](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Jeśli używasz aktualizacją 3 lub nowszym, można przerwać dół wykorzystania pojemności magazynu głównego poszczególnych woluminów, wszystkie woluminy, wszystkie woluminy warstwowe i wszystkich woluminów przypiętych lokalnie w sposób przedstawiony poniżej. Dzielenie przez wszystkich woluminów przypiętych lokalnie pozwoli szybko ustalić, ile warstwie lokalnej jest wykorzystane.

![Wykorzystanie pojemności głównej dla wszystkich woluminów warstwowych](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Wykorzystanie pojemności głównej dla wszystkich woluminów przypiętych lokalnie](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Pozwala dodatkowo kliknij na każdym woluminie, na liście i odpowiednie użytkowania.

![Wykorzystanie pojemności głównej dla wszystkich woluminów przypiętych lokalnie](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Użycie magazynu w chmurze
Te na wykresach wielkość magazynu w chmurze używane. Te dane są deduplikowane i skompresowane. Ta wartość obejmuje migawki w chmurze, które mogą zawierać dane, które nie jest uwzględniana w głównej woluminami i jest przechowywana na potrzeby przechowywania starszych lub wymagane. Możesz porównać podstawową i chmury magazynu danych, aby poznać zmniejszenie szybkości danych, mimo że nie liczba będzie równa dokładnie.

Następujące wykresy Pokaż wykorzystanie magazynu chmury urządzenia StorSimple w przypadku migawki chmury.

* Migawka w chmurze rozpoczęty o godzinie około 11:50:00 na tym urządzeniu i widać, że przed chmury migawki, nie ma nie używać magazynu w chmurze. 
* Raz ukończone migawka w chmurze, wykorzystanie magazynu w chmurze zrzut zapasowej 0,89 GB. 
* Podczas migawka w chmurze jest w toku, dostępna jest również odpowiedniego szczytu w We/Wy z urządzenia do chmury.

    ![Wykorzystanie magazynu w chmurze przed migawka w chmurze](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Wykorzystanie magazynu w chmurze po migawka w chmurze](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![We/Wy z urządzenia do chmury podczas migawka w chmurze](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Użycie lokalnego magazynu
Te na wykresach łączne użycie dla urządzenia, które będzie użycie więcej niż podstawowy magazynu, ponieważ zawiera on liniowej warstwy dysków SSD. Ta warstwa zawiera ilość danych istnieje również na urządzenia do innej warstwy. Ponownym włączeniu pojemności w liniowej warstwy dysków SSD, dzięki czemu mają nowe dane, stare dane jest przenoszony do warstwy dysków HDD (po tym czasie jest deduplikowany i skompresowane), a następnie do chmury.

W czasie, podstawowego magazynu używane i lokalny magazyn używany najprawdopodobniej zwiększyć ze sobą, dopóki dane rozpocznie się do warstwy do chmury. W tym momencie prawdopodobnie rozpocznie lokalny magazyn używany mniej, ale podstawowy magazyn, używany będzie większa, gdy zapisywanych jest więcej danych.

Podstawowy magazyn używany dla urządzenia StorSimple, gdy migawki chmury wykresach. Migawka w chmurze zaczęła na 11:50:00, a Magazyn lokalny zmniejszenie o tej godzinie. Lokalny magazyn używany zakończył działanie z 1.445 GB 1,09 GB. To wskazuje, że prawdopodobnie nieskompresowanych danych w warstwie dysków SSD liniowej został deduplikowane, skompresowane i przeniesione do warstwy dysków HDD. Należy pamiętać, że jeśli urządzenie już dużej ilości danych w warstwach zarówno dysków SSD i HDD, mogą nie być widoczne to zmniejszenie. W tym przykładzie urządzenie ma niewielką ilość danych.

![Użycie magazynu lokalnego po migawka w chmurze](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Wydajność
**Wydajność** śledzi metryk powiązanych z liczbą odczytu i zapisu między albo interfejsy inicjatora iSCSI na serwerze hosta i urządzenie lub urządzenia i chmury. To wydajność może być zmierzony dla określonego woluminu, określony wolumin kontenera lub wszystkie kontenery woluminów. Wydajność także wykorzystanie procesora CPU i przepustowość sieci dla poszczególnych interfejsów sieciowych na urządzeniu.

### <a name="io-performance-for-initiator-to-device"></a>Wydajność We/Wy dla inicjatora dla urządzenia
Poniższej tabeli przedstawiono we/wy dla inicjatora na urządzeniu dla wszystkich woluminów urządzenia produkcji. Metryki wykreślić są odczytu i zapisu bajtów na sekundę. Można również wykresu odczytu, zapisu i oczekujących operacji We/Wy lub odczytu i zapisu opóźnienia.

![Wydajność We/Wy dla inicjatora dla urządzenia](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Wydajność We/Wy urządzenia do chmury
Dla tego samego urządzenia operacje We/Wy są kreślone danych z urządzenia do chmury dla wszystkich kontenerów woluminu. Na tym urządzeniu dane są tylko w warstwie liniowej i nic nie ma rozrzucone do chmury. Nie ma żadnych operacje odczytu i zapisu występujących z urządzenia do chmury. W związku z tym pików na wykresie są co 5 minut umożliwiająca częstotliwość sprawdzania pulsu między urządzeniem i usługi.

Dla tego samego urządzenia chmury migawki danych woluminu, zaczynając od 11:50:00. To sprawia, że dane przepływające z urządzenia do chmury. Zapisy zostały obsłużone do chmury w ten czas trwania. Wykres we/wy pokazuje szczytu w zapisu bajtów/s odpowiadający czas, kiedy migawki.

![We/Wy z urządzenia do chmury podczas migawka w chmurze](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Przepustowość sieci dla interfejsów sieciowych urządzenia
**Przepustowość sieci** śledzi metryki dotyczące ilość danych przesyłanych z interfejsów sieciowych inicjatora iSCSI na serwerze hosta, a urządzenie i między urządzeniem i chmurą. Ta metryka dla poszczególnych interfejsów sieciowych iSCSI można monitorować na urządzeniu.

Następujące wykresy pokazują przepustowość sieci dla danych 0, 1, 1 GbE sieci na swoim urządzeniu, która była zarówno włączoną obsługę chmury (ustawienie domyślne) i włączono interfejs iSCSI. Na tym urządzeniu 14 czerwca na około 21: 00 do danych został warstwy do chmury (nie chmury wykonano migawek w tym czasie wskazujący na obsługę poziomów jest mechanizm przenoszenia danych w chmurze) co spowodowało we/wy obsługiwanej w chmurze. Brak odpowiedniego szczytu na wykresie przepustowości sieci w tym samym czasie i większość ruchu sieciowego jest ruch wychodzący do chmury.

![Przepustowość sieci dla interfejsu dane 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Jeśli przyjrzymy wykresu przepływność danych 1 interfejs sieciowe innego 1 GbE interfejs, który był tylko włączono interfejs iSCSI, a następnie nie było praktycznie ruchu sieciowego w ten czas trwania.

![Przepustowość sieci dla danych 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Użycie procesora CPU dla urządzenia
**Użycie procesora CPU** śledzi metryki dotyczące Procesora używane na urządzeniu. W poniższej tabeli przedstawiono Statystyka wykorzystania procesora CPU dla urządzenia w środowisku produkcyjnym.

![Użycie procesora CPU dla urządzenia](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [pulpit nawigacyjny urządzenia usługi Menedżer StorSimple urządzenia](storsimple-device-dashboard.md).
* Dowiedz się, jak [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-manager-service-administration.md).

