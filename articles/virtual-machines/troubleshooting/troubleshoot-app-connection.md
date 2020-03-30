---
title: Rozwiązywanie problemów z dostępem do aplikacji maszyn wirtualnych na platformie Azure | Dokumenty firmy Microsoft
description: Te szczegółowe kroki rozwiązywania problemów można wyizolować podczas łączenia się z aplikacjami działającymi na maszynach wirtualnych na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nie można uruchomić aplikacji, program nie otworzy, port nasłuchiwać zablokowany, nie można uruchomić programu, port nasłuchiwać zablokowany
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: caf73ffbc18a603ace22acfbd0da490048da698a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058116"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Rozwiązywanie problemów z łącznością aplikacji na maszynach wirtualnych platformy Azure

Istnieją różne powody, dla których nie można uruchomić lub połączyć się z aplikacją uruchomiającą na maszynie wirtualnej platformy Azure (VM). Przyczyny obejmują aplikację nie działa lub nasłuchiwanie na oczekiwanych portach, port nasłuchiwania zablokowany lub reguły sieciowe nie poprawnie przekazując ruch do aplikacji. W tym artykule opisano metodyczne podejście do znajdowania i rozwiązywania problemu.

Jeśli występują problemy z połączeniem się z maszyną wirtualną przy użyciu protokołu RDP lub SSH, najpierw zobacz jeden z następujących artykułów:

