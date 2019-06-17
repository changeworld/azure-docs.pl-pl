---
title: Tworzenie sieci wirtualnej dla wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia sieci wirtualnej, w którym można wdrożyć wystąpienia zarządzanego Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 5e8b385d018482d281153f1cf80f9953cb8c7f06
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60700504"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Tworzenie sieci wirtualnej dla wystąpienia zarządzanego Azure SQL Database

W tym artykule opisano sposób tworzenia prawidłową sieć wirtualną i podsieć, w którym można wdrożyć wystąpienia zarządzanego Azure SQL Database.

Wystąpienie usługi Azure SQL Database Managed musi zostać wdrożony w ramach platformy Azure [sieci wirtualnej](../virtual-network/virtual-networks-overview.md). To wdrożenie umożliwia następujące scenariusze:

- Zabezpieczanie prywatny adres IP
- Połączenie do wystąpienia zarządzanego bezpośrednio z siecią lokalną
- Nawiązywanie połączenia z wystąpienia zarządzanego połączonego serwera lub innego środowiska lokalnego magazynu danych
- Wystąpienie zarządzane nawiązywania połączenia z zasobami platformy Azure  

> [!Note]
> Należy [określi rozmiar podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md) przed przystąpieniem do wdrażania pierwszego wystąpienia. Nie można zmienić rozmiaru podsieci, po umieszczeniu zasobów w obrębie.
>
> Jeśli planujesz użyć istniejącej sieci wirtualnej, należy zmodyfikować tej konfiguracji sieci, aby pomieścić wystąpienia zarządzanego. Aby uzyskać więcej informacji, zobacz [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Najprostszym sposobem tworzenia i konfigurowania sieci wirtualnej jest użycie szablonu wdrożenia usługi Azure Resource Manager.

1. Zaloguj się do Portalu Azure.

2. Wybierz **Wdróż na platformie Azure** przycisku:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Ten przycisk otwiera formularz, który służy do konfigurowania środowisko sieciowe do której można wdrożyć wystąpienie zarządzane.

   > [!Note]
   > Ten szablon usługi Azure Resource Manager umożliwia wdrożenie sieci wirtualnej z dwiema podsieciami. Jedną podsieć, o nazwie **ManagedInstances**, jest zarezerwowany dla wystąpienia zarządzanego i ma jedną tabelę tras wstępnie skonfigurowane. Inne podsieci o nazwie **domyślne**, jest używana do innych zasobów, które powinien uzyskiwać dostęp do wystąpienia zarządzanego (na przykład maszyny wirtualne platformy Azure).

3. Konfigurowanie środowiska sieciowego. Na następującej formy można skonfigurować parametrów środowiska sieciowego:

   ![Szablon usługi Resource Manager w celu skonfigurowania sieci platformy Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Może zmieniać nazwy sieci wirtualnej i podsieci, a następnie dostosować zakresów adresów IP skojarzone z zasobami sieci. Po wybraniu **zakupu** przycisk, ten formularz, spowoduje to utworzenie i skonfiguruj środowisko. Jeśli dwie podsieci nie jest konieczne, należy usunąć domyślny.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
- Dowiedz się więcej o [architektura łączności w wystąpieniu zarządzanym](sql-database-managed-instance-connectivity-architecture.md).
- Dowiedz się, jak [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md).
- Aby uzyskać samouczek, który pokazuje, jak utworzyć sieć wirtualną, utworzyć wystąpienie zarządzane i przywrócić bazę danych z kopii zapasowej bazy danych, zobacz [Tworzenie wystąpienia usługi Azure SQL Database Managed](sql-database-managed-instance-get-started.md).
- W przypadku problemów DNS, zobacz [Konfigurowanie niestandardowych DNS](sql-database-managed-instance-custom-dns.md).
