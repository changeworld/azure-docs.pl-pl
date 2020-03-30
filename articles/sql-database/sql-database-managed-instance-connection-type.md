---
title: Typy połączeń wystąpienia zarządzanego
description: Dowiedz się więcej o typach połączeń wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819457"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Typy połączeń wystąpień zarządzanych usługi Azure SQL Database

W tym artykule wyjaśniono, jak klienci łączą się z wystąpieniem zarządzanym usługi Azure SQL Database w zależności od typu połączenia. Poniżej przedstawiono przykłady skryptów do zmiany typów połączeń, wraz z zagadnieniami związanymi ze zmianą domyślnych ustawień łączności.

## <a name="connection-types"></a>Typy połączeń

Wystąpienie zarządzanej usługi Azure SQL Database obsługuje następujące dwa typy połączeń:

- **Przekierowanie (zalecane):** Klienci nawiązywać połączenia bezpośrednio do węzła hosting bazy danych. Aby włączyć łączność za pomocą przekierowania, należy otworzyć zapory i sieciowe grupy zabezpieczeń ,,sieciowe grupy zabezpieczeń), aby zezwolić na dostęp do portów 1433 i 11000-11999. Pakiety przejść bezpośrednio do bazy danych, a zatem istnieją opóźnienia i przepustowość poprawy wydajności za pomocą przekierowania za pośrednictwem serwera proxy.
- **Serwer proxy (domyślnie):** W tym trybie wszystkie połączenia są używane składnik bramy proxy. Aby włączyć łączność, należy otworzyć tylko port 1433 dla sieci prywatnych i port 3342 dla połączenia publicznego. Wybranie tego trybu może spowodować większe opóźnienie i niższą przepływność, w zależności od charakteru obciążenia. Zdecydowanie zalecamy zasady połączeń przekierowania za pośrednictwem zasad połączenia serwera proxy dla najniższego opóźnienia i najwyższej przepływności.

## <a name="redirect-connection-type"></a>Przekierowanie typu połączenia

Przekierowanie typu połączenia oznacza, że po ustanowieniu sesji TCP do aparatu SQL sesja klienta uzyskuje docelowy wirtualny adres IP węzła klastra wirtualnego z modułu równoważenia obciążenia. Kolejne pakiety przepływają bezpośrednio do węzła klastra wirtualnego, omijając bramę. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![redirect.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Typ połączenia przekierowania działa obecnie tylko dla prywatnego punktu końcowego. Niezależnie od ustawienia typu połączenia połączenia przychodzące przez publiczny punkt końcowy będzie za pośrednictwem serwera proxy.

## <a name="proxy-connection-type"></a>Typ połączenia serwera proxy

Typ połączenia serwera proxy oznacza, że sesja TCP jest ustanawiana przy użyciu bramy, a wszystkie kolejne pakiety przepływają przez nią. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![proxy.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Skrypt do zmiany ustawień typu połączenia przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W poniższym skrypcie programu PowerShell pokazano, jak zmienić typ połączenia dla wystąpienia zarządzanego na Przekierowanie.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Następne kroki

- [Przywracanie bazy danych do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md)
- Dowiedz się, jak [skonfigurować publiczny punkt końcowy w wystąpieniu zarządzanym](sql-database-managed-instance-public-endpoint-configure.md)
- Dowiedz się więcej o [architekturze łączności wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md)