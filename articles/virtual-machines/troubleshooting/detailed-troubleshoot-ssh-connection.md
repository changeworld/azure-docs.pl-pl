---
title: Szczegóły SSH rozwiązywania problemów z Maszyną wirtualną platformy Azure | Dokumentacja firmy Microsoft
description: Szczegółowe kroki rozwiązywania problemów dotyczących problemy z nawiązywaniem połączenia z maszyną wirtualną platformy Azure SSH
keywords: SSH połączenia zostało odrzucone, ssh błąd, azure ssh, połączenie SSH nie powiodło się
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: aedf06c5a5e225f0cafb81b17923d6c742da69eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506239"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>SSH szczegółowe kroki rozwiązywania problemów dotyczących problemy z połączeniem do maszyny Wirtualnej z systemem Linux na platformie Azure
Istnieje wiele możliwych przyczyn, które klient SSH może nie można nawiązać połączenia z usługą SSH na maszynę Wirtualną. Jeśli wykonano za pomocą bardziej [SSH ogólne kroki rozwiązywania problemów](troubleshoot-ssh-connection.md), należy rozwiązać problem z połączeniem. Ten artykuł przeprowadzi Cię przez szczegółowe kroki rozwiązywania problemów, aby określić, gdzie występuje błąd połączenia SSH i sposobie jego rozwiązania.

## <a name="take-preliminary-steps"></a>Wykonaj kroki wstępnego
Na poniższym diagramie przedstawiono składniki, które są zaangażowane.

