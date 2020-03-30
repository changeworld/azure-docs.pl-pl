---
title: Instalowanie urządzenia Microsoft Azure StorSimple 8600
description: W tym artykule opisano sposób rozpakowania, montażu w stelażu i kabla urządzenia StorSimple 8600 przed wdrożeniem i skonfigurowaniem oprogramowania.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254757"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Rozpakuj, montuj stelaż i przekakowo urządzenie StorSimple 8600

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie
Usługa Microsoft Azure StorSimple 8600 jest urządzeniem z dwiema obudową i składa się z obudowy podstawowej i EBOD. W tym samouczku wyjaśniono, jak rozpakować, zamontować w stelażu i podłączyć sprzęt urządzenia StorSimple 8600 przed skonfigurowaniem oprogramowania StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Rozpakuj urządzenie StorSimple 8600
Poniższe kroki zawierają jasne, szczegółowe instrukcje dotyczące rozpakowania urządzenia magazynującego StorSimple 8600. To urządzenie jest dostarczane w dwóch polach, jeden dla obudowy podstawowej, a drugi dla obudowy EBOD. Te dwa pola są następnie umieszczane w jednym pudełku.

### <a name="prepare-to-unpack-your-device"></a>Przygotowanie do rozpakowania urządzenia
Przed rozpakowaniem urządzenia zapoznaj się z poniższymi informacjami.

![Ikona](./media/storsimple-safety/IC740879.png)![ostrzeżenia](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) o wadze ciężkiej **UWAGA!**

1. Upewnij się, że masz dwie osoby dostępne do zarządzania wagą urządzenia, jeśli obsługujesz je ręcznie. W pełni skonfigurowana obudowa może ważyć do 32 kg.).
2. Umieść pudełko na płaskiej, poziomej powierzchni.

Następnie wykonaj następujące czynności, aby rozpakować urządzenie.

#### <a name="to-unpack-your-device"></a>Aby rozpakować urządzenie
1. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. Skontaktuj [się z pomocą techniczną firmy Microsoft,](storsimple-8000-contact-microsoft-support.md) aby pomóc ci ocenić, czy urządzenie jest w dobrym stanie.
2. Otwórz pole zewnętrzne, a następnie wyjmij dwa pola odpowiadające obudowom podstawowym i EBOD. Teraz można rozpakować obudowy podstawowe i EBOD. Na poniższej ilustracji przedstawiono rozpakowany widok jednej z obudów.
   
    ![Rozpakuj urządzenie pamięci masowej](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Rozpakowany widok urządzenia pamięci masowej**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Pudełko na opakowania |
   |   2 |Kable SAS (w akcesoriach i zasobniku na kable) |
   |   3 |Pianka dolna |
   |   4 |Urządzenie |
   |   5 |Pianka górna |
   |   6 |Skrzynka na akcesoria |
3. Po rozpakowaniu dwóch pudełek upewnij się, że masz:
   
   * 1 obudowa podstawowa (obudowa podstawowa i obudowa EBOD znajdują się w dwóch oddzielnych pudełkach)
   * 1 Obudowa EBOD
   * 4 przewody zasilające, 2 w każdym pudełku
   * 2 kable SAS (do podłączenia obudowy podstawowej do obudowy EBOD)
   * 1 kabel Ethernet crossover
   * 2 przewody konsoli szeregowej
   * 1 konwerter szeregowy USB do dostępu szeregowego
   * 4 adaptery QSFP-to-SFP+ do użytku z interfejsami sieciowymi 10 GbE
   * 2 zestawy do montażu w stelażu (4 boczne szyny ze sprzętem montażowym, po 2 dla obudowy głównej i obudowy EBOD), 1 w każdym pudełku
   * Dokumentacja wprowadzenie
     
     Jeśli nie otrzymałeś żadnego z elementów wymienionych powyżej, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).  

Następnym krokiem jest zamontowanie urządzenia na stojaku.

## <a name="rack-mount-your-storsimple-8600-device"></a>Urządzenie StorSimple 8600 montuje w stelażu
Wykonaj następne kroki, aby zainstalować urządzenie pamięci masowej StorSimple 8600 w standardowym 19-calowym stelażu z przednimi i tylnymi słupkami. To urządzenie jest wyposażone w dwie obudowy: obudowę podstawową i obudowę EBOD. Oba muszą być montowane w stelażu.

Instalacja składa się z wielu kroków, z których każdy jest omówiony w poniższych procedurach.

> [!IMPORTANT]
> Urządzenia StorSimple muszą być montowane w stelażu w celu prawidłowego działania.
> 
> 

