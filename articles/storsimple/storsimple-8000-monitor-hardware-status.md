---
title: Składniki sprzętowe serii StorSimple 8000 i stan | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować składniki sprzętowe w urządzeniu StorSimple przy użyciu usługi Menedżer urządzeń StorSimple.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632868"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Usługa Menedżer urządzeń StorSimple składniki sprzętowe monitora i stanu

## <a name="overview"></a>Omówienie
W tym artykule opisano różne składniki fizyczne i logiczne na urządzeniu serii StorSimple 8000 w środowisku lokalnym. Opisano również sposób monitorować stan składnika urządzenia za pomocą **stan i kondycja sprzętu** bloku usługi Menedżer urządzeń StorSimple.

**Stan i kondycja sprzętu** bloku wyświetlany jest stan sprzętu wszystkie składniki urządzenia StorSimple.

W obszarze listy składników 8100 istnieją trzy sekcje, które opisują:

* **Udostępnione składniki** — nie są częścią kontrolerów, takich jak stacje dysków, obudowy, składniki zasilania i chłodzenia modułu PCM () i temperatury PCM napięcia linii i czujniki bieżącego wiersza.
* **Składniki kontrolera 0** — składniki, które znajdują się na kontrolerze 0, takich jak kontroler, funkcja SAS expander i łącznika, czujnikami temperatury kontrolera i różnych interfejsów sieciowych.
* **Składniki kontrolera 1** — składników wchodzących w skład kontrolera 1, podobne do tych szczegółowe dla kontrolera 0.

Do urządzenia 8600 ma dodatkowe składniki, które odpowiadają obudowy rozszerzone wiele z dysków (EBOD). W obszarze lista składników istnieją pięciu sekcji. Z tych opcji istnieją trzy sekcje, które zawierają składniki w obudowie podstawowy i są takie same jak te opisane dla 8100. Istnieją dwa dodatkowe sekcje dla obudowy EBOD, które opisują:

* **Składniki kontrolera EBOD 0** — składniki, które znajdują się w obudowie EBOD 0, takich jak kontrolera EBOD SAS expander i łącznika, a także kontrolera czujników temperatury.
* **Składniki 1 kontrolera EBOD** — składników wchodzących w skład obudowy EBOD 1, podobne do tych szczegółowe dla obudowy EBOD 0.
* **Składniki udostępnione obudowy EBOD** — składniki obecne w obudowie EBOD i PCM, które nie są częścią kontrolera EBOD.

