---
title: Typowe problemy podczas tworzenia dysków VHD (FAQ)
description: Często zadawane pytania dotyczące typowych problemów podczas tworzenia wirtualnego dysku twardego (VHD).
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266268"
---
# <a name="common-issues-during-vhd-creation"></a>Typowe problemy podczas tworzenia dysków VHD

> [!NOTE]
> Przenosimy zarządzanie ofertami maszyn wirtualnych platformy Azure z portalu Cloud Partner do Centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami [typowymi problemami podczas tworzenia dysków wirtualnych (FAQ)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) w portalu Cloud Partner Portal, aby zarządzać ofertami.

Te często zadawane pytania (CZĘSTO ZADAWANE PYTANIA) obejmują typowe problemy, które mogą wystąpić podczas tworzenia wirtualnego dysku twardego (VHD) dla oferty maszyny wirtualnej platformy Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Jak utworzyć maszynę wirtualną z witryny Azure portal przy użyciu dysku VHD w magazynie w wersji Premium?

Usługa Azure Marketplace nie obsługuje obecnie tworzenia ofert maszyn wirtualnych z obrazów w zarządzanym magazynie lub z usługi Azure Premium Storage. Aby uzyskać szczegółowe informacje, zobacz [Omówienie dysków zarządzanych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Czy mogę używać maszyn wirtualnych generacji 2 do ofert?

Nie, obsługiwane są tylko vedy generacji 1. Jednak obecnie współpracujemy z zespołem platformy Microsoft Azure w celu zbadania pomocy technicznej dla maszyn wirtualnych generacji 2. Aby uzyskać szczegółowe informacje, zobacz [Czy należy utworzyć maszynę wirtualną generacji 1 lub 2 w funkcji Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Jak zmienić nazwę hosta?

Nie można. Po utworzeniu maszyny Wirtualnej użytkownicy (w tym właściciele) nie mogą zaktualizować nazwy hosta.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Jak zresetować usługę pulpitu zdalnego lub hasło logowania?

W tych artykułach wyjaśniono, jak przeprowadzać resetowanie usług pulpitu zdalnego dla maszyn wirtualnych z systemem Windows i Linux:

* [Jak zresetować usługę Pulpit zdalny lub jej hasło logowania dla maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Jak zresetować hasło maszyny Wirtualnej systemu Linux lub klucz SSH, naprawić konfigurację SSH i sprawdzić spójność dysku przy użyciu rozszerzenia VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Jak wygenerować nowe certyfikaty SSH?

Generowanie certyfikatów jest wyjaśnione w [certyfikacji obrazów maszyn wirtualnych platformy Azure.](https://aks.ms/CertifyVMimage)

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Jak skonfigurować wirtualną sieć prywatną (VPN) do pracy z maszynami wirtualnymi?

Jeśli używasz modelu wdrażania usługi Azure Resource Manager, masz trzy opcje:

* [Tworzenie bramy sieci VPN opartej na trasach przy użyciu portalu Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Tworzenie bramy sieci VPN opartej na trasie przy użyciu programu Azure PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Tworzenie bramy sieci VPN opartej na trasie przy użyciu interfejsu wiersza polecenia](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Jakie są zasady pomocy technicznej firmy Microsoft dotyczące uruchamiania oprogramowania serwera firmy Microsoft na maszynach wirtualnych opartych na platformie Azure?

Szczegółowe informacje można znaleźć na [stronie Pomocy technicznej dla maszyn wirtualnych platformy Microsoft Azure.](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Czy maszyny wirtualne mają unikatowe identyfikatory skojarzone z nimi?

Tak, jeśli jest hostowany na platformie Azure. Platforma Azure przypisuje unikatowy identyfikator, zwany [unikatowym identyfikatorem maszyny wirtualnej platformy Azure,](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)do każdego utworzonego nowego zasobu maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz unikatowy identyfikator maszyny wirtualnej platformy Azure. Identyfikator ten można również uzyskać za pośrednictwem [interfejsu API listy](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Jak zarządzać rozszerzeniem skryptu niestandardowego w zadaniu uruchamiania na maszynie wirtualnej?

Aby uzyskać szczegółowe informacje na temat używania rozszerzenia skryptu niestandardowego przy użyciu modułu Programu Azure PowerShell, szablonów usługi Azure Resource Manager i kroków rozwiązywania problemów w systemach Windows, zobacz [Niestandardowe rozszerzenie skryptu dla systemu Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Czy aplikacje lub usługi 32-bitowe są obsługiwane w portalu Azure Marketplace?

Ogólnie rzecz biorąc — nie. Obsługiwane systemy operacyjne i standardowe usługi dla maszyn wirtualnych platformy Azure są 64-bitowe. Chociaż większość 64-bitowych systemów operacyjnych obsługuje 32-bitowe wersje aplikacji w celu zapewnienia zgodności z powrotem, używanie aplikacji 32-bitowych jako części rozwiązania maszyny Wirtualnej jest nieobsługiwało się i było wysoce odradzane. Odtwórz aplikację jako projekt 64-bitowy.

Więcej informacji można znaleźć w tych artykułach:

* [Uruchamianie aplikacji 32-bitowych](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Obsługa 32-bitowych systemów operacyjnych na maszynach wirtualnych platformy Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Pomoc techniczna dotycząca oprogramowania serwerowego firmy Microsoft dla maszyn wirtualnych platformy Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Błąd: VHD jest już zarejestrowany w repozytorium obrazów jako zasób

Za każdym razem, gdy próbuję utworzyć obraz z moich vhds, pojawia się błąd "VHD jest już zarejestrowany w repozytorium obrazów jako zasób" w programie Azure PowerShell. Nie utworzyłem wcześniej żadnego obrazu ani nie znalazłem żadnego obrazu o tej nazwie na platformie Azure. Jak rozwiązać ten problem?

Ten problem zwykle pojawia się, jeśli utworzono maszynę wirtualną z dysku wirtualnego, który ma blokadę na nim. Upewnij się, że nie ma żadnych maszyn wirtualnych przydzielonych z tego dysku wirtualnego, a następnie ponów próbę wykonania operacji. Jeśli ten problem będzie się powtarzał, otwórz bilet pomocy technicznej. Zobacz [Pomoc techniczna dla Centrum partnerów](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).
