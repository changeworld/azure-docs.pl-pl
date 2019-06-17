---
title: Modyfikowanie konfiguracji urządzenia StorSimple 8000 series | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zmienić konfigurację urządzenia StorSimple, która została już wdrożona za pomocą usługi Menedżer urządzeń StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 774f5a73a5fc30352698c0af0c279fbbe488c480
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60632236"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Modyfikowanie konfiguracji urządzenia StorSimple przy użyciu usługi Menedżer urządzeń StorSimple

## <a name="overview"></a>Omówienie

Witryna Azure portal **ustawienia urządzenia** sekcji **ustawienia** blok zawiera wszystkie parametry urządzenia, które można skonfigurować ponownie na urządzeniu StorSimple, który jest zarządzany przez usługę Menedżer urządzeń StorSimple . W tym samouczku wyjaśniono, jak można użyć **ustawienia** bloku, aby wykonać poniższe zadania poziom urządzenia:

* Modyfikowanie przyjazna nazwa urządzenia
* Zmodyfikuj ustawienia czasu urządzenia
* Przypisz pomocniczy serwer DNS
* Modyfikowanie interfejsów sieciowych
* Zamień lub ponowne przypisywanie adresów IP

## <a name="modify-device-friendly-name"></a>Modyfikowanie przyjazna nazwa urządzenia

Aby zmienić nazwę urządzenia i przypisz go unikatową przyjazną nazwę wybranego, można użyć witryny Azure portal. Użyj **ustawienia ogólne** bloku na urządzeniu, aby zmodyfikować przyjazna nazwa urządzenia. Przyjazna nazwa może zawierać dowolne znaki oraz może zawierać maksymalnie 64 znaki.

> [!NOTE] 
> Nazwa urządzenia w witrynie Azure portal można modyfikować tylko przed ukończeniem instalacji urządzenia. Po ukończeniu minimalnej konfiguracji urządzenia nie można zmienić nazwy urządzenia.

