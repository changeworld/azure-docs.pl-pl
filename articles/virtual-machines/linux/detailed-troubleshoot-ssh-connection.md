---
title: Szczegółowe rozwiązywanie SSH dla maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Szczegółowe kroki rozwiązywania problemów dotyczących problemów nawiązywania połączenia z maszyny wirtualnej platformy Azure SSH
keywords: SSH połączenia zostało odrzucone, ssh błędu, platforma azure ssh, połączenia SSH nie powiodło się
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 88f3ca3202359f9f45f5b9a5054ab95b40558520
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365495"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>SSH szczegółowe kroki rozwiązywania problemów dotyczących problemów, połączenie z maszyną wirtualną systemu Linux na platformie Azure
Istnieje wiele przyczyn, które klient SSH mogą nie być możliwe nawiązanie łączności usługi SSH na maszynie Wirtualnej. Jeśli wykonano za pomocą bardziej [SSH ogólne kroki rozwiązywania problemów](troubleshoot-ssh-connection.md), należy rozwiązać problem z połączeniem. W tym artykule przedstawiono szczegółowe kroki rozwiązywania problemów, aby określić, gdzie kończy się niepowodzeniem połączenia SSH i sposobu rozwiązania go.

## <a name="take-preliminary-steps"></a>Czynności wstępne
Na poniższym diagramie przedstawiono składniki, które nie są związane.

