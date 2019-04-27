---
title: Instalowanie programu Microsoft Azure StorSimple 8100 urządzenia | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób Rozpakowywanie, montowanie i Podłączanie kabli do urządzenia StorSimple 8100, aby wdrożyć i skonfigurować oprogramowanie.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: b367b6e7126a442dc68646ff52a29c955f50b798
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631230"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Je rozpakować, zamontować w stojaku i Podłączanie kabli do urządzenia StorSimple 8100
## <a name="overview"></a>Omówienie
Usługi Microsoft Azure StorSimple 8100 to obudowy pojedynczego urządzenia montowane w stojaku. W tym samouczku wyjaśniono, jak rozpakować, zamontować w stojaku i kabel StorSimple 8100 urządzeń sprzętowych przed skonfigurowaniem i wdrażanie urządzenia StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Rozpakowywanie urządzenia StorSimple 8100
W poniższych krokach przedstawiono wyraźne, szczegółowe instrukcje dotyczące rozpakowania urządzenia StorSimple 8100 magazynu. To urządzenie jest dostarczane w jednym pudełku.

### <a name="prepare-to-unpack-your-device"></a>Przygotowanie do rozpakowywania urządzenia
Zanim można rozpakować urządzenie, przejrzyj następujące informacje.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png)![ikonę ciężki](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **ostrzeżenie!**

1. Upewnij się, że dwie osoby dostępne na potrzeby zarządzania wagę obudowa, jeśli są obsługującą go ręcznie. W pełni skonfigurowany obudowy można porównać do 32 kg (70 lbs.).
2. Umieść pudełko na płaskiej, poziomej powierzchni.

Następnie wykonaj poniższe kroki, aby rozpakować urządzenie.

#### <a name="to-unpack-your-device"></a>Aby rozpakować urządzenie
1. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. Proszę [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) ułatwią Ci ocenić, czy urządzenie jest w dobrym stanie.
2. Rozpakuj zawartość pudełka. Na poniższej ilustracji przedstawiono widok nierozpakowane urządzenia StorSimple.
   
     ![Rozpakowywanie urządzenia magazynu](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Wczytaj widok urządzenia magazynu**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Okno dokumentu |
   |   2 |Dolny maskotkę witryny |
   |   3 |Urządzenie |
   |   4 |Najważniejsze maskotkę witryny |
   |   5 |Pole akcesoriów |
3. Po rozpakowaniu upewnij się, że masz:
   
   * 1 urządzenie pojedynczego obudowy
   * 2 przewodów zasilania
   * 1 skrzyżowanego kabla Ethernet
   * 2 kable konsoli szeregowej
   * Konwerter USB seryjny 1 dostęp szeregowy
   * 1 śrubokręt T10 odporną na manipulacje
   * 4 QSFP-do-SFP + kart do użycia z interfejsów sieciowych 10 GbE
   * 1-zamontować w stojaku kit (2 po stronie rails z instalowania sprzętu)
   * Getting Started dokumentacji
     
     Jeśli nie masz żadnych elementów wymienionych powyżej, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).

Następnym krokiem jest zamontowanie urządzenia na stojaku.

## <a name="rack-mount-your-storsimple-8100-device"></a>Urządzenie StorSimple 8100 zamontować w stojaku
Wykonaj poniższe czynności, aby zainstalować urządzenia magazynu StorSimple 8100 w standardowym stelażu 19-calowy z przodu i wpisy na tylnej. Urządzenie StorSimple 8100 ma jednej podstawowej obudowie.

Instalacja składa się z wielu kroków, z których każdy jest omówione w poniższych procedurach.

> [!IMPORTANT]
> Urządzenia StorSimple musi być montowane w stojaku do prawidłowego działania.
> 
> 

### <a name="prepare-the-site"></a>Przygotowanie lokacji
Urządzenie musi być zainstalowany w standardowych stojaku 19-calowy, która ma zarówno frontonu, jak i tylnej wpisów. Poniższa procedura umożliwia przygotowanie do instalacji stojaka.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Aby przygotować lokacji stojak instalacji
1. Upewnij się, że urządzenie zostało bezpiecznie umieszczone na płaskiej, stabilnej i poziomej powierzchni roboczej (lub podobnej).
2. Sprawdź, czy witryny, na którym zamierzasz skonfigurować ma standardowy zasilacza niezależne źródło lub stojak jednostki dystrybucji zasilania (PDU) przy użyciu zasilacz awaryjny (UPS).
3. Upewnij się, że ten jedno gniazdo 2U jest dostępna w stojaku, w którym zamierzasz zainstalować na urządzeniu.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png)![ikonę ciężki](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **ostrzeżenie!**

Upewnij się, że dwie osoby dostępne na potrzeby zarządzania wagi, jeśli są w nim Obsługa konfiguracji urządzenia ręcznie. W pełni skonfigurowany obudowy można porównać do 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Wymagania wstępne stojak
Obudowa 8100 jest przeznaczona dla instalacji w standardowym stelażu 19-calowy cabinet za pomocą:

* Minimalna głębokość 27.84 cala od regału post do wpisu.
* Waga maksymalny 32 kg dla urządzenia
* Maksymalne wykorzystanie wstecz Pascal 5 (0,5 mm wody miernika).

### <a name="rack-mounting-rail-kit"></a>Instalowanie w stelażu szyny kit
Do użytku z pliku cabinet stojak 19-calowy jest dostępny zestaw zainstalowania platformy rails. Do obsługi wagi maksymalna obudowy zostały przetestowane szyny. Te platformy rails będzie również umożliwiać instalację wielu obudowach bez utraty miejsca w stojaku.

#### <a name="to-install-the-device-on-the-rails"></a>Do zainstalowania na urządzeniu na szyny
1. Ten krok należy wykonać tylko wtedy, gdy rails wewnętrzne nie są zainstalowane na urządzeniu. Zazwyczaj rails wewnętrzne są instalowane na etapie produkcji. Jeśli nie zainstalowano platformy rails, zainstaluj slajdy szyny po lewej stronie i szyny po prawej stronie na stronach obudowy obudowy. Dołącz one, przy użyciu sześciu śruby metryki na każdej stronie. Aby ułatwić orientacji, slajdy szyny są oznaczone **LH — przód** i **RH — przód**, i zakończenia, który jest dołączony do tyłu obudowa ma końcem.<br/>
   
    ![Dołączanie slajdy szyny do obudowy obudowy](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Dołączanie wewnętrzny szyny slajdów na stronach obudowa**
   
    Label | Opis
    ----- | -----------
    1     | M 3 x 4 śruby przycisk główny
    2     | Podstawa montażowa slajdów

2. Dołączanie zewnętrznej szyny po lewej stronie i zestawy zewnętrzne po prawej stronie do stojak cabinet pionowej elementy członkowskie. Nawiasy są oznaczone **LH**, **RH**, i **ta strona się** przeprowadzenie Cię przez proces poprawnej orientacji.
3. Znajdź bolce szyny z przodu i z tyłu zestawu prowadnic. Rozszerz szyny mieści się między wpisów w stojaku i wstawianie numery PIN przedniej i tylnej stojak wpis składowej pionowej luki. Pamiętaj, że zestaw szyny jest poziom.
4. Zapewnienie pionowej elementy członkowskie zestawu szyny w stojaku, użyj dwóch podanych śruby metryki. Użyj jednej śrubie na frontonu i jednej z tyłu.
5. Powtórz te kroki dla innych zestawów szyny.<br/>
   
     ![Dołączanie slajdy szyny stojaku cabinet](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Dołączanie zestawy zewnętrzne szyny w stojaku**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Granica gwintowanym |
   |   2 |Gwintowanym wpis w stojaku frontonu dziura kwadrat |
   |   3 |Numery PIN przednim lokalizacji szyny po lewej stronie |
   |   4 |Granica gwintowanym |
   |   5 |Numery PIN tylnej lokalizacji po lewej stronie szyny |

### <a name="mounting-the-device-in-the-rack"></a>Instalowanie urządzenia w stojaku
Za pomocą stojaku, które właśnie zostały zainstalowane, wykonaj następujące kroki, aby zainstalować urządzenia w stojaku.

#### <a name="to-mount-the-device"></a>Do zainstalowania na urządzeniu
1. Z Asystentem Podnieś obudowa i dostosować ją przy użyciu platformy rails stojaka.
2. Dokładnie Wstawianie urządzenia platformy rails, a następnie Wypchnij urządzenie całkowicie w stojaku cabinet.<br/>
   
    ![Wstawianie urządzenia w stojaku](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Instalowanie urządzenia w stojaku**
3. Ściąganie bezpłatne limity, aby usunąć limity kołnierza po lewej i prawej frontonu. Limity kołnierza po prostu przyciąganie do stopka.
4. Zabezpiecz obudowy w stojaku, co podane gwintowanym Phillips head za pośrednictwem każdej kołnierza, lewy i prawy instalowanie.
5. Zainstaluj caps kołnierza, naciskając je w określonej pozycji i przyciąganie ich w miejscu.<br/>
   
     ![Instalowanie caps kołnierza](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalowanie caps kołnierza**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Gwintowanym zaczepienia obudowy |

Następnym krokiem jest Podłączanie kabli do urządzenia do zasilania, sieci i dostęp szeregowy.

## <a name="cable-your-storsimple-8100-device"></a>Podłączanie kabli do urządzenia StorSimple 8100
Poniższe procedury dotyczą sposobu podłączanie kabli do urządzenia StorSimple 8100 zasilania, sieci i połączenia szeregowe.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem podłączania kabli urządzenia będą potrzebne następujące elementy:

* Urządzenie magazynujące, całkowicie rozpakowane i stojaku.
* 2 kable zasilania, które są dołączone do urządzenia
* Dostęp do 2 jednostki dystrybucji zasilania (zalecane).
* Kable sieciowe
* Podany kable szeregowe
* Serial Konwerter USB przy użyciu odpowiedniego sterownika, które są zainstalowane na komputerze (w razie potrzeby)
* Podany 4 QSFP-do-SFP + kart do użycia z interfejsów sieciowych 10 GbE
* [Obsługiwany sprzęt dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Przewody zasilania
Urządzenia zawiera nadmiarowe zasilania i chłodzenia modułów (PCMs). Zarówno PCMs musi być zainstalowane i połączone z różnymi źródłami zasilania aby zapewnić wysoką dostępność.

Wykonaj poniższe kroki, aby Podłączanie kabli do urządzenia zasilania.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Okablowanie sieci
Urządzenie jest w konfiguracji aktywne-w gotowości: w dowolnym momencie jeden moduł kontrolera jest aktywny, a przetwarzanie wszystkich operacji dysku i sieci podczas moduł kontroler jest w stanie wstrzymania. Jeśli kontrolera nie powiedzie się, kontroler zapasowy jest aktywowany bezpośrednio i kontynuuje, dysku i sieci operacji.

Aby zapewnić obsługę pracy w trybie failover nadmiarowe kontrolera, musisz okablowanie sieci urządzenia, zgodnie z opisem w poniższych krokach.

#### <a name="to-cable-for-network-connection"></a>Aby kabel dla połączenia sieciowego
1. Urządzenie ma sześć interfejsów sieciowych na każdym kontrolerze: cztery 1 GB/s i dwa 10 GB/s Ethernet portów. Identyfikowanie różnych portów danych płyty montażowej urządzenia.
   
    ![Płyty montażowej urządzenia 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Tylnej stronie urządzeń przedstawiający porty danych**
   
   | Label | Opis |
   | --- | --- |
   |   0,1,4,5 |Interfejsy sieciowe w usłudze 1 GbE |
   |   2,3 |Interfejsów sieciowych 10 GbE |
   |   6 |Porty szeregowe |
2. Zobacz poniższy diagram okablowanie sieci. (Konfiguracja minimalna sieci jest reprezentowany przez niebieska linia ciągła. Dodatkowych czynności konfiguracyjnych wymaganych na potrzeby wysokiej dostępności i wydajności znajduje się za pomocą linii kropkowanej).

    ![Podłączanie kabli do urządzenia 2U sieci](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Okablowania dla urządzenia sieciowego**

   |Label | Opis |
   |----- | ----------- |
   | A    | Sieć LAN z dostępem do Internetu |
   | B    | Kontrolera 0 |
   | C    | MODULE PCM 0 |
   | D    | Kontrolera 1 |
   | E    | PCM 1 |
   | F, G | Hosts |
   | 0-5  | Interfejsy sieciowe |



W przypadku okablowania urządzenia, wymaga minimalnej konfiguracji:

* Co najmniej dwa interfejsy sieciowe podłączone na każdym kontrolerze przy użyciu jednego, aby uzyskać dostęp do chmury i jeden dla interfejsu iSCSI. DANE 0 portu jest automatycznie włączona i skonfigurowana za pośrednictwem konsoli szeregowej urządzenia. Oprócz interfejs DATA 0 inny port danych wymaga także można skonfigurować za pomocą klasycznego portalu Azure. W tym przypadku łącz dane 0 port do podstawowej sieci LAN (sieci z dostępem do Internetu). Inne porty dane mogą być połączone do segmentu sieci, w zależności od tego, ma pełnić SAN/iSCSI sieci LAN (VLAN).
* Identyczne interfejsy na każdym kontrolerze połączone z tą samą siecią, aby zapewnić dostępność w przypadku przejścia do trybu failover kontrolera. Na przykład jeśli chcesz połączyć interfejs DATA 0 i DATA 3 na jeden z kontrolerów należy połączyć odpowiadające im dane 0 i DATA 3 na drugim kontrolerze.

Należy pamiętać o wysokiej dostępności i wydajności:

* Jeśli to możliwe, należy skonfigurować parę interfejsu sieciowego, aby uzyskać dostęp do chmury (1 GbE) i kolejną parę iSCSI (zalecane 10 GbE) na każdym kontrolerze.
* Jeśli to możliwe, interfejsy sieciowe z każdego kontrolera połączyć się z dwóch różnych przełączników, aby zapewnić dostępność awariami przełącznika. Na rysunku przedstawiono dwa 10 GbE interfejsy sieciowe, dane 2 i DATA 3 z każdego kontrolera podłączone do dwóch różnych przełączników.

Aby uzyskać więcej informacji, zobacz **interfejsy sieciowe** w obszarze [wymaganiami wysokiej dostępności dla urządzenia StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Jeśli SFP + należy za pomocą usługi interfejsów sieciowych 10 GbE, użyj podanego QSFP-SFP + kart. Aby uzyskać więcej informacji, przejdź do [obsługiwany sprzęt dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Okablowanie portu szeregowego
Wykonaj poniższe kroki, aby podłączyć do portu szeregowego.

#### <a name="to-cable-for-serial-connection"></a>Aby kabel szeregowy połączenia
1. Urządzenie ma portu szeregowego na każdym kontrolerze, który jest identyfikowany przez ikona klucza. Można znaleźć na ilustracji w [okablowanie sieci](#network-cabling) sekcji, aby zlokalizować portów szeregowych płyty montażowej urządzenia.
2. Identyfikowanie aktywnego kontrolera na płyty montażowej Twojego urządzenia. Migająca dioda niebieski wskazuje, czy kontroler jest aktywny.
3. Użyj podanych kable szeregowe (jeśli jest to konieczne, konwerter szeregowego USB na komputerze przenośnym), a następnie podłączyć konsoli lub komputerze (przy użyciu emulacji terminala na urządzeniu) do portu szeregowego aktywny kontroler.
4. Na komputerze, należy zainstalować sterowniki USB seryjny (dostarczane z urządzeniem).
5. Skonfiguruj połączenie szeregowe w następujący sposób: 115 200 transmisji 8 bitów danych, 1 bit zatrzymania, bez parzystości i przepływ sterowania Ustaw na wartość None.
6. Sprawdź, czy połączenie działa, naciskając klawisz Enter w konsoli. Powinny być wyświetlane menu konsoli szeregowej.

> [!NOTE]
> **Zarządzanie obsługą technologii lights-Out**: Gdy urządzenie jest zainstalowane w zdalnym centrum danych lub w pomieszczeniu o ograniczonym dostępie, komputerów, upewnij się, czy połączenia szeregowe na obu kontrolerach zawsze są podłączone do konsoli szeregowej lub podobnego sprzętu. Dzięki temu out-of-band zdalnego sterowania i pomocy technicznej operacji w przypadku zakłócenia w sieci lub nieoczekiwanych awarii.
> 
> 

Urządzenie jest teraz rozpakowane dla mocy obliczeniowej, dostępu do sieci i połączenie szeregowe. Następnym krokiem jest skonfigurowanie oprogramowania i wdrożyć urządzenie.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [wdrożyć i skonfigurować urządzenie StorSimple w środowisku lokalnym](storsimple-8000-deployment-walkthrough-u2.md).