![Urządzenie ogólnie rzecz biorąc Nazwa ustawienia](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Urządzenia StorSimple, która jest połączona z usługą Menedżera urządzeń StorSimple, ma przypisaną nazwę domyślną. Domyślna nazwa zazwyczaj odzwierciedla numer seryjny urządzenia. Na przykład domyślna nazwa urządzenia jest 15 znaków, takich jak 8600-SHX0991003G44HT wskazuje następujące czynności:

* **8600** — wskazuje, model urządzenia.
* **SHX** — wskazuje witryny produkcyjnej.
* **0991003** — wskazuje określony produkt.
* **G44HT**-5 ostatnich cyfr są zwiększane, aby utworzyć unikatowe numery seryjne. Może być zestaw sekwencyjne.

## <a name="modify-device-description"></a>Modyfikacja opisu urządzenia

Użyj **ustawienia ogólne** bloku na urządzeniu, aby modyfikacja opisu urządzenia.

![Opis urządzenia w obszarze Ustawienia ogólne](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Opis urządzenia niemniej przykład często pomoże zidentyfikować właściciela i fizycznej lokalizacji urządzenia. Pole Opis musi zawierać mniej niż 256 znaków.

## <a name="modify-time-settings"></a>Zmodyfikuj ustawienia czasu

Urządzenie musi synchronizować czas w celu uwierzytelniania za pomocą dostawcę usługi magazynu w chmurze. Użyj **ustawienia ogólne** bloku na urządzeniu, aby zmodyfikować ustawienia czasu urządzenia.

![Opis urządzenia w obszarze Ustawienia ogólne](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Wybierz strefę czasową z listy rozwijanej. Można określić maksymalnie dwa serwery protokołu NTP (Network Time):

 - **Podstawowy serwer NTP** -konfiguracja jest wymagana i jest określony, kiedy używać środowiska Windows PowerShell dla usługi StorSimple do skonfigurowania urządzenia. Można określić domyślne systemu Windows Server **time.windows.com** serwer NTP. Podstawowa konfiguracja serwera NTP przy użyciu witryny Azure portal można wyświetlić, ale należy użyć interfejsu programu Windows PowerShell, aby ją zmienić. Użyj `Set-HcsNTPClientServerAddress` polecenia cmdlet, aby zmodyfikować podstawowy serwer NTP dla urządzenia. Aby uzyskać więcej informacji, przejdź do składnia [HcsNTPClientServerAddress zestaw](https://technet.microsoft.com/library/dn688138.aspx) polecenia cmdlet.

- **Pomocniczy serwer NTP** -konfiguracja jest opcjonalne. Portal umożliwia Konfigurowanie zapasowego serwera NTP.

Podczas konfigurowania serwera NTP, upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu. Podczas określania publiczny serwer NTP, upewnij się, że zapory sieciowe i inne urządzenia zabezpieczeń są skonfigurowane i umożliwiają ruch NTP przesyłane do i z siecią zewnętrzną. Jeśli dwukierunkowy ruch NTP nie jest dozwolona, należy użyć wewnętrzny serwer NTP (kontroler domeny Windows zapewnia tę funkcję). Jeśli urządzenie nie może zsynchronizować czasu, nie można może komunikować się z dostawcą magazynu w chmurze.

Aby wyświetlić listę serwerów NTP publicznych, przejdź do [Serwery NTP w sieci Web](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Co się stanie, jeśli urządzenie jest wdrażana w innej strefie czasowej?

Jeśli urządzenie jest wdrażana w innej strefie czasowej, strefę czasową urządzenia zostanie zmieniona. Biorąc pod uwagę, że wszystkie zasady tworzenia kopii zapasowych Użyj strefę czasową urządzenia, zasady tworzenia kopii zapasowych spowoduje automatyczne dopasowanie zgodnie z nowej strefy czasowej. Interwencja użytkownika nie jest wymagane.

## <a name="modify-dns-settings"></a>Modyfikowanie ustawień DNS

Serwer DNS jest używany, gdy urządzenie próbuje nawiązać połączenia z usługą dostawcę usługi magazynu w chmurze. Użyj **ustawienia sieciowe** bloku na urządzeniu, aby wyświetlać i modyfikować skonfigurowane ustawienia systemu DNS. 

![Ustawienia DNS w ustawieniach sieci](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Wysoką dostępność są wymagane do skonfigurowania podstawowy i pomocniczy serwery DNS podczas wdrażania urządzenie początkowe.

**Podstawowy serwer DNS** — Użyj programu Windows PowerShell dla usługi StorSimple najpierw określić podstawowy serwer DNS podczas początkowej konfiguracji. Można ponownie skonfigurować podstawowy serwer DNS tylko za pośrednictwem interfejsu programu Windows PowerShell. Użyj `Set-HcsDNSClientServerAddress` polecenia cmdlet, aby zmodyfikować podstawowy serwer DNS w urządzeniu. Aby uzyskać więcej informacji, przejdź do składnia [HcsDNSClientServerAddress zestaw](https://technet.microsoft.com/library/dn688138.aspx) polecenia cmdlet.

**Pomocniczy serwer DNS** — Aby zmodyfikować pomocniczy serwer DNS, należy użyć `Set-HcsDNSClientServerAddress` polecenia cmdlet w interfejsie programu Windows PowerShell urządzenia lub **ustawienia sieciowe** bloku urządzenia StorSimple w witrynie Azure portal.

Aby zmodyfikować pomocniczy serwer DNS w witrynie Azure portal, wykonaj następujące czynności.

1. Przejdź do usługi Menedżer urządzeń StorSimple. Z listy urządzeń wybierz i kliknij swoje urządzenie.

2. W **ustawienia** przejdź do bloku **ustawienia urządzenia > sieć**. Spowoduje to otwarcie **ustawienia sieciowe** bloku. Kliknij przycisk **ustawienia DNS** kafelka. Zmodyfikuj adres IP pomocniczego serwera DNS.

    ![Zmodyfikuj adres IP pomocniczego serwera DNS](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Na pasku poleceń kliknij **Zapisz** i po wyświetleniu monitu o potwierdzenie, kliknij przycisk **OK**.

    ![Zapisz i Potwierdź zmiany](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Modyfikowanie interfejsów sieciowych

Urządzenie ma sześć interfejsów sieciowych urządzenia, z których cztery są 1 GbE i dwa z nich są 10 GbE. Te interfejsy są oznaczone jako interfejs DATA 0 – 5 danych. Interfejs DATA 0, 1 danych, dane 4 i 5 danych są 1 GbE dane 2 i DATA 3 są interfejsów sieciowych 10 GbE.

Użyj **ustawienia sieciowe** blok umożliwiający skonfigurowanie wszystkich interfejsów, które ma być używany.

![Skonfiguruj interfejsy sieciowe za pośrednictwem ustawień sieci](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Aby zapewnić wysoką dostępność, zaleca się mieć co najmniej dwóch interfejsów iSCSI i dwóch interfejsów z włączoną obsługę chmury na urządzenia. Firma Microsoft zaleca się, ale nie wymagają wyłączone nieużywane interfejsy.

Dla każdego interfejsu sieciowego są wyświetlane następujące parametry:

* **Szybkość** — nie jest parametrem konfigurowanych przez użytkownika. Interfejs DATA 0, 1 danych, dane 4 i 5 dane są zawsze 1 GbE, dane 2 i DATA 3 są interfejsów 10 GbE.
  
  > [!NOTE]
  > Szybkość i dupleks są zawsze auto negocjowane. Duże ramki nie są obsługiwane.
  
* **Stan interfejsu** — interfejs może zostać włączona lub wyłączona. Jeśli włączona, urządzenie będzie podejmować próby przy użyciu interfejsu. Firma Microsoft zaleca włączenie tylko te interfejsy, które są połączone z siecią i używane. Wyłącz wszystkie interfejsy, które nie korzystają.
* **Typ interfejsu** — ten parametr umożliwia do izolacji ruchu iSCSI z ruchu magazynu w chmurze. Ten parametr może być jedną z następujących czynności:
  
  * **Chmura włączona** — po włączeniu urządzenia będzie używać tego interfejsu do komunikowania się z chmurą.
  * **włączoną obsługę iSCSI** — po włączeniu urządzenia będzie używać tego interfejsu do komunikacji z hostem iSCSI.
    
    Zalecamy izolowanie ruchu iSCSI z ruchu magazynu w chmurze. Należy również zauważyć, jeśli hosta w tej samej podsieci co urządzenie, nie trzeba przypisać bramę; jednak hosta znajduje się w innej podsieci niż urządzenie, należy przypisać bramy.
* **Adres IP** — po skonfigurowaniu wszystkich interfejsów sieciowych, należy skonfigurować wirtualnego adresu IP (VIP). Może to być IPv4 lub IPv6 lub oba. Rodziny adresów IPv4 i IPv6 są obsługiwane w przypadku interfejsów sieciowych urządzenia. Korzystając z protokołu IPv4, określ adres IP, 32-bitowych (*xxx.xxx.xxx.xxx*) w zapisie kropkowo-cyfrowym. Korzystając z protokołu IPv6, po prostu podać prefiks 4-cyfrowego i 128-bitowy adres zostanie automatycznie wygenerowany dla interfejsu sieciowego urządzenia na podstawie tego prefiksu.
* **Podsieci** — to odnosi się do maski podsieci i jest skonfigurowany za pośrednictwem interfejsu programu Windows PowerShell.
* **Brama** — jest to brama domyślna, które mają być używane przez ten interfejs, podczas próby komunikacji z węzłów, które nie są dostępne w ramach tej samej przestrzeni adresów IP (podsieć). Brama domyślna musi być w tej samej przestrzeni adresowej (podsieć), co interfejs adres IP, zgodnie z ustaleniami maskę podsieci.
* **Ustalone adresu IP** — to pole jest dostępne tylko wtedy, gdy Konfigurowanie DATA 0 interfejsu. Dla operacji takich jak aktualizacje lub Rozwiązywanie problemów z urządzeniem może być konieczne do nawiązania bezpośredniego połączenia z kontrolerem urządzenia. Stały adres IP można uzyskać dostęp, aktywnych i pasywnych kontrolera na urządzeniu z systemem.

> [!NOTE]
> * Aby zapewnić prawidłowe działanie, sprawdź szybkość interfejsu i dupleks na przełącznik, który jest połączony interfejs każdego urządzenia. Interfejsy przełącznik należy albo negocjowania za pomocą lub można skonfigurować sieci Gigabit Ethernet (1000 MB/s) i być pełny dupleks. Interfejsy działających w wolniejszej lub półdupleksu spowoduje problemy z wydajnością.
> * Aby zminimalizować zakłócenia i przestoje, firma Microsoft zaleca, aby włączyć portfast na wszystkich portów przełącznika które będą łączyć interfejsu sieci iSCSI urządzenia. Pozwoli to zagwarantować, że połączenie sieciowe może zostać szybko ustanowiona zdarzenia na pracę awaryjną.

### <a name="configure-data-0"></a>Skonfiguruj interfejs DATA 0

Interfejs DATA 0 jest chmura włączona domyślnie. Podczas konfigurowania interfejs DATA 0, również musieli skonfigurować dwie stałe adresy IP, jeden dla każdego kontrolera. Te stałe adresy IP można uzyskać bezpośredni dostęp kontrolery urządzeń pamięci i są przydatne podczas instalowania aktualizacji na urządzeniu, do wyrzucania elementów bezużytecznych zapewnić prawidłowe działanie, lub gdy uzyskujesz dostęp do kontrolerów na potrzeby rozwiązywania problemów.

Można ponownie skonfigurować stały kontrolerów IP za pomocą bloku ustawienia 0 danych.

![Skonfiguruj interfejs sieciowy — DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Stałe adresy IP dla kontrolera są używane do obsługi aktualizacji urządzenia i algorytmy odzyskiwanie miejsca (wyrzucania elementów bezużytecznych), aby zapewnić prawidłowe działanie. W związku z tym stałe adresy IP muszą być routowalne i mieć możliwość łączenia z Internetem.

### <a name="configure-data-1---data-5"></a>Konfigurowanie danych 1 — dane 5

Dla danych 1 - interfejsów sieciowych 5 danych, można skonfigurować ustawienia sieciowe, jak pokazano na poniższym zrzucie ekranu:

![Skonfiguruj interfejsy sieciowe DATA 1 - 5 danych](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Zamień lub ponowne przypisywanie adresów IP

Obecnie każdy interfejs sieciowy na kontrolerze jest przypisany adres VIP, która jest używana (za pomocą tego samego urządzenia lub innego urządzenia w sieci), następnie kontrolera zakończy się niepowodzeniem przez. Wymiany lub ponownego przypisania adresów VIP dla interfejsu sieciowego urządzenia, należy wykonać procedury właściwe jako można utworzyć zduplikowanego sytuacji IP.

Wykonaj poniższe kroki, aby zamienić lub ponowne przypisywanie adresów VIP dla wszystkich interfejsów sieciowych:

#### <a name="to-reassign-ips"></a>Ponowne przypisywanie adresów IP

1. Wyczyść adres IP dla obu interfejsów.
2. Po adresy IP są wyczyszczone, należy przypisać nowe adresy IP do odpowiednich interfejsów.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [konfigurowanie wielościeżkowego wejścia/wyjścia dla urządzenia StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Dowiedz się, jak [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