### <a name="site-preparation"></a>Przygotowanie terenu
Obudowy muszą być zainstalowane w standardowym 19-calowym stelażu z przednimi i tylnymi słupkami. Poniższa procedura umożliwia przygotowanie do instalacji stelaża.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Aby przygotować teren do montażu stelaża
1. Upewnij się, że obudowy pierwotne i EBOD znajdują się bezpiecznie na płaskiej, stabilnej i równej powierzchni roboczej (lub podobnej).
2. Sprawdź, czy w lokacji, w której zamierzasz skonfigurować, jest standardowa moc ac z niezależnego źródła lub jednostki dystrybucji zasilania (PDU) w stelażu z zasilaczem awaryjnym (UPS).
3. Upewnij się, że jedno gniazdo 4U (2 X 2U) jest dostępne w stelażu, w którym zamierzasz zamontować obudowy.

![Ikona](./media/storsimple-safety/IC740879.png)![ostrzeżenia](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) o wadze ciężkiej **UWAGA!**

 Upewnij się, że masz dwie osoby dostępne do zarządzania wagi, jeśli obsługujesz konfigurację urządzenia ręcznie. W pełni skonfigurowana obudowa może ważyć do 32 kg.).

### <a name="rack-prerequisites"></a>Wymagania wstępne stelaża
Obudowy są przeznaczone do montażu w standardowej 19-calowej szafie rack z:

* Minimalna głębokość 27,84 cala od słupka do słupka
* Maksymalna waga urządzenia 32 kg
* Maksymalne ciśnienie wsteczne 5 Pascal (0,5 mm miernik wody)

