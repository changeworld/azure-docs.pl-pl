---
title: Instalowanie programu Microsoft Azure StorSimple 8600 urządzenia | Dokumentacja firmy Microsoft
description: Opisuje sposób Rozpakowywanie, montowanie i Podłączanie kabli do urządzenia StorSimple 8600, aby wdrożyć i skonfigurować oprogramowanie.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 5a8b460441323cb668a3d9939cce434636afc44d
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743724"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Rozpakowywanie, w stojaku, a Podłączanie kabli do urządzenia StorSimple 8600
## <a name="overview"></a>Przegląd
Do urządzenia 8600 Microsoft Azure StorSimple urządzenia podwójną obudowa i składa się z podstawowym i obudowy EBOD. W tym samouczku wyjaśniono, jak rozpakować, zamontować w stojaku i kabel StorSimple 8600 urządzeń sprzętowych przed należy skonfigurować oprogramowanie StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Rozpakuj do urządzenia StorSimple 8600
Poniższe kroki zawierają wyraźne, szczegółowe instrukcje na temat rozpakować urządzenia magazynującego StorSimple 8600. To urządzenie jest dostarczany w dwóch polach, jeden dla podstawowego obudowy i drugi dla obudowa EBOD. Te dwa pola następnie są umieszczane w jednym polu.

