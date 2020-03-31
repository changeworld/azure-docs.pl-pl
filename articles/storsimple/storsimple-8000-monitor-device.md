---
title: Monitorowanie urządzenia z serii StorSimple 8000
description: W tym artykule opisano sposób używania usługi StorSimple Device Manager do monitorowania użycia, wydajności we/wy i wykorzystania pojemności.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: b3b77024606c5cdb02ff7bdd357c2d14a2415efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277056"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Monitorowanie urządzenia StorSimple za pomocą usługi StorSimple Device Manager

## <a name="overview"></a>Omówienie
Za pomocą usługi StorSimple Device Manager można monitorować określone urządzenia w ramach rozwiązania StorSimple. Można tworzyć niestandardowe wykresy na podstawie wydajności we/wy, wykorzystania pojemności, przepływności sieci i metryk wydajności urządzenia i przypiąć je do pulpitu nawigacyjnego. Aby uzyskać więcej informacji, przejdź do [dostosowywania pulpitu nawigacyjnego portalu](../azure-portal/azure-portal-dashboards.md).

Aby wyświetlić informacje o monitorowaniu określonego urządzenia, w witrynie Azure portal wybierz usługę StorSimple Device Manager. Z listy urządzeń wybierz urządzenie, a następnie przejdź do **opcji Monitoruj**. Zostaną wyświetlony **wykresy Pojemność,** **Użycie**i **Wydajność** dla wybranego urządzenia.

## <a name="capacity"></a>Pojemność
**Pojemność** śledzi aprowizowanego miejsca i miejsca pozostałego na urządzeniu. Pozostała pojemność jest następnie wyświetlana jako przypięta lokalnie lub warstwowa.

Aprowizowana i pozostała pojemność jest dalej rozkładana według woluminów warstwowych i przypiętych lokalnie. Dla każdego woluminu wyświetlana jest aprowizowana pojemność i pozostała pojemność urządzenia.

