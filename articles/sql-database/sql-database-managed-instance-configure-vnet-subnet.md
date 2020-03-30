---
title: Konfigurowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego
description: W tym artykule opisano sposób konfigurowania istniejącej sieci wirtualnej i podsieci, w której można wdrożyć wystąpienie zarządzane według bazy danych SQL platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 50b832baa9253f47b5f10980ae1764c9425ed4d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476953"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Konfigurowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego usługi Azure SQL Database

Wystąpienie zarządzanej bazy danych SQL platformy Azure musi zostać wdrożone w [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) platformy Azure i podsieci przeznaczonej tylko dla wystąpień zarządzanych. Można użyć istniejącej sieci wirtualnej i podsieci, jeśli jest skonfigurowana zgodnie z [wymaganiami sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Jeśli ma zastosowanie jeden z następujących przypadków, można zweryfikować i zmodyfikować sieć za pomocą skryptu wyjaśnionego w tym artykule:

- Masz nową podsieć, która nadal nie jest skonfigurowana.
- Nie masz pewności, czy podsieć jest wyrównana z [wymaganiami](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Chcesz sprawdzić, czy podsieć nadal spełnia [wymagania sieciowe](sql-database-managed-instance-connectivity-architecture.md#network-requirements) po wszczęciu zmian.

> [!Note]
> Wystąpienie zarządzanego można utworzyć tylko w sieciach wirtualnych utworzonych za pośrednictwem modelu wdrażania usługi Azure Resource Manager. Sieci wirtualne platformy Azure utworzone za pośrednictwem klasycznego modelu wdrażania nie są obsługiwane. Oblicz rozmiar podsieci, postępając zgodnie z wytycznymi w artykule [Określanie rozmiaru podsieci dla wystąpień zarządzanych.](sql-database-managed-instance-determine-size-vnet-subnet.md) Nie można zmienić rozmiaru podsieci po wdrożeniu zasobów wewnątrz.
>
> Po utworzeniu wystąpienia zarządzanego przeniesienie wystąpienia zarządzanego lub sieci wirtualnej do innej grupy zasobów lub subskrypcji nie jest obsługiwane.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Sprawdzanie poprawności i modyfikowanie istniejącej sieci wirtualnej

Jeśli chcesz utworzyć wystąpienie zarządzane wewnątrz istniejącej podsieci, zalecamy następujący skrypt programu PowerShell, aby przygotować podsieć:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Skrypt przygotowuje podsieć w trzech krokach:

1. Sprawdź poprawność: sprawdza poprawność wybranej sieci wirtualnej i podsieci dla wymagań sieciowych wystąpienia zarządzanego.
2. Potwierdź: pokazuje użytkownikowi zestaw zmian, które należy wprowadzili w celu przygotowania podsieci do wdrożenia wystąpienia zarządzanego. Prosi również o zgodę.
3. Przygotowanie: Prawidłowo konfiguruje sieć wirtualną i podsieć.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie, zobacz [Co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
- W samouczku, który pokazuje, jak utworzyć sieć wirtualną, utworzyć wystąpienie zarządzane i przywrócić bazę danych z kopii zapasowej bazy danych, zobacz [Tworzenie wystąpienia zarządzanego bazy danych Azure SQL](sql-database-managed-instance-get-started.md)Database .
- W przypadku problemów z systemem DNS zobacz [Konfigurowanie niestandardowego systemu DNS](sql-database-managed-instance-custom-dns.md).
