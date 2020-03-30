---
title: Konfigurowanie serwera przetwarzania VMware/fizycznego powrotu po awarii w usłudze Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania serwera przetwarzania na platformie Azure, aby po awarii maszyn wirtualnych platformy Azure do VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083948"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Konfigurowanie serwera przetwarzania na platformie Azure na potrzeby powrotu po awarii

Po przełączeniu maszyn wirtualnych VMware lub serwerów fizycznych na platformę Azure przy użyciu [usługi Site Recovery](site-recovery-overview.md)można je po awarii z powrotem do lokacji lokalnej, gdy jest uruchomiony ponownie. Aby przywrócić po awarii, należy skonfigurować tymczasowy serwer procesów na platformie Azure, aby obsłużyć replikację z platformy Azure do lokalnej. Tę maszynę wirtualną można usunąć po zakończeniu powrotu po awarii.

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się więcej o [procesie ponownego odtwarzania](vmware-azure-reprotect.md) i powrotu po [awarii.](vmware-azure-failback.md)

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Wdrażanie serwera przetwarzania na platformie Azure

1. W magazynie > **Infrastruktura**> odzyskiwania**lokacji****Zarządzaj serwerami** > konfiguracji wybierz serwer konfiguracji.
2. Na stronie serwera kliknij przycisk **+ Serwer przetwarzania**
3. Na stronie **Dodaj serwer procesu** i wybierz, aby wdrożyć serwer przetwarzania na platformie Azure.
4. Określ ustawienia platformy Azure, w tym subskrypcję używaną do pracy awaryjnej, grupę zasobów, region platformy Azure używany do pracy awaryjnej i sieć wirtualną, w której znajdują się maszyny wirtualne platformy Azure. Jeśli używasz wielu sieci platformy Azure, potrzebujesz serwera przetwarzania w każdym z nich.

   ![Dodawanie elementu galerii serwera procesów](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. W **obszarze Nazwa serwera**, Nazwa **użytkownika**i **Hasło**określ nazwę serwera przetwarzania i poświadczenia, które będą przypisywane uprawnienia administratora na serwerze.
5. Określ konto magazynu, które ma być używane dla dysków maszyn wirtualnych serwera, podsieć, w której będzie znajdować się maszyna wirtualna serwera przetwarzania, oraz adres IP serwera, który zostanie przypisany po uruchomieniu maszyny Wirtualnej.
6. Kliknij przycisk **OK,** aby rozpocząć wdrażanie maszyny Wirtualnej serwera przetwarzania. Serwer przetwarzania zostanie wdrożony w Standard_A8_v2 jednostce SKU. Upewnij się, że ta jednostka SKU maszyny Wirtualnej jest dostępna dla twojej subskrypcji.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Rejestrowanie serwera przetwarzania (uruchomionego na platformie Azure) na serwerze konfiguracji (działającym lokalnie)

Po uruchomieniu maszyny wirtualnej serwera przetwarzania należy zarejestrować ją na lokalnym serwerze konfiguracji w następujący sposób:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


