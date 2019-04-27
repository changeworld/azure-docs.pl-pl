---
title: Instalowanie programu Microsoft Azure StorSimple 8600 urządzenia | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób Rozpakowywanie, montowanie i Podłączanie kabli do urządzenia StorSimple 8600, aby wdrożyć i skonfigurować oprogramowanie.
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
ms.openlocfilehash: be3f68a00647840801e7c205d7abb34b718bd61c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630941"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Je rozpakować, zamontować w stojaku i Podłączanie kabli do urządzenia StorSimple 8600
## <a name="overview"></a>Omówienie
Usługi Microsoft Azure StorSimple 8600 urządzenia podwójną obudowy i składa się z podstawowym i obudowy EBOD. W tym samouczku wyjaśniono, jak rozpakować, zamontować w stojaku i StorSimple 8600 kabel urządzeń sprzętowych przed należy skonfigurować oprogramowanie StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Rozpakowywanie urządzenia StorSimple 8600
W poniższych krokach przedstawiono wyraźne, szczegółowe instrukcje dotyczące rozpakowania urządzenia magazynu StorSimple 8600. To urządzenie jest dostarczany w dwóch polach: jeden dla podstawowego obudowy i inny wpis dla obudowy EBOD. Następnie umieścić te dwa pola w jednym polu.

