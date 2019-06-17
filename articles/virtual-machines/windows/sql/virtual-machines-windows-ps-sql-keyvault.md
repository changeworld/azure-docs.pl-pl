---
title: Integracja usługi Key Vault z programem SQL Server na maszynach wirtualnych z Windows na platformie Azure (Resource Manager) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zautomatyzować proces konfiguracji szyfrowania programu SQL Server do użycia z usługą Azure Key Vault. W tym temacie opisano sposób integracji magazynu kluczy Azure za pomocą programu SQL Server maszyny wirtualne utworzone przy użyciu usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 13d698cfbc0241248a77fd5f3b148a9393320c64
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076011"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurowanie integracji usługi Azure Key Vault dla programu SQL Server na maszynach wirtualnych platformy Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Omówienie
Istnieje wiele funkcji szyfrowania programu SQL Server, takich jak [technologii transparent data encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [szyfrowania na poziomie kolumny (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), i [szyfrowania kopii zapasowych](https://msdn.microsoft.com/library/dn449489.aspx). Te rodzaje szyfrowania wymagają przechowywania kluczy kryptograficznych używanego do szyfrowania i zarządzania nimi. Usługa Azure Key Vault (AKV) zaprojektowano w celu poprawy zabezpieczeń i zarządzania tych kluczy w bezpiecznych i wysoko dostępnych lokalizacji. [Łącznik usług SQL Server](https://www.microsoft.com/download/details.aspx?id=45344) umożliwia SQL Server, aby użyć tych kluczy z usługi Azure Key Vault.

Jeśli używasz programu SQL Server na maszynach w środowisku lokalnym, istnieją [kroki można wykonać, aby uzyskać dostęp do usługi Azure Key Vault z komputera programu SQL Server w środowisku lokalnym](https://msdn.microsoft.com/library/dn198405.aspx). Ale programu SQL Server na maszynach wirtualnych platformy Azure, możesz zaoszczędzić czas przy użyciu *integracji magazynu kluczy Azure* funkcji.

Po włączeniu tej funkcji, automatycznie jest instalowana łącznik programu SQL Server, konfiguruje dostawcę EKM dostępu do usługi Azure Key Vault i tworzy poświadczeń, aby zezwolić na dostęp do magazynu. Jeśli cookie kroki opisane w dokumentacji wymienione wcześniej w środowisku lokalnym, zostanie wyświetlony, że ta funkcja automatyzuje kroki 2 i 3. Jedyną czynnością, którą należy nadal wykonać ręcznie, jest tworzenie magazynu kluczy i kluczy. W efekcie wszystkie ustawienia maszyny Wirtualnej SQL jest zautomatyzowane. Po ukończeniu tej konfiguracji tej funkcji możesz wykonać instrukcje języka T-SQL, aby rozpocząć szyfrowanie z bazy danych lub tworzenie kopii zapasowych, tak jak zwykle.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Skorzystaj z dostawcy EKM wersji 1.0.4.0 jest zainstalowany na maszynę Wirtualną programu SQL Server za pośrednictwem [rozszerzenie SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Uaktualnianie rozszerzenie SQL IaaS nie zaktualizuje wersja dostawcy. Proszę uwzględnieniu ręcznego uaktualniania wersji dostawcy EKM. Jeśli to konieczne, (na przykład podczas migracji do wystąpienia zarządzanego SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Umożliwia włączenie i skonfigurowanie Integracja
Można włączyć integracja podczas inicjowania obsługi lub skonfigurować go do istniejących maszyn wirtualnych.

### <a name="new-vms"></a>Nowe maszyny wirtualne
W przypadku udostępniania nowej maszyny wirtualnej programu SQL Server przy użyciu usługi Resource Manager, witryny Azure portal umożliwia włączanie integracji usługi Azure Key Vault. Funkcja usługi Azure Key Vault jest dostępna tylko w przypadku Enterprise, Developer i wersji ewaluacyjnej programu SQL Server.

![Integracja magazynu kluczy Usług SQL Azure](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Aby uzyskać szczegółowy przewodnik dotyczący inicjowania obsługi administracyjnej, zobacz [Aprowizowanie maszyny wirtualnej programu SQL Server w witrynie Azure portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Istniejące maszyny wirtualne

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Dla istniejących maszyn wirtualnych programu SQL Server, Otwórz swoje [zasobów maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) i wybierz **zabezpieczeń** w obszarze **ustawienia**. Wybierz **Włącz** można włączyć integracji usługi Azure Key Vault. 

![Integracja programu SQL dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Po zakończeniu wybierz pozycję **Zastosuj** przycisk w dolnej części **zabezpieczeń** strony, aby zapisać zmiany.

> [!NOTE]
> Nazwa poświadczenia, którą utworzyliśmy, w tym miejscu będą mapowane na nazwę logowania SQL później. Dzięki temu identyfikator logowania SQL do dostępu do magazynu kluczy. 


> [!NOTE]
> Można również skonfigurować integracja przy użyciu szablonu. Aby uzyskać więcej informacji, zobacz [szablon szybkiego startu platformy Azure dla usługi Azure Key Vault integration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
