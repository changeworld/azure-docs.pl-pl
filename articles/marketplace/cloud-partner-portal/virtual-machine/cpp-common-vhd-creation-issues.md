---
title: Typowe problemy występujące podczas tworzenia wirtualnego dysku twardego (często zadawane pytania) w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące tworzenia wirtualnego dysku twardego i skojarzone problemów.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: Patrick.Butler
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 10/02/2018
ms.author: hascipio; v-divte; v-miclar
ms.openlocfilehash: 381f88c4641417bceca0f988d4b1a187aedaa642
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744197"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Typowe problemy występujące podczas tworzenia wirtualnego dysku twardego (często zadawane pytania)

Poniższe często zadawane pytania (FAQ) cover typowych problemów napotkanych podczas maszyn wirtualnych i wirtualny dysk twardy (VHD), tworzenie oferty maszyny Wirtualnej (VM). 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Jak utworzyć Maszynę wirtualną w witrynie Azure portal przy użyciu wirtualnego dysku twardego, który zostanie przekazany do magazynu w warstwie premium

Portal Azure Marketplace obecnie nie obsługuje tworzenia oferty maszyny Wirtualnej z obrazów znajdujących się na magazynu zarządzanego lub z usługi Azure Premium Storage.  Aby uzyskać więcej informacji na temat tych opcji magazynu, zobacz [Omówienie usługi Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Maszyny wirtualne generacji 2 można użyć w przypadku ofert?

Nie, tylko generacja 1 wirtualne dyski twarde są obsługiwane.  Jednak obecnie pracujemy z zespołem platformy Microsoft Azure w celu badanie pomocy technicznej dla maszyn wirtualnych generacji 2.  Aby uzyskać więcej informacji na temat różnic, zobacz [należy utworzyć maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Jak zmienić nazwę hosta

To nie jest możliwe.  Po utworzeniu maszyny Wirtualnej użytkowników (w tym właściciele) nie można zaktualizować nazwy hosta.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Jak zresetować jego hasło logowania lub usługi Pulpit zdalny?

W poniższych artykułach opisano, jak przeprowadzić resetowanie usług pulpitu zdalnego dla maszyn wirtualnych z systemami Windows i Linux:   

- [Jak zresetować usługę Pulpit zdalny lub jej hasło logowania dla maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Jak zresetować hasła maszyny Wirtualnej systemu Linux lub klucz SSH, naprawianie konfiguracji SSH i sprawdzanie spójności dysku przy użyciu rozszerzenia VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Jak wygenerować nowe certyfikaty protokołu SSH

Generowanie certyfikatów opisanej w artykule [identyfikatora URI sygnatury dostępu współdzielonego Get obrazu maszyny Wirtualnej](./cpp-get-sas-uri.md) w kolejnej sekcji [Utwórz zasoby techniczne dla oferty maszyny Wirtualnej](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Jak skonfigurować wirtualnej sieci prywatnej (VPN), aby pracować z moich maszyn wirtualnych?

Jeśli używasz modelu wdrażania usługi Azure Resource Manager, masz trzy typowe opcje konfigurowania sieci VPN:
- [Tworzenie bramy sieci VPN opartej na trasach przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Tworzenie bramy sieci VPN opartej na trasach przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Tworzenie bramy sieci VPN opartej na trasach przy użyciu interfejsu wiersza polecenia](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Co to są zasady pomocy technicznej firmy Microsoft do uruchamiania oprogramowania serwerowego firmy Microsoft na maszynach wirtualnych z systemem Azure?

Obsługa, te zasady są szczegółowo opisane w artykule [pomocy technicznej oprogramowanie serwera firmy Microsoft dla maszyn wirtualnych Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Maszyny wirtualne mają unikatowych identyfikatorów skojarzonych z nimi?

Tak, jeśli hostowanej na platformie Azure.  Platforma Azure przypisuje unikatowy identyfikator, o nazwie maszyny wirtualnej platformy Azure Unikatowy identyfikator do każdego nowego zasobu maszyny Wirtualnej, który jest tworzony.  Aby uzyskać więcej informacji, przeczytaj wpis w blogu [Unikatowy identyfikator maszyny wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Możesz również uzyskać ten identyfikator programowo za pomocą [API listy](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Na maszynie wirtualnej jak możesz zarządzać niestandardowego rozszerzenia skryptu w zadaniu uruchamiania?

Następujący artykuł szczegółowo opisuje sposób użycia rozszerzenia niestandardowego skryptu przy użyciu modułu Azure PowerShell, szablony usługi Azure Resource Manager i rozwiązywanie problemów w systemach Windows szczegółowe informacje: [Rozszerzenie niestandardowego skryptu dla Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Czy 32-bitowych aplikacji i usług obsługiwanych w witrynie Azure Marketplace?

Rzecz biorąc — nie.  Obsługiwane systemy operacyjne i usług w warstwie standardowa dla maszyn wirtualnych platformy Azure są wszystkie 64-bitowe.  Z technicznego punktu widzenia najbardziej 64-bitowych systemach operacyjnych obsługuje jednak uruchomiona 32-bitowe wersje aplikacji, zgodności z poprzednimi wersjami.  Jednak użyć 32-bitowych aplikacji, jako część rozwiązania maszyny Wirtualnej nie jest obsługiwana i dlatego jest *zdecydowanie nie zaleca się*.  Zamiast tego należy ponownie skompilować aplikację jako projekt 64-bitowych.

Aby uzyskać więcej informacji zobacz następujące artykuły:
- [Uruchamianie aplikacji 32-bitowy](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Obsługa 32-bitowych systemach operacyjnych w maszynach wirtualnych platformy Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Pomoc techniczna dotycząca oprogramowania serwerowego firmy Microsoft dla maszyn wirtualnych platformy Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Za każdym razem, gdy próbuję Tworzenie obrazu na podstawie moich wirtualne dyski twarde, zgłaszany jest błąd `.VHD is already registered with image repository as the resource` w programie PowerShell. Nie utworzył obrazów przed ani I znalazł dowolnego obrazu o tej nazwie na platformie Azure. Jak rozwiązać ten problem?

Ten problem zazwyczaj występuje, gdy użytkownik aprowizowane Maszynę wirtualną z wirtualnego dysku twardego, który jest zablokowany.  Sprawdź, czy jest nie maszyny Wirtualnej przydzielonych z tego dysku VHD, a następnie spróbuj ponownie wykonać operację.  Jeśli ten problem będzie się powtarzać, otwórz bilet pomocy technicznej zgodnie z objaśnieniem w [obsługę portalu Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