### <a name="rack-mounting-rail-kit"></a>Zestaw szyn montażowych do montażu w stelażu
Zestaw szyn montażowych będzie przeznaczony do użytku z 19-calową szafką rack. Szyny zostały przetestowane pod w celu obsługi maksymalnej masy obudowy. Szyny te pozwolą również na montaż wielu obudów bez utraty miejsca w stelażu. Najpierw zainstaluj obudowę EBOD.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Aby zainstalować obudowę EBOD na szynach
1. Ten krok należy wykonać tylko wtedy, gdy na urządzeniu nie są zainstalowane szyny wewnętrzne. Zazwyczaj wewnętrzne szyny są instalowane fabrycznie. Jeśli szyny nie są zainstalowane, należy zainstalować prowadnice z lewej szyny i prawej szyny po bokach podwozia obudowy. Mocują się za pomocą sześciu śrub metrycznych z każdej strony. Aby ułatwić orientację, prowadnice szyny są oznaczone **LH – Przód** i **RH – przód,** a koniec, który jest przymocowany do tylnej części obudowy, ma stożkowy koniec.
   
    ![Mocowanie prowadnic kolejowych do obudowy](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Mocowanie prowadnic kolejowych po bokach obudowy**
   
   | Label | Opis |
   | --- | --- |
   |  1 |Śruby z głowicą przyciskową M 3x4 |
   |  2 |Prowadnice podwozia |
2. Przymocuj lewą szynę i prawe zespoły szyny do pionowych elementów szafy rack. Nawiasy są oznaczone **LH,** **RH**i **Ta strona do góry,** aby poprowadzić Cię przez prawidłową orientację.
3. Znajdź bolce szyny z przodu i z tyłu zestawu prowadnic. Rozszerz szynę, aby zmieściła się między słupkami stojaka i włóż kołki do przednich i tylnych otworów prętowych. Upewnij się, że zespół szyny jest poziom.
4. Przymoocnie zespół szyny do pionowych elementów stelaża za pomocą dwóch dostarczonych śrub metrycznych. Użyj jednej śruby z przodu i jednej z tyłu.
5. Powtórz te kroki dla innego zespołu szyny.
   
     ![Mocowanie prowadnic kolejowych do szafy rack](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Mocowanie zespołów szyny do stelaża**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Śruba mocująca |
   |   2 |Kwadratowa przednia śruba słupkowa |
   |   3 |Lewe styki lokalizacji szyny przedniej |
   |   4 |Śruba mocująca |
   |   5 |Lewe styki lokalizacji tylnej szyny |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montaż obudowy EBOD w stelażu
Korzystając z szyn stelaża, które zostały właśnie zainstalowane, wykonaj następujące czynności, aby zamontować obudowę EBOD w stelażu.

#### <a name="to-mount-the-ebod-enclosure"></a>Aby zamontować obudowę EBOD
1. Za pomocą asystenta unieś obudowę i wyrównaj ją do szyn stelaża.
2. Ostrożnie włóż obudowę do szyn, a następnie wciśnij ją całkowicie do szafy rack.
   
    ![Wkładanie urządzenia do stelaża](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montaż obudowy w stelażu**
3. Usuń lewe i prawe przednie nasadki kołnierza, wyciągając za darmo nasadki. Nasadki kołnierzowe po prostu zatrzaskują się na kołnierzach.
4. Zamocować obudowę do stelaża, montując jedną śrubę krzyżakową przez każdy kołnierz, lewy i prawy.
5. Zamontować nasadki kołnierza, naciskając je w pozycji i zatrzaskując je na swoim miejscu.
   
     ![Montaż nasadek kołnierzowych](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Montaż nasadek kołnierzowych**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Śruba mocująca obudowę |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montaż obudowy głównej w stelażu
Po zakończeniu montażu obudowy EBOD należy zamontować obudowę podstawową zgodnie z tymi samymi krokami.

> [!NOTE]
> * Istnieje możliwość mieć kilka pustych gniazd w stojaku między obudową podstawową a obudową EBOD.
> * Użyj dostarczonego kabla SAS 2m, aby podłączyć obudowę podstawową do obudowy EBOD.
> * Nie ma żadnych ograniczeń dotyczących względnego rozmieszczenia jednostki głowicy w jednostce EBOD. W związku z tym obudowa podstawowa może być umieszczona w górnym gnieździe i obudowie EBOD poniżej — lub odwrotnie.
> 
> 

Następnym krokiem jest kabel urządzenia do zasilania, sieci i dostępu szeregowego.

## <a name="cable-your-storsimple-8600-device"></a>Podłączanie kabli do urządzenia StorSimple 8600
W poniższych procedurach wyjaśniono, jak podłączyć urządzenie StorSimple 8600 do połączeń zasilania, sieci i połączeń szeregowych.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem kabli urządzenia należy:

* Obudowa podstawowa i obudowa EBOD, całkowicie rozpakowane
* 4 kable zasilające (po 2 dla obudowy podstawowej i EBOD) dostarczane z urządzeniem
* 2 Kable SAS dostarczane z urządzeniem do podłączenia obudowy EBOD do obudowy podstawowej
* Dostęp do 2 jednostek dystrybucji mocy (PDU) (zalecane)
* Kable sieciowe
* Pod warunkiem, że kable szeregowe
* Konwerter szeregowo-USB z odpowiednim sterownikiem zainstalowanym na komputerze (w razie potrzeby)
* Dostępne 4 adaptery QSFP-to-SFP+ do użytku z interfejsami sieciowymi 10 GbE
* [Obsługiwany sprzęt dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>Sas i okablowanie zasilania
Urządzenie ma zarówno obudowę podstawową, jak i obudowę EBOD. Wymaga to, aby urządzenia były połączone ze sobą w celu łączności i zasilania SCSI (SYK) podłączonej do szeregowej.

Podczas konfigurowania tego urządzenia po raz pierwszy wykonaj kroki dotyczące okablowania SAS, a następnie wykonaj kroki dotyczące okablowania zasilania.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Okablowanie sieciowe
Urządzenie jest w konfiguracji aktywnej: w danym momencie jeden moduł kontrolera jest aktywny i przetwarza wszystkie operacje dysku i sieci, podczas gdy drugi moduł kontrolera jest w trybie gotowości. Jeśli wystąpi awaria kontrolera, kontroler rezerwowy natychmiast aktywuje i kontynuuje wszystkie operacje dysku i sieci.

Aby obsługiwać ten nadmiarowy kontroler w pracy awaryjnej, należy przelać sieć urządzeń, jak pokazano w poniższych krokach.

#### <a name="to-cable-for-network-connection"></a>Do kabla do połączenia sieciowego
1. Urządzenie ma sześć interfejsów sieciowych na każdym kontrolerze: cztery 1 Gb/s i dwa porty Ethernet 10 Gb/s. Zapoznaj się z poniższą ilustracją, aby zidentyfikować porty danych na backplane urządzenia.
   
     ![Zaoplanek urządzenia 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Tył urządzenia z portami danych**
   
   | Label | Opis |
   | --- | --- |
   |   0,1,4,5 |1 Interfejsy sieciowe GbE |
   |   2,3 |10 interfejsów sieciowych GbE |
   |   6 |Porty szeregowe |
2. Na poniższym diagramie okablowania sieciowego. (Minimalna konfiguracja sieci jest wyświetlana przez niebieskie linie. Aby uzyskać wysoką dostępność i wydajność, wymagana dodatkowa konfiguracja jest wyświetlana przez linie kropkowane.)

![Kabel urządzenia 4U do sieci](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Okablowanie sieciowe dla twojego urządzenia**

| Label | Opis |
| --- | --- |
| A |SIEĆ LAN z dostępem do Internetu |
| B |Kontroler 0 |
| C |PCM 0 |
| D |Kontroler 1 |
| E |PCM 1 |
| F |Sterownik EBOD 0 |
| G |Sterownik EBOD 1 |
| H,I |Hosty (na przykład serwery plików) |
| 0-5 |Interfejsy sieciowe |
| 6 |Obudowa podstawowa |
| 7 |Obudowa EBOD |

Podczas okablowania urządzenia wymagana jest minimalna konfiguracja:

* Co najmniej dwa interfejsy sieciowe połączone na każdym kontrolerze z jednym dla dostępu do chmury i jednym dla iSCSI. Port DATA 0 jest automatycznie włączany i konfigurowany za pomocą konsoli szeregowej urządzenia. Oprócz danych 0, inny port danych również musi być skonfigurowany za pośrednictwem klasycznego portalu platformy Azure. W takim przypadku należy podłączyć port DATA 0 do podstawowej sieci LAN (sieci z dostępem do Internetu). Inne porty danych można podłączyć do segmentu sieci LAN (VLAN) sieci SAN/iSCSI, w zależności od zamierzonej roli.
* Identyczne interfejsy na każdym kontrolerze podłączonym do tej samej sieci w celu zapewnienia dostępności w przypadku pracy awaryjnej kontrolera. Na przykład, jeśli zdecydujesz się połączyć DANE 0 i DATA 3 dla jednego z kontrolerów, musisz podłączyć odpowiednie dane 0 i DANE 3 na drugim kontrolerze.

Pamiętaj, aby uzyskać wysoką dostępność i wydajność:

* Jeśli to możliwe, skonfiguruj parę interfejsu sieciowego dla dostępu do chmury (1 GbE) i inną parę dla iSCSI (zalecane 10 GbE) na każdym kontrolerze.
* Jeśli to możliwe, połącz interfejsy sieciowe z każdego kontrolera z dwoma różnymi przełącznikami, aby zapewnić dostępność przed awarią przełącznika. Rysunek ilustruje dwa interfejsy sieciowe 10 GbE, DATA 2 i DATA 3, z każdego kontrolera podłączonego do dwóch różnych przełączników. Aby uzyskać więcej informacji, zapoznaj się z **interfejsami sieciowymi** w [obszarze Wymagania dotyczące wysokiej dostępności urządzenia StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Jeśli korzystasz z nadajników nadawczo-odbiorczych SFP+ z interfejsami sieciowymi 10 GbE, użyj dostarczonych adapterów QSFP-SFP+. Aby uzyskać więcej informacji, przejdź do [obsługiwanego sprzętu dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Okablowanie portów szeregowych
Wykonaj następujące czynności, aby zaalifikować port szeregowy.

#### <a name="to-cable-for-serial-connection"></a>Do kabla do podłączenia szeregowego
1. Urządzenie ma port szeregowy na każdym kontrolerze, który jest identyfikowany przez ikonę klucza. Aby zlokalizować porty szeregowe, zapoznaj się z ilustracją przedstawiającą porty danych z tyłu urządzenia.
2. Zidentyfikuj aktywny kontroler na zawróceniu samolotu urządzenia. Migająca niebieska dioda LED wskazuje, że kontroler jest aktywny.
3. Użyj dostarczonego kabla szeregowego (w razie potrzeby konwertera szeregowego USB dla laptopa) i podłącz konsolę lub komputer (z emulacją terminala do urządzenia) do portu szeregowego aktywnego kontrolera.
4. Zainstaluj sterowniki szeregowe USB (dostarczane z urządzeniem) na komputerze.
5. Skonfiguruj połączenie szeregowe w następujący sposób:
   
   * 115 200 bodów
   * 8 bitów danych
   * 1 bit stopu
   * Brak parzystości
   * Sterowanie przepływem ustawione na **Brak**
6. Sprawdź, czy połączenie działa, naciskając klawisz Enter na konsoli. Powinno pojawić się menu konsoli szeregowej.

> [!NOTE]
> **Zarządzanie zgaszaniem światła:** Gdy urządzenie jest zainstalowane w zdalnym centrum danych lub w pomieszczeniu komputerowym z ograniczonym dostępem, upewnij się, że połączenia szeregowe z obu kontrolerami są zawsze podłączone do przełącznika konsoli szeregowej lub podobnego sprzętu. Umożliwia to pozapasmowe zdalne sterowanie i obsługę techniczną w przypadku zakłóceń sieci lub nieoczekiwanych awarii.
> 
> 

Ukończono okablowanie urządzenia w celu zasilania, dostępu do sieci i połączenia szeregowego. Następnym krokiem jest skonfigurowanie oprogramowania na urządzeniu.

## <a name="next-steps"></a>Następne kroki
Teraz można przystąpić do [wdrażania i konfigurowania lokalnego urządzenia StorSimple.](storsimple-8000-deployment-walkthrough-u2.md)

