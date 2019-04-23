---
title: Konfigurowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania istniejącą sieć wirtualną i podsieć, w którym można wdrożyć wystąpienia zarządzanego Azure SQL Database.
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
ms.openlocfilehash: c4ff12f0c9adcb9943a6e2426eaf2740ba171e39
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792439"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Konfigurowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego Azure SQL Database

Wystąpienie usługi Azure SQL Database Managed musi zostać wdrożony w ramach platformy Azure [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) i podsieć, w wersji dedykowanej dla wystąpień zarządzanych tylko. Jeśli jest skonfigurowany zgodnie z opisem w można użyć istniejącej sieci wirtualnej i podsieci [wymagania dotyczące sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Jeśli jeden z następujących przypadków ma zastosowanie do Ciebie, można sprawdzić i modyfikować sieci przy użyciu skryptu opisanych w tym artykule:

- Masz nowej podsieci, które nadal nie jest skonfigurowane.
- Nie masz pewności, że podsieć jest powiązana z [wymagania](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Aby sprawdzić, czy podsieci, spełnia [wymagania dotyczące sieciowej](sql-database-managed-instance-connectivity-architecture.md#network-requirements) po dokonaniu zmiany.

> [!Note]
> Można utworzyć wystąpienie zarządzane tylko w sieciach wirtualnych utworzonych za pomocą modelu wdrażania usługi Azure Resource Manager. Sieci wirtualne platformy Azure utworzonych za pomocą klasycznego modelu wdrażania nie są obsługiwane. Oblicz rozmiar podsieci, postępując zgodnie z wytycznymi podanymi w [określi rozmiar podsieci wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md) artykułu. Po wdrożeniu zasobów w obrębie nie można zmienić rozmiaru tej podsieci.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Weryfikowanie i modyfikowanie istniejącej sieci wirtualnej

Jeśli chcesz utworzyć wystąpienie zarządzane wewnątrz istniejącej podsieci, zaleca się poniższy skrypt programu PowerShell, aby przygotować się do podsieci:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Skrypt przygotowuje podsieci w trzech krokach:

1. Sprawdzanie poprawności: Sprawdza poprawność wybranej sieci wirtualnej i podsieci dla wystąpienia zarządzanego wymagań sieciowych.
2. Potwierdź: Użytkownik pokazuje zestaw zmian, które należy podjąć, przygotować podsieci do wdrożenia wystąpienia zarządzanego. Również prosi o ich zgodę.
3. Przygotuj się: Prawidłowo skonfiguruje sieć wirtualną i podsieć.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
- Aby uzyskać samouczek, który pokazuje, jak utworzyć sieć wirtualną, utworzyć wystąpienie zarządzane i przywrócić bazę danych z kopii zapasowej bazy danych, zobacz [Tworzenie wystąpienia usługi Azure SQL Database Managed](sql-database-managed-instance-get-started.md).
- W przypadku problemów DNS, zobacz [Konfigurowanie niestandardowych DNS](sql-database-managed-instance-custom-dns.md).
