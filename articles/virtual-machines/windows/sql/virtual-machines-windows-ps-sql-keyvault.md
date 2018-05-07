---
title: Integracja magazynu kluczy z programem SQL Server na maszynach wirtualnych systemu Windows na platformie Azure (Resource Manager) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zautomatyzować konfigurację programu SQL Server szyfrowania do użycia z usługą Azure Key Vault. W tym temacie opisano sposób integracji magazynu kluczy Azure za pomocą programu SQL Server maszyn wirtualnych utworzonych za pomocą Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
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
ms.author: jroth
ms.openlocfilehash: 2b398f59aed1610825f495a6089990d393531305
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurowanie integracji magazynu kluczy Azure dla programu SQL Server na maszynach wirtualnych Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Przegląd
Istnieje wiele funkcji szyfrowania programu SQL Server, takich jak [przezroczystego szyfrowania danych (funkcji TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [szyfrowania na poziomie kolumny (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), i [szyfrowania kopii zapasowych](https://msdn.microsoft.com/library/dn449489.aspx). Te formy szyfrowania wymagają przechowywania kluczy kryptograficznych używanego do szyfrowania i zarządzania nimi. Usługa Azure Key Vault (AKV) zaprojektowano w celu poprawy zabezpieczeń i zarządzania tych kluczy w lokalizacji bezpieczne i wysokiej dostępności. [Łącznik usług SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) umożliwia SQL Server do użycia tych kluczy z usługi Azure Key Vault.

Jeśli korzystasz z programu SQL Server z lokalnymi maszynami, istnieją [kroki można wykonać, aby uzyskać dostęp do usługi Azure Key Vault z komputera lokalnego programu SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Jednak dla programu SQL Server na maszynach wirtualnych Azure, można oszczędzić czas za pomocą *integracji magazynu kluczy Azure* funkcji.

Gdy ta funkcja jest włączona, automatycznie jest instalowana łącznika programu SQL Server, konfiguruje dostawcy EKM. Aby uzyskać dostępu do usługi Azure Key Vault i tworzy poświadczenia, aby umożliwić użytkownikowi dostęp do magazynu. Jeśli przeglądał kroki opisane w dokumentacji lokalnego opisane powyżej, zobaczysz, że ta funkcja pozwala zautomatyzować kroki 2 i 3. Jedyną operacją, której nadal trzeba wykonać ręcznie polega na utworzeniu magazynu kluczy i kluczy. Z tego miejsca wszystkie ustawienia maszyny Wirtualnej SQL jest zautomatyzowany. Po ukończeniu tej konfiguracji tej funkcji można wykonywać instrukcje T-SQL, aby rozpocząć szyfrowania z bazy danych lub kopii zapasowych w zwykły sposób.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Włączanie i konfigurowanie Integracja
Można włączyć integracja podczas inicjowania obsługi lub skonfiguruj ją dla istniejących maszyn wirtualnych.

### <a name="new-vms"></a>Nowe maszyny wirtualne
W przypadku udostępniania nowej maszyny wirtualnej programu SQL Server za pomocą Menedżera zasobów Azure portal udostępnia sposób, aby włączyć integrację z usługą Azure Key Vault. Funkcja usługi Azure Key Vault jest dostępna tylko w przypadku Enterprise, Developer i wersji ewaluacyjnej programu SQL Server.

![Integracja magazynu kluczy Usług SQL Azure](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Aby uzyskać szczegółowe wskazówki dotyczące inicjowania obsługi administracyjnej, zobacz [Aprowizowanie maszyny wirtualnej programu SQL Server w portalu Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Istniejące maszyny wirtualne
W przypadku istniejących maszyn wirtualnych programu SQL Server należy wybrać maszyny wirtualnej programu SQL Server. Następnie wybierz **konfigurację programu SQL Server** sekcji **ustawienia** bloku.

![Integracja programu SQL dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

W **konfigurację programu SQL Server** bloku, kliknij przycisk **Edytuj** przycisk w sekcji Integracja magazynu kluczy automatycznego.

![Konfigurowanie integracja SQL dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Gdy skończysz, kliknij przycisk **OK** przycisk w dolnej części **konfigurację programu SQL Server** bloku, aby zapisać zmiany.

> [!NOTE]
> Nazwa poświadczenia, tworzone w tym miejscu zostaną zmapowane do logowania SQL później. Dzięki temu logowania SQL można uzyskać dostępu do magazynu kluczy. 
>
>

> [!NOTE]
> Można również skonfigurować integracja przy użyciu szablonu. Aby uzyskać więcej informacji, zobacz [szablonu Azure Szybki Start dla usługi Azure Key Vault integration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

