---
title: Szczegółowe rozwiązywanie problemów z systemem SSH dla maszyny Wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Bardziej szczegółowe kroki rozwiązywania problemów z rozwiązywaniem problemów związanych z połączeniem z maszyną wirtualną platformy Azure
keywords: ssh połączenie odmówił, ssh błąd,azure ssh,SSH połączenie nie powiodło się
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ee6d437915f6c87ce9ef5f9c711d90793a96048c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920131"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Szczegółowe procedury rozwiązywania problemów dotyczących połączenia SSH z maszyną wirtualną z systemem Linux na platformie Azure
Istnieje wiele możliwych przyczyn, dla których klient SSH może nie być w stanie dotrzeć do usługi SSH na maszynie wirtualnej. Jeśli przeprowadzono bardziej [ogólne kroki rozwiązywania problemów z SSH,](troubleshoot-ssh-connection.md)należy rozwiązać problem z połączeniem. W tym artykule przedstawiono szczegółowe kroki rozwiązywania problemów, aby ustalić, gdzie połączenie SSH nie powiódł się i jak je rozwiązać.

## <a name="take-preliminary-steps"></a>Podejmij wstępne kroki
Na poniższym diagramie przedstawiono składniki, które są zaangażowane.

![Diagram przedstawiający składniki usługi SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Poniższe kroki pomagają wyizolować źródło błędu i dowiedzieć się rozwiązań lub obejścia.

1. Sprawdź stan maszyny Wirtualnej w portalu.
   W [portalu Azure](https://portal.azure.com)wybierz*nazwę maszyny* **wirtualne** > .

   W okienku stanu maszyny Wirtualnej powinno być wyświetlane **uruchomione**. Przewiń w dół, aby wyświetlić ostatnią aktywność zasobów obliczeniowych, magazynowych i sieciowych.

2. Wybierz **ustawienia,** aby sprawdzić punkty końcowe, adresy IP, sieciowe grupy zabezpieczeń i inne ustawienia.

   Maszyna wirtualna powinna mieć punkt końcowy zdefiniowany dla ruchu SSH, który można wyświetlić w **punktach końcowych** lub **[grupie zabezpieczeń sieci .](../../virtual-network/security-overview.md)** Punkty końcowe na maszynach wirtualnych utworzonych przy użyciu Menedżera zasobów są przechowywane w sieciowej grupie zabezpieczeń. Sprawdź, czy reguły zostały zastosowane do sieciowej grupy zabezpieczeń i odwołuje się do podsieci.

Aby zweryfikować łączność sieciową, sprawdź skonfigurowane punkty końcowe i sprawdź, czy można połączyć się z maszyną wirtualną za pośrednictwem innego protokołu, takiego jak HTTP lub inna usługa.

Po tych krokach spróbuj ponownie użyć połączenia SSH.

## <a name="find-the-source-of-the-issue"></a>Znajdź źródło problemu
Klient SSH na komputerze może nie łączyć się z usługą SSH na maszynie Wirtualnej Platformy Azure z powodu problemów lub błędnych konfiguracji w następujących obszarach:

* [Komputer kliencki SSH](#source-1-ssh-client-computer)
* [Urządzenie brzegowe organizacji](#source-2-organization-edge-device)
* [Lista punktów końcowych usług w chmurze i kontroli dostępu (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Grupy zabezpieczeń sieci](#source-4-network-security-groups)
* [Maszyna wirtualna platformy Azure oparta na systemie Linux](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Źródło 1: Komputer kliencki SSH
Aby wyeliminować komputer jako źródło błędu, sprawdź, czy może on nawiązywać połączenia SSH z innym lokalnym komputerem z systemem Linux.

![Diagram, który wyróżnia składniki komputera klienckiego SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Jeśli połączenie nie powiedzie się, sprawdź następujące problemy na komputerze:

* Ustawienie zapory lokalnej blokujące przychodzący lub wychodzący ruch SSH (TCP 22)
* Lokalnie zainstalowane oprogramowanie proxy klienta, które uniemożliwia połączenia SSH
* Lokalnie zainstalowane oprogramowanie do monitorowania sieci, które zapobiega połączeniom SSH
* Inne rodzaje oprogramowania zabezpieczającego, które monitorują ruch lub zezwalają/nie zezwalają na określone typy ruchu

Jeśli ma zastosowanie jeden z tych warunków, tymczasowo wyłącz oprogramowanie i spróbuj nawiązać połączenie SSH z komputerem lokalnym, aby dowiedzieć się, dlaczego połączenie jest blokowane na komputerze. Następnie skontaktuj się z administratorem sieci, aby poprawić ustawienia oprogramowania, aby zezwolić na połączenia SSH.

Jeśli używasz uwierzytelniania certyfikatów, sprawdź, czy masz te uprawnienia do folderu .ssh w katalogu domowym:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (lub inne pliki, które mają zapisane klucze prywatne)
* Chmod 644 ~/.ssh/known_hosts (zawiera hosty, z którymi masz połączenie za pośrednictwem SSH)

## <a name="source-2-organization-edge-device"></a>Źródło 2: Urządzenie brzegowe organizacji
Aby wyeliminować urządzenie brzegowe organizacji jako źródło błędu, sprawdź, czy komputer bezpośrednio połączony z Internetem może nawiązywać połączenia SSH z maszyną wirtualną platformy Azure. Jeśli uzyskujesz dostęp do maszyny Wirtualnej za pośrednictwem sieci VPN typu lokacja lokacja lub połączenia usługi Azure ExpressRoute, przejdź do [źródła 4: sieciowe grupy zabezpieczeń](#nsg).

![Diagram, który wyróżnia urządzenie brzegowe organizacji](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Jeśli nie masz komputera, który jest bezpośrednio połączony z Internetem, utwórz nową maszynę wirtualną platformy Azure w własnej grupie zasobów lub usłudze w chmurze i użyj tej nowej maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure](../linux/quick-create-cli.md). Usuń grupę zasobów lub maszynę wirtualną i usługę w chmurze po zakończeniu testowania.

Jeśli można utworzyć połączenie SSH z komputerem, który jest bezpośrednio połączony z Internetem, sprawdź urządzenie brzegowe organizacji pod kątem:

* Wewnętrzna zapora blokująca ruch SSH z Internetem
* Serwer proxy, który uniemożliwia połączenia SSH
* Oprogramowanie do wykrywania włamań lub monitorowania sieci działające na urządzeniach w sieci brzegowej, które zapobiega połączeniom SSH

Skontaktuj się z administratorem sieci, aby poprawić ustawienia urządzeń brzegowych organizacji, aby zezwolić na ruch SSH z Internetem.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Źródło 3: Punkt końcowy usługi w chmurze i ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> To źródło dotyczy tylko maszyn wirtualnych, które zostały utworzone przy użyciu klasycznego modelu wdrażania. W przypadku maszyn wirtualnych utworzonych przy użyciu Menedżera zasobów przejdź do [źródła 4: Sieciowe grupy zabezpieczeń](#nsg).

Aby wyeliminować punkt końcowy usługi w chmurze i listy ACL jako źródło błędu, sprawdź, czy inna maszyna wirtualna platformy Azure w tej samej sieci wirtualnej może łączyć się przy użyciu SSH.

![Diagram, który wyróżnia punkt końcowy usługi w chmurze i listy ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Jeśli nie masz innej maszyny Wirtualnej w tej samej sieci wirtualnej, można łatwo utworzyć. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny Wirtualnej systemu Linux na platformie Azure przy użyciu interfejsu wiersza polecenia](../linux/quick-create-cli.md). Usuń dodatkową maszynę wirtualną po zakończeniu testowania.

Jeśli można utworzyć połączenie SSH z maszyną wirtualną w tej samej sieci wirtualnej, sprawdź następujące obszary:

* **Konfiguracja punktu końcowego dla ruchu SSH na docelowej maszynie Wirtualnej.** Prywatny port TCP punktu końcowego powinien być zgodny z portem TCP, na którym nasłuchuje usługa SSH na maszynie wirtualnej. (Domyślny port to 22). Sprawdź numer portu TCP SSH w portalu Azure, wybierając punkty**końcowe ustawień** > *nazw maszyn* >  **wirtualnych.** > **Endpoints**
* **Listy ACL dla punktu końcowego ruchu SSH na docelowej maszynie wirtualnej.** Listy ACL umożliwia określenie dozwolonego lub odrzuconego ruchu przychodzącego z Internetu na podstawie jego źródłowego adresu IP. Nieprawidłowo skonfigurowane listy ACL mogą uniemożliwić przychodzący ruch SSH do punktu końcowego. Sprawdź listy ACL, aby upewnić się, że ruch przychodzący z publicznych adresów IP serwera proxy lub innego serwera brzegowego jest dozwolony. Aby uzyskać więcej informacji, zobacz [Informacje o listach kontroli dostępu do sieci (Listy kontroli dostępu do sieci).](../../virtual-network/virtual-networks-acl.md)

Aby wyeliminować punkt końcowy jako źródło problemu, usuń bieżący punkt końcowy, utwórz inny punkt końcowy i określ nazwę SSH (port TCP 22 dla numeru portu publicznego i prywatnego). Aby uzyskać więcej informacji, zobacz [Konfigurowanie punktów końcowych na maszynie wirtualnej na platformie Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Źródło 4: Sieciowe grupy zabezpieczeń
Sieciowe grupy zabezpieczeń umożliwiają bardziej szczegółową kontrolę dozwolonego ruchu przychodzącego i wychodzącego. Można utworzyć reguły obejmujące podsieci i usługi w chmurze w sieci wirtualnej platformy Azure. Sprawdź reguły sieciowej grupy zabezpieczeń, aby upewnić się, że ruch SSH do i z Internetu jest dozwolony.
Aby uzyskać więcej informacji, zobacz [Informacje o grupach zabezpieczeń sieci](../../virtual-network/security-overview.md).

Można również użyć weryfikacji adresu IP, aby sprawdzić poprawność konfiguracji sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [omówienie monitorowania sieci platformy Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Źródło 5: Maszyna wirtualna platformy Azure oparta na systemie Linux
Ostatnim źródłem możliwych problemów jest sama maszyna wirtualna platformy Azure.

![Diagram, który wyróżnia maszynę wirtualną platformy Azure opartą na systemie Linux](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Jeśli jeszcze tego nie zrobiłeś, postępuj zgodnie z instrukcjami, [aby zresetować hasło maszyny wirtualne oparte na systemie Linux.](../linux/reset-password.md)

Spróbuj ponownie połączyć się z komputerem. Jeśli nadal nie powiedzie się, oto niektóre z możliwych problemów:

* Usługa SSH nie jest uruchomiona na docelowej maszynie wirtualnej.
* Usługa SSH nie nasłuchuje na porcie TCP 22. Aby przetestować, zainstaluj klienta telnet na komputerze lokalnym i uruchom "telnet *cloudServiceName*.cloudapp.net 22". Ten krok określa, czy maszyna wirtualna umożliwia komunikację przychodzącą i wychodzącą do punktu końcowego SSH.
* Zapora lokalna na docelowej maszynie wirtualnej ma reguły, które uniemożliwiają przychodzący lub wychodzący ruch SSH.
* Wykrywanie włamań lub oprogramowanie do monitorowania sieci, które jest uruchomione na maszynie wirtualnej platformy Azure uniemożliwia połączenia SSH.

## <a name="additional-resources"></a>Zasoby dodatkowe
Aby uzyskać więcej informacji na temat rozwiązywania problemów z dostępem do aplikacji, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie wirtualnej platformy Azure](../linux/troubleshoot-app-connection.md)