* [Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną platformy Azure z systemem Windows](troubleshoot-rdp-connection.md)
* [Rozwiązywanie problemów z połączeniami secure shell (SSH) z maszyną wirtualną platformy Azure opartą na systemie Linux.](troubleshoot-ssh-connection.md)

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [platformie MSDN Azure i forach przepełnienia stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.

## <a name="quick-start-troubleshooting-steps"></a>Szybkie uruchamianie kroków rozwiązywania problemów
Jeśli masz problemy z nawiązaniem połączenia z aplikacją, wypróbuj następujące ogólne kroki rozwiązywania problemów. Po każdym kroku spróbuj ponownie połączyć się z aplikacją:

* Ponowne uruchomienie maszyny wirtualnej
* Ponowne tworzenie reguł punktu końcowego / zapory / sieciowej grupy zabezpieczeń (NSG)
  * [Model Menedżera zasobów — zarządzanie grupami zabezpieczeń sieci](../../virtual-network/manage-network-security-group.md)
  * [Model klasyczny — zarządzanie punktami końcowymi usług w chmurze](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Łączenie z innej lokalizacji, takiej jak inna sieć wirtualna platformy Azure
* Ponowne wdrożenie maszyny wirtualnej
  * [Ponowne wdrożenie maszyny Wirtualnej systemu Windows](redeploy-to-new-node-windows.md)
  * [Ponowne wdrożenie maszyny Wirtualnej z systemem Linux](redeploy-to-new-node-linux.md)
* Odtworzenie maszyny wirtualnej

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznością punktu końcowego (błędy RDP/SSH/HTTP itp.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Szczegółowy przegląd rozwiązywania problemów
Istnieją cztery główne obszary, aby rozwiązać problem z dostępem aplikacji, która jest uruchomiona na maszynie wirtualnej platformy Azure.

![rozwiązywanie problemów nie można uruchomić aplikacji](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Aplikacja uruchomiona na maszynie wirtualnej platformy Azure.
   * Czy sama aplikacja działa poprawnie?
2. Maszyna wirtualna platformy Azure.
   * Czy sama maszyna wirtualna działa poprawnie i odpowiada na żądania?
3. Punkty końcowe sieci platformy Azure.
   * Punkty końcowe usługi w chmurze dla maszyn wirtualnych w modelu wdrażania klasycznego.
   * Sieciowe grupy zabezpieczeń i przychodzące reguły TRANSLATORA dla maszyn wirtualnych w modelu wdrażania Menedżera zasobów.
   * Czy ruch z użytkowników do maszyny Wirtualnej/aplikacji na oczekiwanych portach?
4. Urządzenie o krawędzi internetowej.
   * Czy reguły zapory uniemożliwiają prawidłowe przepływanie ruchu?

W przypadku komputerów klienckich, które uzyskują dostęp do aplikacji za pośrednictwem sieci VPN lokacji lub połączenia Usługi ExpressRoute, główne obszary, które mogą powodować problemy, to aplikacja i maszyna wirtualna platformy Azure.

Aby ustalić źródło problemu i jego korektę, wykonaj następujące kroki.

## <a name="step-1-access-application-from-target-vm"></a>Krok 1: Dostęp do aplikacji z docelowej maszyny Wirtualnej
Spróbuj uzyskać dostęp do aplikacji za pomocą odpowiedniego programu klienckiego z maszyny Wirtualnej, na której jest uruchomiona. Użyj nazwy hosta lokalnego, lokalnego adresu IP lub adresu sprzężenia zwrotnego (127.0.0.1).

![uruchamianie aplikacji bezpośrednio z maszyny Wirtualnej](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Na przykład jeśli aplikacja jest serwerem sieci web, otwórz przeglądarkę na maszynie Wirtualnej i spróbuj uzyskać dostęp do strony sieci Web hostowane na maszynie Wirtualnej.

Jeśli masz dostęp do aplikacji, przejdź do [kroku 2](#step2).

Jeśli nie możesz uzyskać dostępu do aplikacji, sprawdź następujące ustawienia:

* Aplikacja jest uruchomiona na docelowej maszynie wirtualnej.
* Aplikacja nasłuchuje na oczekiwanych portach TCP i UDP.

Na maszynach wirtualnych opartych na systemie Windows i Linux użyj polecenia **netstat -a,** aby wyświetlić aktywne porty nasłuchiwania. Sprawdź dane wyjściowe dla oczekiwanych portów, na których aplikacja powinna nasłuchiwanie. Uruchom ponownie aplikację lub skonfiguruj ją tak, aby w razie potrzeby używała oczekiwanych portów i spróbuj ponownie uzyskać dostęp do aplikacji lokalnie.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>Krok 2: Dostęp do aplikacji z innej maszyny Wirtualnej w tej samej sieci wirtualnej
Spróbuj uzyskać dostęp do aplikacji z innej maszyny Wirtualnej, ale w tej samej sieci wirtualnej, przy użyciu nazwy hosta maszyny Wirtualnej lub jej adresu IP przypisanego przez platformę Azure. W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania nie należy używać publicznego adresu IP usługi w chmurze.

![uruchamianie aplikacji z innej maszyny Wirtualnej](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Na przykład jeśli aplikacja jest serwerem sieci web, spróbuj uzyskać dostęp do strony sieci web z przeglądarki na innej maszynie wirtualnej w tej samej sieci wirtualnej.

Jeśli masz dostęp do aplikacji, przejdź do [kroku 3](#step3).

Jeśli nie możesz uzyskać dostępu do aplikacji, sprawdź następujące ustawienia:

* Zapora hosta na docelowej maszynie Wirtualnej zezwala na ruch żądań przychodzących i wychodzących odpowiedzi.
* Wykrywanie włamań lub oprogramowanie do monitorowania sieci uruchomione na docelowej maszynie wirtualnej zezwala na ruch.
* Punkty końcowe usług w chmurze lub sieciowe grupy zabezpieczeń zezwalają na ruch:
  * [Model klasyczny — zarządzanie punktami końcowymi usług w chmurze](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Model Menedżera zasobów — zarządzanie grupami zabezpieczeń sieci](../../virtual-network/manage-network-security-group.md)
* Oddzielny składnik uruchomiony na maszynie wirtualnej w ścieżce między testową maszyną wirtualną a maszyną wirtualną, taki jak moduł równoważenia obciążenia lub zapora, zezwala na ruch.

Na maszynie wirtualnej opartej na systemie Windows użyj Zapory systemu Windows z zabezpieczeniami zaawansowanymi, aby ustalić, czy reguły zapory wykluczają ruch przychodzący i wychodzący aplikacji.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>Krok 3: Dostęp do aplikacji spoza sieci wirtualnej
Spróbuj uzyskać dostęp do aplikacji z komputera spoza sieci wirtualnej jako maszyny Wirtualnej, na której aplikacja jest uruchomiona. Użyj innej sieci jako oryginalnego komputera klienckiego.

![uruchamianie aplikacji z komputera spoza sieci wirtualnej](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Na przykład jeśli aplikacja jest serwerem sieci web, spróbuj uzyskać dostęp do strony sieci web z przeglądarki uruchomionej na komputerze, który nie znajduje się w sieci wirtualnej.

Jeśli nie możesz uzyskać dostępu do aplikacji, sprawdź następujące ustawienia:

* W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania:
  
  * Sprawdź, czy konfiguracja punktu końcowego maszyny Wirtualnej zezwala na ruch przychodzący, zwłaszcza protokół (TCP lub UDP) oraz numery portów publicznych i prywatnych.
  * Sprawdź, czy listy kontroli dostępu (ACL) w punkcie końcowym nie uniemożliwiają ruchu przychodzącego z Internetu.
  * Aby uzyskać więcej informacji, zobacz [Jak skonfigurować punkty końcowe na maszynie wirtualnej](../windows/classic/setup-endpoints.md).
* W przypadku maszyn wirtualnych utworzonych przy użyciu modelu wdrażania Menedżera zasobów:
  
  * Sprawdź, czy konfiguracja reguły przychodzącego translatora adresów sieciowych dla maszyny Wirtualnej zezwala na ruch przychodzący, w szczególności protokół (TCP lub UDP) oraz numery portów publicznych i prywatnych.
  * Sprawdź, czy sieciowe grupy zabezpieczeń zezwalają na ruch żądań przychodzących i wychodzących.
  * Aby uzyskać więcej informacji, zobacz [Co to jest grupa zabezpieczeń sieci?](../../virtual-network/security-overview.md)

Jeśli maszyna wirtualna lub punkt końcowy jest członkiem zestawu z równoważenia obciążenia:

* Sprawdź, czy protokół sondy (TCP lub UDP) i numer portu są poprawne.
* Jeśli protokół sondy i port różnią się od protokołu i portu zestawu z równoważenia obciążenia:
  * Sprawdź, czy aplikacja nasłuchuje na protokole sondowania (TCP lub UDP) i numerze portu (użyj **netstat –a** na docelowej maszynie wirtualnej).
  * Sprawdź, czy zapora hosta na docelowej maszynie wirtualnej zezwala na przychodzące żądanie sondy i ruch odpowiedzi sondy wychodzącej.

Jeśli masz dostęp do aplikacji, upewnij się, że urządzenie o krawędzi internetowej zezwala na:

* Ruch żądania aplikacji wychodzącej z komputera klienckiego do maszyny wirtualnej platformy Azure.
* Ruch odpowiedzi aplikacji przychodzącej z maszyny wirtualnej platformy Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Krok 4 Jeśli nie możesz uzyskać dostępu do aplikacji, użyj funkcji Weryfikuj adresy IP, aby sprawdzić ustawienia. 

Aby uzyskać więcej informacji, zobacz [omówienie monitorowania sieci platformy Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Zasoby dodatkowe
[Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną platformy Azure z systemem Windows](troubleshoot-rdp-connection.md)

[Rozwiązywanie problemów z połączeniami secure shell (SSH) z maszyną wirtualną platformy Azure opartą na systemie Linux](troubleshoot-ssh-connection.md)


