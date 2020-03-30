---
title: Modyfikowanie konfiguracji urządzenia storsimple serii 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak za pomocą usługi StorSimple Device Manager do ponownego skonfigurowania urządzenia StorSimple, które zostało już wdrożone.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267692"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Użyj usługi StorSimple Device Manager, aby zmodyfikować konfigurację urządzenia StorSimple

## <a name="overview"></a>Omówienie

Sekcja **Ustawienia urządzenia** portalu Azure w bloku **Ustawienia** zawiera wszystkie parametry urządzenia, które można ponownie skonfigurować na urządzeniu StorSimple, które jest zarządzane przez usługę StorSimple Device Manager. W tym samouczku wyjaśniono, jak za pomocą **bloku Ustawienia** wykonywać następujące zadania na poziomie urządzenia:

* Modyfikowanie nazwy przyjaznej dla urządzenia
* Modyfikowanie ustawień czasu urządzenia
* Przypisywanie dodatkowego systemu DNS
* Modyfikowanie interfejsów sieciowych
* Zamienianie lub przypisywanie plików IP

## <a name="modify-device-friendly-name"></a>Modyfikowanie nazwy przyjaznej dla urządzenia

Za pomocą witryny Azure Portal można zmienić nazwę urządzenia i przypisać jej unikatową przyjazną nazwę do wyboru. Użyj bloku **Ustawienia ogólne** w urządzeniu, aby zmodyfikować nazwę przyjazną dla urządzenia. Przyjazna nazwa może zawierać dowolne znaki i może mieć maksymalnie 64 znaki.

> [!NOTE] 
> Nazwę urządzenia można zmodyfikować tylko w witrynie Azure portal, zanim zostanie ukończona konfiguracja urządzenia. Po zakończeniu minimalnej konfiguracji urządzenia nie można zmienić nazwy urządzenia.

