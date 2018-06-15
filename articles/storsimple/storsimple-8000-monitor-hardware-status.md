---
title: Składniki sprzętowe serii StorSimple 8000 i stan | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować składniki sprzętowe urządzenia StorSimple przez usługę Menedżer StorSimple urządzenia.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 90724099842eac513c39dccf113ad1c0a63983f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875956"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Korzystanie z usługi Menedżera urządzeń StorSimple monitorem składniki sprzętowe i stanu
## <a name="overview"></a>Omówienie
W tym artykule opisano różne składniki fizyczne i logiczne w urządzeniu serii StorSimple 8000 lokalnymi. Wyjaśniono również sposób monitorować stan składnika urządzenia za pomocą **sprzętu i stan kondycji** bloku w usłudze Menedżer StorSimple urządzenia.

**Sprzętu i stan kondycji** bloku pokazuje stan sprzętu wszystkie składniki urządzenia StorSimple.

Na liście składników 8100 istnieją trzy sekcje, które opisują:

* **Udostępniane składniki** — te nie są częścią kontrolerów, takie jak stacje dysków, obudowy, składniki PCM temperatury PCM napięcia wiersz i czujników bieżącego wiersza.
* **Składniki kontrolera 0** — składniki, które znajdują się na kontrolerze 0, takie jak kontroler, expander sygnatury dostępu Współdzielonego i łącznika czujnikami temperatury kontrolera i różnych interfejsów sieciowych.
* **Składniki kontrolera 1** — składników, które stanowią kontrolera 1 podobne do tych szczegółowe dla kontrolera 0.

Urządzenia 8600 ma dodatkowe składniki, które odpowiadają obudowa rozszerzony Bunch dla dysków (EBOD). Na liście składników istnieją pięciu sekcji. Z nich istnieją trzy sekcje, które zawierają składniki w obudowie podstawowego i są takie same, jak firma opisana dla 8100. Istnieją dwa dodatkowe sekcje dla obudowa EBOD opisujące:

* **Składniki EBOD kontrolera 0** — składniki, które znajdują się w obudowie EBOD 0, takich jak kontroler EBOD czujnikami temperatury expander i łącznika i kontrolera SAS.
* **Składniki 1 kontrolera EBOD** — składników, które stanowią EBOD obudowa 1 podobne do tych szczegółowych dla EBOD obudowa 0.
* **Składniki udostępnione obudowy EBOD** — składniki znajdujących się w EBOD obudowy i PCM, które nie są częścią EBOD kontrolera.

