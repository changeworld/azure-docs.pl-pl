---
title: Włączanie konfiguracji podsieci wspomaganej usługą dla wystąpienia zarządzanego usługi Azure SQL Database
description: Włączanie konfiguracji podsieci wspomaganej usługą dla wystąpienia zarządzanego usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533270"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Włączanie konfiguracji podsieci wspomaganej usługą dla wystąpienia zarządzanego usługi Azure SQL Database
Konfiguracja podsieci wspomagana usługami zapewnia automatyczne zarządzanie konfiguracją sieci dla podsieci obsługujących wystąpienia zarządzane. Dzięki obsłudze konfiguracji podsieci użytkownik pozostaje w pełnej kontroli dostępu do danych (przepływów ruchu TDS), podczas gdy wystąpienie zarządzane bierze odpowiedzialność za zapewnienie nieprzerwanego przepływu ruchu zarządzania w celu spełnienia umowy SLA.

Automatycznie skonfigurowane sieciowe grupy zabezpieczeń i reguły tabeli tras są widoczne dla klienta i oznaczone prefiksem _Microsoft.Sql-managedInstances_UseOnly__.

Konfiguracja wspomagana usługami jest włączana automatycznie po włączeniu [delegowania podsieci](../virtual-network/subnet-delegation-overview.md) dla `Microsoft.Sql/managedInstances` dostawcy zasobów.

> [!IMPORTANT] 
> Po włączeniu delegowania podsieci nie można go wyłączyć, dopóki nie usuniesz ostatniego klastra wirtualnego z podsieci. Aby uzyskać więcej informacji na temat usuwania klastra wirtualnego, zobacz następujący [artykuł](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal).

> [!NOTE] 
> Ponieważ konfiguracja podsieci wspomaganej usługami jest niezbędną funkcją do utrzymania umowy SLA, od 1 maja 2020 r. nie będzie możliwe wdrożenie wystąpień zarządzanych w podsieciach, które nie są delegowane do dostawcy zasobów wystąpienia zarządzanego. 1 lipca 2020 r. wszystkie podsieci zawierające wystąpienia zarządzane zostaną automatycznie delegowane do dostawcy zasobów wystąpienia zarządzanego. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Włączanie delegowania podsieci dla nowych wdrożeń
Aby wdrożyć wystąpienie zarządzane w pustej `Microsoft.Sql/managedInstances` podsieci, należy delegować je dostawcy zasobów zgodnie z opisem w poniższym [artykule](../virtual-network/manage-subnet-delegation.md). _Należy pamiętać, że artykuł, do którego istnieje odwołanie, używa `Microsoft.DBforPostgreSQL/serversv2` na przykład dostawcy zasobów. Zamiast tego należy `Microsoft.Sql/managedInstances` użyć dostawcy zasobów._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Włączanie delegowania podsieci dla istniejących wdrożeń

Aby włączyć delegowanie podsieci dla istniejącego wdrożenia wystąpienia zarządzanego, należy znaleźć podsieć sieci wirtualnej, gdzie jest ona umieszczona. 

Aby się tego `Virtual network/subnet` dowiedzieć, możesz sprawdzić w bloku `Overview` portalu wystąpienia zarządzanego.

Alternatywnie można uruchomić następujące polecenia programu PowerShell, aby się tego nauczyć. Zamień **identyfikator subskrypcji** na identyfikator subskrypcji. Zastąp również **nazwę rg** grupą zasobów dla wystąpienia zarządzanego i zastąp **nazwę mi** nazwą wystąpienia zarządzanego.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Po znalezieniu podsieci wystąpienia zarządzanego należy delegować ją do `Microsoft.Sql/managedInstances` dostawcy zasobów, jak opisano w poniższym [artykule](../virtual-network/manage-subnet-delegation.md). _Należy pamiętać, że artykuł, do którego istnieje odwołanie, używa `Microsoft.DBforPostgreSQL/serversv2` na przykład dostawcy zasobów. Zamiast tego należy `Microsoft.Sql/managedInstances` użyć dostawcy zasobów._


> [!IMPORTANT]
> Włączenie konfiguracji wspomaganej usługami nie powoduje pracy awaryjnej ani przerwy w łączności dla wystąpień zarządzanych, które są już w podsieci.
