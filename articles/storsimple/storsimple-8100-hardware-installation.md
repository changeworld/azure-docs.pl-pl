---
title: Instalowanie urządzenia Microsoft Azure StorSimple 8100
description: W tym artykule opisano sposób rozpakowania, montażu w stelażu i kabla urządzenia StorSimple 8100 przed wdrożeniem i skonfigurowaniem oprogramowania.
author: alkohli
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 113b72ddf7e5d508c8a0b577d4004d4fbd83e8e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267588"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Rozpakuj, montuj stelaż i przekakowo urządzenie StorSimple 8100

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie
Twój Microsoft Azure StorSimple 8100 to jednoojedna, montowane w stojaku urządzenie. W tym samouczku wyjaśniono, jak rozpakować, zamontować w stojaku i podłączyć sprzęt urządzenia StorSimple 8100 przed skonfigurowaniem i wdrożeniem urządzenia StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Rozpakuj urządzenie StorSimple 8100
Poniższe kroki zawierają jasne, szczegółowe instrukcje dotyczące rozpakowania urządzenia magazynującego StorSimple 8100. To urządzenie jest dostarczane w jednym pudełku.

### <a name="prepare-to-unpack-your-device"></a>Przygotowanie do rozpakowania urządzenia
Przed rozpakowaniem urządzenia zapoznaj się z poniższymi informacjami.

![Ikona](./media/storsimple-safety/IC740879.png)![ostrzeżenia](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) o wadze ciężkiej **UWAGA!**

1. Upewnij się, że masz dwie osoby dostępne do zarządzania wagą obudowy, jeśli obsługujesz ją ręcznie. W pełni skonfigurowana obudowa może ważyć do 32 kg.).
2. Umieść pudełko na płaskiej, poziomej powierzchni.

Następnie wykonaj następujące czynności, aby rozpakować urządzenie.

#### <a name="to-unpack-your-device"></a>Aby rozpakować urządzenie
1. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. Skontaktuj [się z pomocą techniczną firmy Microsoft,](storsimple-8000-contact-microsoft-support.md) aby pomóc ci ocenić, czy urządzenie jest w dobrym stanie.
2. Rozpakuj zawartość pudełka. Na poniższej ilustracji przedstawiono rozpakowany widok urządzenia StorSimple.
   
     ![Rozpakuj urządzenie pamięci masowej](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Rozpakowany widok urządzenia pamięci masowej**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Pudełko na opakowania |
   |   2 |Pianka dolna |
   |   3 |Urządzenie |
   |   4 |Pianka górna |
   |   5 |Skrzynka na akcesoria |
3. Po rozpakowaniu upewnij się, że masz:
   
   * 1 pojedyncze urządzenie obudowy
   * 2 przewody zasilające
   * 1 kabel Ethernet crossover
   * 2 przewody konsoli szeregowej
   * 1 konwerter szeregowy USB do dostępu szeregowego
   * 1 śrubokręt T10 odporny na manipulacje
   * 4 adaptery QSFP-to-SFP+ do użytku z interfejsami sieciowymi 10 GbE
   * 1 zestaw do montażu w stelażu (2 boczne szyny ze sprzętem montażowym)
   * Dokumentacja wprowadzenie
     
     Jeśli nie otrzymałeś żadnego z elementów wymienionych powyżej, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

Następnym krokiem jest zamontowanie urządzenia na stojaku.

## <a name="rack-mount-your-storsimple-8100-device"></a>Urządzenie StorSimple 8100 montuje w stelażu
Wykonaj następne kroki, aby zainstalować urządzenie pamięci masowej StorSimple 8100 w standardowym 19-calowym stojaku z przednimi i tylnymi słupkami. Urządzenie StorSimple 8100 ma jedną obudowę podstawową.

Instalacja składa się z wielu kroków, z których każdy jest omówiony w poniższych procedurach.

> [!IMPORTANT]
> Urządzenia StorSimple muszą być montowane w stelażu w celu prawidłowego działania.
> 
> 

### <a name="prepare-the-site"></a>Przygotowanie strony
Urządzenie musi być zainstalowane w standardowym 19-calowym stelażu z przednimi i tylnymi słupkami. Poniższa procedura umożliwia przygotowanie do instalacji stelaża.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Aby przygotować teren do montażu stelaża
1. Upewnij się, że urządzenie zostało bezpiecznie umieszczone na płaskiej, stabilnej i poziomej powierzchni roboczej (lub podobnej).
2. Sprawdź, czy w miejscu, w którym zamierzasz skonfigurować, jest standardowa moc ac z niezależnego źródła lub jednostki dystrybucji zasilania stelaża (PDU) z zasilaczem awaryjnym (UPS).
3. Upewnij się, że jeden slot 2U jest dostępny w stelażu, w którym zamierzasz zamontować urządzenie.

![Ikona](./media/storsimple-safety/IC740879.png)![ostrzeżenia](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) o wadze ciężkiej **UWAGA!**

Upewnij się, że masz dwie osoby dostępne do zarządzania wagi, jeśli obsługujesz konfigurację urządzenia ręcznie. W pełni skonfigurowana obudowa może ważyć do 32 kg.).

