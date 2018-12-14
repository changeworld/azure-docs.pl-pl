---
title: Wystąpienie zarządzane usługi Azure SQL Database tworzenie sieci wirtualnej | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób tworzenia sieci wirtualnej (VNet), których można wdrożyć bazy danych wystąpienia zarządzanego Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: ebdfc80d3802ad8eb7da6fb7f152efdaee8d777d
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346234"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Konfigurowanie sieci wirtualnej dla wystąpienie zarządzane usługi Azure SQL Database

W tym temacie opisano sposób tworzenia prawidłową sieć wirtualną i podsieć, w którym można wdrożyć wystąpieniach zarządzanych bazy danych SQL Azure.

Wystąpienie usługi Azure SQL Database Managed musi zostać wdrożony w ramach platformy Azure [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md). To wdrożenie umożliwia następujące scenariusze: 
- Zabezpiecz prywatny adres IP.
- Połączenie do wystąpienia zarządzanego bezpośrednio z siecią lokalną 
- Nawiązywanie połączenia z wystąpienia zarządzanego połączonego serwera lub innego środowiska lokalnego magazynu danych 
- Wystąpienie zarządzane nawiązywania połączenia z zasobami platformy Azure  

  > [!Note]
  > Należy [określi rozmiar podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md) przed wdrożeniem pierwsze wystąpienie, ponieważ sunet nie można zmienić rozmiaru po wprowadzeniu zasobów w obrębie.
  > Jeśli planujesz użyć istniejącej sieci wirtualnej, należy zmodyfikować tej konfiguracji sieci, aby pomieścić wystąpienia zarządzanego. Aby uzyskać więcej informacji, zobacz [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md). 

## <a name="create-a-new-virtual-network"></a>Utwórz nową sieć wirtualną

Najprostszym sposobem tworzenia i konfigurowania sieci wirtualnej jest użycie szablonu wdrożenia usługi Azure Resource Manager.

1. Zaloguj się do Portalu Azure.

2. Użyj **Wdróż na platformie Azure** przycisk, aby wdrożyć sieć wirtualną w chmurze platformy Azure:

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Kliknięcie tego przycisku zostanie otwarty formularz, który można użyć do konfigurowania środowisko sieciowe, w którym można wdrożyć wystąpienie zarządzane usługi.

  > [!Note]
  > Ten szablon usługi Azure Resource Manager umożliwia wdrożenie sieci wirtualnej z dwiema podsieciami. Jedną podsieć o nazwie **ManagedInstances** jest zarezerwowany dla wystąpienia zarządzanego i ma wstępnie skonfigurowane na tabelę tras, podczas gdy inne podsieci o nazwie **domyślne** służy do innych zasobów, które powinien uzyskiwać dostęp do zarządzanego Wystąpienie (na przykład maszyny wirtualne platformy Azure). Możesz usunąć **domyślne** podsieci, o ile nie są potrzebne.

3. Konfigurowanie środowiska sieciowego. W następującej postaci można skonfigurować parametrów środowiska sieciowego:

![Skonfiguruj sieć platformy azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

Może zmieniać nazwy sieci wirtualnej i podsieci i dostosować zakresów adresów IP skojarzonych z zasobami w sieci. Po naciśnięciu przycisku "Kup", ta forma będzie tworzyć i konfigurować środowiska. Jeśli dwie podsieci nie jest konieczne, należy usunąć domyślny. 

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md).
- Dowiedz się więcej o [architektura łączności w wystąpieniu zarządzanym](sql-database-managed-instance-connectivity-architecture.md).
- Wyczyść jak [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md)
- Aby uzyskać samouczek, w którym pokazano, jak utworzyć sieć wirtualną, utworzyć wystąpienie zarządzane i przywrócić bazę danych z kopii zapasowej bazy danych, zobacz [Tworzenie wystąpienia usługi Azure SQL Database Managed](sql-database-managed-instance-get-started.md).
- W przypadku problemów DNS, zobacz [Konfigurowanie niestandardowych serwerów DNS](sql-database-managed-instance-custom-dns.md)