![Diagram pokazujący składniki usługi SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Poniższe kroki pomogą Ci wyizolować źródła błędów i ustalić rozwiązania lub obejścia.

1. Sprawdź stan maszyny Wirtualnej w portalu.
   W [witryny Azure portal](https://portal.azure.com), wybierz opcję **maszyn wirtualnych** > *nazwa_maszyny_wirtualnej*.

   W okienku stanu maszyny wirtualnej powinien zostać wyświetlony **systemem**. Przewiń w dół do pokazu ostatnią aktywność, zasobów obliczeniowych, magazynu i zasobów sieciowych.

2. Wybierz **ustawienia** zbadanie punktów końcowych, adresy IP, sieciowe grupy zabezpieczeń i inne ustawienia.

   Maszyna wirtualna powinna mieć punkt końcowy zdefiniowany dla ruchu protokołu SSH, który można wyświetlić w **punktów końcowych** lub  **[sieciowej grupy zabezpieczeń](../../virtual-network/security-overview.md)**. Punkty końcowe na maszynach wirtualnych, które zostały utworzone przy użyciu usługi Resource Manager są przechowywane w sieciowej grupie zabezpieczeń. Sprawdź, czy zasady zostały zastosowane do sieciowej grupy zabezpieczeń i do których istnieją odwołania w tej podsieci.

Aby sprawdzić łączność sieciową, sprawdź skonfigurowane punkty końcowe i zobacz, jeśli możesz połączyć z maszyną wirtualną za pomocą protokołu innego, takiego jak HTTP lub innej usługi.

Po wykonaniu tych kroków spróbuj ponownie za pomocą połączenie SSH.

## <a name="find-the-source-of-the-issue"></a>Znajdź źródło problemu
Klient SSH na komputerze może się nie powieść się z usługą SSH na maszynie Wirtualnej platformy Azure, ze względu na problemy lub błędy konfiguracji w następujących obszarach:

* [Komputer kliencki SSH](#source-1-ssh-client-computer)
* [Urządzenie brzegowe organizacji](#source-2-organization-edge-device)
* [Punkt końcowy usługi w chmurze i dostęp do listy kontroli (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Sieciowe grupy zabezpieczeń](#source-4-network-security-groups)
* [Opartą na systemie Linux maszyny Wirtualnej platformy Azure](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Źródło 1: Komputer kliencki SSH
Aby wyeliminować komputera jako źródło błędu, sprawdź, czy może sprawić, że połączenia SSH do innego serwera lokalnego, komputer oparty na systemie Linux.

![Diagram, który wyróżnia elementy komputera klienta SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Jeśli połączenie nie powiedzie się, sprawdź, czy następujące problemy na komputerze:

* Ustawienie zapory lokalnej, która blokuje ruch przychodzący lub wychodzący ruch SSH (TCP 22)
* Lokalnie zainstalowane oprogramowanie serwera proxy klienta, który uniemożliwia nawiązanie połączenia SSH
* Lokalnie zainstalowane oprogramowanie, które uniemożliwia nawiązanie połączenia SSH do monitorowania sieci
* Inne rodzaje oprogramowania zabezpieczającego, przeznaczonych do monitorowania ruchu lub umożliwić/nie zezwalaj na określone typy ruchu

Jeśli występuje jeden z tych warunków, tymczasowo wyłączone i spróbuj połączenie SSH na komputerze lokalnym, aby dowiedzieć się, powodów, dla którego połączenie jest blokowane na tym komputerze. Następnie skontaktowanie się z administratorem sieci, aby poprawić ustawień oprogramowania, aby zezwolić na połączenia SSH.

Jeśli używasz uwierzytelniania certyfikatów, sprawdź, ma te uprawnienia do folderu .ssh w katalogu macierzystym:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (lub innych plików, które mają klucze prywatne przechowywane w nich)
* ~/.Ssh/known_hosts chmod 644 (zawiera hosty, które zostały połączone za pośrednictwem protokołu SSH)

## <a name="source-2-organization-edge-device"></a>Źródło 2: Urządzenie brzegowe organizacji
Aby usunąć urządzenie brzegowe organizacji jako źródło błędu, sprawdź, czy komputer bezpośrednio połączony z Internetem ułatwia połączeń SSH z maszyną wirtualną platformy Azure. Jeśli uzyskujesz dostęp do maszyny Wirtualnej za pośrednictwem sieci VPN lokacja lokacja lub połączenia usługi ExpressRoute platformy Azure, przejdź do [4 źródła: Sieciowe grupy zabezpieczeń](#nsg).

![Diagram, który wyróżnia urządzenie brzegowe organizacji](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Jeśli nie masz komputera, który jest bezpośrednio połączony z Internetem, Utwórz nową maszynę Wirtualną platformy Azure w jego własnej grupie zasobów lub usługi w chmurze i korzystanie z tej nowej maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Utwórz maszynę wirtualną z systemem Linux na platformie Azure](../linux/quick-create-cli.md). Po zakończeniu testowania, Usuń grupę zasobów lub usługi maszyny Wirtualnej i w chmurze.

Jeśli tworzysz połączenie SSH z komputerem, który jest bezpośrednio połączony z Internetem, sprawdź organizacji edge na urządzeniu:

* Wewnętrzny zapory, która blokuje ruch SSH z Internetu
* Serwer proxy, który uniemożliwia nawiązanie połączenia SSH
* Wykrywanie włamań lub oprogramowanie działające na urządzeniach w sieci krawędź, który uniemożliwia nawiązanie połączenia SSH do monitorowania sieci

Praca z administratorem sieci, aby poprawić ustawienia urządzenia brzegowe organizacji, aby zezwolić na ruch SSH z Internetu.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Źródło 3: Punkt końcowy usługi w chmurze i listy ACL
> [!NOTE]
> To źródło ma zastosowanie tylko do maszyn wirtualnych, które zostały utworzone przy użyciu klasycznego modelu wdrażania. Dla maszyn wirtualnych, które zostały utworzone przy użyciu usługi Resource Manager, przejdź do [źródła 4: Sieciowe grupy zabezpieczeń](#nsg).

Aby usunąć punkt końcowy usługi w chmurze i listę ACL jako źródło błędu, sprawdź innej maszyny Wirtualnej platformy Azure w tej samej sieci wirtualnej mogą nawiązywać połączenie przy użyciu protokołu SSH.

![Diagram, który wyróżnia punkt końcowy usługi w chmurze i listy ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Jeśli nie masz inną maszynę Wirtualną w tej samej sieci wirtualnej, można łatwo można utworzyć jeden. Aby uzyskać więcej informacji, zobacz [utworzyć Maszynę wirtualną systemu Linux na platformie Azure przy użyciu interfejsu wiersza polecenia](../linux/quick-create-cli.md). Po zakończeniu testowania, należy usunąć dodatkowe maszyny Wirtualnej.

Jeśli tworzysz połączenie SSH z maszyną Wirtualną w tej samej sieci wirtualnej, sprawdź następujące obszary:

* **Konfiguracja punktu końcowego dla ruchu protokołu SSH na docelowej maszynie Wirtualnej.** Port TCP prywatny punktu końcowego powinna odpowiadać portu TCP, na którym nasłuchuje usługa SSH na maszynie Wirtualnej. (Domyślny port to 22). Sprawdź numer portu TCP protokołu SSH w witrynie Azure portal, wybierając **maszyn wirtualnych** > *nazwa_maszyny_wirtualnej* > **ustawienia**  >   **Punkty końcowe**.
* **Listy ACL dla punktu końcowego ruchu protokołu SSH na docelowej maszynie wirtualnej.** Listy ACL można określić dozwolony lub niedozwolony ruch przychodzący z Internetu, w oparciu o jego źródłowy adres IP. Nieprawidłowo skonfigurowane listy ACL może uniemożliwić ruch przychodzący protokołu SSH do punktu końcowego. Sprawdź swoje listy ACL, aby upewnić się, że ruch przychodzący z publicznych adresów IP na serwerze proxy lub inny serwer krawędzi jest dozwolony. Aby uzyskać więcej informacji, zobacz [o dostęp do sieci list (kontroli dostępu ACL) kontroli](../../virtual-network/virtual-networks-acl.md).

Aby usunąć punkt końcowy jako źródło problemu, Usuń bieżący punkt końcowy, tworzenie kolejnego punktu końcowego i określ nazwę protokołu SSH (port TCP 22 dla numeru portu publicznego i prywatnego). Aby uzyskać więcej informacji, zobacz [skonfigurować punkty końcowe na maszynie wirtualnej na platformie Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Źródło 4: Grupy zabezpieczeń sieci
Sieciowe grupy zabezpieczeń umożliwiają zapewniają bardziej szczegółową kontrolę dozwolonego ruchu przychodzącego i wychodzącego. Można utworzyć reguły, które obejmować podsieci i usług w chmurze w sieci wirtualnej platformy Azure. Sprawdź reguły grupy zabezpieczeń sieci, tak aby upewnić się, że ruch SSH z Internetu i może.
Aby uzyskać więcej informacji, zobacz [temat sieciowych grup zabezpieczeń](../../virtual-network/security-overview.md).

Umożliwia także sprawdzić adresów IP do sprawdzania poprawności konfiguracji sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [sieci platformy Azure, omówienie monitorowania](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Źródło 5: Maszyna wirtualna platformy Azure opartych na systemie Linux
Ostatnie źródło potencjalnych problemów jest samej maszyny wirtualnej platformy Azure.

![Diagram, który wyróżnia opartych na systemie Linux maszyny wirtualnej platformy Azure](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Jeśli użytkownik jeszcze tego nie zrobiono, wykonaj instrukcje [do zresetowania hasła opartych na systemie Linux wirtualne](../linux/reset-password.md).

Spróbuj ponownie nawiązać połączenie z komputera. Jeśli nadal nie, poniżej przedstawiono niektóre możliwe problemy:

* Usługa SSH nie jest uruchomiona na docelowej maszynie wirtualnej.
* Usługa SSH nie nasłuchuje na porcie TCP 22. Do testowania, zainstalować klienta usługi telnet na komputerze lokalnym i uruchamiania "telnet *cloudServiceName*. cloudapp.net 22". Ten krok określa, jeśli maszyna wirtualna umożliwia przychodzący i wychodzący komunikacji z punktem końcowym protokołu SSH.
* Zapory lokalnej na docelowej maszynie wirtualnej ma reguł, które nie pozwalają na ruch przychodzący lub wychodzący ruch SSH.
* Wykrywanie włamań lub oprogramowania, który jest uruchomiony na maszynie wirtualnej platformy Azure do monitorowania sieci uniemożliwia nawiązanie połączenia SSH.

## <a name="additional-resources"></a>Dodatkowe zasoby
Aby uzyskać więcej informacji na temat rozwiązywania problemów z dostępem do aplikacji, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchamianej na maszynie wirtualnej platformy Azure](../linux/troubleshoot-app-connection.md)