> [!NOTE]
> **Stan sprzętu nie jest dostępne dla urządzenia StorSimple w chmurze (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Monitor stanu sprzętu
Wykonaj poniższe kroki, aby wyświetlić stan sprzętu składnik urządzenia:

1. Przejdź do **urządzeń**, wybrać konkretne urządzenie StorSimple. Przejdź do **Monitor > kondycja sprzętu**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Znajdź **składniki sprzętowe** sekcji i wybierz jedną z dostępnych składnikach. Po prostu kliknij etykietę składnika, aby rozwinąć listę i wyświetlić stan różnych składników urządzenia. Zobacz [lista składników szczegółowe dla podstawowego obudowy](#component-list-for-primary-enclosure-of-storsimple-device) i [listy szczegółowe części obudowy EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Poniższy schemat kodowania kolorami umożliwia interpretować stan składnika:
   
   * **Kolor zielony wyboru** — oznacza dobrej kondycji składnika za pomocą **OK** stanu.
   * **Żółty** — oznacza o obniżonym poziomie składnika na liście **ostrzeżenie** stanu.
   * **Czerwonego wykrzyknika** — Denotes nie powiodło się składnik, który ma **błąd** stanu.
   * **Biały na czarny tekst** — oznacza składnik, który nie jest obecny.
   
   Poniższy zrzut ekranu przedstawia urządzenia, zawierającej składniki w **OK**, **ostrzeżenie**, i **błąd** stanu.
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Rozwijanie **listy składniki współużytkowane**, możemy zobaczyć uszkodzenie pamięci NVRAM i klastra.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Rozwijanie **składniki kontrolera 1** listy, widzimy, że węzeł klastra nie powiodło się.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Jeśli napotkasz składnik, który nie znajduje się w **dobra kondycja** stanie, skontaktuj się z Microsoft Support. Alerty są włączone na urządzeniu, otrzymasz wiadomość e-mail z alertem. Jeśli trzeba wymienić składnik sprzętowy nie powiodło się, zobacz [wymiana składników sprzętu StorSimple](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Lista składników dla podstawowego Obudowa urządzenia StorSimple
W poniższej tabeli przedstawiono składniki fizyczne i logiczne, znajdujących się w obudowie głównej (istnieje zarówno w 8100 i 8600) urządzenia StorSimple w środowisku lokalnym.

| Składnik | Moduł | Type | Lokalizacja | Pole (jednostkę FRU replaceable unit)? | Opis |
| --- | --- | --- | --- | --- | --- |
| Napęd w gnieździe [0-11] |Stacje dysków |Fizyczne |Udostępniona |Yes |Jeden wiersz jest prezentowana dla każdego z dysków SSD i dysków HDD, w obudowie podstawowego. |
| Czujnik temperatury otoczenia |Obudowy |Fizyczne |Udostępniona |Nie |Mierzy temperatury w obudowie. |
| Czujnik temperatury środkowa |Obudowy |Fizyczne |Udostępniona |Nie |Mierzy temperatura środkowa. |
| Alarm dźwiękowy |Obudowy |Fizyczne |Udostępniona |Nie |Wskazuje, czy w podsystemie alarmu w obudowie funkcjonalności. |
| Obudowy |Obudowy |Fizyczne |Udostępniona |Yes |Wskazuje obecność podstawę. |
| Ustawienia obudowy |Obudowy |Fizyczne |Udostępniona |Nie |Odnosi się do panelu przedniego obudowy. |
| Czujnikami napięcia linii |MODUŁU PCM |Fizyczne |Udostępniona |Nie |Liczne czujnikami napięcia linii mają ich stan, który wskazuje, czy mierzonego napięcia mieści się w granicach. |
| Bieżący czujniki linii |MODUŁU PCM |Fizyczne |Udostępniona |Nie |Wiele czujników bieżący wiersz ma ich stan, który wskazuje, czy bieżący mierzonego w granicach tolerancji. |
| Czujnikami temperatury w module PCM |MODUŁU PCM |Fizyczne |Udostępniona |Nie |Wiele czujników temperatury wlotu np. informacji o hotspotach czujników się ich stanu wyświetlane, wskazującą, czy temperatura mierzone jest w granicach tolerancji. |
| Zasilacz [0-1] |MODUŁU PCM |Fizyczne |Udostępniona |Yes |Jeden wiersz jest prezentowana dla każdego zasilacze w dwóch PCMs, znajduje się tyłu na urządzeniu. |
| Chłodzenie [0-1] |MODUŁU PCM |Fizyczne |Udostępniona |Yes |Dla każdego z czterech wentylatory, znajdującej się w dwóch PCMs znajduje się jeden wiersz. |
| Bateria [0-1] |MODUŁU PCM |Fizyczne |Udostępniona |Yes |Dla każdego z modułów baterii zapasowej, które są umieszczone w module PCM znajduje się jeden wiersz. |
| Metis |ND |Logiczny |Udostępniona |ND |Wyświetla stan baterii: czy potrzebujesz, ładowania i zbliża się wycofanych z eksploatacji. |
| Klaster |ND |Logiczny |Udostępniona |ND |Wyświetla stan klastra, który jest tworzony między dwoma modułami zintegrowanego kontrolera. |
| Węzeł klastra |ND |Logiczny |Udostępniona |ND |Wskazuje stan kontrolera jako część klastra. |
| Kworum klastra |ND |Logiczny | |ND |Wskazuje obecność dysku większość należeć pula magazynów dysków Twardych. |
| Obszar danych dysku |ND |Logiczny |Udostępniona |ND |Miejsce do magazynowania służy do danych w puli magazynu na dysku twardym (HDD). |
| Obszar zarządzania dysku |ND |Logiczny |Udostępniona |ND |Miejsce zarezerwowane w puli magazynów dysku twardego dla zadań zarządzania. |
| Obszar kworum |ND |Logiczny |Udostępniona |ND |Miejsce zarezerwowane w puli magazynów dysku dla kworum klastra. |
| Obszar zamiany dysku twardego |ND |Logiczny |Udostępniona |ND |Miejsce zarezerwowane w puli magazynów dysków Twardych do zastąpienia kontrolera. |
| Obszar danych dysku SSD |ND |Logiczny |Udostępniona |ND |Używane miejsce w magazynie danych w puli magazynów pełny stan dysku (SSD). |
| SSD NVRAM space |ND |Logiczny |Udostępniona |ND |Miejsca do magazynowania w puli magazynów dysków SSD, które są przeznaczone do logiki NVRAM. |
| Pula magazynów dysków Twardych |ND |Logiczny |Udostępniona |ND |Wyświetla stan puli magazynu logicznego, która jest tworzona z urządzenia dysków twardych. |
| Pula magazynów dysków SSD |ND |Logiczny |Udostępniona |ND |Wyświetla stan puli magazynu logicznego, która jest tworzona na urządzeniu SSD. |
| Kontroler [0-1] [Stan] |OPERACJE WE/WY |Fizyczne |Kontroler |Yes |Wyświetla stan kontrolera, oraz czy jest w trybie aktywny lub wstrzymania w obudowie. |
| Czujnikami temperatury w kontrolerze |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wiele czujników temperatury, takich jak moduł we/wy, temperatury procesora CPU, czujniki DIMM i PCIe ma ich stan, który wskazuje, czy temperatura napotkano mieści się w granicach. |
| Funkcja SAS expander |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan serial ekspander SCSI (SAS) dołączonego, który jest używany do łączenia z magazynu zintegrowanego z kontrolerem. |
| Złącze SAS [0-1] |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan każdego łącznika sygnatury dostępu Współdzielonego, który jest używany do łączą magazyn zintegrowanego z funkcją SAS expander. |
| Połączenie warstwy pośredniej SBB |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan łącznika środkowa, który jest używany do łączenia każdego kontrolera do warstwy pośredniej. |
| Rdzeń procesora |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan rdzeni procesora w ramach każdego kontrolera. |
| Zasilanie elementów elektronicznych obudowy |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan systemu zasilania, używanego przez obudowa. |
| Diagnostyka elementów elektronicznych obudowy |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan podsystemów diagnostyki, dostarczone przez kontroler. |
| Kontroler zarządzania płytą główną (BMC) |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan kontrolera zarządzania płytą główną (BMC), który jest procesor specjalistycznych usług, który monitoruje urządzenia sprzętowego za pośrednictwem czujników i komunikuje się z administratorem systemu, niezależne połączenie. |
| Ethernet |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan wszystkich interfejsów sieciowych, oznacza to, zarządzania i porty danych podany w kontrolerze. |
| NVRAM |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan pamięci NVRAM, trwałej pamięci objęta baterii, która służy do przechowywania informacji o aplikacji o znaczeniu krytycznym w przypadku awarii zasilania. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lista składników dla EBOD Obudowa urządzenia StorSimple
W poniższej tabeli przedstawiono składniki fizyczne i logiczne, znajdujących się w obudowie EBOD (istnieje tylko w modelu 8600) urządzenia StorSimple w środowisku lokalnym.

| Składnik | Moduł | Type | Lokalizacja | JEDNOSTKI FRU? | Opis |
| --- | --- | --- | --- | --- | --- |
| Napęd w gnieździe [0-11] |Stacje dysków |Fizyczne |Udostępniona |Yes |Jeden wiersz jest prezentowana dla każdego z stacje dysków Twardych uzyskać obudowy EBOD. |
| Czujnik temperatury otoczenia |Obudowy |Fizyczne |Udostępniona |Nie |Mierzy temperatury w obudowie. |
| Czujnik temperatury środkowa |Obudowy |Fizyczne |Udostępniona |Nie |Mierzy temperatura środkowa. |
| Alarm dźwiękowy |Obudowy |Fizyczne |Udostępniona |Nie |Wskazuje, czy w podsystemie alarmu w obudowie funkcjonalności. |
| Obudowy |Obudowy |Fizyczne |Udostępniona |Yes |Wskazuje obecność podstawę. |
| Ustawienia obudowy |Obudowy |Fizyczne |Udostępniona |Nie |Odnosi się do platformy OPS lub z panelu przedniego obudowy. |
| Czujnikami napięcia linii |MODUŁU PCM |Fizyczne |Udostępniona |Nie |Liczne czujnikami napięcia linii mają ich stan, który wskazuje, czy mierzonego napięcia mieści się w granicach. |
| Bieżący czujniki linii |MODUŁU PCM |Fizyczne |Udostępniona |Nie |Wiele czujników bieżący wiersz ma ich stan, który wskazuje, czy bieżący mierzonego w granicach tolerancji. |
| Czujnikami temperatury w module PCM |MODUŁU PCM |Fizyczne |Udostępniona |Nie |Wiele czujników temperatury wlotu np. informacji o hotspotach czujników się ich stan, który wskazuje, czy temperatura mierzonego w granicach tolerancji. |
| Zasilacz [0-1] |MODUŁU PCM |Fizyczne |Udostępniona |Yes |Jeden wiersz jest prezentowana dla każdego zasilacze w dwóch PCMs, znajduje się tyłu na urządzeniu. |
| Chłodzenie [0-1] |MODUŁU PCM |Fizyczne |Udostępniona |Yes |Dla każdego z czterech wentylatory, znajdującej się w dwóch PCMs znajduje się jeden wiersz. |
| Lokalny magazyn [HDD] |ND |Logiczny |Udostępniona |ND |Wyświetla stan puli magazynu logicznego, która jest tworzona z urządzenia dysków twardych. |
| Kontroler [0-1] [Stan] |OPERACJE WE/WY |Fizyczne |Kontroler |Yes |Wyświetla stan kontrolerów w EBOD module. |
| Czujnikami temperatury w EBOD |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wiele czujników temperatury z każdy kontroler ma ich stan, który wskazuje, czy temperatura napotkano mieści się w granicach. |
| Funkcja SAS expander |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan funkcją SAS expander, który jest używany do łączenia z magazynu zintegrowanego z kontrolerem. |
| Złącze SAS [0-2] |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan każdego łącznika sygnatury dostępu Współdzielonego, który jest używany do łączą magazyn zintegrowanego z funkcją SAS expander. |
| Połączenie warstwy pośredniej SBB |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan łącznika środkowa, który jest używany do łączenia każdego kontrolera do warstwy pośredniej. |
| Zasilanie elementów elektronicznych obudowy |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan systemu zasilania, używanego przez obudowa. |
| Diagnostyka elementów elektronicznych obudowy |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan podsystemów diagnostyki, dostarczone przez kontroler. |
| Połączenie z kontrolerem urządzeń |OPERACJE WE/WY |Fizyczne |Kontroler |Nie |Wskazuje stan połączenia między modułem EBOD operacji We/Wy i z kontrolerem urządzenia. |

## <a name="next-steps"></a>Kolejne kroki
* Aby korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem, przejdź do [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).
* Jeśli trzeba rozwiązać składnik urządzenia, który ma nieprawidłowe działanie lub uszkodzenie stan, zapoznaj się [StorSimple wskaźniki monitorowania](storsimple-monitoring-indicators.md).
* Aby wymienić składnik sprzętowy nie powiodło się, zobacz [wymiana składników sprzętu StorSimple](storsimple-hardware-component-replacement.md).
* Jeśli nadal występują problemy dotyczące urządzeń, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).