> [!NOTE]
> **Stan sprzętu nie jest dostępna dla urządzenia StorSimple w chmurze (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Monitor stanu sprzętu
Wykonaj poniższe kroki, aby wyświetlić stan sprzętu składnika urządzenia:

1. Przejdź do **urządzeń**, wybierz określonego urządzenia StorSimple. Przejdź do **Monitor > kondycji sprzętu**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Zlokalizuj **składniki sprzętowe** sekcji i wybierz jedną z dostępnych składników. Po prostu kliknij etykietę składnika rozwiń listę i wyświetlanie stanu różnych składników urządzenia. Zobacz [listę składników szczegółowe dla podstawowego obudowa](#component-list-for-primary-enclosure-of-storsimple-device) i [listy szczegółowe części obudowy EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Interpretować stan składnika przy użyciu następujących schemat kodowania kolorów:
   
   * **Zielone wyboru** — oznacza składnik dobrej kondycji z **OK** stanu.
   * **Żółty** — oznacza składnik obniżeniem **ostrzeżenie** stanu.
   * **Czerwony wykrzyknik** — Denotes nie powiodło się składnika, który **błąd** stanu.
   * **Białe czarny tekst** — określa składnik, który nie jest obecny.
   
   Poniższy zrzut ekranu przedstawia urządzenie, które zawiera składniki w **OK**, **ostrzeżenie**, i **błąd** stanu.
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Rozszerzanie **listy składników Shared**, zobaczysz ograniczone NVRAM i klastra.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Rozszerzanie **składniki kontrolera 1** listy, zobaczysz, że węzeł klastra nie powiodła się.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Jeśli wystąpią składnik, który nie znajduje się w **dobra kondycja** stanu, skontaktuj się z Microsoft Support. Alerty są włączone na urządzeniu, otrzymasz alert e-mail. Jeśli trzeba wymienić składniki sprzętowe nie powiodło się, zobacz [wymiana składników sprzętowych StorSimple](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Lista składników dla podstawowego Obudowa urządzenia StorSimple
W poniższej tabeli przedstawiono składniki fizyczne i logiczne zawartych w obudowie głównej (istnieje zarówno w 8100 i 8600) lokalnego urządzenia StorSimple.

| Składnik | Moduł | Typ | Lokalizacja | Pole (jednostkę FRU replaceable unit)? | Opis |
| --- | --- | --- | --- | --- | --- |
| Dysk w gnieździe [0-11] |Stacje dysków |Fizyczne |Udostępniona |Tak |Jeden wiersz jest widoczne dla każdego z dysków SSD i dysków HDD w obudowie podstawowego. |
| Czujnik temperatury otoczenia |Obudowa |Fizyczne |Udostępniona |Nie |Mierzy temperatury w obudowie. |
| Czujnik temperatury środkowa |Obudowa |Fizyczne |Udostępniona |Nie |Mierzy temperatury środkowa. |
| Alarmu |Obudowa |Fizyczne |Udostępniona |Nie |Wskazuje, czy w podsystemie alarmu w obudowie funkcjonalności. |
| Obudowa |Obudowa |Fizyczne |Udostępniona |Tak |Wskazuje na obecność podstawę. |
| Obudowa ustawienia |Obudowa |Fizyczne |Udostępniona |Nie |Odwołanie do panelu przedniego obudowy. |
| Wiersz czujnikami napięcia |PCM |Fizyczne |Udostępniona |Nie |Wiele czujnikami napięcia wiersz ma ich stanie wyświetlony, wskazuje, czy napięcia mierzoną w granicach tolerancji. |
| Czujniki bieżącego wiersza |PCM |Fizyczne |Udostępniona |Nie |Wiele czujników bieżący wiersz ma ich stanie wyświetlony, wskazuje, czy bieżący mierzoną w granicach tolerancji. |
| Czujnikami temperatury w PCM |PCM |Fizyczne |Udostępniona |Nie |Wiele czujnikami temperatury, takich jak Inlet i czujników punkt aktywny mają ich stanie wyświetlony, wskazującą, czy zmierzone temperatury mieści się w granicach. |
| Zasilacz [0-1] |PCM |Fizyczne |Udostępniona |Tak |Jeden wiersz jest widoczne dla każdego zasilacze w dwóch PCMs, znajduje się tyłu na urządzeniu. |
| Chłodzenia [0-1] |PCM |Fizyczne |Udostępniona |Tak |Dla każdego z czterech wentylatory znajdującej się w dwóch PCMs zobaczy jeden wiersz. |
| Baterii [0-1] |PCM |Fizyczne |Udostępniona |Tak |Jeden wiersz, jest widoczne dla każdego z modułów baterii kopii zapasowej, które są umieszczone w PCM. |
| Metis |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Wyświetla stan baterie: Określa, czy należy ładowania i zbliża się koniec życia. |
| Klaster |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Wyświetla stan klastra, który jest tworzony między dwa moduły zintegrowanego kontrolera. |
| Węzeł klastra |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Wskazuje stan kontrolera jako część klastra. |
| Kworum klastra |Nie dotyczy |Logiczne | |Nie dotyczy |Wskazuje na obecność dysku większość członkostwa w puli magazynów dysku twardego. |
| Miejsce na dysku twardego danych |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Miejsce do magazynowania służy do danych w puli magazynu na dysku twardym (HDD). |
| Miejsce na dysku twardego zarządzania |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Miejsce zarezerwowane w puli magazynów dysku twardego dla zadań zarządzania. |
| Miejsce na dysku kworum |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Miejsce zarezerwowane w puli magazynów dysku dla kworum klastra. |
| Dysk twardy zamiany miejsca |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Miejsce zarezerwowane w puli magazynu dysk twardy do zastąpienia kontrolera. |
| Miejsca na dysku SSD danych |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Używanego miejsca w magazynie danych w puli magazynu półprzewodnikowych (SSD) dysku. |
| NVRAM dysków SSD miejsca |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Miejsca do magazynowania w puli pamięci masowej SSD przeznaczona do obsługi logiki NVRAM. |
| Dysk twardy puli magazynu |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Wyświetla stan puli magazynu logicznego, która jest tworzona z urządzenia dysków twardych. |
| Puli pamięci masowej SSD |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Wyświetla stan puli magazynu logicznego, która jest tworzona z urządzenia dyski SSD. |
| Kontroler [0-1] [state] |WE/WY |Fizyczne |Kontrolera |Tak |Wyświetla stan kontrolera, oraz czy jest w trybie aktywny lub wstrzymania w obudowie. |
| Czujnikami temperatury w kontrolerze |WE/WY |Fizyczne |Kontrolera |Nie |Wiele czujnikami temperatury, takich jak moduł we/wy, temperatury procesora CPU, czujników DIMM i PCIe ma ich stanie wyświetlony, wskazuje, czy temperatury napotkano mieści się w granicach. |
| SAS expander |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan serial expander SCSI (SAS) podłączone, który jest używany do nawiązania połączenia z kontrolerem zintegrowanej pamięci masowej. |
| Łącznik SAS [0-1] |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan poszczególnych łączników sygnatury dostępu Współdzielonego, który jest używany do nawiązania zintegrowanej pamięci masowej SAS expander. |
| Między połączeniami wzajemnymi SBB środkowa |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan łącznika środkowa, który jest używany do nawiązania połączenia środkowa każdego kontrolera. |
| Rdzeń procesora |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan rdzeni procesora w ramach każdego kontrolera. |
| Obudowa electronics zasilania |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan zasilania systemu obudowa. |
| Obudowa electronics diagnostyki |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan podsystemów diagnostyki udostępniane przez kontroler. |
| Kontroler zarządzania płytą główną (BMC) |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan kontrolera zarządzania płytą główną (BMC), który jest procesor specjalne usług, który monitoruje urządzenia sprzętowego przez czujniki i komunikuje się z administratorem systemu za pośrednictwem połączenia niezależne. |
| Ethernet |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan wszystkich interfejsów sieciowych, oznacza to, zarządzania i porty dane podane w kontrolerze. |
| NVRAM |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan NVRAM, trwałej pamięci baterii, która służy do przechowywania informacji o aplikacji o znaczeniu krytycznym w przypadku awarii zasilania kopię zapasową. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lista składników dla obudowa EBOD urządzenia StorSimple
W poniższej tabeli przedstawiono składniki fizyczne i logiczne zawartych w obudowie EBOD (istnieje tylko w modelu 8600) lokalnego urządzenia StorSimple.

| Składnik | Moduł | Typ | Lokalizacja | JEDNOSTKI FRU? | Opis |
| --- | --- | --- | --- | --- | --- |
| Dysk w gnieździe [0-11] |Stacje dysków |Fizyczne |Udostępniona |Tak |Dla poszczególnych dysków HDD przodu obudowy EBOD zobaczy jeden wiersz. |
| Czujnik temperatury otoczenia |Obudowa |Fizyczne |Udostępniona |Nie |Mierzy temperatury w obudowie. |
| Czujnik temperatury środkowa |Obudowa |Fizyczne |Udostępniona |Nie |Mierzy temperatury środkowa. |
| Alarmu |Obudowa |Fizyczne |Udostępniona |Nie |Wskazuje, czy w podsystemie alarmu w obudowie funkcjonalności. |
| Obudowa |Obudowa |Fizyczne |Udostępniona |Tak |Wskazuje na obecność podstawę. |
| Obudowa ustawienia |Obudowa |Fizyczne |Udostępniona |Nie |Odnosi się do OPS lub z panelu przedniego obudowy. |
| Wiersz czujnikami napięcia |PCM |Fizyczne |Udostępniona |Nie |Wiele czujnikami napięcia wiersz ma ich stanie wyświetlony, wskazuje, czy napięcia mierzoną w granicach tolerancji. |
| Czujniki bieżącego wiersza |PCM |Fizyczne |Udostępniona |Nie |Wiele czujników bieżący wiersz ma ich stanie wyświetlony, wskazuje, czy bieżący mierzoną w granicach tolerancji. |
| Czujnikami temperatury w PCM |PCM |Fizyczne |Udostępniona |Nie |Wiele czujnikami temperatury, takich jak Inlet i czujników punkt aktywny mają ich stanie wyświetlony, wskazuje, czy temperatury mierzoną w granicach tolerancji. |
| Zasilacz [0-1] |PCM |Fizyczne |Udostępniona |Tak |Jeden wiersz jest widoczne dla każdego zasilacze w dwóch PCMs, znajduje się tyłu na urządzeniu. |
| Chłodzenia [0-1] |PCM |Fizyczne |Udostępniona |Tak |Dla każdego z czterech wentylatory znajdującej się w dwóch PCMs zobaczy jeden wiersz. |
| Lokalny magazyn [dysk twardy] |Nie dotyczy |Logiczne |Udostępniona |Nie dotyczy |Wyświetla stan puli magazynu logicznego, która jest tworzona z urządzenia dysków twardych. |
| Kontroler [0-1] [state] |WE/WY |Fizyczne |Kontrolera |Tak |Wyświetla stan kontrolerów w EBOD module. |
| Czujnikami temperatury w EBOD |WE/WY |Fizyczne |Kontrolera |Nie |Wiele czujnikami temperatury przez każdy z kontrolerów ma ich stanie wyświetlony, wskazuje, czy temperatury napotkano mieści się w granicach. |
| SAS expander |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan expander sygnatury dostępu Współdzielonego, który jest używany do nawiązania połączenia z kontrolerem zintegrowanej pamięci masowej. |
| Łącznik SAS [0-2] |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan poszczególnych łączników sygnatury dostępu Współdzielonego, który jest używany do nawiązania zintegrowanej pamięci masowej SAS expander. |
| Między połączeniami wzajemnymi SBB środkowa |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan łącznika środkowa, który jest używany do nawiązania połączenia środkowa każdego kontrolera. |
| Obudowa electronics zasilania |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan zasilania systemu obudowa. |
| Obudowa electronics diagnostyki |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan podsystemów diagnostyki udostępniane przez kontroler. |
| Połączenie do urządzenia kontrolera |WE/WY |Fizyczne |Kontrolera |Nie |Wskazuje stan połączenia między modułem EBOD We/Wy i kontrolerów urządzeń. |

## <a name="next-steps"></a>Następne kroki
* Aby korzystać z usługi Menedżer StorSimple urządzenia do administrowania urządzenia, przejdź do [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).
* Rozwiązywać składnik urządzenia o stanie działanie lub uszkodzenie należy odwoływać się do [StorSimple wskaźniki monitorowania](storsimple-monitoring-indicators.md).
* Aby zastąpić składnik sprzętowy nie powiodło się, zobacz [wymiana składników sprzętowych StorSimple](storsimple-hardware-component-replacement.md).
* Jeśli nadal występują problemy dotyczące urządzenia, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).