![Nazwa urządzenia w ustawieniach ogólnych](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Urządzeniu StorSimple, które jest połączone z usługą StorSimple Device Manager, jest przypisana nazwa domyślna. Domyślna nazwa zazwyczaj odzwierciedla numer seryjny urządzenia. Na przykład domyślna nazwa urządzenia o długości 15 znaków, taka jak 8600-SHX0991003G44HT, wskazuje następujące informacje:

* **8600** – Wskazuje model urządzenia.
* **SHX** — wskazuje miejsce produkcji.
* **0991003** - Wskazuje konkretny produkt.
* **G44HT**— ostatnie 5 cyfr jest zwiększanych w celu utworzenia unikatowych numerów seryjnych. Może to nie być zestaw sekwencyjny.

## <a name="modify-device-description"></a>Modyfikowanie opisu urządzenia

Użyj bloku **Ustawienia ogólne** w urządzeniu, aby zmodyfikować opis urządzenia.

![Opis urządzenia w ustawieniach ogólnych](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Opis urządzenia zwykle pomaga zidentyfikować właściciela i fizyczną lokalizację urządzenia. Pole opisu musi zawierać mniej niż 256 znaków.

## <a name="modify-time-settings"></a>Modyfikowanie ustawień czasu

Urządzenie musi synchronizować czas w celu uwierzytelnienia z dostawcą usług magazynu w chmurze. Użyj bloku **Ustawienia ogólne** w urządzeniu, aby zmodyfikować ustawienia czasu urządzenia.

![Opis urządzenia w ustawieniach ogólnych](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Wybierz strefę czasową z listy rozwijanej. Można określić maksymalnie dwa serwery NTP (Network Time Protocol):

 - **Podstawowy serwer NTP** — konfiguracja jest wymagana i jest określona podczas konfigurowania urządzenia za pomocą programu Windows PowerShell for StorSimple. Domyślny **time.windows.com** systemu Windows Server można określić jako serwer NTP. Podstawową konfigurację serwera NTP można wyświetlić za pośrednictwem portalu Azure, ale aby go zmienić, należy użyć interfejsu programu Windows PowerShell. Użyj `Set-HcsNTPClientServerAddress` polecenia cmdlet, aby zmodyfikować podstawowy serwer NTP urządzenia. Aby uzyskać więcej informacji, przejdź do składni polecenia cmdlet [Set-HcsNTPClientServerAddress.](https://technet.microsoft.com/library/dn688138.aspx)

- **Pomocniczy serwer NTP** — konfiguracja jest opcjonalna. Za pomocą portalu można skonfigurować pomocniczy serwer NTP.

Podczas konfigurowania serwera NTP upewnij się, że sieć umożliwia przepływ ruchu NTP z centrum danych do Internetu. Określając publiczny serwer NTP, należy upewnić się, że zapory sieciowe i inne urządzenia zabezpieczające są skonfigurowane tak, aby zezwalały na przemieszczanie się ruchu NTP do i z sieci zewnętrznej. Jeśli dwukierunkowy ruch NTP jest niedozwolony, należy użyć wewnętrznego serwera NTP (kontroler domeny systemu Windows udostępnia tę funkcję). Jeśli urządzenie nie może zsynchronizować czasu, może nie być w stanie komunikować się z dostawcą magazynu w chmurze.

Aby wyświetlić listę publicznych serwerów NTP, przejdź do sieci [Web Serwery NTP](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Co się stanie, jeśli urządzenie zostanie wdrożone w innej strefie czasowej?

Jeśli urządzenie zostanie wdrożone w innej strefie czasowej, strefa czasowa urządzenia ulegnie zmianie. Biorąc pod uwagę, że wszystkie zasady tworzenia kopii zapasowych używają strefy czasowej urządzenia, zasady tworzenia kopii zapasowych zostaną automatycznie dostosowane zgodnie z nową strefą czasową. Interwencja użytkownika nie jest wymagana.

## <a name="modify-dns-settings"></a>Modyfikowanie ustawień DNS

Serwer DNS jest używany, gdy urządzenie próbuje komunikować się z dostawcą usług magazynu w chmurze. Użyj bloku **Ustawienia sieciowe** na urządzeniu, aby wyświetlić i zmodyfikować skonfigurowane ustawienia DNS. 

![Ustawienia DNS w ustawieniach sieci](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Aby uzyskać wysoką dostępność, należy skonfigurować zarówno podstawowe, jak i pomocnicze serwery DNS podczas początkowego wdrażania urządzeń.

**Podstawowy serwer DNS** — podczas początkowej konfiguracji używasz programu Windows PowerShell for StorSimple do określenia podstawowego serwera DNS. Podstawowy serwer DNS można ponownie skonfigurować tylko za pośrednictwem interfejsu programu Windows PowerShell. Użyj `Set-HcsDNSClientServerAddress` polecenia cmdlet, aby zmodyfikować podstawowy serwer DNS urządzenia. Aby uzyskać więcej informacji, przejdź do składni polecenia cmdlet [Set-HcsDNSClientServerAddress.](https://technet.microsoft.com/library/dn688138.aspx)

**Pomocniczy serwer DNS** — aby zmodyfikować `Set-HcsDNSClientServerAddress` pomocniczy serwer DNS, użyj polecenia cmdlet w interfejsie programu Windows PowerShell urządzenia lub bloku **ustawień sieciowych** urządzenia StorSimple w witrynie Azure portal.

Aby zmodyfikować pomocniczy serwer DNS w witrynie Azure portal, wykonaj następujące kroki.

1. Przejdź do usługi Menedżer urządzeń StorSimple. Z listy urządzeń wybierz i kliknij urządzenie.

2. W bloku **Ustawienia** przejdź do **pozycji Ustawienia urządzenia > Network**. Spowoduje to otwarcie bloku **Ustawienia sieci.** Kliknij kafelek **ustawień DNS.** Zmodyfikuj pomocniczy adres IP serwera DNS.

    ![Modyfikowanie pomocniczego adresu IP serwera DNS](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Na pasku poleceń kliknij przycisk **Zapisz,** a po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**.

    ![Zapisywanie i potwierdzanie zmian](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Modyfikowanie interfejsów sieciowych

Urządzenie ma sześć interfejsów sieciowych urządzeń, z których cztery to 1 GbE, z których dwa to 10 GbE. Interfejsy te są oznaczone jako DANE 0 – DANE 5. DANE 0, DATA 1, DATA 4 i DATA 5 to 1 GbE, natomiast DANE 2 i DATA 3 to interfejsy sieciowe 10 GbE.

Użyj **bloku Ustawienia sieci,** aby skonfigurować każdy z interfejsów, które mają być używane.

![Konfigurowanie interfejsów sieciowych za pomocą ustawień sieciowych](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Aby zapewnić wysoką dostępność, zaleca się, że masz co najmniej dwa interfejsy iSCSI i dwa interfejsy z obsługą chmury na urządzeniu. Zaleca się, ale nie wymagają, aby nieużywane interfejsy były wyłączone.

Dla każdego interfejsu sieciowego wyświetlane są następujące parametry:

* **Szybkość** — nie jest to parametr konfigurowany przez użytkownika. DANE 0, DATA 1, DATA 4 i DATA 5 są zawsze 1 GbE, podczas gdy DANE 2 i DATA 3 to interfejsy 10 GbE.
  
  > [!NOTE]
  > Szybkość i dupleks są zawsze automatycznie negocjowane. Ramki jumbo nie są obsługiwane.
  
* **Stan interfejsu** — interfejs można włączyć lub wyłączyć. Jeśli ta opcja jest włączona, urządzenie podejmie próbę użycia interfejsu. Zaleca się włączenie tylko tych interfejsów, które są podłączone do sieci i używane. Wyłącz wszystkie interfejsy, których nie używasz.
* **Typ interfejsu** — ten parametr umożliwia izolowanie ruchu iSCSI od ruchu magazynu w chmurze. Ten parametr może być jedną z następujących czynności:
  
  * **Chmura włączona** — po włączeniu urządzenie użyje tego interfejsu do komunikowania się z chmurą.
  * **iSCSI włączone** — po włączeniu, urządzenie będzie używać tego interfejsu do komunikowania się z hostem iSCSI.
    
    Zaleca się odizolowanie ruchu iSCSI od ruchu magazynu w chmurze. Należy również pamiętać, że jeśli host znajduje się w tej samej podsieci co urządzenie, nie trzeba przypisywać bramy; Jeśli jednak host znajduje się w innej podsieci niż urządzenie, należy przypisać bramę.
* **Adres IP** — podczas konfigurowania dowolnego interfejsu sieciowego należy skonfigurować wirtualny adres IP (VIP). Może to być IPv4 lub IPv6 lub oba. Rodziny adresów IPv4 i IPv6 są obsługiwane dla interfejsów sieciowych urządzenia. W przypadku korzystania z protokołu IPv4 należy określić 32-bitowy adres IP (*xxx.xxx.xxx.xxx*) w notacji kropka-dziesiętna. Podczas korzystania z IPv6 wystarczy podać 4-cyfrowy prefiks, a 128-bitowy adres zostanie wygenerowany automatycznie dla interfejsu sieciowego urządzenia na podstawie tego prefiksu.
* **Podsieć** — odnosi się do maski podsieci i jest skonfigurowany za pośrednictwem interfejsu programu Windows PowerShell.
* **Brama** — jest to brama domyślna, która powinna być używana przez ten interfejs podczas próby komunikowania się z węzłami, które nie znajdują się w tej samej przestrzeni adresowej IP (podsieci). Brama domyślna musi znajdować się w tej samej przestrzeni adresowej (podsieci) co adres IP interfejsu, zgodnie z maską podsieci.
* **Stały adres IP** — to pole jest dostępne tylko podczas konfigurowania interfejsu DATA 0. W przypadku operacji, takich jak aktualizacje lub rozwiązywanie problemów z urządzeniem, może być konieczne połączenie bezpośrednio z kontrolerem urządzenia. Stały adres IP może służyć do uzyskania dostępu zarówno do aktywnego, jak i pasywnego kontrolera na urządzeniu.

> [!NOTE]
> * Aby zapewnić prawidłowe działanie, sprawdź szybkość interfejsu i dupleks na przełączniku, do który jest podłączony każdy interfejs urządzenia. Interfejsy przełączników powinny negocjować z gigabitową siecią Ethernet (1000 Mb/s) lub być skonfigurowane dla gigabitowej sieci Ethernet (1000 Mb/s) i być w trybie pełnego dupleksu. Interfejsy działające z mniejszą prędkością lub w trybie półdupleksowym powodują problemy z wydajnością.
> * Aby zminimalizować zakłócenia i przestoje, zaleca się włączenie portufast na każdym z portów przełączników, z którymi będzie łączyć się interfejs sieciowy iSCSI urządzenia. Zapewni to szybkie ustanowienie łączności sieciowej w przypadku pracy awaryjnej.

### <a name="configure-data-0"></a>Konfigurowanie danych 0

DATA 0 jest domyślnie włączona w chmurze. Podczas konfigurowania danych 0, należy również skonfigurować dwa stałe adresy IP, po jednym dla każdego kontrolera. Te stałe adresy IP mogą służyć do bezpośredniego dostępu do kontrolerów urządzeń i są przydatne podczas instalowania aktualizacji na urządzeniu, do wyrzucania elementów bezużytecznych do poprawnego działania lub podczas uzyskiwania dostępu do kontrolerów w celu rozwiązywania problemów.

Stałe kontrolery IP można ponownie skonfigurować za pomocą bloku ustawień DANYCH 0.

![Konfigurowanie interfejsu sieciowego — DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Stałe adresy IP dla kontrolera są używane do obsługi aktualizacji urządzenia i algorytmów rekultywacji miejsca (wyrzucania elementów bezużytecznych) do poprawnego działania. W związku z tym stałe adresy IP muszą być routowalne i mieć możliwość łączenia z Internetem.

### <a name="configure-data-1---data-5"></a>Konfigurowanie DANYCH 1 - DANE 5

Dla interfejsów sieciowych DATA 1 - DATA 5 można skonfigurować wszystkie ustawienia sieciowe, jak pokazano na poniższym zrzucie ekranu:

![Konfigurowanie interfejsów sieciowych DATA 1 - DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Zamienianie lub przypisywanie plików IP

Obecnie, jeśli dowolny interfejs sieciowy na kontrolerze jest przypisany adres VIP, który jest używany (przez to samo urządzenie lub inne urządzenie w sieci), a następnie kontroler zostanie przejęty awaryjnie. W przypadku wymiany lub ponownego przypisania adresów VIP dla interfejsu sieciowego urządzenia należy postępować zgodnie z właściwą procedurą, ponieważ można utworzyć zduplikowaną sytuację adresów IP.

Wykonaj następujące kroki, aby zamienić lub ponownie przypisać adresy VIP dla dowolnego interfejsu sieciowego:

#### <a name="to-reassign-ips"></a>Aby ponownie przypisać odpowiedzi IP

1. Wyczyść adres IP dla obu interfejsów.
2. Po wyczyszczeniu adresów IP przypisz nowe adresy IP do odpowiednich interfejsów.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [skonfigurować mpio dla urządzenia StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Dowiedz się, jak [zarządzać urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

