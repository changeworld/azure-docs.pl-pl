---
title: Konfigurowanie środowiska źródłowego (VMware do platformy Azure) | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania środowiska lokalnego do rozpoczęcia replikacji maszyn wirtualnych VMware do platformy Azure.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 2b6b0e5cc95f28b5596e7e898f5f035e3647d9c5
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768438"
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Konfigurowanie środowiska źródłowego (VMware do platformy Azure)
> [!div class="op_single_selector"]
> * [Z programu VMware do platformy Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Fizycznych do platformy Azure](./site-recovery-set-up-physical-to-azure.md)

W tym artykule opisano, jak skonfigurować źródła środowiska lokalnego do replikowania maszyn wirtualnych uruchomionych na VMware do platformy Azure. Obejmuje on kroki wybranie scenariusza replikacji, konfigurowania komputera lokalnego, co serwer konfiguracji usługi Site Recovery i automatycznie odnajdywania lokalnych maszyn wirtualnych. 

## <a name="prerequisites"></a>Wymagania wstępne

Artykuł założono, że masz już:
- [Konfigurowanie zasobów](tutorial-prepare-azure.md) w [portalu Azure](http://portal.azure.com).
- [Konfigurowanie lokalnego VMware](tutorial-prepare-on-premises-vmware.md), łącznie z dedykowane konto automatycznego wykrywania.



## <a name="choose-your-protection-goals"></a>Wybranie celów ochrony

1. W portalu Azure, przejdź do **usług odzyskiwania** magazyn bloku i wybierz magazyn.
2. W menu zasobów magazynu, przejdź do **wprowadzenie** > **usługi Site Recovery** > **krok 1: Przygotowanie infrastruktury** > **cel ochrony**.

    ![Wybieranie celów](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. W **cel ochrony**, wybierz pozycję **do platformy Azure**i wybierz polecenie **tak, z programem VMware vSphere Hypervisor**. Następnie kliknij przycisk **OK**.

    ![Wybieranie celów](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Konfigurowanie serwera konfiguracji

Konfigurowanie serwera konfiguracji jako VMware lokalnej maszyny Wirtualnej, użyj szablonu Open Virtualization Format (OVF). [Dowiedz się więcej](concepts-vmware-to-azure-architecture.md) o składnikach, które zostaną zainstalowane na Maszynie wirtualnej VMware. 

1. Dowiedz się więcej o [wymagania wstępne](how-to-deploy-configuration-server.md#prerequisites) dla wdrożenia serwera konfiguracji. [Sprawdź pojemność liczby](how-to-deploy-configuration-server.md#capacity-planning) dla wdrożenia.
2. [Pobierz](how-to-deploy-configuration-server.md#download-the-template) i [zaimportować](how-to-deploy-configuration-server.md#import-the-template-in-vmware) szablonu OVF (how-do-wdrażanie konfiguracji server.md), aby skonfigurować VMware lokalnej maszyny Wirtualnej, który uruchamia serwer konfiguracji.
3. Włączaj maszynę Wirtualną VMware, i [go zarejestrować](how-to-deploy-configuration-server.md#register-the-configuration-server) w usługach odzyskiwania magazynu.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Dodaj konto VMware automatycznego wykrywania

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Nawiąż połączenie z serwerem VMware

Aby umożliwić usługi Azure Site Recovery odnaleźć maszyn wirtualnych działających w środowisku lokalnym, należy połączyć z usługą Site Recovery hostach ESXi vSphere lub programu VMware vCenter Server, na których.

Wybierz **+ vCenter** można uruchomić połączenia programu VMware vCenter server lub VMware vSphere ESXi hosta.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Typowe problemy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie środowiska docelowego](./site-recovery-prepare-target-vmware-to-azure.md) na platformie Azure.