### <a name="rack-prerequisites"></a>Wymagania wstępne stelaża
Obudowa 8100 jest przeznaczona do montażu w standardowej 19-calowej szafie rack z:

* Minimalna głębokość 27,84 cala od słupka do słupka.
* Maksymalna waga urządzenia 32 kg
* Maksymalne ciśnienie wsteczne 5 Pascal (0,5 mm miernik wody).

### <a name="rack-mounting-rail-kit"></a>Zestaw szyn montażowych do montażu w stelażu
Zestaw szyn montażowych jest przeznaczony do użytku z 19-calową szafką rack. Szyny zostały przetestowane pod w celu obsługi maksymalnej masy obudowy. Szyny te pozwolą również na montaż wielu obudów bez utraty miejsca w stelażu.

#### <a name="to-install-the-device-on-the-rails"></a>Aby zainstalować urządzenie na szynach
1. Ten krok należy wykonać tylko wtedy, gdy na urządzeniu nie są zainstalowane szyny wewnętrzne. Zazwyczaj wewnętrzne szyny są instalowane fabrycznie. Jeśli szyny nie są zainstalowane, należy zainstalować prowadnice z lewej szyny i prawej szyny po bokach podwozia obudowy. Mocują się za pomocą sześciu śrub metrycznych z każdej strony. Aby ułatwić orientację, prowadnice szyny są oznaczone **LH – Przód** i **RH – przód,** a koniec, który jest przymocowany do tylnej części obudowy, ma stożkowy koniec.<br/>
   
    ![Mocowanie prowadnic kolejowych do obudowy](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Mocowanie wewnętrznych prowadnic szyny po bokach obudowy**
   
    Label | Opis
    ----- | -----------
    1     | Śruby z głowicą przyciskową M 3x4
    2     | Prowadnice podwozia

2. Przymocuj zewnętrzną lewą szynę i zewnętrzne zespoły prawej szyny do pionowych elementów szafy rack. Nawiasy są oznaczone **LH,** **RH**i **Ta strona do góry,** aby poprowadzić Cię przez właściwą orientację.
3. Znajdź bolce szyny z przodu i z tyłu zestawu prowadnic. Rozszerz szynę, aby zmieściła się między słupkami stojaka i włóż kołki do przednich i tylnych pionowych otworów prętowych. Upewnij się, że zespół szyny jest poziom.
4. Użyj dwóch dostarczonych śrub metrycznych, aby przymocować zespół szyny do pionowych elementów stelaża. Użyj jednej śruby z przodu i jednej z tyłu.
5. Powtórz te kroki dla innego zespołu szyny.<br/>
   
     ![Mocowanie prowadnic kolejowych do szafy rack](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Mocowanie zespołów szyny zewnętrznej do stelaża**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Śruba mocująca |
   |   2 |Kwadratowa przednia śruba słupkowa |
   |   3 |Lewe styki lokalizacji przedniej szyny |
   |   4 |Śruba mocująca |
   |   5 |Sworznie lokalizacji tylnej lewej szyny |

### <a name="mounting-the-device-in-the-rack"></a>Montaż urządzenia w stelażu
Korzystając z szyn stelaża, które zostały właśnie zainstalowane, wykonaj następujące czynności, aby zamontować urządzenie w stelażu.

#### <a name="to-mount-the-device"></a>Aby zamontować urządzenie
1. Za pomocą asystenta unieś obudowę i wyrównaj ją do szyn stelaża.
2. Ostrożnie włóż urządzenie do szyn, a następnie wciśnij urządzenie całkowicie do szafki rack.<br/>
   
    ![Wkładanie urządzenia do stelaża](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montaż urządzenia w stelażu**
3. Usuń lewe i prawe przednie nasadki kołnierza, wyciągając za darmo nasadki. Nasadki kołnierzowe po prostu zatrzaskują się na kołnierzach.
4. Zabezpieczyć obudowę w stelażu, montując jedną śrubę krzyżakową przez każdy kołnierz, lewy i prawy.
5. Zamontować nasadki kołnierza, naciskając je w pozycji i zatrzaskując je na miejscu.<br/>
   
     ![Montaż nasadek kołnierzowych](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Montaż nasadek kołnierzowych**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Śruba mocująca obudowę |

Następnym krokiem jest kabel urządzenia do zasilania, sieci i dostępu szeregowego.

## <a name="cable-your-storsimple-8100-device"></a>Podłączanie kabli do urządzenia StorSimple 8100
W poniższych procedurach wyjaśniono, jak podłączyć urządzenie StorSimple 8100 do połączeń zasilania, sieci i szeregowych.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem podłączania kabli urządzenia będą potrzebne następujące elementy:

* Urządzenie pamięci masowej, całkowicie rozpakowane i zamontowane w stelażu.
* 2 kable zasilające dołodowe z urządzeniem
* Dostęp do 2 jednostek dystrybucji mocy (zalecane).
* Kable sieciowe
* Pod warunkiem, że kable szeregowe
* Szeregowy konwerter USB z odpowiednim sterownikiem zainstalowanym na komputerze (w razie potrzeby)
* Dostępne 4 adaptery QSFP-to-SFP+ do użytku z interfejsami sieciowymi 10 GbE
* [Obsługiwany sprzęt dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Okablowanie zasilania
Urządzenie zawiera nadmiarowe moduły zasilania i chłodzenia (PCM). Oba pcms muszą być zainstalowane i podłączone do różnych źródeł zasilania, aby zapewnić wysoką dostępność.

Wykonaj następujące czynności, aby sprzewodu urządzenia do zasilania.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Okablowanie sieciowe
Urządzenie jest konfiguracją aktywnego w trybie gotowości: w danym momencie jeden moduł kontrolera jest aktywny i przetwarza wszystkie operacje dysku i sieci, podczas gdy drugi moduł kontrolera jest w trybie gotowości. Jeśli kontroler ulegnie awarii, kontroler rezerwowy zostanie natychmiast aktywowany i kontynuuje wszystkie operacje dysku i sieci.

Aby obsługiwać ten nadmiarowy kontroler w pracy awaryjnej, należy przelać sieć urządzeń, zgodnie z opisem w poniższych krokach.

#### <a name="to-cable-for-network-connection"></a>Do kabla do połączenia sieciowego
1. Urządzenie ma sześć interfejsów sieciowych na każdym kontrolerze: cztery 1 Gb/s i dwa porty Ethernet 10 Gb/s. Zidentyfikuj różne porty danych na zaoście urządzenia.
   
    ![Zaoplanek urządzenia 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Tył urządzenia z portami danych**
   
   | Label | Opis |
   | --- | --- |
   |   0,1,4,5 |1 Interfejsy sieciowe GbE |
   |   2,3 |10 interfejsów sieciowych GbE |
   |   6 |Porty szeregowe |
2. Na poniższym diagramie okablowania sieciowego. (Minimalna konfiguracja sieci jest wyświetlana przez niebieskie linie. Dodatkowa konfiguracja wymagana do uzyskania wysokiej dostępności i wydajności jest wyświetlana przez linie kropkowane.)

    ![Kabel urządzenia 2U do sieci](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Okablowanie sieciowe dla twojego urządzenia**

   |Label | Opis |
   |----- | ----------- |
   | A    | SIEĆ LAN z dostępem do Internetu |
   | B    | Kontroler 0 |
   | C    | PCM 0 |
   | D    | Kontroler 1 |
   | E    | PCM 1 |
   | F, G | Hosts |
   | 0-5  | Interfejsy sieciowe |



Podczas okablowania urządzenia wymagana jest minimalna konfiguracja:

* Co najmniej dwa interfejsy sieciowe połączone na każdym kontrolerze z jednym dla dostępu do chmury i jednym dla iSCSI. Port DATA 0 jest automatycznie włączany i konfigurowany za pomocą konsoli szeregowej urządzenia. Oprócz danych 0, inny port danych również musi być skonfigurowany za pośrednictwem klasycznego portalu platformy Azure. W takim przypadku należy podłączyć port DATA 0 do podstawowej sieci LAN (sieci z dostępem do Internetu). Inne porty danych można podłączyć do segmentu sieci LAN (VLAN) sieci SAN/iSCSI, w zależności od zamierzonej roli.
* Identyczne interfejsy na każdym kontrolerze podłączonym do tej samej sieci w celu zapewnienia dostępności w przypadku pracy awaryjnej kontrolera. Na przykład, jeśli zdecydujesz się połączyć DANE 0 i DATA 3 dla jednego z kontrolerów, musisz podłączyć odpowiednie dane 0 i DANE 3 na drugim kontrolerze.

Pamiętaj, aby uzyskać wysoką dostępność i wydajność:

* Jeśli to możliwe, skonfiguruj parę interfejsu sieciowego dla dostępu do chmury (1 GbE) i inną parę dla iSCSI (zalecane 10 GbE) na każdym kontrolerze.
* Jeśli to możliwe, połącz interfejsy sieciowe z każdego kontrolera z dwoma różnymi przełącznikami, aby zapewnić dostępność przed awarią przełącznika. Rysunek ilustruje dwa interfejsy sieciowe 10 GbE, DATA 2 i DATA 3, z każdego kontrolera podłączonego do dwóch różnych przełączników.

Aby uzyskać więcej informacji, zapoznaj się z **interfejsami sieciowymi** w [obszarze Wymagania dotyczące wysokiej dostępności urządzenia StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Jeśli używasz transceivers SFP+ z interfejsami sieciowymi 10 GbE, użyj dostarczonych adapterów QSFP-SFP+. Aby uzyskać więcej informacji, przejdź do [obsługiwanego sprzętu dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Okablowanie portów szeregowych
Wykonaj następujące czynności, aby zaalifikować port szeregowy.

#### <a name="to-cable-for-serial-connection"></a>Do kabla do podłączenia szeregowego
1. Urządzenie ma port szeregowy na każdym kontrolerze, który jest identyfikowany przez ikonę klucza. Zapoznaj się z ilustracją w sekcji [Okablowanie sieciowe,](#network-cabling) aby zlokalizować porty szeregowe na płytce montażowej urządzenia.
2. Zidentyfikuj aktywny kontroler na zawróceniu samolotu urządzenia. Migająca niebieska dioda LED wskazuje, że kontroler jest aktywny.
3. Użyj dostarczonych kabli szeregowych (w razie potrzeby konwertera szeregowego USB dla laptopa) i podłącz konsolę lub komputer (z emulacją terminala do urządzenia) do portu szeregowego aktywnego kontrolera.
4. Zainstaluj sterowniki szeregowe USB (dostarczane z urządzeniem) na komputerze.
5. Skonfiguruj połączenie szeregowe w następujący sposób: 115 200 bodów, 8 bitów danych, 1 bit stop, brak parzystości i kontrola przepływu ustawiona na Brak.
6. Sprawdź, czy połączenie działa, naciskając klawisz Enter na konsoli. Powinno pojawić się menu konsoli szeregowej.

> [!NOTE]
> **Zarządzanie wyjściami świetlnymi:** Gdy urządzenie jest zainstalowane w zdalnym centrum danych lub w pomieszczeniu komputerowym z ograniczonym dostępem, upewnij się, że połączenia szeregowe z obu kontrolerami są zawsze podłączone do przełącznika konsoli szeregowej lub podobnego sprzętu. Umożliwia to pozapasmowe zdalne sterowanie i obsługę techniczną w przypadku zakłóceń sieci lub nieoczekiwanych awarii.
> 
> 

Urządzenie jest teraz zasilane, dostęp do sieci i łączność szeregowa. Następnym krokiem jest skonfigurowanie oprogramowania i wdrożenie urządzenia.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć i skonfigurować lokalne urządzenie StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

