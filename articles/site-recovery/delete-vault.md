---
title: Usuwanie Recovery Services magazynu skonfigurowanego dla usługi Azure Site Recovery
description: Dowiedz się, jak usunąć magazyn Recovery Services skonfigurowany dla Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: a13dee2010688b02fd86fb05900826470a7d7a08
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876037"
---
# <a name="delete-a-site-recovery-services-vault"></a>Usuwanie magazynu usług Site Recovery Services

Zależności uniemożliwiają usunięcie magazynu Azure Site Recovery. Akcje, które należy podjąć, różnią się w zależności od scenariusza Site Recovery. Aby usunąć magazyn używany w Azure Backup, zobacz [Usuwanie magazynu kopii zapasowych na platformie Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-a-site-recovery-vault"></a>Usuwanie magazynu Site Recovery 
Aby usunąć magazyn, postępuj zgodnie z zalecanymi krokami.
### <a name="azure-vms-to-azure"></a>Maszyny wirtualne platformy Azure do platformy Azure

1. Usuń wszystkie chronione maszyny wirtualne, wykonując czynności opisane w sekcji [wyłączanie ochrony dla programu VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure).
2. Usuń magazyn.

### <a name="vmware-vms-to-azure"></a>Maszyny wirtualne VMware do platformy Azure

1. Usuń wszystkie chronione maszyny wirtualne, wykonując czynności opisane w sekcji [wyłączanie ochrony dla programu VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Usuń wszystkie zasady replikacji, wykonując czynności opisane w temacie [usuwanie zasad replikacji](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Usuń odwołania do programu vCenter, wykonując czynności opisane w temacie [usuwanie serwera vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Usuń serwer konfiguracji, wykonując czynności opisane w temacie [Likwidowanie serwera konfiguracji](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Usuń magazyn.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Maszyny wirtualne funkcji Hyper-V (z programem VMM) do platformy Azure
1. Usuń wszystkie chronione maszyny wirtualne, wykonując czynności opisane w sekcji[wyłączanie ochrony maszyny wirtualnej funkcji Hyper-V (z programem VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Usuń skojarzenie & usunąć wszystkie zasady replikacji, przechodząc do magazynu > **Site Recovery infrastruktury** — > dla**zasad replikacji** **programu System Center VMM** -> 

3.  Usuń odwołania do serwerów programu VMM, wykonując czynności opisane w sekcji [Wyrejestrowanie połączonego serwera programu VMM](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Usuń magazyn.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Maszyny wirtualne funkcji Hyper-V (bez Virtual Machine Manager) do platformy Azure
1. Usuń wszystkie chronione maszyny wirtualne, wykonując czynności opisane w sekcji [wyłączanie ochrony maszyny wirtualnej funkcji Hyper-v (funkcja Hyper-v do platformy Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Usuń skojarzenie & usunąć wszystkie zasady replikacji, przechodząc do magazynu > **Site Recovery infrastruktury** — > dla**zasad replikacji**  ->  **lokacji funkcji Hyper-V**

3. Usuń odwołania do serwerów funkcji Hyper-V, wykonując czynności opisane w temacie [Wyrejestrowywanie hosta funkcji Hyper-v](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Usuń lokację funkcji Hyper-V.

5. Usuń magazyn.


## <a name="use-powershell-to-force-delete-the-vault"></a>Wymuś usunięcie magazynu za pomocą programu PowerShell 

> [!Important]
> Jeśli testujesz produkt i nie obawiasz się o utracie danych, użyj metody Force Delete, aby szybko usunąć magazyn i wszystkie jego zależności.
> Polecenie programu PowerShell usuwa całą zawartość magazynu i **nie**jest odwracalne.

Aby usunąć magazyn Site Recovery, nawet jeśli są chronione elementy, użyj następujących poleceń:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Dowiedz się więcej na temat [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)i [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
