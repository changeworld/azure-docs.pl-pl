---
title: Instalowanie programu Microsoft Azure StorSimple 8100 urządzenia | Dokumentacja firmy Microsoft
description: Opisuje sposób Rozpakowywanie, montowanie i Podłączanie kabli do urządzenia StorSimple 8100, aby wdrożyć i skonfigurować oprogramowanie.
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
ms.openlocfilehash: 5fbc407a9792d033037fdaa2b14f4055d94c15ab
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743470"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Rozpakowywanie, w stojaku, a Podłączanie kabli do urządzenia StorSimple 8100
## <a name="overview"></a>Przegląd
Do urządzenia 8100 Microsoft Azure StorSimple jest obudowa pojedynczego urządzenia montowane w stojaku. W tym samouczku wyjaśniono, jak je rozpakować, zamontować w stojaku i kabel StorSimple 8100 urządzeń sprzętowych przed Konfigurowanie i wdrażanie urządzenia StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Rozpakuj do urządzenia StorSimple 8100
W poniższych krokach przedstawiono wyraźne, szczegółowe instrukcje dotyczące rozpakowania urządzenia magazynującego StorSimple 8100. To urządzenie jest dostarczany w jednym polu.

### <a name="prepare-to-unpack-your-device"></a>Przygotowanie do rozpakowania urządzenia
Aby rozpakować urządzenia, przejrzyj następujące informacje.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png)![ikona ciężki](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **ostrzeżenie!**

1. Upewnij się, że masz dwie osoby dostępne na potrzeby zarządzania wagę obudowa, jeśli są obsługuje go ręcznie. Obudowa pełni skonfigurowany można porównać do 32 kg (70 kg).
2. Umieść pole na powierzchni płaskiej, poziomu.

Następnie wykonaj poniższe kroki, aby rozpakować urządzenia.

#### <a name="to-unpack-your-device"></a>Aby rozpakować urządzenia
1. Sprawdź, czy pole i piana pakowania crushes, części, szkody limitu górnego lub widocznego uszkodzenia. Jeśli pole lub pakowania poważnie jest uszkodzony, nie otwieraj okno. Sprawdź [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) ułatwiające oceny, czy urządzenie jest w dobrym stanie.
2. Rozpakuj pola. Na poniższej ilustracji przedstawiono widok rozpakowanego urządzenia StorSimple.
   
     ![Rozpakuj urządzenia magazynu](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Wczytaj widoku urządzenia pamięci masowej**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Pole pakowania |
   |   2 |Pianki dołu |
   |   3 |Urządzenie |
   |   4 |Górny pianki |
   |   5 |Pole akcesoriów |
3. Po rozpakowywania pole, upewnij się, że masz:
   
   * 1 urządzenie jednej obudowie
   * 2 przewodów zasilania
   * 1 skrzyżowanego kabla Ethernet
   * 2 kable konsoli szeregowej
   * Konwerter USB seryjny 1 dostęp szeregowy
   * 1 śrubokręt T10 odporne
   * 4 QSFP-do-kart SFP + do użytku z interfejsami sieciowymi Ethernet 10
   * 1 w stojaku kit (2 po stronie szyny z instalowanie sprzętu)
   * Pobieranie rozpoczęte dokumentacji
     
     Jeśli nie masz żadnego z elementów wymienionych powyżej, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).

Następnym krokiem jest w stojaku urządzenia.

## <a name="rack-mount-your-storsimple-8100-device"></a>W stojaku do urządzenia StorSimple 8100
Wykonaj poniższe czynności, aby zainstalować urządzenia magazynującego StorSimple 8100 w stojaku standardowe 19 cala z przodu i tylnej wpisów. Urządzenie StorSimple 8100 ma jednej obudowie podstawowego.

Instalacja składa się z wielu kroków, z których każdy została szczegółowo opisana w poniższych procedurach.

> [!IMPORTANT]
> Urządzenia StorSimple musi być montowane w stojaku do prawidłowego działania.
> 
> 

### <a name="prepare-the-site"></a>Przygotowanie lokacji
Urządzenie musi być zainstalowany w standardowe stojak cala 19, który ma przodu oraz tylnej wpisów. Poniższa procedura umożliwia przygotowanie do instalacji w stojaku.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Aby przygotować lokacji stojak instalacji
1. Upewnij się, urządzenie opiera się na prosty, stabilny i poziomu pracy powierzchni (lub podobny) bezpiecznie.
2. Sprawdź, czy lokacja, w której chcesz skonfigurować ma standardowe zasilacza niezależne źródło lub stojak jednostki dystrybucji zasilania (PDU) z zasilacz awaryjny (UPS).
3. Upewnij się, że jeden gniazdo 2U jest dostępna w stojaku, w którym chcesz zainstalować na urządzeniu.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png)![ikona ciężki](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **ostrzeżenie!**

Upewnij się, że masz dwie osoby dostępne na potrzeby zarządzania wagi, jeśli Instalator urządzenia są Obsługa ręcznie. Obudowa pełni skonfigurowany można porównać do 32 kg (70 kg).

### <a name="rack-prerequisites"></a>Wymagania wstępne w stojaku
Obudowa 8100 jest przeznaczony do instalacji w stojaku standard CAL 19 cabinet z:

* Minimalna głębokość 27.84 cali w stojaku post i post.
* Maksymalna waga 32 kg dla urządzenia
* Maksymalnego wykorzystania wstecz Pascal 5 (miernika wody 0,5 mm).

### <a name="rack-mounting-rail-kit"></a>Stojakach kolei kit
Instalowanie szyny zestaw jest przeznaczony dla pliku cabinet stojak 19 cala. Szyny zostały przetestowane do obsługi wagi obudowa maksymalna. Te szyny będzie również umożliwiać instalacji wielu obudowach bez utraty miejsca w stojaku.

#### <a name="to-install-the-device-on-the-rails"></a>Do zainstalowania na urządzeniu na szyny
1. Wykonaj ten krok tylko wtedy, gdy wewnętrzny szyny nie są zainstalowane na urządzeniu. Zazwyczaj wewnętrzny szyny są instalowane w fabryce. Jeśli szyny nie są zainstalowane, zainstaluj kolei po lewej i prawej kolei slajdów na stronach obudowy obudowy. Dołącz ich przy użyciu sześciu śruby metryki na każdej stronie. Ułatwiają orientację, są oznaczone slajdów kolei **LH — przodu** i **RH — przodu**, i zakończenia, który jest dołączony do tyłu obudowa ma końcem.<br/>
   
    ![Dołączanie slajdów kolei obudowie obudowy](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Dołączanie wewnętrzny kolei slajdów na stronach obudowa**
   
    Etykieta | Opis
    ----- | -----------
    1     | M 3 x 4 śruby head przycisku
    2     | Podstawa montażowa slajdów

2. Dołącz zewnętrzne kolei po lewej stronie i zestawy zewnętrzne po prawej stronie członkom stojak cabinet pionowej. Nawiasy kwadratowe są oznaczone **LH**, **RH**, i **ta strona w górę** do przeprowadzenia poprawnej orientacji.
3. Znajdź numery PIN kolei na tylne zestawu kolei. Rozszerzanie szyny mieści się między wpisów w stojaku i wstawić numery PIN do przodu i luk element pionowy w stojaku tylnej post. Pamiętaj, że zestaw kolei jest poziom.
4. Użyj dwóch podanych śrub metryki zabezpieczyć zestawu kolei w stojaku pionowej elementy członkowskie. Użyj jednej śrubie na wierzchu i jeden z tyłu.
5. Powtórz te kroki dla innych zestawu kolei.<br/>
   
     ![Dołączanie slajdów kolei stojaku cabinet](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Dołączanie zestawy zewnętrzne kolei w stojaku**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Ładunku gwintowanym |
   |   2 |Gwintowanym post stojak front przerw kwadratowe |
   |   3 |Numery PIN front lokalizacji kolei po lewej stronie |
   |   4 |Ładunku gwintowanym |
   |   5 |Numery PIN tylnej lokalizacji kolei po lewej stronie |

### <a name="mounting-the-device-in-the-rack"></a>Instalowanie urządzenia w stojaku
Za pomocą stojaku, które właśnie zostały zainstalowane, wykonaj następujące kroki, aby zainstalować w stojaku urządzenia.

#### <a name="to-mount-the-device"></a>Do zainstalowania na urządzeniu
1. Z Asystentem Podnieś obudowa i wyrównanie go z stojaku.
2. Starannie Włóż urządzenie do szyny, a następnie Wypchnij urządzenia całkowicie w stojaku cabinet.<br/>
   
    ![Wstawianie urządzenia w stojaku](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Instalowanie urządzenia w stojaku**
3. Usuń caps kołnierza po lewej i prawej front ściąganie wolnego caps. Caps kołnierza po prostu przyciąganie na stopka.
4. Zabezpiecz obudowa w stojaku, instalując co podane gwintowanym Phillips head za pośrednictwem każdej kołnierza, lewy i prawy.
5. Zainstaluj caps kołnierza naciskając je w miejscu i przyciąganie ich w miejscu.<br/>
   
     ![Instalowanie kołnierza CAP](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalowanie caps kołnierza**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Obudowa gwintowanym zaczepienia |

Następnym krokiem jest Podłączanie kabli do urządzenia do zasilania, sieci i dostęp szeregowy.

## <a name="cable-your-storsimple-8100-device"></a>Podłączanie kabli do urządzenia StorSimple 8100
Poniższe procedury dotyczą sposobu podłączanie kabli do urządzenia StorSimple 8100 do zasilania, sieci i połączenia szeregowego.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem okablowania urządzenia, będą potrzebne:

* Urządzenie magazynujące, całkowicie rozpakowane i stojaku.
* 2 kable zasilania, które są dołączone do urządzenia
* Dostęp do 2 jednostki dystrybucji zasilania (zalecane).
* Kable sieciowe
* Podany kable szeregowe
* Szeregowego USB konwertera z odpowiedni sterownik zainstalowane na komputerze (w razie potrzeby)
* Podany 4 QSFP-do-kart SFP + do użytku z interfejsami sieciowymi Ethernet 10
* [Obsługiwane sprzętu dla interfejsów sieciowych 10 GbE w urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Okablowanie zasilania
Urządzenie zawiera nadmiarowe zasilania i chłodzenia modułów (PCMs). Zarówno PCMs musi być zainstalowane i połączone z różnych źródeł napędu aby zapewnić wysoką dostępność.

Wykonaj poniższe kroki, aby Podłączanie kabli do urządzenia zasilania.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Okablowanie sieci
Urządzenie jest Konfiguracja aktywnego gotowości: w dowolnym momencie, jeden moduł kontrolera jest aktywna i przetwarzanie wszystkie operacje dysku i sieci podczas moduł kontroler jest w stanie wstrzymania. W przypadku niepowodzenia kontrolera rezerwy kontrolera jest aktywowany bezpośrednio i kontynuuje dysku i operacji sieciowych.

Do obsługi tej pracy awaryjnej nadmiarowych kontrolera, musisz kabla sieci urządzenia, zgodnie z opisem w poniższych krokach.

#### <a name="to-cable-for-network-connection"></a>Aby kabel połączenia sieciowego
1. Urządzenie ma sześć interfejsów sieciowych na każdym kontrolerze: cztery 1 GB/s i dwa 10 GB/s Ethernet portów. Określ różne porty danych na płyty montażowej urządzenia.
   
    ![Płyty montażowej urządzenia 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Powrót z urządzenia przedstawiający porty danych**
   
   | Etykieta | Opis |
   | --- | --- |
   |   0,1,4,5 |Interfejsy sieciowe Ethernet 1 |
   |   2,3 |Interfejsy sieciowe Ethernet 10 |
   |   6 |Porty szeregowe |
2. Zobacz na poniższym diagramie okablowanie sieci. (Konfiguracja minimalna sieci znajduje się pełny liniami niebieski. Dodatkowe czynności konfiguracyjne wymagane wysokiej dostępności i wydajności znajduje się za pomocą linii kropkowanej).

    ![Podłączanie kabli do urządzenia 2U sieci](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Okablowania urządzenia sieciowego**

   |Etykieta | Opis |
   |----- | ----------- |
   | A    | Sieć LAN z dostępem do Internetu |
   | B    | Kontrolera 0 |
   | C    | PCM 0 |
   | D    | Kontrolera 1 |
   | E    | PCM 1 |
   | F, G | Hosts |
   | 0-5  | Interfejsy sieciowe |



W przypadku okablowania urządzenia, wymaga minimalnej konfiguracji:

* Co najmniej dwa interfejsy sieciowe podłączone na każdym kontrolerze, aby uzyskać dostęp z chmury i jeden dla interfejsu iSCSI. DANE 0 portu jest automatycznie włączona i skonfigurowana za pośrednictwem konsoli szeregowej urządzenia. Oprócz dane 0 innego portu danych musi również zostać skonfigurowane przy użyciu klasycznego portalu Azure. W takim przypadku połączyć dane 0 port do głównej sieci LAN (sieci z dostępem do Internetu). Inne porty danych można podłączyć do segmentu sieci, w zależności od roli zamierzone SAN/iSCSI sieci LAN (VLAN).
* Interfejsy identyczne na każdym kontrolerze podłączone do tej samej sieci, aby zapewnić dostępność, jeśli do pracy awaryjnej kontrolera. Na przykład jeśli chcesz połączyć dane 0 i dane 3 dla jednego z kontrolerów konieczne łączenie odpowiednie dane 0 i dane 3 na innym kontrolerze.

Należy pamiętać o wysokiej dostępności i wydajność:

* Jeśli to możliwe, należy skonfigurować parę interfejsu sieciowego, aby uzyskać dostęp do chmury (1 GbE) i inną parę dla interfejsu iSCSI (10 GbE zalecana) na każdym kontrolerze.
* Jeśli to możliwe, interfejsów sieciowych przez każdy z kontrolerów połączyć się z dwóch różnych przełączników, aby zapewnić dostępność awariami przełącznika. Na rysunku przedstawiono dwie 10 GbE interfejsy sieciowe, dane 2 i dane 3 przez każdy z kontrolerów podłączone do dwóch różnych przełączników.

Aby uzyskać więcej informacji, zapoznaj się **interfejsy sieciowe** w obszarze [wymagania dotyczące wysokiej dostępności dla urządzenia StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Jeśli nadawczo-odbiorczych SFP + za pomocą interfejsów sieciowych 10 GbE, należy użyć podanego QSFP-SFP + kart. Aby uzyskać więcej informacji, przejdź do [obsługiwanym sprzęcie dla interfejsów sieciowych 10 GbE w urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Okablowanie portu szeregowego
Wykonaj poniższe kroki, aby Podłączanie kabli portu szeregowego.

#### <a name="to-cable-for-serial-connection"></a>Aby kabel szeregowy połączenia
1. Urządzenie ma portu szeregowego na każdym kontrolerze, który jest identyfikowany przez ikona klucza. Zapoznaj się na ilustracji w [okablowanie sieci](#network-cabling) sekcji, aby zlokalizować portów szeregowych płyty montażowej urządzenia.
2. Zidentyfikuj active kontroler na płyty montażowej Twoje urządzenie. Migający LED niebieski wskazuje, że kontrolera jest aktywny.
3. Użyj podana kable szeregowe (w razie potrzeby przenośnego konwerter szeregowego USB), a podłączyć do portu szeregowego active kontrolera konsoli lub komputera (z emulacji terminala do urządzenia).
4. Zainstaluj sterowniki seryjny USB (dostarczane z urządzeniem) na komputerze.
5. Skonfiguruj połączenie szeregowe w następujący sposób: 115 200 transmisji, 8 bitów danych 1 bit zatrzymania, bez parzystości i sterowanie przepływem wartość None.
6. Sprawdź, czy połączenie działa, naciskając klawisz Enter w konsoli. Menu konsoli szeregowej powinny być wyświetlane.

> [!NOTE]
> **Zarządzania lights-Out**: gdy urządzenie jest zainstalowane w zdalnym centrum danych lub w pokoju komputera o ograniczonym dostępie, upewnij się, czy połączenia szeregowe do obu kontrolerów zawsze są podłączone do przełącznika konsoli szeregowej lub podobnego sprzętu. Dzięki temu poza pasmem zdalnego sterowania i pomocy technicznej operacje przypadku zakłócenia w sieci lub nieoczekiwanych awarii.
> 
> 

Urządzenie jest teraz kablem zasilania, dostępu do sieci i połączenie szeregowe. Następnym krokiem jest konfigurowanie oprogramowania i wdrażania urządzenia.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [wdrażania i konfigurowania lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

