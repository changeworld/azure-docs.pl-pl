---
title: Tworzenie sieci wirtualnej dla wystąpienia zarządzanego
description: W tym artykule opisano sposób tworzenia sieci wirtualnej, w której można wdrożyć wystąpienie zarządzane usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 9f115d51657993562642391a235de79420aa434a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823374"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Tworzenie sieci wirtualnej dla wystąpienia zarządzanego usługi Azure SQL Database

W tym artykule wyjaśniono, jak utworzyć prawidłową sieć wirtualną i podsieć, w której można wdrożyć wystąpienie zarządzane według bazy danych SQL platformy Azure.

Wystąpienie zarządzanej bazy danych SQL platformy Azure musi zostać wdrożone w [sieci wirtualnej](../virtual-network/virtual-networks-overview.md)platformy Azure. To wdrożenie umożliwia następujące scenariusze:

- Bezpieczny prywatny adres IP
- Łączenie się z wystąpieniem zarządzanym bezpośrednio z sieci lokalnej
- Łączenie wystąpienia zarządzanego z serwerem połączonym lub innym lokalnym magazynem danych
- Łączenie wystąpienia zarządzanego z zasobami platformy Azure  

> [!Note]
> Przed wdrożeniem pierwszego wystąpienia należy [określić rozmiar podsieci dla wystąpienia zarządzanego.](sql-database-managed-instance-determine-size-vnet-subnet.md) Nie można zmienić rozmiaru podsieci po umieszczeniu zasobów wewnątrz.
>
> Jeśli planujesz używać istniejącej sieci wirtualnej, należy zmodyfikować tę konfigurację sieci, aby pomieścić wystąpienie zarządzane. Aby uzyskać więcej informacji, zobacz [Modyfikowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md).
>
> Po utworzeniu wystąpienia zarządzanego przeniesienie wystąpienia zarządzanego lub sieci wirtualnej do innej grupy zasobów lub subskrypcji nie jest obsługiwane.


## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Najprostszym sposobem tworzenia i konfigurowania sieci wirtualnej jest użycie szablonu wdrożenia usługi Azure Resource Manager.

1. Zaloguj się do Portalu Azure.

2. Wybierz przycisk **Wdrażanie na platformie Azure:**

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Ten przycisk otwiera formularz, za pomocą którego można skonfigurować środowisko sieciowe, w którym można wdrożyć wystąpienie zarządzane.

   > [!Note]
   > Ten szablon usługi Azure Resource Manager wdroży sieć wirtualną z dwoma podsieciami. Jedna podsieć o nazwie **ManagedInstances**jest zarezerwowana dla wystąpienia zarządzanego i ma wstępnie skonfigurowaną tabelę marszrut. Druga podsieć o nazwie **Default**jest używana dla innych zasobów, które powinny uzyskać dostęp do wystąpienia zarządzanego (na przykład maszyny wirtualne platformy Azure).

3. Skonfiguruj środowisko sieciowe. W następującym formularzu można skonfigurować parametry środowiska sieciowego:

   ![Szablon Usługi Resource Manager do konfigurowania sieci platformy Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Można zmienić nazwy sieci wirtualnej i podsieci i dostosować zakresy adresów IP skojarzone z zasobami sieciowymi. Po wybraniu przycisku **Zakup** ten formularz utworzy i skonfiguruje środowisko. Jeśli nie potrzebujesz dwóch podsieci, możesz usunąć domyślną.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie, zobacz [Co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
- Dowiedz się więcej o [architekturze łączności w wystąpieniu zarządzanym](sql-database-managed-instance-connectivity-architecture.md).
- Dowiedz się, jak [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md).
- W samouczku, który pokazuje, jak utworzyć sieć wirtualną, utworzyć wystąpienie zarządzane i przywrócić bazę danych z kopii zapasowej bazy danych, zobacz [Tworzenie wystąpienia zarządzanego bazy danych Azure SQL](sql-database-managed-instance-get-started.md)Database .
- W przypadku problemów z systemem DNS zobacz [Konfigurowanie niestandardowego systemu DNS](sql-database-managed-instance-custom-dns.md).
