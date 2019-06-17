---
title: Monitorowanie z urządzenia StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Opisuje sposób używania usługi Menedżer urządzeń StorSimple do monitorowania użycia, wydajności we/wy i wykorzystanie pojemności.
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
ms.openlocfilehash: 602514df69977891167f341db0ab20913bcacc9f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60634566"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Monitorować urządzenie StorSimple przy użyciu usługi Menedżer urządzeń StorSimple

## <a name="overview"></a>Omówienie
Usługa Menedżer urządzeń StorSimple do monitorowania określonych urządzeń w ramach rozwiązania StorSimple. Można tworzyć niestandardowe wykresy na podstawie wydajności operacji We/Wy, wykorzystanie pojemności, przepływności sieci i metryki wydajności urządzenia i przypiąć do pulpitu nawigacyjnego. Aby uzyskać więcej informacji, przejdź do [Dostosowywanie pulpitu nawigacyjnego portalu](../azure-portal/azure-portal-dashboards.md).

Aby wyświetlić dane monitorowania dla określonego urządzenia, w witrynie Azure portal, wybierz usługę Menedżer urządzeń StorSimple. Z listy urządzeń wybierz urządzenie, a następnie przejdź do **Monitor**. Możesz sprawdzić **pojemności**, **użycia**, i **wydajności** wykresy dla wybranego urządzenia.

## <a name="capacity"></a>Pojemność
**Pojemność** śledzi aprowizowane miejsce i miejsca na urządzeniu. Pozostała pojemność zostanie wyświetlona jako przypięty lokalnie lub warstwy.

Elastycznie i pozostałej pojemności jest dalszych rozbiciu woluminy warstwowe i przypiętego lokalnie. Dla każdego woluminu wyświetlany jest zaprowizowaną pojemnością i pozostałe zasoby na urządzeniu.

