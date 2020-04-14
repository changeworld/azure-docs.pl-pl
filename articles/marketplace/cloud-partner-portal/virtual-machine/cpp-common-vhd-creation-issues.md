---
title: Typowe problemy podczas tworzenia dysków wirtualnych (CZĘSTO ZADAWANE PYTANIA) dla portalu Azure Marketplace
description: Często zadawane pytania dotyczące tworzenia dysków VHD i powiązanych problemów.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: dsindona
ms.openlocfilehash: 988121effb35949cce442ba9458789105e88feec
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273447"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Typowe problemy podczas tworzenia dysków VHD (FAQ)

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [10 najczęściej zadawanych pytań dotyczących tworzenia dysków wirtualnych,](https://aka.ms/VHDcreationIssues) aby zarządzać zmigrowanymi ofertami.

Poniższe często zadawane pytania obejmują typowe problemy napotkane podczas tworzenia wirtualnego dysku twardego (VHD) i maszyny wirtualnej (VM) dla ofert maszyn wirtualnych. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Jak utworzyć maszynę wirtualną z witryny Azure portal przy użyciu dysku VHD, który jest przekazyzony do magazynu w wersji Premium?

Usługa Azure Marketplace nie obsługuje obecnie tworzenia ofert maszyn wirtualnych z obrazów zamieszkałych w zarządzanym magazynie lub z usługi Azure Premium Storage.  Aby uzyskać więcej informacji na temat tych opcji magazynu, zobacz [Omówienie dysków zarządzanych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Czy możesz używać maszyn wirtualnych generacji 2 do ofert?

Nie, obsługiwane są tylko vedy generacji 1.  Jednak obecnie współpracujemy z zespołem platformy Microsoft Azure w celu zbadania obsługi maszyn wirtualnych generacji 2.  Aby uzyskać więcej informacji na temat różnic, zobacz [Czy należy utworzyć maszynę wirtualną generacji 1 lub 2 w funkcji Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Jak zmienić nazwę hosta?

Nie można.  Po utworzeniu maszyny Wirtualnej użytkownicy (w tym właściciele) nie mogą zaktualizować nazwy hosta.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Jak zresetować usługę pulpitu zdalnego lub hasło logowania?

W poniższych artykułach wyjaśniono, jak przeprowadzać resetowanie usług pulpitu zdalnego dla maszyn wirtualnych z systemem Windows i Linux:   

- [Jak zresetować usługę Pulpit zdalny lub jej hasło logowania dla maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Jak zresetować hasło maszyny Wirtualnej systemu Linux lub klucz SSH, naprawić konfigurację SSH i sprawdzić spójność dysku przy użyciu rozszerzenia VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Jak wygenerować nowe certyfikaty SSH?

Generowanie certyfikatów jest wyjaśnione w artykule [Pobierz identyfikator URI podpisu dostępu współdzielonego dla obrazu maszyny Wirtualnej](./cpp-get-sas-uri.md) w następnej sekcji [Tworzenie zasobów technicznych dla oferty maszyny Wirtualnej](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Jak skonfigurować wirtualną sieć prywatną (VPN) do pracy z moimi maszynami wirtualnymi?

Jeśli używasz modelu wdrażania usługi Azure Resource Manager, masz trzy typowe opcje konfigurowania sieci VPN:
- [Tworzenie bramy sieci VPN opartej na trasach przy użyciu portalu Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Tworzenie bramy sieci VPN opartej na marszrutach przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Tworzenie bramy sieci VPN opartej na trasie przy użyciu interfejsu wiersza polecenia](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Jakie są zasady pomocy technicznej firmy Microsoft dotyczące uruchamiania oprogramowania serwera firmy Microsoft na maszynach wirtualnych opartych na platformie Azure?

Te zasady pomocy technicznej są szczegółowo opisane w artykule [Obsługa oprogramowania serwera firmy Microsoft dla maszyn wirtualnych platformy Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Czy maszyny wirtualne mają unikatowe identyfikatory skojarzone z nimi?

Tak, jeśli jest hostowany na platformie Azure.  Platforma Azure przypisuje unikatowy identyfikator o nazwie Unikatowy identyfikator maszyny wirtualnej platformy Azure do każdego nowego zasobu maszyny wirtualnej, który jest tworzony.  Aby uzyskać więcej informacji, przeczytaj wpis w blogu [Unikatowy identyfikator maszyny wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Identyfikator ten można również uzyskać programowo za pośrednictwem [interfejsu API listy](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Jak zarządzać rozszerzeniem skryptu niestandardowego w zadaniu uruchamiania na maszynie wirtualnej?

Poniższy artykuł zawiera szczegółowe informacje dotyczące używania rozszerzenia skryptu niestandardowego przy użyciu modułu programu Azure PowerShell, szablonów usługi Azure Resource Manager oraz szczegółowych kroków rozwiązywania problemów w systemach Windows: [Niestandardowe rozszerzenie skryptu dla systemu Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Czy aplikacje lub usługi 32-bitowe są obsługiwane w portalu Azure Marketplace?

Ogólnie rzecz biorąc — nie.  Obsługiwane systemy operacyjne i standardowe usługi dla maszyn wirtualnych platformy Azure są 64-bitowe.  Jednak z technicznego punktu widzenia większość 64-bitowych systemów operacyjnych obsługuje uruchamianie 32-bitowych wersji aplikacji w celu zapewnienia zgodności z powrotem.  Jednak korzystanie z aplikacji 32-bitowych jako części rozwiązania maszyny Wirtualnej nie jest obsługiwane i dlatego jest *wysoce odradzane.*  Zamiast tego ponownie skompiluj aplikację jako projekt 64-bitowy.

Aby uzyskać więcej informacji zobacz następujące artykuły:
- [Uruchamianie aplikacji 32-bitowych](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Obsługa 32-bitowych systemów operacyjnych na maszynach wirtualnych platformy Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Pomoc techniczna dotycząca oprogramowania serwerowego firmy Microsoft dla maszyn wirtualnych platformy Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Za każdym razem, gdy próbuję utworzyć obraz z moich `.VHD is already registered with image repository as the resource` VHD, pojawia się błąd w programie PowerShell. Nie utworzyłem wcześniej żadnego obrazu ani nie znalazłem żadnego obrazu o tej nazwie na platformie Azure. Jak rozwiązać ten problem?

Ten problem zwykle występuje, jeśli użytkownik aprowizował maszynę wirtualną z dysku wirtualnego, który ma blokadę na nim.  Sprawdź, czy nie ma żadnych maszyn wirtualnych przydzielonych z tego dysku wirtualnego, a następnie ponów próbę wykonania operacji.  Jeśli ten problem będzie się powtarzał, otwórz bilet pomocy technicznej, jak wyjaśniono w [pomocy technicznej dla portalu cloud partner.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal) 

