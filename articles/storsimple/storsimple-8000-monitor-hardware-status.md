---
title: Komponenty sprzętowe i stan storSimple serii 8000 | Dokumenty firmy Microsoft
description: Dowiedz się, jak monitorować składniki sprzętowe urządzenia StorSimple za pośrednictwem usługi StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: a987239669e7437a179f5f24034f4dbe45535663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254744"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Usługa StorSimple Device Manager służy do monitorowania składników sprzętowych i stanu

## <a name="overview"></a>Omówienie
W tym artykule opisano różne składniki fizyczne i logiczne w urządzeniu z serii StorSimple 8000. Wyjaśniono również, jak monitorować stan składnika urządzenia przy użyciu **bloku kondycji stanu i sprzętu** w usłudze StorSimple Device Manager.

Stan **i kondycja sprzętu** bloku pokazuje stan sprzętu wszystkich składników urządzenia StorSimple.

Pod listą składników dla 8100 znajdują się trzy sekcje opisujące:

* **Współdzielone składniki** — nie są one częścią kontrolerów, takich jak dyski, obudowa, komponenty modułu zasilania i chłodzenia (PCM) oraz czujniki temperatury, napięcia linii i prądu PCM.
* **Składniki kontrolera 0** — składniki, które znajdują się na kontrolerze 0, takie jak kontroler, rozszerzacz i złącze SAS, czujniki temperatury kontrolera i różne interfejsy sieciowe.
* **Sterownik 1 Składniki** — składniki, które stanowią kontroler 1, podobne do tych szczegółowych dla kontrolera 0.

Urządzenie 8600 ma dodatkowe składniki, które odpowiadają obudowie Extended Bunch of Disks (EBOD). Pod listą komponentów znajduje się pięć sekcji. Wśród nich istnieją trzy sekcje, które zawierają komponenty w obudowie podstawowej i są identyczne z tymi opisanymi dla 8100. Istnieją dwie dodatkowe sekcje dla obudowy EBOD, które opisują:

* **Sterownik EBOD 0 Składniki** — komponenty, które znajdują się w obudowie EBOD 0, takie jak kontroler EBOD, ekspander i złącze SAS oraz czujniki temperatury kontrolera.
* **Sterownik EBOD 1 Components** — komponenty stanowiące obudowę EBOD 1, podobne do tych, które zostały wyszczególnione dla obudowy EBOD 0.
* **Współużytkowane składniki obudowy EBOD** — składniki obecne w obudowie EBOD i PCM, które nie są częścią kontrolera EBOD.

