---
title: Konfigurowanie środowiska źródłowego dla VMware do platformy Azure replikacji za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania środowiska lokalnego do replikowania maszyn wirtualnych VMware do platformy Azure z usługą Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 01b0717660265b28d4ea7d804a761e7e425c997c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319604"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Konfigurowanie środowiska źródłowego dla VMware do platformy Azure replikacji

W tym artykule opisano, jak można skonfigurować w lokalnym środowisku źródłowym, aby replikować maszyny wirtualne VMware do platformy Azure. Obejmuje on kroki wybranie scenariusza replikacji, konfigurowania komputera lokalnego, co serwer konfiguracji usługi Site Recovery i automatycznie odnajdywania lokalnych maszyn wirtualnych. 

## <a name="prerequisites"></a>Wymagania wstępne

Artykuł założono, że masz już:
- [Konfigurowanie zasobów](tutorial-prepare-azure.md) w [portalu Azure](http://portal.azure.com).
- [Konfigurowanie lokalnego VMware](vmware-azure-tutorial-prepare-on-premises.md), łącznie z dedykowane konto automatycznego wykrywania.



## <a name="choose-your-protection-goals"></a>Wybranie celów ochrony

1. W portalu Azure, przejdź do **usług odzyskiwania** magazyn bloku i wybierz magazyn.
2. W menu zasobów magazynu, przejdź do **wprowadzenie** > **usługi Site Recovery** > **krok 1: Przygotowanie infrastruktury**  >  **Cel ochrony**.

    ![Wybieranie celów](./media/vmware-azure-set-up-source/choose-goals.png)
3. W **cel ochrony**, wybierz pozycję **do platformy Azure**i wybierz polecenie **tak, z programem VMware vSphere Hypervisor**. Następnie kliknij przycisk **OK**.

    ![Wybieranie celów](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Konfigurowanie serwera konfiguracji

Można skonfigurować serwer konfiguracji jako VMware lokalnej maszyny Wirtualnej przy użyciu szablonu aplikacji komórek (Open Virtualization jajowych). [Dowiedz się więcej](concepts-vmware-to-azure-architecture.md) o składnikach, które zostaną zainstalowane na Maszynie wirtualnej VMware.

1. Dowiedz się więcej o [wymagania wstępne](vmware-azure-deploy-configuration-server.md#prerequisites) dla wdrożenia serwera konfiguracji.
2. [Sprawdź pojemność liczby](vmware-azure-deploy-configuration-server.md#capacity-planning) dla wdrożenia.
3. [Pobierz](vmware-azure-deploy-configuration-server.md#download-the-template) i [zaimportować](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) szablonu komórki jajowe skonfigurować VMware lokalnej maszyny Wirtualnej, który uruchamia serwer konfiguracji. Licencja podana przy użyciu szablonu jest licencji ewaluacyjnej i jest ważna przez 180 dni. Post tego okresu, nabywca musi wykonać aktywację systemu windows z licencji uzyskiwanych.
4. Włączaj maszynę Wirtualną VMware, i [go zarejestrować](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) w usługach odzyskiwania magazynu.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Dodaj konto VMware automatycznego wykrywania

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Nawiąż połączenie z serwerem VMware

Aby umożliwić usługi Azure Site Recovery odnaleźć maszyn wirtualnych działających w środowisku lokalnym, należy połączyć z usługą Site Recovery hostach ESXi vSphere lub programu VMware vCenter Server, na których.

Wybierz **+ vCenter** można uruchomić połączenia programu VMware vCenter server lub VMware vSphere ESXi hosta.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Typowe problemy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie środowiska docelowego](./vmware-azure-set-up-target.md) na platformie Azure.
