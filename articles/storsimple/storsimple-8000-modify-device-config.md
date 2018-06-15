---
title: Zmodyfikuj konfigurację urządzenia serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania usługi Menedżer StorSimple urządzenia ponownej konfiguracji urządzenia StorSimple, która została już wdrożona.
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
ms.openlocfilehash: 13ff24c24a881297775fa5f65821e53ceb83c351
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875158"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Modyfikowanie konfiguracji urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia

## <a name="overview"></a>Omówienie

Azure portal **ustawienia urządzenia** sekcji **ustawienia** blok zawiera wszystkie parametry urządzenia, które można skonfigurować na urządzeniu StorSimple, które jest zarządzane przez usługę Menedżer StorSimple urządzenia . W tym samouczku opisano, jak używasz **ustawienia** bloku, aby wykonać poniższe zadania poziomie urządzeń:

* Modyfikowanie przyjazna nazwa urządzenia
* Zmodyfikuj ustawienia czasu urządzenia
* Przypisz pomocniczy serwer DNS
* Modyfikowanie interfejsy sieciowe
* Zamień lub ponowne przypisywanie adresów IP

## <a name="modify-device-friendly-name"></a>Modyfikowanie przyjazna nazwa urządzenia

Aby zmienić nazwę urządzenia i przypisz do niego unikatową przyjazną nazwę wybranego, można użyć portalu Azure. Użyj **ustawienia ogólne** bloku na urządzeniu, aby zmodyfikować przyjazna nazwa urządzenia. Przyjazna nazwa może zawierać żadnych znaków i może zawierać maksymalnie 64 znaki.

> [!NOTE] 
> Nazwa urządzenia w portalu Azure można modyfikować tylko przed ukończeniem instalacji urządzenia. Po zakończeniu minimalną konfigurację urządzenia nie można zmienić nazwy urządzenia.

