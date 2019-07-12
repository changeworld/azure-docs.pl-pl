---
title: Rozwiązywanie problemów z dostępem aplikacji maszyny Wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Aby wyizolować problemy z połączeniem do aplikacji działających na maszynach wirtualnych platformy Azure, należy użyć te szczegółowe kroki rozwiązywania problemów.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Nie można uruchomić aplikacji, program nie będzie można otworzyć, nasłuchiwać portu blokowany, nie można uruchomić programu, zablokowany port nasłuchujący
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 9bc528cdd098a2e355c542c3ca8f9bcb0287f339
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710520"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Aplikacja Rozwiązywanie problemów z łącznością w przypadku maszyn wirtualnych na platformie Azure

Istnieją różne powody, gdy nie można uruchomić lub połączyć się aplikacji uruchamianej na maszynie wirtualnej platformy Azure (VM). Przyczyny aplikacji nie jest uruchomiona i nasłuchuje na oczekiwanych portach, port nasłuchujący zablokowane lub sieć zasady nie zostały prawidłowo przekazywanie ruchu do aplikacji. W tym artykule opisano metodyczny podejście, aby znaleźć i naprawić problem.

Jeśli występują problemy z nawiązywaniem połączenia z maszyną wirtualną przy użyciu protokołu RDP lub SSH, zobacz jeden z następujących artykułów najpierw:

* [Rozwiązywanie problemów z połączeniami pulpitu zdalnego na podstawie Windows maszyny wirtualnej platformy Azure](troubleshoot-rdp-connection.md)
* [Rozwiązywanie problemów z połączeniami protokołu Secure Shell (SSH) opartą na systemie Linux maszyny wirtualnej platformy Azure](troubleshoot-ssh-connection.md).

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.

## <a name="quick-start-troubleshooting-steps"></a>Szybki start, kroki rozwiązywania problemów
Jeśli masz problemy z połączeniem z aplikacją, wypróbuj następujące ogólne kroki rozwiązywania problemów. Po każdym kroku spróbuj nawiązać połączenie ponownie aplikację:

* Ponowne uruchomienie maszyny wirtualnej
* Utwórz ponownie punkt końcowy / reguły zapory / reguł Sieciowej grupy zabezpieczeń sieciowych
  * [Model usługi Resource Manager — Zarządzanie sieciowymi grupami zabezpieczeń](../../virtual-network/manage-network-security-group.md)
  * [Model klasyczny — punkty końcowe zarządzających usługami w chmurze](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Łączenie z innej lokalizacji, np. innej sieci wirtualnej platformy Azure
* Ponowne wdrożenie maszyny wirtualnej
  * [Ponowne wdrażanie maszyny Wirtualnej Windows](redeploy-to-new-node-windows.md)
  * [Ponowne wdrażanie maszyny Wirtualnej systemu Linux](redeploy-to-new-node-linux.md)
* Ponowne utworzenie maszyny wirtualnej

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznością punktu końcowego (RDP/SSH/HTTP, błędy itp.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Szczegółowe omówienie dotyczące rozwiązywania problemów
Istnieją cztery główne obszary do Rozwiązywanie problemów z dostępem aplikacji, która jest uruchomiona na maszynie wirtualnej platformy Azure.

![Rozwiązywanie problemów z aplikaci nelze spustit.](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Aplikacja uruchomiona na maszynie wirtualnej platformy Azure.
   * Sama aplikacja działa poprawnie?
2. Maszyna wirtualna platformy Azure.
   * Poprawne działanie i odpowiadanie na żądania, jest samej maszyny Wirtualnej?
3. Punkty końcowe sieci platformy Azure.
   * Punkty końcowe maszyn wirtualnych w klasycznym modelu wdrażania usługi w chmurze.
   * Sieciowe grupy zabezpieczeń i reguł translatora adresów Sieciowych dla ruchu przychodzącego dla maszyn wirtualnych w modelu wdrażania usługi Resource Manager.
   * Można ruch przepływu z użytkowników do maszyny Wirtualnej/aplikacji na oczekiwanych portach?
4. Urządzenie brzegowe Internet.
   * Reguły zapory w miejscu uniemożliwiają ruch przepływający prawidłowo?

Dla komputerów klienckich, które uzyskują dostęp do aplikacji za pośrednictwem połączenia lokacja lokacja sieci VPN lub usługi ExpressRoute główne obszary, które mogą powodować problemy są aplikacji i maszyn wirtualnych platformy Azure.

Aby określić źródło problemu i jego skorygowania, wykonaj następujące kroki.

## <a name="step-1-access-application-from-target-vm"></a>Krok 1: Dostęp do aplikacji docelowej maszyny Wirtualnej
Spróbuj uzyskać dostęp do aplikacji przy użyciu odpowiedniego programu klienckiego z maszyny Wirtualnej, na którym jest uruchomiony. Użyj nazwy hosta lokalnego, lokalny adres IP lub adres sprzężenia zwrotnego (127.0.0.1).

![Uruchom aplikację bezpośrednio z poziomu maszyny Wirtualnej](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Na przykład jeśli aplikacja znajduje się serwer sieci web, otwórz przeglądarkę na maszynie Wirtualnej i próbuje uzyskać dostęp do strony sieci web hostowanych na maszynie Wirtualnej.

Jeśli uzyskujesz dostęp do aplikacji, przejdź do strony [kroku 2](#step2).

Jeśli nie można uzyskać dostępu do aplikacji, sprawdź następujące ustawienia:

* Aplikacja jest uruchomiona na docelowej maszynie wirtualnej.
* Aplikacja nasłuchuje na oczekiwanych portach TCP i UDP.

Windows i maszyn wirtualnych z systemem Linux użyj **polecenie netstat - a** polecenie, aby wyświetlić aktywne porty nasłuchiwania. Sprawdź dane wyjściowe dla oczekiwanego portów, na których powinien nasłuchiwać aplikacji. Ponowne uruchomienie aplikacji lub jest skonfigurowana do korzystania z portów oczekiwane, zgodnie z potrzebami, a następnie spróbuj ponownie uzyskać dostęp aplikacji w środowisku lokalnym.

## <a id="step2"></a>Krok 2. Dostęp do aplikacji z innej maszyny Wirtualnej w tej samej sieci wirtualnej
Spróbuj uzyskać dostęp do aplikacji z innej maszyny Wirtualnej, ale w tej samej sieci wirtualnej przy użyciu nazwy hosta maszyny Wirtualnej lub przypisane Azure prywatnej, publicznej lub dostawca adresu IP. W przypadku maszyn wirtualnych utworzonych za pomocą klasycznego modelu wdrażania należy używać publiczny adres IP usługi w chmurze.

![Uruchom aplikację z innej maszyny Wirtualnej](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Na przykład jeśli aplikacja znajduje się serwer sieci web, spróbuj uzyskać dostęp do strony sieci web z przeglądarki innej maszyny wirtualnej w tej samej sieci wirtualnej.

Jeśli uzyskujesz dostęp do aplikacji, przejdź do strony [kroku 3](#step3).

Jeśli nie można uzyskać dostępu do aplikacji, sprawdź następujące ustawienia:

* Zapora hosta na docelowej maszynie Wirtualnej zezwala na żądanie przychodzące i wychodzące odpowiedzi ruchu.
* Wykrywanie włamań lub oprogramowanie uruchomione na docelowej maszynie Wirtualnej do monitorowania sieci zezwala na ruch.
* Punkty końcowe usługi w chmurze lub grup zabezpieczeń sieci umożliwia ruch:
  * [Model klasyczny — punkty końcowe zarządzających usługami w chmurze](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Model usługi Resource Manager — Zarządzanie sieciowymi grupami zabezpieczeń](../../virtual-network/manage-network-security-group.md)
* Inny składnik uruchomiony na maszynie wirtualnej w ścieżce między testu, maszyna wirtualna i maszyny Wirtualnej, takiego jak zapora, lub moduł równoważenia obciążenia zezwala na ruch.

Na maszynie wirtualnej na podstawie Windows umożliwia zapory Windows z zabezpieczeniami zaawansowanymi ustalić, czy reguły zapory wykluczanie aplikacji ruchu przychodzącego i wychodzącego.

## <a id="step3"></a>Krok 3. Dostęp do aplikacji spoza sieci wirtualnej
Spróbuj uzyskać dostęp do aplikacji na komputerze spoza sieci wirtualnej co maszyna wirtualna, na którym działa aplikacja. Korzystanie z różnych sieci jako oryginalnego komputera klienckiego.

![Uruchom aplikację na komputerze spoza sieci wirtualnej](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Na przykład jeśli aplikacja znajduje się serwer sieci web, spróbuj uzyskać dostęp do strony sieci web w przeglądarce uruchomionego na komputerze, który nie znajduje się w sieci wirtualnej.

Jeśli nie można uzyskać dostępu do aplikacji, sprawdź następujące ustawienia:

* W przypadku maszyn wirtualnych utworzonych za pomocą klasycznego modelu wdrażania:
  
  * Sprawdź, czy konfiguracja punktu końcowego dla maszyny Wirtualnej zezwala na ruch przychodzący, szczególnie protocol (TCP lub UDP) i numery portów publicznych i prywatnych.
  * Sprawdź, czy listy kontroli dostępu (ACL) dla punktu końcowego nie uniemożliwiają ruch przychodzący z Internetu.
  * Aby uzyskać więcej informacji, zobacz [jak się punkty końcowe do maszyny wirtualnej](../windows/classic/setup-endpoints.md).
* W przypadku maszyn wirtualnych utworzonych za pomocą modelu wdrażania usługi Resource Manager:
  
  * Sprawdź, czy dla ruchu przychodzącego konfiguracji reguły translatora adresów Sieciowych dla maszyny Wirtualnej zezwala na ruch przychodzący, szczególnie protocol (TCP lub UDP) i numery portów publicznych i prywatnych.
  * Sprawdź, czy sieciowe grupy zabezpieczeń są zezwala na żądanie przychodzące i wychodzące odpowiedzi ruchu.
  * Aby uzyskać więcej informacji, zobacz [co to jest sieciowa grupa zabezpieczeń?](../../virtual-network/security-overview.md)

Jeśli maszyna wirtualna lub punkt końcowy jest członkiem zestawu z równoważeniem obciążenia:

* Sprawdź, czy protokół sondy (TCP lub UDP) i numer portu są poprawne.
* Jeśli port i protokół sondy różni się od zestawu z równoważeniem obciążenia, protokół i port:
  * Sprawdź, czy aplikacja nasłuchuje na protokół sondy (TCP lub UDP) i numer portu (Użyj **polecenie netstat – a** na docelowej maszynie Wirtualnej).
  * Sprawdź, czy Zapora hosta na docelowej maszynie Wirtualnej zezwala na żądania sondowania dla ruchu przychodzącego i wychodzącego sondy ruch odpowiedzi.

Jeśli uzyskujesz dostęp do aplikacji, upewnij się, że zezwala na urządzeniu usługi edge Internet:

* Wychodzące żądanie ruchu aplikacji z komputera klienckiego do maszyny wirtualnej platformy Azure.
* Ruch przychodzący aplikacji odpowiedzi z maszyny wirtualnej platformy Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Krok 4 nie masz dostępu do aplikacji, należy sprawdzić IP Sprawdź ustawienia. 

Aby uzyskać więcej informacji, zobacz [sieci platformy Azure, omówienie monitorowania](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Dodatkowe zasoby
[Rozwiązywanie problemów z połączeniami pulpitu zdalnego na podstawie Windows maszyny wirtualnej platformy Azure](troubleshoot-rdp-connection.md)

[Rozwiązywanie problemów z połączeniami protokołu Secure Shell (SSH) opartą na systemie Linux maszyny wirtualnej platformy Azure](troubleshoot-ssh-connection.md)