### <a name="prepare-to-unpack-your-device"></a>Przygotowanie do rozpakowywania urządzenia
Zanim można rozpakować urządzenie, przejrzyj następujące informacje.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png)![ikonę ciężki](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **ostrzeżenie!**

1. Upewnij się, że dwie osoby dostępne na potrzeby zarządzania wagę urządzenia, jeśli są obsługującą go ręcznie. W pełni skonfigurowany obudowy można porównać do 32 kg (70 lbs.).
2. Umieść pudełko na płaskiej, poziomej powierzchni.

Następnie wykonaj poniższe kroki, aby rozpakować urządzenie.

#### <a name="to-unpack-your-device"></a>Aby rozpakować urządzenie
1. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. Proszę [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) ułatwią Ci ocenić, czy urządzenie jest w dobrym stanie.
2. Otwórz okno zewnętrzny, a następnie podjęcia się dwa pola odpowiadające podstawową i obudowy EBOD. Można teraz Rozpakuj podstawową i EBOD obudowy. Na poniższej ilustracji przedstawiono nierozpakowane widok jednego obudowy.
   
    ![Rozpakowywanie urządzenia magazynu](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Wczytaj widok urządzenia magazynu**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Okno dokumentu |
   |   2 |Kabli SAS (w zasobniku kable i akcesoriów) |
   |   3 |Dolny maskotkę witryny |
   |   4 |Urządzenie |
   |   5 |Najważniejsze maskotkę witryny |
   |   6 |Pole akcesoriów |
3. Po rozpakowaniu dwa pola, upewnij się, że masz:
   
   * 1 obudowy podstawowej (głównej obudowy i obudowy EBOD znajdują się w dwóch oddzielnych pól)
   * 1 obudowy EBOD
   * kable 4, 2, w każdym polu
   * 2 kable sygnatury dostępu Współdzielonego (nawiązać głównej obudowy obudowy EBOD)
   * 1 skrzyżowanego kabla Ethernet
   * 2 kable konsoli szeregowej
   * Konwerter USB seryjny 1 dostęp szeregowy
   * 4 QSFP-do-SFP + kart do użycia z interfejsów sieciowych 10 GbE
   * 2 stojak zestawów (4 po stronie platformy rails za pomocą zainstalowanie sprzętu, 2 dla podstawowego obudowy i obudowy EBOD), 1 w każdym polu
   * Wprowadzenie dokumentacji wprowadzenie
     
     Jeśli nie masz żadnych elementów wymienionych powyżej, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).  

Następnym krokiem jest zamontowanie urządzenia na stojaku.

## <a name="rack-mount-your-storsimple-8600-device"></a>Urządzenie StorSimple 8600 zamontować w stojaku
Wykonaj poniższe czynności, aby zainstalować urządzenia magazynu StorSimple 8600 w standardowym stelażu 19-calowy z przodu i wpisy na tylnej. To urządzenie jest dostarczany z dwa załączniki: podstawowy obudowy i obudowy EBOD. Oba te muszą być montowane w stojaku.

Instalacja składa się z wielu kroków, z których każdy jest omówione w poniższych procedurach.

> [!IMPORTANT]
> Urządzenia StorSimple musi być montowane w stojaku do prawidłowego działania.
> 
> 

### <a name="site-preparation"></a>Przygotowanie miejsca
Obudowach musi być zainstalowany w standardowych stojaku 19-calowy, która ma zarówno frontonu, jak i tylnej wpisów. Poniższa procedura umożliwia przygotowanie do instalacji stojaka.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Aby przygotować lokacji stojak instalacji
1. Upewnij się, że podstawowy i obudów EBOD są nieaktywnych bezpiecznie na powierzchni Praca stała stabilne i poziomu (lub podobny).
2. Sprawdź, czy witryny, na którym zamierzasz skonfigurować ma standardowy Przywrócenie zasilania sieciowego z niezależne źródło lub stojak jednostki dystrybucji zasilania (PDU) przy użyciu zasilacz awaryjny (UPS).
3. Upewnij się, że jeden 4U gniazdo (2 X 2U) jest dostępna w stojaku, w którym zamierzasz zainstalować obudowach.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png)![ikonę ciężki](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **ostrzeżenie!**

 Upewnij się, że dwie osoby dostępne na potrzeby zarządzania wagi, jeśli są w nim Obsługa konfiguracji urządzenia ręcznie. W pełni skonfigurowany obudowy można porównać do 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Wymagania wstępne stojak
Obudowach są przeznaczone do instalacji w standardowym stelażu 19-calowy cabinet za pomocą:

* Minimalna głębokość 27.84 cala od regału post do wpisu
* Waga maksymalny 32 kg dla urządzenia
* Maksymalna ciśnienia z 5 Pascal (0,5 mm wody miernika)

### <a name="rack-mounting-rail-kit"></a>Instalowanie w stelażu szyny kit
Instalowanie platformy rails zestaw zapewnia się do użytku z pliku cabinet 19-calowy stojaka. Do obsługi wagi maksymalna obudowy zostały przetestowane szyny. Te platformy rails będzie również umożliwiać instalację wielu obudowach bez utraty miejsca w stojaku. Najpierw zainstaluj obudowy EBOD.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Aby zainstalować obudowy EBOD na szyny
1. Ten krok należy wykonać tylko wtedy, gdy rails wewnętrzne nie są zainstalowane na urządzeniu. Zazwyczaj rails wewnętrzne są instalowane na etapie produkcji. Jeśli nie zainstalowano platformy rails, zainstaluj slajdy szyny po lewej stronie i szyny po prawej stronie na stronach obudowy obudowy. Dołącz one, przy użyciu sześciu śruby metryki na każdej stronie. Aby ułatwić orientacji, slajdy szyny są oznaczone **LH — przód** i **RH — przód**, i zakończenia, który jest dołączony do tyłu obudowa ma końcem.
   
    ![Dołączanie slajdy szyny do obudowy obudowy](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Dołączanie szyny slajdów na stronach obudowa**
   
   | Label | Opis |
   | --- | --- |
   |  1 |M 3 x 4 śruby przycisk główny |
   |  2 |Podstawa montażowa slajdów |
2. Dołącz szyny po lewej stronie i zestawów po prawej stronie do stojak cabinet pionowej elementy członkowskie. Nawiasy są oznaczone **LH**, **RH**, i **ta strona się** przeprowadzenie Cię przez proces poprawnej orientacji.
3. Znajdź bolce szyny z przodu i z tyłu zestawu prowadnic. Rozszerz szyny mieści się między wpisów w stojaku i wstawianie numery PIN frontonu i wpis w stojaku tyłu składowej pionowej luki. Pamiętaj, że zestaw szyny jest poziom.
4. Zabezpiecz zestawu szyny w stojaku pionowej elementy członkowskie, używając dwóch śrub metryki udostępniane. Użyj jednej śrubie na frontonu i jednej z tyłu.
5. Powtórz te kroki dla innych zestawów szyny.
   
     ![Dołączanie slajdy szyny stojaku cabinet](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Dołączanie szyny zestawów w stojaku**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Granica gwintowanym |
   |   2 |Gwintowanym wpis w stojaku frontonu dziura kwadrat |
   |   3 |LEFT numerów PIN lokalizacji frontonu szyny |
   |   4 |Granica gwintowanym |
   |   5 |Numery PIN lokalizacji po lewej stronie tylnej szyny |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Instalowanie obudowy EBOD w stojaku
Za pomocą stojaku, które właśnie zostały zainstalowane, wykonaj następujące kroki, aby zainstalować obudowy EBOD w stojaku.

#### <a name="to-mount-the-ebod-enclosure"></a>Aby zainstalować obudowy EBOD
1. Z Asystentem Podnieś obudowa i dostosować ją przy użyciu platformy rails stojaka.
2. Dokładnie Wstawianie obudowa platformy rails, a następnie Wypchnij je całkowicie w stojaku cabinet.
   
    ![Wstawianie urządzenia w stojaku](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Instalowanie obudowy w stojaku**
3. Ściąganie bezpłatne limity, aby usunąć limity kołnierza po lewej i prawej frontonu. Limity kołnierza po prostu przyciąganie do stopka.
4. Zabezpiecz obudowa w stojaku, instalując jednej podanej śrubie Phillips head za pośrednictwem każdej kołnierza, lewy i prawy.
5. Zainstaluj caps kołnierza, naciskając je w określonej pozycji i przyciąganie ich w miejscu.
   
     ![Instalowanie caps kołnierza](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalowanie caps kołnierza**
   
   | Label | Opis |
   | --- | --- |
   |   1 |Gwintowanym zaczepienia obudowy |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Instalowanie podstawowego obudowy w stojaku
Po zakończeniu instalowania obudowy EBOD należy zainstalować podstawowy obudowy, wykonując czynności.

> [!NOTE]
> * Użytkownik może mieć kilka pustych miejsc w stojaku między podstawowym obudowy i obudowy EBOD.
> * Przy użyciu kabla sygnatury dostępu Współdzielonego podana 2 mln nawiązać obudowy EBOD obudowy podstawowego.
> * Nie ma żadnych ograniczeń względne położenie głównego jednostki do jednostki EBOD. W związku z tym, obudowy podstawowego można umieścić w górnym miejsca i obudowy EBOD poniżej — i odwrotnie.
> 
> 

Następnym krokiem jest Podłączanie kabli do urządzenia do zasilania, sieci i dostęp szeregowy.

## <a name="cable-your-storsimple-8600-device"></a>Podłączanie kabli do urządzenia StorSimple 8600
Poniższe procedury dotyczą sposobu podłączanie kabli do urządzenia StorSimple 8600 zasilania, sieci i połączenia szeregowe.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem Podłączanie kabli do urządzenia, będą potrzebne:

* Całkowicie rozpakowane swojej głównej obudowy i obudowa EBOD
* 4 kable zasilania (2 dla podstawowej i obudowy EBOD), które są dołączone do urządzenia
* 2 kabli SAS dostarczone z urządzeniem, aby połączyć obudowy EBOD głównej obudową
* Dostęp do 2 jednostki dystrybucji zasilania (PDU) (zalecane)
* Kable sieciowe
* Podany kable szeregowe
* Konwerter seryjny-USB z odpowiedniego sterownika, które są zainstalowane na komputerze (w razie potrzeby)
* Podany 4 QSFP-do-SFP + kart do użycia z interfejsów sieciowych 10 GbE
* [Obsługiwany sprzęt dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>Sygnatury dostępu Współdzielonego i okablowanie zasilania
Urządzenie ma podstawowy obudowy i obudowy EBOD. Wymaga to jednostki, które można ze sobą rozpakowane Serial Attached SCSI (SAS) łączność i zasilania.

Podczas konfigurowania tego urządzenia po raz pierwszy, sygnatury dostępu Współdzielonego okablowania najpierw wykonaj kroki, a następnie wykonaj kroki odpowiednie dla przewody zasilania.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Okablowanie sieci
Urządzenie jest w konfiguracji aktywne-w gotowości: w dowolnym momencie jeden moduł kontrolera jest aktywny, a przetwarzanie wszystkich operacji dysku i sieci podczas moduł kontroler jest w stanie wstrzymania. W przypadku awarii kontrolera kontroler zapasowy natychmiast aktywuje i kontynuuje wszystkie operacje dysku i sieci.

Aby zapewnić obsługę pracy w trybie failover nadmiarowe kontrolera, musisz okablowanie sieci urządzeń, jak pokazano w poniższych krokach.

#### <a name="to-cable-for-network-connection"></a>Aby kabel dla połączenia sieciowego
1. Urządzenie ma sześć interfejsów sieciowych na każdym kontrolerze: cztery 1 GB/s i 10 dwa porty Ethernet GB/s. Odnoszą się do poniższej ilustracji w celu identyfikowania portów danych na płyty montażowej urządzenia.
   
     ![Płyty montażowej urządzenia 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Tylnej stronie urządzenia przedstawiający porty danych**
   
   | Label | Opis |
   | --- | --- |
   |   0,1,4,5 |Interfejsy sieciowe w usłudze 1 GbE |
   |   2,3 |Interfejsów sieciowych 10 GbE |
   |   6 |Porty szeregowe |
2. Zobacz poniższy diagram okablowanie sieci. (Konfiguracja minimalna sieci jest reprezentowany przez niebieska linia ciągła. Aby uzyskać wysoką dostępność i wydajność dodatkowej konfiguracji znajduje się za pomocą linii kropkowanej.)

![Podłączanie kabli do urządzenia 4U sieci](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Okablowania dla urządzenia sieciowego**

| Label | Opis |
| --- | --- |
| A |Sieć LAN z dostępem do Internetu |
| B |Kontrolera 0 |
| C |MODULE PCM 0 |
| D |Kontrolera 1 |
| E |PCM 1 |
| F |Kontrolera EBOD 0 |
| G |Kontrolera EBOD 1 |
| GODZ., I |Hosty (na przykład serwery plików) |
| 0-5 |Interfejsy sieciowe |
| 6 |Podstawowy obudowy |
| 7 |Obudowa EBOD |

W przypadku okablowania urządzenia, wymaga minimalnej konfiguracji:

* Co najmniej dwa interfejsy sieciowe podłączone na każdym kontrolerze przy użyciu jednego, aby uzyskać dostęp do chmury i jeden dla interfejsu iSCSI. DANE 0 portu jest automatycznie włączona i skonfigurowana za pośrednictwem konsoli szeregowej urządzenia. Oprócz interfejs DATA 0 inny port danych wymaga także można skonfigurować za pomocą klasycznego portalu Azure. W tym przypadku łącz dane 0 port do podstawowej sieci LAN (sieci z dostępem do Internetu). Inne porty dane mogą być połączone do segmentu sieci, w zależności od tego, ma pełnić SAN/iSCSI sieci LAN (VLAN).
* Identyczne interfejsy na każdym kontrolerze połączone z tą samą siecią, aby zapewnić dostępność w przypadku przejścia do trybu failover kontrolera. Na przykład jeśli chcesz połączyć interfejs DATA 0 i DATA 3 na jeden z kontrolerów należy połączyć odpowiadające im dane 0 i DATA 3 na drugim kontrolerze.

Należy pamiętać o wysokiej dostępności i wydajności:

* Jeśli to możliwe, należy skonfigurować parę interfejsu sieciowego, aby uzyskać dostęp do chmury (1 GbE) i kolejną parę iSCSI (zalecane 10 GbE) na każdym kontrolerze.
* Jeśli to możliwe, interfejsy sieciowe z każdego kontrolera połączyć się z dwóch różnych przełączników, aby zapewnić dostępność awariami przełącznika. Na rysunku przedstawiono dwa 10 GbE interfejsy sieciowe, dane 2 i DATA 3 z każdego kontrolera podłączone do dwóch różnych przełączników. Aby uzyskać więcej informacji, zobacz **interfejsy sieciowe** w obszarze [wymaganiami wysokiej dostępności dla urządzenia StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Korzystania SFP + należy za pomocą usługi interfejsów sieciowych 10 GbE, użyj podanego QSFP-SFP + kart. Aby uzyskać więcej informacji, przejdź do [obsługiwany sprzęt dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Okablowanie portu szeregowego
Wykonaj poniższe kroki, aby podłączyć do portu szeregowego.

#### <a name="to-cable-for-serial-connection"></a>Aby kabel szeregowy połączenia
1. Urządzenie ma portu szeregowego na każdym kontrolerze, który jest identyfikowany przez ikona klucza. Aby zlokalizować portów szeregowych, zapoznaj się z ilustracją, na której przedstawiono dane porty na odwrocie podkładki urządzenia.
2. Identyfikowanie aktywnego kontrolera na płyty montażowej Twojego urządzenia. Migająca dioda niebieski wskazuje, czy kontroler jest aktywny.
3. Podana kabla szeregowego (jeśli jest to konieczne, konwerter szeregowego USB na komputerze przenośnym) i podłączyć konsoli lub komputera (przy użyciu emulacji terminala na urządzeniu) do portu szeregowego aktywnego kontrolera.
4. Na komputerze, należy zainstalować sterowniki USB seryjny (dostarczane z urządzeniem).
5. Skonfiguruj połączenie szeregowe w następujący sposób:
   
   * 115 200 transmisji
   * 8 bitów danych
   * 1 bit stopu
   * Brak parzystości
   * Ustaw na sterowanie przepływem **None**
6. Sprawdź, czy połączenie działa, naciskając klawisz Enter w konsoli. Powinny być wyświetlane menu konsoli szeregowej.

> [!NOTE]
> **Zarządzanie obsługą technologii lights-Out:** Gdy urządzenie jest zainstalowane w zdalnym centrum danych lub w pomieszczeniu o ograniczonym dostępie, komputerów, upewnij się, czy połączenia szeregowe na obu kontrolerach zawsze są podłączone do konsoli szeregowej lub podobnego sprzętu. Dzięki temu out-of-band zdalnego sterowania i pomocy technicznej operacji w przypadku przerwy w działaniu sieci lub nieoczekiwanych awarii.
> 
> 

Ukończono okablowania urządzenia pod kątem mocy obliczeniowej, dostępu do sieci i połączenie szeregowe. Następnym krokiem jest skonfigurowanie oprogramowania na urządzeniu.

## <a name="next-steps"></a>Kolejne kroki
Teraz można przystąpić do [wdrożyć i skonfigurować urządzenie StorSimple w środowisku lokalnym](storsimple-8000-deployment-walkthrough-u2.md).