![Nazwa urządzenia jest ogólnie ustawienia](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Urządzenia StorSimple, która jest połączona z usługą Menedżera urządzeń StorSimple przypisano nazwę domyślną. Domyślna nazwa zazwyczaj odzwierciedla numer seryjny urządzenia. Na przykład domyślna nazwa urządzenia jest 15 znaków, takich jak 8600-SHX0991003G44HT wskazuje następujące czynności:

* **8600** — wskazuje model urządzenia.
* **SHX** — wskazuje miejsce produkcji.
* **0991003** -wskazuje określony produkt.
* **G44HT**-5 ostatnich cyfr numeru są zwiększane, aby utworzyć unikatowe numery seryjne. Może być zestawem sekwencyjnych.

## <a name="modify-device-description"></a>Zmodyfikuj opis urządzenia

Użyj **ustawienia ogólne** bloku na urządzeniu, aby zmodyfikować opis urządzenia.

![Opis urządzenia w obszarze Ustawienia ogólne](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Opis urządzenia zwykle pomaga zidentyfikować właściciela i lokalizacji fizycznej urządzenia. Pole opisu musi zawierać mniej niż 256 znaków.

## <a name="modify-time-settings"></a>Zmodyfikuj ustawienia czasu

Urządzenie musi synchronizować czas w celu uwierzytelniania za pomocą dostawcy usługi magazynu w chmurze. Użyj **ustawienia ogólne** bloku na urządzeniu, aby zmodyfikować ustawienia czasu urządzenia.

![Opis urządzenia w obszarze Ustawienia ogólne](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Wybierz strefę czasową z listy rozwijanej. Można określić maksymalnie dwa serwery protokołu NTP (Network Time):

 - **Podstawowy serwer NTP** — konfiguracja jest wymagana i określono, kiedy skonfigurować urządzenie za pomocą programu Windows PowerShell dla urządzenia StorSimple. Można określić domyślne systemu Windows Server **time.windows.com** jako serwer NTP. Można wyświetlić konfiguracji podstawowego serwera NTP za pośrednictwem portalu Azure, ale muszą używać interfejsu programu Windows PowerShell, jego zmiany. Użyj `Set-HcsNTPClientServerAddress` polecenia cmdlet, aby zmodyfikować podstawowy serwer NTP urządzenia. Aby uzyskać więcej informacji przejdź do synxtax dla polecenia cmdlet [Set-HcsNTPClientServerAddress] (https://technet.microsoft.com/library/dn688138.aspx).

- **Pomocniczy serwer NTP** — konfiguracja jest opcjonalne. Portal umożliwia konfigurowanie pomocniczy serwer NTP.

Podczas konfigurowania serwera NTP, upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu. Podczas określania publicznego serwera NTP, należy się upewnić zapory sieciowe i inne urządzenia zabezpieczeń skonfigurowano zezwalająca na ruch NTP do i z siecią zewnętrzną. Jeśli ruch NTP dwukierunkowego nie jest dozwolone, należy użyć wewnętrzny serwer NTP (kontrolera domeny systemu Windows zapewnia tę funkcję). Jeśli urządzenie nie może zsynchronizować czasu, nie można mogły komunikować się z dostawcą magazynu chmury.

Aby wyświetlić listę serwerów NTP publicznych, przejdź do [Web Serwery NTP](http://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Co się stanie, jeśli urządzenie jest wdrażana w innej strefie czasowej?

Jeśli urządzenie jest wdrażana w innej strefie czasowej, strefa czasowa urządzenia zostanie zmieniona. Biorąc pod uwagę, że wszystkie zasady tworzenia kopii zapasowych Użyj strefy czasowej urządzenia, zasad tworzenia kopii zapasowych spowoduje automatyczne dopasowanie zgodnie z nową strefą czasową. Interwencja użytkownika nie jest wymagane.

## <a name="modify-dns-settings"></a>Zmodyfikuj ustawienia DNS

Serwer DNS jest używany, gdy urządzenie próbuje nawiązać połączenia z dostawcą usług magazynu w chmurze. Użyj **ustawień sieciowych** bloku na urządzeniu, aby wyświetlać i modyfikować skonfigurowanych ustawień DNS. 

![Ustawienia DNS w ustawieniach sieci](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Wysokiej dostępności są wymagane do skonfigurowania serwera podstawowego i pomocniczego serwerów DNS podczas wdrażania wstępna urządzenia.

**Podstawowy serwer DNS** — przy użyciu programu Windows PowerShell dla urządzenia StorSimple można najpierw określić podstawowy serwer DNS podczas instalacji początkowej. Można ponownie skonfigurować podstawowy serwer DNS tylko za pośrednictwem interfejsu programu Windows PowerShell. Użyj `Set-HcsDNSClientServerAddress` polecenia cmdlet, aby zmodyfikować podstawowy serwer DNS urządzenia. Aby uzyskać więcej informacji, przejdź do synxtax dla [HcsDNSClientServerAddress zestaw](https://technet.microsoft.com/library/dn688138.aspx) polecenia cmdlet.

**Pomocniczy serwer DNS** — Aby zmodyfikować pomocniczy serwer DNS, użyj `Set-HcsDNSClientServerAddress` polecenia cmdlet w interfejsie programu Windows PowerShell, urządzenia lub **ustawień sieciowych** bloku urządzenia StorSimple w portalu Azure.

Aby zmodyfikować pomocniczy serwer DNS w portalu Azure, wykonaj następujące kroki.

1. Przejdź do usługi Menedżer urządzeń StorSimple. Z listy urządzeń wybierz i kliknij urządzenie.

2. W **ustawienia** bloku, przejdź do **ustawienia urządzenia > sieci**. Spowoduje to otwarcie **ustawień sieciowych** bloku. Kliknij przycisk **ustawienia DNS** kafelka. Zmodyfikuj dodatkowej adres IP serwera DNS.

    ![Modyfikowanie dodatkowej adderss IP serwera DNS](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Na pasku poleceń kliknij **zapisać** i po wyświetleniu monitu o potwierdzenie, kliknij przycisk **OK**.

    ![Zapisz i potwierdzenie zmian](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Modyfikowanie interfejsy sieciowe

Urządzenie ma sześć interfejsów sieciowych urządzenia, z których cztery są 1 GbE i dwa z nich są 10 GbE. Te interfejsy są oznaczone jako dane 0 – 5 danych. DANE 0, 1 danych, dane 4 i 5 danych są 1 GbE, a dane 2 i dane 3 10 GbE interfejsów.

Użyj **ustawień sieciowych** bloku, aby skonfigurować wszystkie interfejsy do użycia.

![Konfigurowanie interfejsów sieciowych za pośrednictwem ustawień sieci](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Aby zapewnić wysoką dostępność, zaleca się ma co najmniej dwa interfejsy iSCSI i dwa interfejsy włączoną obsługę chmury na urządzeniu. Firma Microsoft zaleca, ale nie wymagają wyłączone nieużywane interfejsów.

Dla każdego interfejsu sieciowego są wyświetlane następujące parametry:

* **Szybkość** — nie można skonfigurować użytkownika parametru. DANE 0, 1 danych, dane 4 i 5 danych są zawsze 1 GbE, a dane 2 i dane 3 10 GbE interfejsów.
  
  > [!NOTE]
  > Szybkość i dupleks są zawsze auto negocjowane. Duże ramki nie są obsługiwane.
  
* **Stan interfejsu** — interfejs może być włączona lub wyłączona. Jeśli włączony, urządzenie będzie podejmować próby przy użyciu interfejsu. Zaleca się włączenie tylko te interfejsy, które są podłączone do sieci i używane. Wyłącz wszystkie interfejsy, które nie są używane.
* **Typ interfejsu** — ten parametr służy do izolacji ruchu iSCSI od ruchu magazynu w chmurze. Ten parametr może mieć jedną z następujących czynności:
  
  * **Chmura włączone** — po włączeniu urządzenia będą używać tego interfejsu do komunikowania się z chmurą.
  * **iSCSI włączone** — po włączeniu urządzenia będą używać tego interfejsu do komunikacji z hostem iSCSI.
    
    Zalecamy izolowanie ruchu iSCSI od ruchu magazynu w chmurze. Należy również zauważyć, jeśli host znajduje się w tej samej podsieci co urządzenie, nie trzeba przypisać bramę; Jednak jeśli host znajduje się w innej podsieci niż urządzenia, należy przypisać bramy.
* **Adres IP** — po skonfigurowaniu wszystkich interfejsów sieciowych, należy skonfigurować wirtualnego adresu IP (VIP). Może to być IPv4 lub IPv6 lub oba. Rodziny adresów IPv4 i IPv6 są obsługiwane dla interfejsów sieciowych urządzenia. Przy użyciu protokołu IPv4, należy określić adres IP 32-bitowych (*xxx.xxx.xxx.xxx*) w notacji dziesiętnej kropki. Podczas korzystania z protokołu IPv6, wystarczy podać prefiks 4-cyfrowego i 128-bitowy adres będą generowane automatycznie dla interfejsu sieci urządzenia na podstawie tego prefiksu.
* **Podsieci** — to odwołuje się do maski podsieci i jest skonfigurowana za pośrednictwem interfejsu programu Windows PowerShell.
* **Brama** — to jest brama domyślna, które mają być używane przez ten interfejs, podczas próby komunikacji z węzłów, które nie są w tej samej przestrzeń adresów IP (podsieci). Bramy domyślnej musi być w tej samej przestrzeni adresowej (podsieć) jako interfejs adres IP, zgodnie z ustaleniami maskę podsieci.
* **Stały adres IP** — to pole jest dostępne tylko podczas konfigurowania dane 0 interfejsu. Dla operacji, takich jak aktualizacje i rozwiązywanie problemów z urządzeniem konieczne może połączyć się bezpośrednio z kontrolerem urządzenia. Stały adres IP umożliwia dostęp do aktywnych i pasywnych kontroler na urządzeniu.

> [!NOTE]
> * Aby zapewnić poprawne działanie, sprawdź szybkość interfejsu i dupleks na przełącznik, który jest połączony interfejs każdego urządzenia. Interfejsy przełącznika negocjowanych albo z ani można skonfigurować dla Gigabit Ethernet (1000 MB/s) i być pełny dupleks. Interfejsy działających w niższych szybkościach lub półdupleksu spowoduje problemy z wydajnością.
> * Aby zminimalizować zakłócenia i przestoje, zaleca się włączenie portfast dla wszystkich portów przełącznika, które będą łączyć urządzenia interfejsu sieciowego iSCSI. Daje to pewność, że połączenie sieciowe może zostać szybko ustanowiona w przypadku trybu failover.

### <a name="configure-data-0"></a>Konfigurowanie danych 0

Interfejs DATA 0 ma włączoną obsługę chmury domyślnie. Podczas konfigurowania dane 0, są również wymagane do skonfigurowania dwie stałe adresy IP, po jednej dla każdego kontrolera. Te stałe adresy IP mogą być używane do dostępu kontrolery urządzeń bezpośrednio i są przydatne podczas instalowania aktualizacji na urządzeniu, na potrzeby wyrzucanie elementów bezużytecznych do poprawnego działania lub gdy uzyskujesz dostęp do kontrolerów na potrzeby rozwiązywania problemów.

Można ponownie skonfigurować stałym kontrolerów IP za pomocą bloku ustawienia 0 danych.

![Skonfiguruj interfejs sieci - DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Stałe adresy IP kontrolera są używane do obsługi aktualizacji urządzenia i algorytmów odzyskiwanie miejsca (pamięci) do poprawnego działania. W związku z tym stałe adresy IP muszą być routowalne i mieć możliwość łączenia z Internetem.

### <a name="configure-data-1---data-5"></a>Konfigurowanie danych 1 - dane 5

Dla danych 1 - interfejsów sieciowych 5 danych, można skonfigurować wszystkie ustawienia sieciowe, jak pokazano na poniższym zrzucie ekranu:

![Skonfiguruj interfejsy sieciowe dane 1 - 5 danych](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Zamień lub ponowne przypisywanie adresów IP

Obecnie żadnym interfejsem sieciowym na kontrolerze przypisane VIP, który jest w użyciu (w tym samym urządzeniu lub innego urządzenia w sieci) kontrolera zostaną przełączone awaryjnie. Zamień lub ponowne przypisywanie adresów VIP dla urządzenia interfejsu sieciowego, należy postępować zgodnie prawidłowe procedury można tworzyć sytuacji zduplikowanego adresu IP.

Wykonaj następujące kroki, aby zamienić lub ponownie przypisać adresy VIP dla wszystkich interfejsów sieciowych:

#### <a name="to-reassign-ips"></a>Ponowne przypisywanie adresów IP

1. Usuń adres IP dla obu interfejsów.
2. Po adresy IP są wyczyszczone, przypisz nowe adresy IP do odpowiednich interfejsów.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [konfigurowanie wielościeżkowego wejścia/wyjścia dla urządzenia StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Dowiedz się, jak [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