![Wydajność We/Wy](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Sposób użycia
**Użycie** śledzi metryki dotyczące ilości miejsca do magazynowania danych, używanego przez woluminy, kontenery woluminów lub urządzenia. Można utworzyć raportów na podstawie wykorzystania pojemności magazynu podstawowego, magazynem w chmurze lub magazynu urządzenia. Użycie wydajności można zmierzyć na określonego woluminu, kontener określonego woluminu lub wszystkie kontenery woluminów.
Domyślnie jest zgłaszany użycia w ciągu ostatnich 24 godzin. Można edytować wykres, aby zmienić czas trwania, którego użycie jest zgłaszany, wybierając:
* Ostatnie 24 godziny
* Ostatnie 7 dni
* Ostatnich 30 dni
* Ostatnie 90 dni
* Ubiegły rok

Dwa kluczowe metrices, rozwój i zakres są raportowane wykresy użycia. Zakres odwołuje się do wartości maksymalnej i minimalnej wartości użycia zgłoszone w wybranym okresie (wystąpienia fo, ostatnie 7 dni).

Wzrost odnosi się do wzrostu użycia od pierwszego dnia na ostatni dzień w wybranym okresie. 

Wskaźnik wzrostu i zakres również może być reprezentowany przez następujących wzorów:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Chmura podstawowa, a używany Magazyn lokalny można przedstawić w następujący sposób:

### <a name="primary-storage-usage"></a>Użycie magazynu podstawowego
Te wykresy pokazują ilość danych zapisanych woluminów StorSimple, zanim dane są deduplikowane, a skompresowane. Można wyświetlić podstawowy magazyn używany przez wszystkie woluminy w kontenerze woluminów lub jednego woluminu. Podstawowym magazynem używanym jest dalszych rozbiciu użyto głównego magazynu warstwowego i głównego magazynu używane przypiętego lokalnie.

Poniższe wykresy pokazują podstawowym magazynem używanym dla urządzenia StorSimple, przed i po migawki w chmurze. Jak jest to po prostu dane woluminu, migawkę w chmurze nie należy zmieniać podstawowy magazyn. Jak widać, wykres pokazuje różnicy w podstawowy magazyn warstwowy lub przypięty lokalnie używany w wyniku wykonywania migawki w chmurze. Migawka w chmurze rozpoczęło się o około 23:50:00 na tym urządzeniu.

![Wykorzystanie pojemności głównej po migawki w chmurze](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Jeśli teraz uruchomić operacji We/Wy na hoście podłączone do urządzenia StorSimple, zostanie wyświetlony wzrost podstawowy magazyn warstwowy lub podstawowego lokalnie przypięte miejsca używanego w zależności od woluminów (warstwy lub przypięty lokalnie) zostanie zapisane dane. Poniżej przedstawiono wykresy użycia magazynu podstawowego dla urządzenia StorSimple. Na tym urządzeniu hosta StorSimple pracę, obsługująca zapisuje się na około 2:30 pm w woluminu warstwowego na urządzeniu. Możesz zobaczyć pikach Bajty zapisu/s odpowiadający operacji We/Wy uruchomiona na hoście.

![Wydajność We/Wy uruchomionych na woluminy warstwowe](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Jeśli analizujesz użyto głównego magazynu warstwowego, stała się natomiast przypięty lokalnie podstawowe użycie pozostaje bez zmian, ponieważ nie zapisy obsłużonych woluminy przypięte lokalnie na urządzeniu.

![Wykorzystanie pojemności głównej po uruchomieniu operacji We/Wy na woluminach warstwowych](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Jeśli korzystasz z aktualizacją 3 lub wyższą od nich, można podzielić wykorzystanie pojemności magazynu podstawowego przez pojedynczy wolumin, wszystkie woluminy, wszystkie woluminy warstwowych i wszystkie woluminy przypięte lokalnie, jak pokazano poniżej. Potężne przez wszystkie woluminy przypięte lokalnie pozwoli szybko ustalić, ile warstwie lokalnej jest zużyte.

![Wykorzystanie pojemności podstawowy, wszystkie woluminy warstwowych](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Wykorzystanie pojemności głównej dla wszystkich woluminów przypiętych lokalnie](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Pozwala dodatkowo klikaj poszczególne woluminy na liście i zobacz odpowiednie użycie.

![Wykorzystanie pojemności głównej dla wszystkich woluminów przypiętych lokalnie](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Użycie magazynu w chmurze
Te wykresy pokazują wielkość wykorzystanego magazynu w chmurze. Te dane są deduplikowane i skompresować. Ta wartość obejmuje migawki w chmurze, które mogą zawierać dane, które nie jest uwzględniana w dowolny wolumin podstawowy i są przechowywane na potrzeby przechowywania starszych lub wymagane. Można porównać podstawowym i w chmurze dane użycia magazynu, aby poznać częstotliwość redukcja danych, mimo że nie będzie dokładna liczba.

Następujące wykresy pokazują wykorzystanie magazynu chmurze urządzenia StorSimple, gdy została wykonana migawka w chmurze.

* Migawka w chmurze rozpoczęło się o około 11:50:00 na tym urządzeniu, a zobaczysz, że przed migawki w chmurze, nie ma żadnych użycie magazynu w chmurze. 
* Raz ukończone migawki w chmurze, wykorzystanie magazynu w chmurze zrzut się 0,89 GB. 
* Podczas migawki w chmurze jest w toku, dostępna jest również odpowiedni szczytu w operacji We/Wy z urządzenia do chmury.

    ![Wykorzystanie magazynu w chmurze przed migawki w chmurze](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Wykorzystanie magazynu w chmurze po migawki w chmurze](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![We/Wy z urządzenia do chmury podczas migawkę w chmurze](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Użycie magazynu lokalnego
Te wykresy pokazują łączne użycie dla urządzenia, który będzie użycie magazynu ponad podstawowego, ponieważ zawiera ona liniowej warstwy dysków SSD. Ta warstwa zawiera ilość danych, który jest także na urządzeniu w innych warstwach. Tak, aby po nadejściu nowych danych stare dane jest przenoszony do warstwy dysków HDD (co jest deduplikowany i skompresowane), a następnie do chmury, ponownym włączeniu pojemności w warstwie dysków SSD liniowego.

Nad przechowywaniem czasu, podstawowe używane i lokalny magazyn używany najprawdopodobniej zwiększyć ze sobą, aż zacznie dane umieszczane w chmurze. W tym momencie używany Magazyn lokalny prawdopodobnie rozpocznie się ustabilizowania, ale podstawowym magazynem używanym więc ceny wzrosną jako zapisywanych jest więcej danych.

Poniższe wykresy pokazują podstawowym magazynem używanym dla urządzenia StorSimple, gdy została wykonana migawka w chmurze. Migawki w chmurze rozpoczęty o godzinie 11:50:00 i Magazyn lokalny pracę, zmniejszając w tym czasie. Używany Magazyn lokalny zakończył działanie z 1.445 GB 1,09 GB. Oznacza to, czy najprawdopodobniej nieskompresowanych danych w liniowej warstwy dysków SSD został deduplikowane, kompresowane i przeniesiony do warstwy dysków HDD. Należy pamiętać, że jeśli urządzenie ma już dużej ilości danych w warstwy dysków SSD i HDD, nie widać to zmniejszenie. W tym przykładzie urządzenie ma niewielką ilość danych.

![Wykorzystanie magazynu lokalnego po migawki w chmurze](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Wydajność
**Wydajność** śledzi metryki związane z liczbą odczytu i zapisu między albo interfejsy inicjatora iSCSI na serwerze hosta i urządzenie lub urządzenia i chmurę. Ta wydajności można zmierzyć dla określonego woluminu, kontener określonego woluminu lub wszystkie kontenery woluminów. Wydajność także wykorzystanie procesora CPU i przepustowość sieci dla różnych interfejsów sieciowych na urządzeniu.

### <a name="io-performance-for-initiator-to-device"></a>Wydajność operacji We/Wy inicjator do urządzenia
Na wykresie poniżej przedstawiono operacji We/Wy dla inicjator do urządzenia pod kątem wszystkich woluminów na potrzeby urządzeń produkcyjnych. Metryki kreślone są Odczyt i zapis bajtów na sekundę. Można również wykresu odczytu, zapisu i oczekujących operacji We/Wy lub odczytu i zapisu opóźnienia.

![Wydajność We/Wy inicjator do urządzenia](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Wydajność operacji We/Wy urządzenia do chmury
Operacje We/Wy dla tego samego urządzenia, są oznaczane na wykresach dla danych z urządzenia do chmury na potrzeby wszystkie kontenery woluminów. Na tym urządzeniu dane są tylko w warstwie liniowej i nic nie ma rozlane do chmury. Nie ma żadnych operacji odczytu i zapisu, pojawiają się z urządzenia do chmury. Dlatego wartości szczytowe na wykresie są z interwałem wynoszącym 5 minut, umożliwiająca częstotliwość sprawdzania pulsu między urządzeniem a usługą.

Na tym samym urządzeniu migawkę w chmurze została wykonana dla woluminu danych, zaczynając od 11:50:00. Pozwoliło to odnotować danych napływających z urządzenia do chmury. Zapisy były obsługiwane w chmurze w tym czasie. Wykres we/wy pokazuje nagły zapisu b/s odpowiadającego do chwili, gdy migawka została utworzona.

![We/Wy z urządzenia do chmury podczas migawkę w chmurze](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Przepustowość sieci dla interfejsów sieciowych urządzenia
**Przepływność sieci** śledzi metryki dotyczące ilości danych przesyłanych z interfejsów sieciowych inicjatora iSCSI na serwerze hosta i urządzenia oraz między urządzeniem i chmurą. Ta metryka można monitorować dla poszczególnych interfejsów sieciowych iSCSI na urządzeniu.

Poniższe wykresy pokazują przepustowość sieci dla Data 0, 1, 1 GbE sieci na urządzeniu została zarówno włączoną obsługę chmury (ustawienie domyślne) i włączono interfejs iSCSI. Na tym urządzeniu 14 czerwca o około 21: 00, dane warstwowe zostało do chmury (nie znaleziono migawek w chmurze zostały pobrane w tym czasie, na które wskazuje na warstw jest mechanizm przenoszenia danych do chmury) co spowodowało we/wy obsługiwanych w chmurze. Istnieje odpowiedni szczytu na wykresie przepustowość sieci na tym samym czasie i większość ruchu sieciowego jest ruch wychodzący do chmury.

![Przepływność sieci interfejsu dane 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Jeśli spojrzymy na wykres przepływności interfejsu Data 1, 1 GbE w innej sieci interfejs, który był tylko włączono interfejs iSCSI, a następnie wystąpił praktycznie żaden ruch sieciowy w tym czasie.

![Przepustowość sieci dla danych 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Użycie procesora CPU dla urządzenia
**Użycie procesora CPU** śledzi metryki związane do wykorzystania na urządzeniu z systemem Procesora. Na poniższym wykresie przedstawiono statystyki wykorzystania procesora CPU dla urządzenia w środowisku produkcyjnym.

![Użycie procesora CPU dla urządzenia](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [korzystać z pulpitu nawigacyjnego z urządzenia usługi Menedżer urządzeń StorSimple](storsimple-device-dashboard.md).
* Dowiedz się, jak [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-manager-service-administration.md).

