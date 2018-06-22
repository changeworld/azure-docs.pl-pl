---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ab668a905b435287a4eaf96ff04b2fa5b54deb1d
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313517"
---
Istnieją różne powody, gdy nie można uruchomić lub połączyć do aplikacji działających na maszynie wirtualnej platformy Azure (VM). Przyczyny aplikacji nie jest uruchomiona lub nasłuchuje na oczekiwanych portach, port nasłuchujący zablokowane lub sieć zasady prawidłowo przekazywanie ruchu do aplikacji. W tym artykule opisano metodyczny podejście, aby odnaleźć i rozwiązać problem.

Jeśli występują problemy dotyczące nawiązywania połączenia z maszyną Wirtualną za pomocą protokołu RDP lub SSH, zobacz następujące artykuły najpierw:

* [Rozwiązywanie problemów z połączeniami pulpitu zdalnego do systemu Windows Azure maszyny wirtualnej](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)
* [Rozwiązywanie problemów z połączeniami Secure Shell (SSH) do maszyny wirtualnej opartych na systemie Linux Azure](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md).

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../articles/resource-manager-deployment-model.md). W tym artykule przedstawiono oba modele, ale firma Microsoft zaleca przeprowadzanie większości nowych wdrożeń z zastosowaniem modelu wdrażania przy użyciu usługi Azure Resource Manager.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [MSDN Azure i fora przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.

## <a name="quick-start-troubleshooting-steps"></a>Szybki start kroki rozwiązywania problemów
Jeśli masz problemy z połączeniem z aplikacji, spróbuj następujące ogólne kroki rozwiązywania problemów. Po wykonaniu każdego kroku spróbuj połączyć się ponownie aplikację:

* Uruchom ponownie maszynę wirtualną
* Utwórz ponownie punkt końcowy / reguł zapory / sieci reguł zabezpieczeń grupy (NSG)
  * [Model usługi Resource Manager — Zarządzanie grupami zabezpieczeń sieci](../articles/virtual-network/manage-network-security-group.md)
  * [Model klasyczny — punkty końcowe usługi zarządzania w chmurze](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
* Nawiązywanie połączenia z innej lokalizacji, takiej jak innej sieci wirtualnej platformy Azure
* Wdróż ponownie maszynę wirtualną
  * [Wdrożenie maszyny Wirtualnej z systemem Windows](../articles/virtual-machines/windows/redeploy-to-new-node.md)
  * [Wdrożenie maszyny Wirtualnej systemu Linux](../articles/virtual-machines/linux/redeploy-to-new-node.md)
* Utwórz ponownie maszynę wirtualną

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznością punktu końcowego (RDP/SSH/HTTP, awarii itp.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Szczegółowe informacje dotyczące rozwiązywania problemów
Istnieją cztery główne obszary rozwiązywać dostęp do aplikacji, która działa na maszynie wirtualnej platformy Azure.

![Rozwiązywanie problemów z nie można uruchomić aplikacji](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Aplikacja była uruchomiona na maszynie wirtualnej Azure.
   * Sama aplikacja działa prawidłowo?
2. Maszyny wirtualnej platformy Azure.
   * Poprawne działanie i odpowiada na żądania, jest samej maszyny Wirtualnej?
3. Punktów końcowych sieci platformy Azure.
   * Punkty końcowe usługi chmury dla maszyn wirtualnych w klasycznym modelu wdrażania.
   * Grupy zabezpieczeń sieci i reguły NAT ruchu przychodzącego dla maszyn wirtualnych w modelu wdrażania usługi Resource Manager.
   * Przepływu od użytkowników do maszyny Wirtualnej/aplikacji na oczekiwanych portach ruchu może?
4. Urządzenie brzegowe internetowej.
   * Reguły zapory w miejscu uniemożliwiają ruchu w drodze poprawnie?

Dla komputerów klienckich, które uzyskują dostęp do aplikacji za pośrednictwem połączenia sieci VPN i ExpressRoute lokacja lokacja główne obszary, które mogą być przyczyną problemów są aplikacji i maszyny wirtualnej platformy Azure.

Aby określić źródło problemu i jego skorygowania, wykonaj następujące kroki.

## <a name="step-1-access-application-from-target-vm"></a>Krok 1: Dostęp do aplikacji z docelową maszynę Wirtualną
Spróbuj uzyskać dostęp do aplikacji w programie odpowiedniego klienta z maszyny Wirtualnej, na którym jest uruchomiony. Użyj nazwy lokalnego hosta lokalnego adresu IP i adres sprzężenia zwrotnego (127.0.0.1).

![Uruchom aplikację bezpośrednio z maszyny Wirtualnej](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Na przykład jeśli aplikacja jest serwerem sieci web, otwórz przeglądarkę na maszynie Wirtualnej i spróbuj uzyskać dostęp do strony sieci web hostowanej na maszynie Wirtualnej.

Jeśli uzyskujesz dostęp do aplikacji, przejdź do [krok 2](#step2).

Jeśli nie można uzyskać dostęp do aplikacji, należy sprawdzić następujące ustawienia:

* Aplikacja jest uruchomiona na docelowej maszynie wirtualnej.
* Aplikacja nasłuchuje na oczekiwanych portach TCP i UDP.

W systemach Windows i maszyn wirtualnych z systemem Linux, użyj **netstat -** polecenie, aby wyświetlić aktywne porty nasłuchiwania. Sprawdź, czy wyjście oczekiwanego portów, na których powinien nasłuchiwać aplikacji. Ponowne uruchomienie aplikacji lub skonfiguruj ją do korzystania z portów oczekiwano, w razie potrzeby, a następnie spróbuj ponownie dostępu do aplikacji lokalnie.

## <a id="step2"></a>Krok 2: Dostęp do aplikacji z innej maszyny Wirtualnej w tej samej sieci wirtualnej
Spróbuj uzyskać dostęp do aplikacji z innej maszyny Wirtualnej, ale w tej samej sieci wirtualnej przy użyciu nazwy hosta maszyny Wirtualnej lub przypisać Azure public, private lub dostawca adres IP. Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania, nie używaj dla publicznego adresu IP usługi w chmurze.

![Uruchom aplikację z innej maszyny Wirtualnej](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Na przykład, gdy aplikacja jest serwerem sieci web, spróbuj uzyskać dostęp do strony sieci web w przeglądarce na innej maszynie Wirtualnej w tej samej sieci wirtualnej.

Jeśli uzyskujesz dostęp do aplikacji, przejdź do [kroku 3](#step3).

Jeśli nie można uzyskać dostęp do aplikacji, należy sprawdzić następujące ustawienia:

* Zapora hosta w celu wirtualna zezwala na żądanie przychodzące i wychodzące odpowiedzi ruchu.
* Sieci, monitorowanie oprogramowania uruchomionego w systemie docelowym maszyny Wirtualnej lub wykrywania nieautoryzowanego dostępu zezwala na ruch.
* Punkty końcowe usług chmury lub grupy zabezpieczeń sieci umożliwia ruch:
  * [Model klasyczny — punkty końcowe usługi zarządzania w chmurze](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Model usługi Resource Manager — Zarządzanie grupami zabezpieczeń sieci](../articles/virtual-network/manage-network-security-group.md)
* Oddzielny składnik uruchomione w maszynie Wirtualnej w ścieżce między testu maszyny Wirtualnej i maszyny Wirtualnej, takie jak usługi równoważenia obciążenia lub zapora zezwala na ruch.

Na maszynie wirtualnej z systemem Windows użyj zapory systemu Windows z zabezpieczeniami zaawansowanymi, aby określić, czy reguły zapory wykluczenia ruchu przychodzącego i wychodzącego aplikacji.

## <a id="step3"></a>Krok 3: Dostęp do aplikacji z spoza sieci wirtualnej
Spróbuj uzyskać dostęp do aplikacji z komputera spoza sieci wirtualnej jako maszyna wirtualna, na którym jest uruchomiona aplikacja. Przy użyciu innej sieci jako oryginalnego komputera klienckiego.

![Uruchom aplikację z komputera spoza sieci wirtualnej](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Na przykład jeśli aplikacja jest serwerem sieci web, spróbuj uzyskać dostęp do strony sieci web w przeglądarce uruchomionej na komputerze, który nie znajduje się w sieci wirtualnej.

Jeśli nie można uzyskać dostęp do aplikacji, należy sprawdzić następujące ustawienia:

* Dla maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania:
  
  * Sprawdź, czy konfiguracja punktu końcowego dla maszyny Wirtualnej zezwala na ruch przychodzący, szczególnie protocol (TCP lub UDP) i numery portów publicznego i prywatnego.
  * Sprawdź, czy listy kontroli dostępu (ACL) dla punktu końcowego nie uniemożliwiają ruch przychodzący z Internetu.
  * Aby uzyskać więcej informacji, zobacz [jak się punkty końcowe z maszyną wirtualną](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Dla maszyn wirtualnych utworzonych przy użyciu modelu wdrażania usługi Resource Manager:
  
  * Sprawdź, czy przychodzące konfiguracji reguły NAT dla maszyny Wirtualnej zezwala na ruch przychodzący, szczególnie protocol (TCP lub UDP) i numery portów publicznego i prywatnego.
  * Upewnij się, żądanie przychodzące i wychodzące odpowiedzi ruchu umożliwia grup zabezpieczeń sieci.
  * Aby uzyskać więcej informacji, zobacz [co to jest grupa zabezpieczeń sieci?](../articles/virtual-network/security-overview.md)

Jeśli maszyny wirtualnej lub punktu końcowego jest członkiem zestawu z równoważeniem obciążenia:

* Sprawdź, czy sondowania protocol (TCP lub UDP) i numer portu są poprawne.
* Jeśli port i protokół sondy jest inny niż zestaw o zrównoważonym obciążeniu protokół i port:
  * Sprawdź, czy aplikacja nasłuchuje na sondowanie protocol (TCP lub UDP) i numer portu (Użyj **polecenie netstat – a** na docelowej maszyny Wirtualnej).
  * Sprawdź, czy Zapora hosta w celu wirtualna zezwala na żądania sondowania dla ruchu przychodzącego i wychodzącego sondowania odpowiedzi ruchu.

Jeśli uzyskujesz dostęp do aplikacji, upewnij się, czy zezwala na urządzenie brzegowe internetowej:

* Wychodzące aplikacji żądania ruch z komputera klienckiego do maszyny wirtualnej platformy Azure.
* Odpowiedzi aplikacji dla ruchu przychodzącego ruchu z maszyny wirtualnej platformy Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Krok 4 nie masz dostępu do aplikacji, należy sprawdzić IP Sprawdź ustawienia. 

Aby uzyskać więcej informacji, zobacz [omówienie monitorowania sieci platformy Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Zasoby dodatkowe
[Rozwiązywanie problemów z połączeniami pulpitu zdalnego do systemu Windows Azure maszyny wirtualnej](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)

[Rozwiązywanie problemów z połączeniami Secure Shell (SSH) do maszyny wirtualnej opartych na systemie Linux platformy Azure](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md)