### <a name="prepare-to-unpack-your-device"></a>Przygotowanie do rozpakowania urządzenia
Aby rozpakować urządzenia, przejrzyj następujące informacje.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png)![ikona ciężki](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **ostrzeżenie!**

1. Upewnij się, że masz dwie osoby dostępne na potrzeby zarządzania wagi urządzenia, jeśli są obsługuje go ręcznie. Obudowa pełni skonfigurowany można porównać do 32 kg (70 kg).
2. Umieść pole na powierzchni płaskiej, poziomu.

Następnie wykonaj poniższe kroki, aby rozpakować urządzenia.

#### <a name="to-unpack-your-device"></a>Aby rozpakować urządzenia
1. Sprawdź, czy pole i piana pakowania crushes, części, szkody limitu górnego lub widocznego uszkodzenia. Jeśli pole lub pakowania poważnie jest uszkodzony, nie otwieraj okno. Sprawdź [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) ułatwiające oceny, czy urządzenie jest w dobrym stanie.
2. Otwórz zewnętrznego pola, a następnie wyjmij dwa pola odpowiadające podstawowym i obudowy EBOD. Można teraz Rozpakuj serwer podstawowy i obudowy EBOD. Na poniższej ilustracji przedstawiono rozpakowanego widoku jednego z obudów.
   
    ![Rozpakuj urządzenia magazynu](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Wczytaj widoku urządzenia pamięci masowej**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Pole pakowania |
   |   2 |Kabli SAS (w pasku Akcesoria i kable) |
   |   3 |Pianki dołu |
   |   4 |Urządzenie |
   |   5 |Górny pianki |
   |   6 |Pole akcesoriów |
3. Po rozpakowywania dwa pola, upewnij się, że masz:
   
   * 1 obudowa głównej (obudowa podstawowego i obudowa EBOD znajdują się w dwóch oddzielnych pola)
   * Obudowa EBOD 1
   * kable 4, 2, w każdym polu
   * 2 kabli SAS (nawiązać głównej obudowy EBOD obudowy)
   * 1 skrzyżowanego kabla Ethernet
   * 2 kable konsoli szeregowej
   * Konwerter USB seryjny 1 dostęp szeregowy
   * 4 QSFP-do-kart SFP + do użytku z interfejsami sieciowymi Ethernet 10
   * 2 stojak zestawów (4 szyny po stronie z instalowania sprzętu, 2 dla podstawowego obudowy i obudowy EBOD), 1 w każdym polu
   * Pobieranie rozpoczęte dokumentacji
     
     Jeśli nie masz żadnego z elementów wymienionych powyżej, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).  

Następnym krokiem jest w stojaku urządzenia.

## <a name="rack-mount-your-storsimple-8600-device"></a>W stojaku do urządzenia StorSimple 8600
Wykonaj kolejne kroki do zainstalowania urządzenia magazynującego StorSimple 8600 w stojaku standardowe 19 cala z przodu i tylnej wpisów. To urządzenie jest dostarczany z dwóch obudów: głównej obudowy i obudowy EBOD. Oba te muszą być montowane w stojaku.

Instalacja składa się z wielu kroków, z których każdy została szczegółowo opisana w poniższych procedurach.

> [!IMPORTANT]
> Urządzenia StorSimple musi być montowane w stojaku do prawidłowego działania.
> 
> 

### <a name="site-preparation"></a>Przygotowanie lokacji
Obudowy musi być zainstalowany w standardowe stojak cala 19, który ma przodu oraz tylnej wpisów. Poniższa procedura umożliwia przygotowanie do instalacji w stojaku.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Aby przygotować lokacji stojak instalacji
1. Upewnij się, że serwer podstawowy i obudowy EBOD są nieaktywnych bezpiecznie na powierzchni płaskiej stabilny i poziomu pracy (lub podobny).
2. Sprawdź, czy lokacja, w której chcesz skonfigurować ma standardowe zasilacza z niezależne źródło lub stojak jednostki dystrybucji zasilania (PDU) z zasilacz awaryjny (UPS).
3. Upewnij się, że gniazdo (2 X 2U) jeden 4U jest dostępna w stojaku, w którym chcesz zainstalować obudowy.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png)![ikona ciężki](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **ostrzeżenie!**

 Upewnij się, że masz dwie osoby dostępne na potrzeby zarządzania wagi, jeśli Instalator urządzenia są Obsługa ręcznie. Obudowa pełni skonfigurowany można porównać do 32 kg (70 kg).

### <a name="rack-prerequisites"></a>Wymagania wstępne w stojaku
Obudowach są przeznaczone do instalacji w stojaku standard CAL 19 cabinet z:

* Minimalna głębokość 27.84 cali w stojaku post i post
* Maksymalna waga 32 kg dla urządzenia
* Maksymalnym wstecz nacisku Pascal 5 (mm 0,5 limitu górnego miernika)

### <a name="rack-mounting-rail-kit"></a>Stojakach kolei kit
Zestaw instalowanie szyny będzie przeznaczony dla pliku cabinet stojak 19 cala. Szyny zostały przetestowane do obsługi wagi obudowa maksymalna. Te szyny będzie również umożliwiać instalacji wielu obudowach bez utraty miejsca w stojaku. Najpierw zainstaluj obudowa EBOD.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Aby zainstalować obudowa EBOD na szyny
1. Wykonaj ten krok tylko wtedy, gdy wewnętrzny szyny nie są zainstalowane na urządzeniu. Zazwyczaj wewnętrzny szyny są instalowane w fabryce. Jeśli szyny nie są zainstalowane, zainstaluj kolei po lewej i prawej kolei slajdów na stronach obudowy obudowy. Dołącz ich przy użyciu sześciu śruby metryki na każdej stronie. Ułatwiają orientację, są oznaczone slajdów kolei **LH — przodu** i **RH — przodu**, i zakończenia, który jest dołączony do tyłu obudowa ma końcem.
   
    ![Dołączanie slajdów kolei obudowie obudowy](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Dołączanie kolei slajdów na stronach obudowa**
   
   | Etykieta | Opis |
   | --- | --- |
   |  1 |M 3 x 4 śruby head przycisku |
   |  2 |Podstawa montażowa slajdów |
2. Dołącz kolei lewej i po prawej stronie zestawów członkom stojak cabinet pionowej. Nawiasy kwadratowe są oznaczone **LH**, **RH**, i **ta strona w górę** do przeprowadzenia poprawnej orientacji.
3. Znajdź numery PIN kolei na tylne zestawu kolei. Rozszerzanie szyny mieści się między wpisów w stojaku i wstawić numery PIN do przodu i luk w stojaku tyłu post element pionowy. Pamiętaj, że zestaw kolei jest poziom.
4. Zabezpiecz zestawu kolei w stojaku pionowej elementy członkowskie przy użyciu dwóch metryki śruby podane. Użyj jednej śrubie na wierzchu i jeden z tyłu.
5. Powtórz te kroki dla innych zestawu kolei.
   
     ![Dołączanie slajdów kolei stojaku cabinet](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Dołączanie zestawy z kolei w stojaku**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Ładunku gwintowanym |
   |   2 |Gwintowanym post stojak front przerw kwadratowe |
   |   3 |Numery PIN lokalizacji front kolei w lewo |
   |   4 |Ładunku gwintowanym |
   |   5 |Po lewej stronie kolei tylnej lokalizacji PIN |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Instalowanie obudowa EBOD w stojaku
Za pomocą stojaku, które właśnie zostały zainstalowane, wykonaj następujące kroki, aby zainstalować obudowa EBOD w stojaku.

#### <a name="to-mount-the-ebod-enclosure"></a>Aby zainstalować obudowa EBOD
1. Z Asystentem Podnieś obudowa i wyrównanie go z stojaku.
2. Starannie Włóż obudowa do szyny, a następnie Wypchnij go całkowicie w stojaku cabinet.
   
    ![Wstawianie urządzenia w stojaku](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Instalowanie obudowa w stojaku**
3. Usuń caps kołnierza po lewej i prawej front ściąganie wolnego caps. Caps kołnierza po prostu przyciąganie na stopka.
4. Zabezpiecz obudowa w stojaku, instalując co podane gwintowanym Phillips head za pośrednictwem każdej kołnierza, lewy i prawy.
5. Zainstaluj caps kołnierza naciskając je w miejscu i przyciąganie ich w miejscu.
   
     ![Instalowanie kołnierza CAP](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalowanie caps kołnierza**
   
   | Etykieta | Opis |
   | --- | --- |
   |   1 |Obudowa gwintowanym zaczepienia |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Instalowanie podstawowego obudowa w stojaku
Po zakończeniu instalowania obudowa EBOD należy zainstalować podstawowy obudowy, wykonując czynności.

> [!NOTE]
> * Użytkownik może mieć kilka pustych miejsc w stojaku między podstawowym obudowy i obudowy EBOD.
> * Przy użyciu kabla SAS podana 2m nawiązać głównej obudowa obudowa EBOD.
> * Nie ma żadnych ograniczeń względne położenie head jednostki do jednostki EBOD. W związku z tym obudowa podstawowego można umieścić w górnym miejsca i obudowy EBOD poniżej — albo na odwrót.
> 
> 

Następnym krokiem jest Podłączanie kabli do urządzenia do zasilania, sieci i dostęp szeregowy.

## <a name="cable-your-storsimple-8600-device"></a>Podłączanie kabli do urządzenia StorSimple 8600
Poniższe procedury dotyczą sposobu podłączanie kabli do urządzenia StorSimple 8600 do zasilania, sieci i połączenia szeregowego.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem Podłączanie kabli do urządzenia, będą potrzebne:

* Twoje głównej obudowy i obudowy EBOD całkowicie rozpakowane.
* 4 kable zasilania (2 dla serwera podstawowego i obudowy EBOD), które są dołączone do urządzenia
* 2 kabli SAS dostarczone z urządzeniem, aby połączyć obudowy EBOD podstawowego systemu
* Dostęp do 2 jednostki dystrybucji zasilania (PDU) (zalecane)
* Kable sieciowe
* Podany kable szeregowe
* Konwerter seryjny USB z odpowiedni sterownik zainstalowane na komputerze (w razie potrzeby)
* Podany 4 QSFP-do-kart SFP + do użytku z interfejsami sieciowymi Ethernet 10
* [Obsługiwane sprzętu dla interfejsów sieciowych 10 GbE w urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>Sygnatury dostępu Współdzielonego i okablowanie zasilania
Urządzenie ma głównej obudowy oraz obudowy EBOD. Wymaga to jednostki do cala ze sobą, połączenie Serial Attached SCSI (SAS) i zasilania.

Podczas konfigurowania tego urządzenia po raz pierwszy, wykonaj kroki dla sygnatury dostępu Współdzielonego okablowania najpierw, a następnie Zakończ kroki okablowania zasilania.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Okablowanie sieci
Urządzenie znajduje się w konfiguracji aktywnego gotowości: w dowolnym momencie, jeden moduł kontrolera jest aktywna i przetwarzanie wszystkie operacje dysku i sieci podczas moduł kontroler jest w stanie wstrzymania. Jeśli wystąpi błąd kontrolera, wstrzymania kontrolera natychmiast aktywuje i kontynuuje wszystkie operacje dysku i sieci.

Do obsługi tej pracy awaryjnej nadmiarowych kontrolera, musisz kabla sieci urządzenia, jak pokazano w poniższych krokach.

#### <a name="to-cable-for-network-connection"></a>Aby kabel połączenia sieciowego
1. Urządzenie ma sześć interfejsów sieciowych na każdym kontrolerze: cztery 1 GB/s i 10 dwa porty Ethernet GB/s. Można znaleźć na poniższej ilustracji, w celu identyfikowania portów danych na płyty montażowej urządzenia.
   
     ![Płyty montażowej urządzenia 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Powrót z urządzenia przedstawiający porty danych**
   
   | Etykieta | Opis |
   | --- | --- |
   |   0,1,4,5 |Interfejsy sieciowe Ethernet 1 |
   |   2,3 |Interfejsy sieciowe Ethernet 10 |
   |   6 |Porty szeregowe |
2. Zobacz na poniższym diagramie okablowanie sieci. (Konfiguracja minimalna sieci znajduje się pełny liniami niebieski. Wysokiej dostępności i wydajności konieczności dodatkowej konfiguracji znajduje się za pomocą linii przerywana.)

![Podłączanie kabli do urządzenia 4U sieci](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Okablowania urządzenia sieciowego**

| Etykieta | Opis |
| --- | --- |
| A |Sieć LAN z dostępem do Internetu |
| B |Kontrolera 0 |
| C |PCM 0 |
| D |Kontrolera 1 |
| E |PCM 1 |
| F |EBOD kontrolera 0 |
| G |EBOD kontrolera 1 |
| H, I |Hosty (na przykład serwery plików) |
| 0-5 |Interfejsy sieciowe |
| 6 |Obudowa podstawowego |
| 7 |Obudowa EBOD |

W przypadku okablowania urządzenia, wymaga minimalnej konfiguracji:

* Co najmniej dwa interfejsy sieciowe podłączone na każdym kontrolerze, aby uzyskać dostęp z chmury i jeden dla interfejsu iSCSI. DANE 0 portu jest automatycznie włączona i skonfigurowana za pośrednictwem konsoli szeregowej urządzenia. Oprócz dane 0 innego portu danych musi również zostać skonfigurowane przy użyciu klasycznego portalu Azure. W takim przypadku połączyć dane 0 port do głównej sieci LAN (sieci z dostępem do Internetu). Inne porty danych można podłączyć do segmentu sieci, w zależności od roli zamierzone SAN/iSCSI sieci LAN (VLAN).
* Interfejsy identyczne na każdym kontrolerze podłączone do tej samej sieci, aby zapewnić dostępność, jeśli do pracy awaryjnej kontrolera. Na przykład jeśli chcesz połączyć dane 0 i dane 3 dla jednego z kontrolerów konieczne łączenie odpowiednie dane 0 i dane 3 na innym kontrolerze.

Należy pamiętać o wysokiej dostępności i wydajność:

* Jeśli to możliwe, należy skonfigurować parę interfejsu sieciowego, aby uzyskać dostęp do chmury (1 GbE) i inną parę dla interfejsu iSCSI (10 GbE zalecana) na każdym kontrolerze.
* Jeśli to możliwe, interfejsów sieciowych przez każdy z kontrolerów połączyć się z dwóch różnych przełączników, aby zapewnić dostępność awariami przełącznika. Na rysunku przedstawiono dwie 10 GbE interfejsy sieciowe, dane 2 i dane 3 przez każdy z kontrolerów podłączone do dwóch różnych przełączników. Aby uzyskać więcej informacji, zapoznaj się **interfejsy sieciowe** w obszarze [wymagania dotyczące wysokiej dostępności dla urządzenia StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Jeśli używasz nadawczo-odbiorczych SFP + z interfejsów sieciowych 10 GbE, użyj QSFP podana-SFP + kart. Aby uzyskać więcej informacji, przejdź do [obsługiwanym sprzęcie dla interfejsów sieciowych 10 GbE w urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Okablowanie portu szeregowego
Wykonaj poniższe kroki, aby Podłączanie kabli portu szeregowego.

#### <a name="to-cable-for-serial-connection"></a>Aby kabel szeregowy połączenia
1. Urządzenie ma portu szeregowego na każdym kontrolerze, który jest identyfikowany przez ikona klucza. Aby zlokalizować portów szeregowych, zapoznaj się na ilustracji przedstawiono dane porty na tylnej urządzenia.
2. Zidentyfikuj active kontroler na płyty montażowej Twoje urządzenie. Migający LED niebieski wskazuje, że kontrolera jest aktywny.
3. Użyj podana kabel szeregowy (w razie potrzeby przenośnego konwerter szeregowego USB), a podłączyć do portu szeregowego active kontrolera konsoli lub komputera (z emulacji terminala do urządzenia).
4. Zainstaluj sterowniki seryjny USB (dostarczane z urządzeniem) na komputerze.
5. Skonfiguruj połączenie szeregowe w następujący sposób:
   
   * 115 200 transmisji
   * 8 bitów danych
   * 1 bit zatrzymania
   * Brak parzystości
   * Przepływ sterowania ustawioną **None**
6. Sprawdź, czy połączenie działa, naciskając klawisz Enter w konsoli. Menu konsoli szeregowej powinny być wyświetlane.

> [!NOTE]
> **Zarządzanie lights-Out:** gdy urządzenie jest zainstalowane w zdalnym centrum danych lub w pokoju komputera o ograniczonym dostępie, upewnij się, czy połączenia szeregowe do obu kontrolerów zawsze są podłączone do przełącznika konsoli szeregowej lub podobnego sprzętu. Dzięki temu poza pasmem zdalnego sterowania i pomocy technicznej operacji w przypadku przerw w działaniu sieci lub nieoczekiwanych awarii.
> 
> 

Okablowanie do zasilania, dostępu do sieci i połączenie szeregowe urządzenia została ukończona. Następnym krokiem jest skonfigurowanie oprogramowania na urządzeniu.

## <a name="next-steps"></a>Kolejne kroki
Teraz można przystąpić do [wdrażania i konfigurowania lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