![Pojemność we/wy](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Sposób użycia
**Użycie** śledzi metryki związane z ilością miejsca do magazynowania danych, który jest używany przez woluminy, kontenery woluminów lub urządzenia. Raporty można tworzyć na podstawie wykorzystania pojemności magazynu podstawowego, magazynu w chmurze lub magazynu urządzeń. Wykorzystanie pojemności można zmierzyć na określonej objętości, kontenerze określonej objętości lub wszystkich pojemnikach objętościowych.
Domyślnie zgłaszane jest użycie w ciągu ostatnich 24 godzin. Wykres można edytować, aby zmienić czas trwania, w którym jest zgłaszane użycie, wybierając z:
* Ostatnie 24 godziny
* Ostatnie 7 dni
* Ostatnie 30 dni
* Ostatnie 90 dni
* Miniony rok

Dwa kluczowe metryki, wzrost i zakres są zgłaszane dla wykresów użycia. Zakres odnosi się do maksymalnej wartości i minimalnych wartości użycia zgłoszonych w wybranym czasie trwania (fo instance, Past 7 days).

Wzrost odnosi się do wzrostu wykorzystania od pierwszego dnia do ostatniego dnia w wybranym czasie trwania. 

Wzrost i zakres mogą być również reprezentowane przez następujące równania:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Używany magazyn podstawowy, chmurowy i lokalny można opisać w następujący sposób:

### <a name="primary-storage-usage"></a>Użycie magazynu podstawowego
Te wykresy pokazują ilość danych zapisanych do woluminów StorSimple przed deduplikowane dane i skompresowane. Można wyświetlić magazyn podstawowy używany przez wszystkie woluminy w kontenerze woluminu lub dla pojedynczego woluminu. Używany magazyn podstawowy jest dalej podzielony według używanego magazynu warstwowego podstawowego i używanego podstawowego magazynu przypiętego lokalnie.

Na poniższych wykresach przedstawiono magazyn podstawowy używany dla urządzenia StorSimple przed i po zrobieniu migawki w chmurze. Ponieważ są to tylko dane woluminu, migawka w chmurze nie powinna zmieniać magazynu podstawowego. Jak widać, wykres nie pokazuje żadnej różnicy w podstawowej warstwowej lub lokalnie przypiętej pamięci masowej używanej w wyniku robienia migawki w chmurze. Migawka chmury rozpoczęła się około 23:50 na tym urządzeniu.

![Wykorzystanie podstawowej pojemności po migawce chmury](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Jeśli teraz uruchomisz we/wy na hoście podłączonym do urządzenia StorSimple, zostanie wyświetlony wzrost podstawowego magazynu warstwowego lub podstawowego lokalnie przypiętego magazynu używanego w zależności od woluminów (warstwowych lub przypiętych lokalnie), na których zapisujesz dane. Oto podstawowe wykresy użycia magazynu dla urządzenia StorSimple. Na tym urządzeniu host StorSimple zaczął obsługiwać zapisy około godziny 14:30 na woluminie warstwowym na urządzeniu. Można zobaczyć szczyt w bajtów zapisu/s odpowiadających we/wy uruchomionej na hoście.

![Wydajność podczas pracy we/wy na woluminach warstwowych](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Jeśli spojrzeć na podstawowy magazyn warstwowy używany, który poszedł w górę, podczas gdy podstawowe użycie przypięte lokalnie pozostaje niezmieniona, ponieważ nie ma zapisów obsługiwanych do woluminów przypiętych lokalnie na urządzeniu.

![Wykorzystanie zdolności podstawowych podczas pracy we/wy na woluminach warstwowych](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Jeśli jest uruchomiona aktualizacja 3 lub wyższa, można podzielić wykorzystanie podstawowej pojemności magazynu przez pojedynczy wolumin, wszystkie woluminy, wszystkie woluminy warstwowe i wszystkie woluminy przypięte lokalnie, jak pokazano poniżej. Podział na wszystkie woluminy przypięte lokalnie pozwoli szybko ustalić, jaka część warstwy lokalnej jest zużywana.

![Podstawowe wykorzystanie zdolności produkcyjnych dla wszystkich woluminów warstwowych](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Podstawowe wykorzystanie mocy produkcyjnych dla wszystkich woluminów przypiętych lokalnie](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Możesz kliknąć na każdy z woluminów na liście i zobaczyć odpowiednie użycie.

![Podstawowe wykorzystanie mocy produkcyjnych dla wszystkich woluminów przypiętych lokalnie](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Użycie pamięci masowej w chmurze
Te wykresy pokazują ilość używanego magazynu w chmurze. Te dane są deduplikowane i kompresowane. Ta kwota obejmuje migawki w chmurze, które mogą zawierać dane, które nie są odzwierciedlane w żadnym woluminie podstawowym i są przechowywane do celów starszego lub wymaganego przechowywania. Możesz porównać dane dotyczące zużycia magazynu podstawowego i w chmurze, aby zorientować się w szybkości redukcji danych, chociaż liczba ta nie będzie dokładna.

Na poniższych wykresach przedstawiono wykorzystanie magazynu w chmurze urządzenia StorSimple podczas migawki w chmurze.

* Migawka w chmurze rozpoczęła się około 11:50 na tym urządzeniu i widać, że przed migawką w chmurze nie było używanego magazynu w chmurze. 
* Po zakończeniu migawki w chmurze wykorzystanie magazynu w chmurze wzrosła o 0,89 GB. 
* Podczas gdy migawka w chmurze była w toku, istnieje również odpowiedni szczyt we we/wy z urządzenia do chmury.

    ![Wykorzystanie pamięci masowej w chmurze przed migawką w chmurze](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Wykorzystanie pamięci masowej w chmurze po migawce w chmurze](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![We/Wy z urządzenia do chmury podczas migawki w chmurze](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Użycie pamięci masowej lokalnej
Te wykresy pokazują całkowite wykorzystanie urządzenia, które będzie więcej niż podstawowe użycie magazynu, ponieważ zawiera warstwę liniową SSD. Ta warstwa zawiera ilość danych, które istnieje również w innych warstwach urządzenia. Pojemność w warstwie liniowej SSD jest przełączana, dzięki czemu po wprowadzeniu nowych danych stare dane są przenoszone do warstwy hdd (w tym czasie są deduplikowane i kompresowane), a następnie do chmury.

Z biegiem czasu używany magazyn podstawowy i używany magazyn lokalny najprawdopodobniej wzrosną razem, dopóki dane nie zaczętą warstwową w chmurze. W tym momencie używana pamięć lokalna prawdopodobnie zacznie plateau, ale używana pamięć podstawowa wzrośnie wraz z zapisem większej liczby danych.

Na poniższych wykresach przedstawiono magazyn podstawowy używany dla urządzenia StorSimple podczas robienia migawki w chmurze. Migawka w chmurze rozpoczęła się o godzinie 11:50, a pamięć lokalna zaczęła się zmniejszać w tym czasie. Używana pamięć lokalna spadła z 1,445 GB do 1,09 GB. Oznacza to, że najprawdopodobniej nieskompresowane dane w warstwie liniowego dysku SSD zostały deduplikowane, skompresowane i przeniesione do warstwy hdd. Należy zauważyć, że jeśli urządzenie ma już dużą ilość danych w warstwach SSD i HDD, może nie zobaczyć tego spadku. W tym przykładzie urządzenie ma niewielką ilość danych.

![Wykorzystanie magazynu lokalnego po migawce w chmurze](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Wydajność
**Wydajność** śledzi metryki związane z liczbą operacji odczytu i zapisu między interfejsami inicjatora iSCSI na serwerze hosta a urządzeniem lub urządzeniem i chmurą. Tę wydajność można zmierzyć dla określonej objętości, określonego kontenera woluminu lub wszystkich kontenerów woluminów. Wydajność obejmuje również wykorzystanie procesora CPU i przepustowość sieci dla różnych interfejsów sieciowych na urządzeniu.

### <a name="io-performance-for-initiator-to-device"></a>Wydajność we/wy dla inicjatora do urządzenia
Poniższy wykres przedstawia we/wy dla inicjatora do urządzenia dla wszystkich woluminów dla urządzenia produkcyjnego. Metryki kreślone są odczytywane i zapisywać bajty na sekundę. Można również wykres odczytu, zapisu i zaległych we/wy lub odczytu i zapisu opóźnienia.

![Wydajność we/wy dla inicjatora do urządzenia](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Wydajność we/wy dla urządzenia do chmury
Dla tego samego urządzenia operacje we/wy są drukowane dla danych z urządzenia do chmury dla wszystkich kontenerów woluminów. Na tym urządzeniu dane są tylko w warstwie liniowej i nic nie rozlało się do chmury. Nie ma żadnych operacji odczytu i zapisu występujących z urządzenia do chmury. W związku z tym szczyty na wykresie są w odstępie 5 minut, który odpowiada częstotliwości, z jaką bicie serca jest sprawdzane między urządzeniem a usługą.

Dla tego samego urządzenia zrobiono migawkę w chmurze dla danych woluminu od godziny 11:50. Spowodowało to przepływ danych z urządzenia do chmury. Zapisy zostały podane do chmury w tym czasie. Wykres we/wy przedstawia szczyt w write bytes/s odpowiadający czasowi, w którym została zrobiona migawka.

![We/Wy z urządzenia do chmury podczas migawki w chmurze](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Przepustowość sieci dla interfejsów sieciowych urządzeń
**Przepustowość sieci** śledzi metryki związane z ilością danych przesyłanych z interfejsów sieciowych inicjatora iSCSI na serwerze hosta i urządzeniu oraz między urządzeniem a chmurą. Tę metrykę można monitorować dla każdego interfejsu sieciowego iSCSI na urządzeniu.

Na poniższych wykresach przedstawiono przepustowość sieci dla sieci Data 0, 1 1 GbE na urządzeniu, która była zarówno włączona w chmurze (domyślnie), jak i z włączoną funkcją iSCSI. Na tym urządzeniu 14 czerwca około godziny 21:00 dane zostały podzielone warstwowo do chmury (w tym czasie nie pobrano żadnych migawek w chmurze, co wskazuje na warstwę jako mechanizm przenoszenia danych do chmury), co spowodowało, że we/wy były obsługiwane w chmurze. Istnieje odpowiedni szczyt na wykresie przepływności sieci w tym samym czasie, a większość ruchu sieciowego jest wychodzących do chmury.

![Przepustowość sieci dla danych 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Jeśli spojrzymy na wykres przepływności interfejsu Danych 1, inny interfejs sieciowy 1 GbE, który był tylko włączony iSCSI, to praktycznie nie było ruchu sieciowego w tym czasie.

![Przepustowość sieci dla danych 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Wykorzystanie procesora dla urządzenia
**Wykorzystanie procesora CPU** śledzi metryki związane z procesorem używanym na urządzeniu. Na poniższym wykresie przedstawiono statystyki wykorzystania procesora CPU dla urządzenia w produkcji.

![Wykorzystanie procesora dla urządzenia](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [korzystać z pulpitu nawigacyjnego urządzenia usługi StorSimple Device Manager](storsimple-device-dashboard.md).
* Dowiedz się, jak [zarządzać urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-manager-service-administration.md)

