---
title: Integracja usługi Key Vault z programem SQL Server na maszynach wirtualnych z systemem Windows na platformie Azure (Menedżer zasobów) | Dokumenty firmy Microsoft
description: Dowiedz się, jak zautomatyzować konfigurację szyfrowania programu SQL Server do użytku z usługą Azure Key Vault. W tym temacie wyjaśniono, jak używać integracji usługi Azure Key Vault z maszynami wirtualnymi programu SQL Server utworzonymi za pomocą Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: cad70169e88e1fafa129c02f30d5288d39e30a9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102149"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurowanie integracji z usługą Azure Key Vault dla programu SQL Server w usłudze Azure Virtual Machines (Resource Manager)

> [!div class="op_single_selector"]
> * [Menedżer zasobów](virtual-machines-windows-ps-sql-keyvault.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Omówienie
Istnieje wiele funkcji szyfrowania programu SQL Server, takich jak [przezroczyste szyfrowanie danych (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [szyfrowanie na poziomie kolumny (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)i [szyfrowanie kopii zapasowych.](https://msdn.microsoft.com/library/dn449489.aspx) Te formy szyfrowania wymagają zarządzania kluczami kryptograficznymi używanymi do szyfrowania i przechowywania ich. Usługa Azure Key Vault (AKV) została zaprojektowana w celu zwiększenia bezpieczeństwa i zarządzania tymi kluczami w bezpiecznej i wysoce dostępnej lokalizacji. Łącznik [programu SQL Server umożliwia](https://www.microsoft.com/download/details.aspx?id=45344) programowi SQL Server używanie tych kluczy z usługi Azure Key Vault.

Jeśli korzystasz z programu SQL Server z komputerami lokalnymi, [należy wykonać kroki, aby uzyskać dostęp do usługi Azure Key Vault z lokalnego komputera programu SQL Server.](https://msdn.microsoft.com/library/dn198405.aspx) Jednak w przypadku programu SQL Server na maszynach wirtualnych platformy Azure można zaoszczędzić czas przy użyciu funkcji *integracji usługi Azure Key Vault.*

Gdy ta funkcja jest włączona, automatycznie instaluje łącznik programu SQL Server, konfiguruje dostawcę EKM, aby uzyskać dostęp do usługi Azure Key Vault i tworzy poświadczenia, aby umożliwić dostęp do magazynu. Jeśli spojrzeno na kroki opisane wcześniej w lokalnej dokumentacji, widać, że ta funkcja automatyzuje kroki 2 i 3. Jedyną rzeczą, którą nadal musisz zrobić ręcznie, jest utworzenie magazynu kluczy i kluczy. Stamtąd cała konfiguracja maszyny Wirtualnej SQL jest zautomatyzowana. Po zakończeniu tej konfiguracji tej funkcji można wykonać instrukcje T-SQL, aby rozpocząć szyfrowanie baz danych lub kopii zapasowych w zwykły sposób.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Dostawca EKM w wersji 1.0.4.0 jest zainstalowany na maszynie Wirtualnej programu SQL Server za pośrednictwem [rozszerzenia SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Uaktualnienie rozszerzenia IaaS SQL nie spowoduje zaktualizowania wersji dostawcy. W razie potrzeby należy rozważyć ręczne uaktualnienie wersji dostawcy EKM (na przykład podczas migracji do wystąpienia zarządzanego SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Włączanie i konfigurowanie integracji AKV
Integrację AKV można włączyć podczas inicjowania obsługi administracyjnej lub skonfigurować ją dla istniejących maszyn wirtualnych.

### <a name="new-vms"></a>Nowe maszyny wirtualne
Jeśli inicjujesz aprowizacji nowej maszyny wirtualnej programu SQL Server z Menedżerem zasobów, portal Azure zawiera sposób, aby włączyć integrację usługi Azure Key Vault. Funkcja usługi Azure Key Vault jest dostępna tylko dla wersji Enterprise, Developer i Evaluation programu SQL Server.

![Integracja magazynu kluczy Usług SQL Azure](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Aby uzyskać szczegółowe informacje na temat inicjowania obsługi administracyjnej, zobacz [Aprowizowanie maszyny wirtualnej programu SQL Server w witrynie Azure portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Istniejące maszyny wirtualne

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących maszyn wirtualnych programu SQL Server otwórz [zasób maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) i wybierz pozycję **Zabezpieczenia** w obszarze **Ustawienia**. Wybierz **włącz,** aby włączyć integrację usługi Azure Key Vault. 

![Integracja AKV SQL dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Po zakończeniu wybierz przycisk **Zastosuj** u dołu strony **Zabezpieczenia,** aby zapisać zmiany.

> [!NOTE]
> Nazwa poświadczeń, które utworzyliśmy w tym miejscu, zostanie później zamapowana na identyfikator SQL. Dzięki temu sql logowania, aby uzyskać dostęp do magazynu kluczy. 


> [!NOTE]
> Można również skonfigurować integrację AKV przy użyciu szablonu. Aby uzyskać więcej informacji, zobacz [Szablon przewodnika Szybki start platformy Azure dla integracji usługi Azure Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
