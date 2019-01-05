---
title: Wystąpienie usługi Azure SQL Database zarządzane Konfigurowanie istniejącej sieci Wirtualnej/podsieci | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób konfigurowania istniejącą sieć wirtualną (VNet) i podsiecią, w którym można wdrożyć wystąpienia zarządzanego Azure SQL Database.
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
ms.date: 01/03/2019
ms.openlocfilehash: 1718177a0902bc7049eb6986e5a1d128eeb3f233
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040962"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>Konfigurowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego Azure SQL Database

Wystąpienie usługi Azure SQL Database Managed musi zostać wdrożony w ramach platformy Azure [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md) i podsieć, w wersji dedykowanej dla wystąpień zarządzanych tylko. Jeśli jest skonfigurowany zgodnie z opisem w można użyć istniejącej sieci wirtualnej i podsieci [wymagania dotyczące zarządzanych sieci wirtualnej wystąpienia](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Jeśli masz nowej podsieci, które nadal nie jest skonfigurowane, nie masz pewności podsieci jest powiązana z [wymagania](sql-database-managed-instance-connectivity-architecture.md#network-requirements), lub chcesz sprawdzić, są nadal zgodne z podsieci [wymagania dotyczące sieciowej](sql-database-managed-instance-connectivity-architecture.md#network-requirements) po pewne zmiany, które zostały wprowadzone, możesz sprawdzić i modyfikować sieci przy użyciu skryptów opisano w tej sekcji.

  > [!Note]
  > Wystąpienie zarządzane można utworzyć tylko w sieci wirtualnej usługi Resource Manager. Sieci wirtualnych platformy Azure wdrożone za pomocą klasycznego modelu wdrażania nie jest obsługiwany. Upewnij się, Oblicz rozmiar podsieci postępując zgodnie z wytycznymi podanymi w [określi rozmiar podsieci wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md) artykułu, ponieważ podsieć nie można zmienić rozmiaru, gdy wdrażanie zasobów w obrębie.

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

Przygotowanie podsieci odbywa się w trzech prostych krokach:

1. Sprawdź poprawność — wybranej sieci wirtualnej i podsieci są prawidłowe dla zarządzanego wystąpienia wymagań sieciowych.
2. Upewnij się, — użytkownik jest wyświetlany zestaw zmian, które muszą zostać wprowadzone do przygotowania podsieci do wdrożenia wystąpienia zarządzanego i monit o zgodę.
3. Przygotowanie — sieci wirtualnej i podsieci są prawidłowo skonfigurowane.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Aby uzyskać samouczek, w którym pokazano, jak utworzyć sieć wirtualną, utworzyć wystąpienie zarządzane i przywrócić bazę danych z kopii zapasowej bazy danych, zobacz [Tworzenie wystąpienia usługi Azure SQL Database Managed](sql-database-managed-instance-get-started.md).
- W przypadku problemów DNS, zobacz [Konfigurowanie niestandardowych serwerów DNS](sql-database-managed-instance-custom-dns.md).
