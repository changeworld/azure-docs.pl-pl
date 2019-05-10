---
title: Usuwanie magazynu usługi Recovery Services skonfigurowane na potrzeby usługi Azure Site Recovery
description: Dowiedz się, jak usunąć magazyn usługi Recovery Services, skonfigurowany dla usługi Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 981b78345a0d9ea589e9c39ddaa2e253f1dd343f
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412835"
---
# <a name="delete-a-site-recovery-services-vault"></a>Usuwanie magazynu usługi Site Recovery Services

Zależności mogą uniemożliwić usuwanie magazynu usługi Azure Site Recovery. Akcje, które należy wykonać różnią się zależnie od scenariusza, Usługa Site Recovery. Aby usunąć magazyn używany w usłudze Azure Backup, zobacz [usunąć w magazynie kopii zapasowych Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-a-site-recovery-vault"></a>Usuwanie magazynu usługi Site Recovery 
Aby usunąć magazyn, wykonaj zalecane czynności dla danego scenariusza.
### <a name="azure-vms-to-azure"></a>Maszyny wirtualne platformy Azure do platformy Azure

1. Usuń wszystkie chronione maszyny wirtualne, wykonując kroki opisane w [wyłączyć ochronę programu VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure).
2. Usuń magazyn.

### <a name="vmware-vms-to-azure"></a>Maszyny wirtualne VMware do platformy Azure

1. Usuń wszystkie chronione maszyny wirtualne, wykonując kroki opisane w [wyłączyć ochronę programu VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Usuń wszystkie zasady replikacji, wykonując kroki opisane w [usuwanie zasad replikacji](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Usuń odwołania do vCenter, wykonując kroki opisane w [usuwanie serwera vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Usuwanie serwera konfiguracji, wykonując kroki opisane w [zlikwidować serwer konfiguracji](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Usuń magazyn.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Maszyny wirtualne funkcji Hyper-V (z programem VMM) do platformy Azure
1. Usuń wszystkie chronione maszyny wirtualne, wykonując kroki opisane w[wyłączyć ochronę maszyny Wirtualnej funkcji Hyper-V (z programem VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Usuń skojarzenie i Usuń wszystkie zasady replikacji, przechodząc do magazynu usługi -> **infrastruktura usługi Site Recovery** -> **dla programu System Center VMM** -> **replikacji Zasady**

3.  Usuń odwołania do serwerów programu VMM, wykonując kroki opisane w [wyrejestrować połączony serwer VMM](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Usuń magazyn.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Maszyny wirtualne funkcji Hyper-V (bez programu Virtual Machine Manager) na platformie Azure
1. Usuń wszystkie chronione maszyny wirtualne, wykonując kroki opisane w [Wyłącz ochronę dla maszyny wirtualnej funkcji Hyper-V (funkcji Hyper-V do platformy Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Usuń skojarzenie i Usuń wszystkie zasady replikacji, przechodząc do magazynu usługi -> **infrastruktura usługi Site Recovery** -> **dla lokacji funkcji Hyper-V** -> **zasady replikacji**

3. Usuń odwołania do serwerów funkcji Hyper-V, wykonując kroki opisane w [wyrejestrować hosta funkcji Hyper-V](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Usuń lokację funkcji Hyper-V.

5. Usuń magazyn.


## <a name="use-powershell-to-force-delete-the-vault"></a>Użyj programu PowerShell, aby wymusić usunięcie magazynu 

> [!Important]
> Jeśli testujesz produktu i nie są dane o utracie danych, umożliwia szybkie usunięcie magazynu i wszystkich jego zależności metodę delete życie.
> Polecenia programu PowerShell spowoduje usunięcie całej zawartości magazynu i jest **nie odwracalnego**.

Aby usunąć magazyn usługi Site Recovery, nawet jeśli są chronione elementy, użyj następujących poleceń:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Dowiedz się więcej o [Get AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault), i [AzRecoveryServicesVault Usuń](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