> [!NOTE]
> **Stan sprzętu nie jest dostępny dla urządzenia StorSimple Cloud Appliance (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Monitorowanie stanu sprzętu
Wykonaj następujące czynności, aby wyświetlić stan sprzętu składnika urządzenia:

1. Przejdź do **pozycji Urządzenia**, wybierz konkretne urządzenie StorSimple. Przejdź do **monitoruj > kondycję sprzętu**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Znajdź sekcję **Składniki sprzętowe** i wybierz jedną z dostępnych składników. Wystarczy kliknąć etykietę komponentu, aby rozwinąć listę i wyświetlić stan różnych składników urządzenia. Zobacz [szczegółową listę komponentów obudowy podstawowej](#component-list-for-primary-enclosure-of-storsimple-device) i [szczegółową listę komponentów obudowy EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Aby zinterpretować stan komponentu, należy użyć następującego schematu kodowania kolorów:
   
   * **Zielony check** - Oznacza składnik w dobrej kondycji o stanie **OK.**
   * **Żółty** — oznacza zdegradowany składnik w stanie **ostrzeżenie.**
   * **Czerwony wykrzyknik** — oznacza składnik nie powiodło się, który ma **stan Błąd.**
   * **Biały z czarnym tekstem** — oznacza składnik, który nie jest obecny.
   
   Poniższy zrzut ekranu przedstawia urządzenie, które ma składniki w **stanie OK**, **Ostrzeżenie**i **Błąd.**
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Rozwiń **listę składników udostępnionych**, widzimy, że NVRAM i klaster są zdegradowane.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Rozszerzając listę **składników kontrolera 1,** widzimy, że węzeł klastra nie powiódł się.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Jeśli napotkasz składnik, który nie jest w dobrej **kondycji,** skontaktuj się z pomocą techniczną firmy Microsoft. Jeśli alerty są włączone na urządzeniu, otrzymasz alert e-mail. Aby zastąpić nieudany składnik sprzętowy, zobacz [StorSimple wymiana składnika sprzętowego](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Lista komponentów dla obudowy podstawowej urządzenia StorSimple
W poniższej tabeli przedstawiono składniki fizyczne i logiczne zawarte w obudowy podstawowej (obecne zarówno w 8100, jak i 8600) lokalnego urządzenia StorSimple.

| Składnik | Moduł | Typ | Lokalizacja | Jednostka wymienna w terenie (FRU)? | Opis |
| --- | --- | --- | --- | --- | --- |
| Napęd w gnieździe [0-11] |Dyski |Fizyczne |Udostępnione |Tak |Jedna linia jest przedstawiona dla każdego dysku SSD lub hdd w obudowie podstawowej. |
| Czujnik temperatury otoczenia |Obudowa |Fizyczne |Udostępnione |Nie |Mierzy temperaturę w podwoziu. |
| Czujnik temperatury w środkowej płaszczyźnie |Obudowa |Fizyczne |Udostępnione |Nie |Mierzy temperaturę płaszczyzny środkowej. |
| Alarm dźwiękowy |Obudowa |Fizyczne |Udostępnione |Nie |Wskazuje, czy podsystem alarmu dźwiękowego w obudowie działa. |
| Obudowa |Obudowa |Fizyczne |Udostępnione |Tak |Wskazuje obecność podwozia. |
| Ustawienia obudowy |Obudowa |Fizyczne |Udostępnione |Nie |Odnosi się do przedniego panelu obudowy. |
| Czujniki napięcia liniowego |PCM |Fizyczne |Udostępnione |Nie |Wyświetlane są liczne czujniki napięcia liniowego, co wskazuje, czy zmierzone napięcie mieści się w granicach tolerancji. |
| Czujniki prądu liniowego |PCM |Fizyczne |Udostępnione |Nie |Wyświetlane są liczne czujniki prądu liniowego, co wskazuje, czy zmierzony prąd mieści się w granicach tolerancji. |
| Czujniki temperatury w PCM |PCM |Fizyczne |Udostępnione |Nie |Wyświetlane są liczne czujniki temperatury, takie jak czujniki Wlotu i Hotspotu, co wskazuje, czy zmierzona temperatura mieści się w granicach tolerancji. |
| Zasilanie [0-1] |PCM |Fizyczne |Udostępnione |Tak |Jedna linia jest przedstawiona dla każdego z zasilaczy w dwóch PCMs znajdujących się z tyłu urządzenia. |
| Chłodzenie [0-1] |PCM |Fizyczne |Udostępnione |Tak |Jedna linia jest przedstawiona dla każdego z czterech wentylatorów chłodzących zamieszkałych w dwóch PCM. |
| Bateria [0-1] |PCM |Fizyczne |Udostępnione |Tak |Jedna linia jest przedstawiona dla każdego z zapasowych modułów baterii, które znajdują się w PCM. |
| Metis |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Wyświetla stan baterii: czy wymagają ładowania i zbliżają się do końca eksploatacji. |
| Klaster |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Wyświetla stan klastra utworzonego między dwoma zintegrowanymi modułami kontrolera. |
| Węzeł klastra |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Wskazuje stan kontrolera jako część klastra. |
| Kworum klastra |Nie dotyczy |Logiczny | |Nie dotyczy |Wskazuje obecność większościowego członkostwa dysku w puli magazynu dysków twardych. |
| Przestrzeń danych HDD |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Miejsce do magazynowania używane dla danych w puli pamięci masowej dysku twardego (HDD). |
| Przestrzeń do zarządzania dyskami twardymi |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Miejsce zarezerwowane w puli pamięci masowej dysków twardych na zadania zarządzania. |
| Miejsce kworum HDD |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Miejsce zarezerwowane w puli magazynu dysku twardego dla kworum klastra. |
| Przestrzeń wymiany dysków twardych |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Miejsce zarezerwowane w puli pamięci masowej dysków twardych do wymiany kontrolera. |
| Przestrzeń danych SSD |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Miejsce do magazynowania używane dla danych w puli pamięci masowej dysków SSD. |
| Przestrzeń SSD NVRAM |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Miejsce do magazynowania w puli magazynu SSD, które jest przeznaczone dla logiki NVRAM. |
| Pula pamięci masowej na dysku twardym |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Wyświetla stan puli magazynu logicznego utworzonego na podstawie dysków twardych urządzeń. |
| Pula pamięci masowej SSD |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Wyświetla stan puli magazynu logicznego utworzonego na podstawie dysków SSD urządzenia. |
| Kontroler [0-1] [stan] |WE/WY |Fizyczne |Kontrolera |Tak |Wyświetla stan kontrolera oraz to, czy jest on w trybie aktywnym czy w trybie czuwania w obudowie. |
| Czujniki temperatury w sterowniku |WE/WY |Fizyczne |Kontrolera |Nie |Wyświetlane są liczne czujniki temperatury, takie jak moduł we/wy, temperatura procesora, czujniki DIMM i PCIe, co wskazuje, czy napotkana temperatura mieści się w granicach tolerancji. |
| Ekspander SAS |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan ekspandera SCSI (SAS), który służy do podłączenia zintegrowanej pamięci masowej do kontrolera. |
| Złącze SAS [0-1] |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan każdego łącznika sygnatury dostępu Współdzielonego, który jest używany do łączenia zintegrowanego magazynu z ekspanderem sygnatury dostępu Współdzielonego. |
| Połączenie międzysystemowe SBB w połowie płaszczyzny |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan złącza płaszczyzny środkowej, który służy do łączenia każdego kontrolera z płaszczyzną środkową. |
| Rdzeń procesora |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan rdzeni procesora w każdym kontrolerze. |
| Zasilanie elektroniki obudowy |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan systemu zasilania używanego przez obudowę. |
| Diagnostyka elektroniki obudowy |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan podsystemów diagnostycznych dostarczonych przez sterownik. |
| Kontroler zarządzania płytą główną (BMC) |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan kontrolera zarządzania płytą główną (BMC), który jest wyspecjalizowanym procesorem usług, który monitoruje urządzenie sprzętowe za pomocą czujników i komunikuje się z administratorem systemu za pośrednictwem niezależnego połączenia. |
| Ethernet |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan każdego z interfejsów sieciowych, czyli portów zarządzania i danych dostarczonych na kontrolerze. |
| Nvram |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan pamięci NVRAM, nieulotnej pamięci dostępu losowego, zapasowej przez baterię, która służy do przechowywania informacji o znaczeniu krytycznym dla aplikacji w przypadku awarii zasilania. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lista komponentów dla obudowy EBOD urządzenia StorSimple
W poniższej tabeli przedstawiono składniki fizyczne i logiczne zawarte w obudowie EBOD (obecne tylko w modelu 8600) lokalnego urządzenia StorSimple.

| Składnik | Moduł | Typ | Lokalizacja | Fru? | Opis |
| --- | --- | --- | --- | --- | --- |
| Napęd w gnieździe [0-11] |Dyski |Fizyczne |Udostępnione |Tak |Jedna linia jest przedstawiona dla każdego z dysków twardych z przodu obudowy EBOD. |
| Czujnik temperatury otoczenia |Obudowa |Fizyczne |Udostępnione |Nie |Mierzy temperaturę w podwoziu. |
| Czujnik temperatury w środkowej płaszczyźnie |Obudowa |Fizyczne |Udostępnione |Nie |Mierzy temperaturę płaszczyzny środkowej. |
| Alarm dźwiękowy |Obudowa |Fizyczne |Udostępnione |Nie |Wskazuje, czy podsystem alarmu dźwiękowego w obudowie działa. |
| Obudowa |Obudowa |Fizyczne |Udostępnione |Tak |Wskazuje obecność podwozia. |
| Ustawienia obudowy |Obudowa |Fizyczne |Udostępnione |Nie |Odnosi się do ops lub przedniego panelu obudowy. |
| Czujniki napięcia liniowego |PCM |Fizyczne |Udostępnione |Nie |Wyświetlane są liczne czujniki napięcia liniowego, co wskazuje, czy zmierzone napięcie mieści się w granicach tolerancji. |
| Czujniki prądu liniowego |PCM |Fizyczne |Udostępnione |Nie |Wyświetlane są liczne czujniki prądu liniowego, co wskazuje, czy zmierzony prąd mieści się w granicach tolerancji. |
| Czujniki temperatury w PCM |PCM |Fizyczne |Udostępnione |Nie |Wyświetlane są liczne czujniki temperatury, takie jak czujniki Wlotu i Hotspotu, co wskazuje, czy zmierzona temperatura mieści się w granicach tolerancji. |
| Zasilanie [0-1] |PCM |Fizyczne |Udostępnione |Tak |Jedna linia jest przedstawiona dla każdego z zasilaczy w dwóch PCMs znajdujących się z tyłu urządzenia. |
| Chłodzenie [0-1] |PCM |Fizyczne |Udostępnione |Tak |Jedna linia jest przedstawiona dla każdego z czterech wentylatorów chłodzących zamieszkałych w dwóch PCM. |
| Pamięć lokalna [HDD] |Nie dotyczy |Logiczny |Udostępnione |Nie dotyczy |Wyświetla stan puli magazynu logicznego utworzonego na podstawie dysków twardych urządzeń. |
| Kontroler [0-1] [stan] |WE/WY |Fizyczne |Kontrolera |Tak |Wyświetla stan kontrolerów w module EBOD. |
| Czujniki temperatury w EBOD |WE/WY |Fizyczne |Kontrolera |Nie |Wyświetlane są liczne czujniki temperatury z każdego sterownika, co wskazuje, czy napotkana temperatura mieści się w granicach tolerancji. |
| Ekspander SAS |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan ekspandera sygnatury dostępu Współdzielonego, który jest używany do łączenia zintegrowanej pamięci masowej z kontrolerem. |
| Złącze SAS [0-2] |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan każdego łącznika sygnatury dostępu Współdzielonego, który jest używany do łączenia zintegrowanego magazynu z ekspanderem sygnatury dostępu Współdzielonego. |
| Połączenie międzysystemowe SBB w połowie płaszczyzny |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan złącza płaszczyzny środkowej, który służy do łączenia każdego kontrolera z płaszczyzną środkową. |
| Zasilanie elektroniki obudowy |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan systemu zasilania używanego przez obudowę. |
| Diagnostyka elektroniki obudowy |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan podsystemów diagnostycznych dostarczonych przez sterownik. |
| Podłączenie do kontrolera urządzenia |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan połączenia między modułem We/Wy EBOD a kontrolerem urządzenia. |

## <a name="next-steps"></a>Następne kroki
* Aby administrować urządzeniem za pomocą usługi StorSimple Device Manager, przejdź do [administrowania urządzeniem StorSimple Device Manager za pomocą usługi StorSimple](storsimple-8000-manager-service-administration.md).
* Jeśli chcesz rozwiązać problem ze składnikiem urządzenia, który ma stan awaryjny lub nie uległ awarii, zapoznaj się ze [wskaźnikami monitorowania StorSimple](storsimple-monitoring-indicators.md).
* Aby zastąpić nieudany składnik sprzętowy, zobacz [StorSimple wymiana składnika sprzętowego](storsimple-hardware-component-replacement.md).
* Jeśli nadal występują problemy z urządzeniem, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