![Diagram pokazujący składniki usługi SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Poniższe etapy ułatwiają wyizolować źródło problemu i ustalić rozwiązania lub obejścia.

1. Sprawdź stan maszyny Wirtualnej w portalu.
   W [portalu Azure](https://portal.azure.com), wybierz pozycję **maszyn wirtualnych** > *nazwę maszyny Wirtualnej*.

   Powinny być widoczne w okienku stanu dla maszyny Wirtualnej **systemem**. Przewiń w dół do pokazania ostatniej aktywności dla zasobów obliczeniowych, magazynu i zasobów sieciowych.

2. Wybierz **ustawienia** zbadanie punktów końcowych, adresy IP, grup zabezpieczeń sieci i innych ustawień.

   Maszyna wirtualna ma punkt końcowy zdefiniowany dla ruchu protokołu SSH, który można wyświetlić w **punkty końcowe** lub  **[sieciowej grupy zabezpieczeń](../../virtual-network/security-overview.md)**. Punkty końcowe na maszynach wirtualnych, które zostały utworzone za pomocą Menedżera zasobów są przechowywane w grupie zabezpieczeń sieci. Sprawdź, czy zasady zostały zastosowane do grupy zabezpieczeń sieci i odwołuje się w podsieci.

Aby sprawdzić łączność sieciową, sprawdź skonfigurowane punkty końcowe i zobacz, jeśli można połączyć się z maszyną Wirtualną za pomocą innego protokołu, na przykład HTTP lub innej usługi.

Po wykonaniu tych kroków ponów próbę połączenia SSH.

## <a name="find-the-source-of-the-issue"></a>Znajdowanie źródła problemu
Na komputerze klienta SSH może się nie powieść się połączyć z usługą SSH na maszynie Wirtualnej Azure z powodu problemów lub błędy konfiguracji w następujących obszarach:

* [Komputer kliencki SSH](#source-1-ssh-client-computer)
* [Urządzenie brzegowe organizacji](#source-2-organization-edge-device)
* [Punkt końcowy usługi w chmurze i uzyskiwać dostęp do listy kontroli (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Sieciowe grupy zabezpieczeń](#source-4-network-security-groups)
* [Maszyna wirtualna Azure opartej na systemie Linux](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Źródło 1: SSH komputera klienckiego
Aby wyeliminować komputera jako źródło błędu, sprawdź, czy go połączeń SSH z innego lokalnego komputera opartego na systemie Linux.

![Diagram, który prezentuje składniki komputera klienta SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Jeśli połączenie nie powiedzie się, sprawdź następujące problemy na komputerze:

* Ustawienie zapory lokalnej, która blokuje ruch SSH ruchu przychodzącego lub wychodzącego (TCP 22)
* Lokalnie zainstalowane oprogramowanie serwera proxy klienta, który uniemożliwia połączeń SSH
* Zainstalowanego lokalnie pakietu oprogramowania, który uniemożliwia połączeń SSH do monitorowania sieci
* Inne rodzaje oprogramowania zabezpieczającego, które monitorowania ruchu lub Zezwalaj/nie zezwalaj na określone typy ruchu

Jeśli jeden z tych warunków, tymczasowo wyłączone i spróbuj połączenie SSH na komputerze lokalnym do ustalenie przyczyny, które połączenie jest blokowane na tym komputerze. Następnie skontaktowanie się z administratorem sieci, aby poprawić ustawień oprogramowania, aby zezwolić na połączenia SSH.

Jeśli korzystasz z uwierzytelniania certyfikatów, sprawdź ma te uprawnienia do folderu .ssh w katalogu macierzystego:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (lub innych plików, które zostały zapisane w ich kluczy prywatnych)
* Chmod 644 ~/.ssh/known_hosts (zawiera hosty, które zostały połączone za pośrednictwem protokołu SSH)

## <a name="source-2-organization-edge-device"></a>Źródło 2: Urządzenie brzegowe organizacji
Aby wyeliminować urządzenie brzegowe organizacji jako źródło problemu, sprawdź, czy komputer bezpośrednio połączony z Internetem mogą nawiązywać połączenia SSH do maszyny Wirtualnej Azure. Jeśli uzyskują dostęp do maszyny Wirtualnej za pośrednictwem sieci VPN lokacja lokacja lub połączenia Azure ExpressRoute, przejdź do [źródła 4: sieciowej grupy zabezpieczeń](#nsg).

![Diagram, który prezentuje urządzenie brzegowe organizacji](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Jeśli nie masz komputera, który jest bezpośrednio połączony z Internetem, tworzenie nowej maszyny Wirtualnej Azure własne grupy zasobów lub usługi w chmurze i użyć tej nowej maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Utwórz maszynę wirtualną z systemem Linux na platformie Azure](quick-create-cli.md). Po zakończeniu testowania Usuń grupę zasobów lub usługi maszyny Wirtualnej i w chmurze.

Jeśli tworzysz połączenie SSH z komputerem, który jest bezpośrednio połączony z Internetem, sprawdź urządzenie brzegowe organizacji dla:

* Wewnętrzny zapory, która blokuje ruch SSH z Internetem
* Serwer proxy, który uniemożliwia połączeń SSH
* Włamań lub oprogramowanie na urządzeniach w sieci krawędzi, która uniemożliwia połączeń SSH do monitorowania sieci

Skontaktować się z administratorem sieci, aby poprawić ustawienia urządzenia brzegowe organizacji zezwalająca na ruch protokołu SSH z Internetem.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Źródło 3: Punkt końcowy usługi w chmurze i listy ACL
> [!NOTE]
> To źródło dotyczą tylko maszyn wirtualnych, które zostały utworzone przy użyciu klasycznego modelu wdrażania. Dla maszyn wirtualnych, które zostały utworzone przy użyciu usługi Resource Manager, przejdź do [źródła 4: sieciowej grupy zabezpieczeń](#nsg).

Aby usunąć punkt końcowy usługi w chmurze i listy ACL jako źródło błędu, należy sprawdzić, czy innej maszyny Wirtualnej platformy Azure w tej samej sieci wirtualnej można połączyć przy użyciu protokołu SSH.

![Diagram, który prezentuje punktu końcowego usługi w chmurze i listy kontroli dostępu](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Jeśli nie masz inną maszynę Wirtualną w tej samej sieci wirtualnej można można łatwo utworzyć. Aby uzyskać więcej informacji, zobacz [Utwórz Maszynę wirtualną systemu Linux na platformie Azure przy użyciu interfejsu wiersza polecenia](quick-create-cli.md). Jeśli po zakończeniu testowania, należy usunąć dodatkowe maszyny Wirtualnej.

Jeśli tworzysz połączenie SSH z maszyną Wirtualną w tej samej sieci wirtualnej, sprawdź następujące obszary:

* **Konfiguracja punktu końcowego dla ruchu SSH na docelowej maszyny Wirtualnej.** Port TCP prywatny punktu końcowego powinien być zgodny z portem TCP, na którym nasłuchuje usługa SSH na maszynie Wirtualnej. (Domyślny port to 22). Sprawdź numer portu SSH TCP w portalu Azure, wybierając **maszyn wirtualnych** > *nazwę maszyny Wirtualnej* > **ustawienia**  >   **Punkty końcowe**.
* **Listy ACL punktu końcowego ruchu SSH na docelowej maszynie wirtualnej.** Listy kontroli dostępu można określić dozwolony lub niedozwolony ruch przychodzący z Internetu, na podstawie jego adresu IP źródłowego. Nieprawidłowo skonfigurowane listy kontroli dostępu może uniemożliwić ruch przychodzący SSH do punktu końcowego. Sprawdź z listy kontroli dostępu, aby upewnić się, że ruch przychodzący z publicznych adresów IP serwera proxy lub inny serwer krawędzi jest dozwolone. Aby uzyskać więcej informacji, zobacz [list (kontroli dostępu ACL) o dostęp do sieci kontroli](../../virtual-network/virtual-networks-acl.md).

Aby usunąć punkt końcowy jako źródło problemu, Usuń bieżący punkt końcowy, Utwórz innym punktem końcowym i określ nazwę SSH (port TCP 22 numeru portu publicznego i prywatnego). Aby uzyskać więcej informacji, zobacz [Konfigurowanie punktów końcowych na maszynie wirtualnej na platformie Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Źródła 4: Grupy zabezpieczeń sieci
Sieciowe grupy zabezpieczeń umożliwiają mieć większą kontrolę nad dozwolonego ruchu przychodzącego i wychodzącego. Można utworzyć reguły, które obejmują podsieci oraz usług w sieci wirtualnej platformy Azure w chmurze. Sprawdź reguły grupy zabezpieczeń sieci, tak aby upewnić się, że może SSH ruch do i z Internetu.
Aby uzyskać więcej informacji, zobacz [dotyczące grup zabezpieczeń sieci](../../virtual-network/security-overview.md).

Umożliwia także sprawdzić IP do sprawdzania poprawności konfiguracji grupy NSG. Aby uzyskać więcej informacji, zobacz [omówienie monitorowania sieci platformy Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Źródło 5: Opartych na systemie Linux maszyny wirtualnej platformy Azure
Źródło ostatniej możliwych problemów jest samej maszyny wirtualnej platformy Azure.

![Diagram, który prezentuje opartych na systemie Linux maszyny wirtualnej platformy Azure](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Jeśli jeszcze tego nie zrobiono tego wcześniej, postępuj zgodnie z instrukcjami [zresetować maszyn wirtualnych opartych na systemie Linux hasła](reset-password.md).

Spróbuj ponownie nawiązać połączenie z komputera. Jeśli nadal nie, poniżej przedstawiono niektóre możliwe problemy:

* Usługa SSH nie jest uruchomiona na docelowej maszynie wirtualnej.
* Usługa SSH nie nasłuchuje na porcie TCP 22. Aby przetestować, zainstalować klienta programu telnet na komputerze lokalnym i uruchom "telnet *cloudServiceName*. cloudapp.net 22". Ten krok określa, czy maszyny wirtualnej umożliwia komunikację przychodzących i wychodzących na punkt końcowy SSH.
* Zapory lokalnej na docelowej maszynie wirtualnej ma reguł, które uniemożliwiają przychodzącego i wychodzącego ruchu SSH.
* Włamań lub oprogramowania, który działa na maszynie wirtualnej platformy Azure do monitorowania sieci uniemożliwia nawiązanie połączenia SSH.

## <a name="additional-resources"></a>Zasoby dodatkowe
Aby uzyskać więcej informacji dotyczących rozwiązywania problemów z dostęp do aplikacji, zobacz [Rozwiązywanie problemów z dostępem do aplikacji działających na maszynie wirtualnej platformy Azure](troubleshoot-app-connection.md)
