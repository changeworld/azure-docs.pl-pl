---
title: Usuwanie magazynu usługi Azure Site Recovery
description: Dowiedz się, jak usunąć magazyn usług odzyskiwania skonfigurowany do odzyskiwania witryny platformy Azure
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 0e409ffdedbac822aedf48833f2dd85f8e04afa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894975"
---
# <a name="delete-a-site-recovery-services-vault"></a>Usuwanie magazynu usług Site Recovery

W tym artykule opisano sposób usuwania magazynu usług odzyskiwania dla usługi site recovery. Aby usunąć przechowalnię używaną w usłudze Azure Backup, zobacz [Usuwanie magazynu kopii zapasowych na platformie Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Przed rozpoczęciem

Przed usunięciem przechowalni należy usunąć zarejestrowane serwery i elementy w przechowalni. To, co należy usunąć, zależy od wdrożonych scenariuszy replikacji. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Usuwanie maszyny Wirtualnej platformy Azure w magazynie na platformie Azure

1. Postępuj zgodnie [z tymi instrukcjami,](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) aby usunąć wszystkie chronione maszyny wirtualne.
2. Następnie usuń przechowalnię.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Usuwanie maszyny wirtualnej vault-VMware na platformie Azure

1. Postępuj zgodnie [z tymi instrukcjami,](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) aby usunąć wszystkie chronione maszyny wirtualne.
2. Wykonaj [następujące kroki,](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) aby usunąć wszystkie zasady replikacji.
3. Usuń odwołania do vCenter, wykonując [następujące kroki](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).
4. Postępuj zgodnie z [tymi instrukcjami,](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) aby zlikwidować serwer konfiguracji.
5. Następnie usuń przechowalnię.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Usuwanie maszyny Wirtualnej z hiper-V magazynu (z programem VMM) na platformie Azure

1. Wykonaj [następujące kroki,](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) aby usunąć maszyny wirtualne funkcji Hyper-V zarządzane przez program System Center VMM.
2. Usuń zespozycję i usuń wszystkie zasady replikacji. Zrób to w magazynie > **infrastruktura odzyskiwania lokacji** > dla**zasad replikacji programu**System Center**VMM** > .
3. Wykonaj [następujące kroki,](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) aby wyrejestrować podłączony serwer programu VMM.
4. Następnie usuń przechowalnię.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Usuwanie maszyny Wirtualnej z hiper-v magazynu na platformie Azure

1. Wykonaj [następujące kroki,](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) aby usunąć wszystkie chronione maszyny wirtualne.
2. Usuń zespozycję i usuń wszystkie zasady replikacji. Zrób to w magazynie > **infrastruktura odzyskiwania lokacji** > dla**zasad replikacji**witryn > **funkcji Hyper-V**.
3. Postępuj zgodnie [z tymi instrukcjami,](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) aby wyrejestrować hosta funkcji Hyper-V.
4. Usuń witrynę funkcji Hyper-V.
5. Następnie usuń przechowalnię.


## <a name="use-powershell-to-force-delete-the-vault"></a>Wymuszanie usuwania przechowalni za pomocą programu PowerShell 

> [!Important]
> Jeśli testujesz produkt i nie martwisz się o utratę danych, użyj metody usuwania siły, aby szybko usunąć magazyn i wszystkie jego zależności.
> Polecenie Programu PowerShell usuwa całą zawartość przechowalni i nie jest **odwracalne**.

Aby usunąć magazyn odzyskiwania witryny, nawet jeśli istnieją chronione elementy, użyj następujących poleceń:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Dowiedz się więcej o [get-azRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)i [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
