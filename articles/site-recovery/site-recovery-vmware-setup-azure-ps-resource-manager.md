---
title: " Zarządzanie serwerem procesów działających na platformie Azure (Resource Manager) | Dokumentacja firmy Microsoft"
description: W tym artykule opisano sposób konfigurowania serwera przetwarzania powrotu po awarii (Resource Manager) na platformie Azure.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: anoopkv
ms.openlocfilehash: 1b75acb13ac4c8990f99f7454a6de5483f6ca2f1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768396"
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>Zarządzanie serwerem procesów działających na platformie Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [Classic ](./site-recovery-vmware-setup-azure-ps-classic.md)

Podczas powrotu po awarii zaleca się wdrożyć serwer przetwarzania na platformie Azure, jeśli istnieje duże opóźnienie między sieci wirtualnej platformy Azure i siecią lokalną. W tym artykule opisano, jak można skonfigurować, konfigurowanie i zarządzanie serwerami procesów działających na platformie Azure.

> [!NOTE]
> Ten artykuł ma być używany, jeśli używasz **Resource Manager** jako model wdrażania dla maszyny wirtualnej podczas pracy awaryjnej. Jeśli używasz **klasycznego** jako model wdrażania, postępuj zgodnie z instrukcjami [jak skonfigurować i konfigurowanie serwera przetwarzania powrotu po awarii (klasyczne)](./site-recovery-vmware-setup-azure-ps-classic.md)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Wdrażanie serwera przetwarzania na platformie Azure
1. W magazynie > **infrastruktura usługi Site Recovery** (w pozycji "Manage") > **serwery konfiguracji** (w pozycji "Dla VMware i maszyn fizycznych"), wybierz serwer konfiguracji.
2. Na stronie szczegółów konfiguracji serwera, który zostanie otwarty, kliknij przycisk "+ serwera przetwarzania"

  ![Dodaj galerii serwera przetwarzania](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  Na **Dodaj serwer przetwarzania** wybierz następujące wartości

  ![Dodawanie elementu galerii serwera przetwarzania](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**Nazwa pola**|**Wartość**|
|-|-|
|Wybierz miejsce wdrożenia serwera przetwarzania|Wybierz wartość **wdrażanie serwera przetwarzania powrotu po awarii na platformie Azure** |
|Subskrypcja|Wybierz subskrypcję Azure, w którym przełączyć maszyny wirtualne|
|Grupa zasobów|Można utworzyć grupy zasobów, aby wdrożyć ten serwer przetwarzania lub wybrać wdrożenie serwera przetwarzania w istniejącej grupy zasobów|
|Lokalizacja|Wybierz centrum danych Azure, w której maszyny wirtualne w przypadku, gdy do przejścia w tryb failover|
|Azure Network|Wybierz Network(VNet) wirtualnych Azure który maszyn wirtualnych w przypadku, gdy do przejścia w tryb failover. Jeśli nie przez maszyny wirtualne do wielu sieci wirtualnych platformy Azure, następnie należy serwer przetwarzania, wdrożone na sieć wirtualną|

4. Wypełnij pozostałe właściwości serwera przetwarzania

  ![Dodaj serwer przetwarzania podsumowania](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**Nazwa pola**|**Wartość**|
|-|-|
|Nazwa serwera|Nazwa wyświetlana i nazwa hosta dla maszyny wirtualnej serwera procesu|
| Nazwa użytkownika|Nazwa użytkownika, który staje się administratorem na tej maszynie wirtualnej|
|Konto magazynu|Nazwa konta magazynu rozmieszczenia dysku wirtualnego maszyny wirtualnej|
|Podsieć|Podsieć sieci wirtualnej Azure, do którego jest podłączony maszyny wirtualnej|
| Adres IP|Adres IP, które chcesz serwera przetwarzania do wniosku po jego rozruchu|
5. Kliknij przycisk OK, aby rozpocząć wdrażanie maszyny wirtualnej serwera procesu.

> [!NOTE]
> Aby można było użyć tego serwera przetwarzania powrotu po awarii, musisz zarejestrowanie go za pomocą lokalnego serwera konfiguracji.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Rejestrowanie serwera przetwarzania (działające na platformie Azure) do konfiguracji serwera (uruchamiane lokalnie)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Uaktualnianie serwera przetwarzania do najnowszej wersji.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Wyrejestrowywanie serwera przetwarzania (działające na platformie Azure) z serwera konfiguracji (uruchamiane lokalnie)

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
