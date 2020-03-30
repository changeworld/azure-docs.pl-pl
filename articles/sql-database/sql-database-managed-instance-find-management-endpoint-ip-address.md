---
title: Odnajdowanie punktu końcowego zarządzania wystąpieniami zarządzanymi
description: Dowiedz się, jak uzyskać publiczny adres IP zarządzania zadaniem zarządzania wystąpieniami zarządzanymi usługi Azure SQL Database i zweryfikować jego wbudowaną ochronę zapory
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825717"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Określanie adresu IP punktu końcowego zarządzania

Klaster wirtualny wystąpienia zarządzanego bazy danych SQL azure zawiera punkt końcowy zarządzania, który jest używany przez firmę Microsoft do operacji zarządzania. Punkt końcowy zarządzania jest chroniony za pomocą wbudowanej zapory na poziomie sieci i wzajemnej weryfikacji certyfikatów na poziomie aplikacji. Można określić adres IP punktu końcowego zarządzania, ale nie można uzyskać dostępu do tego punktu końcowego.

Aby określić adres IP zarządzania, wykonaj wyszukiwanie DNS w `mi-name.zone_id.database.windows.net`zarządzanym wystąpieniu FQDN: . Spowoduje to zwrócenie wpisu DNS, który jest podobny `trx.region-a.worker.vnet.database.windows.net`. Następnie można wykonać wyszukiwanie DNS na tej nazwy FQDN z ".vnet" usunięte. Spowoduje to zwrócenie adresu IP zarządzania. 

Ten program PowerShell zrobi to wszystko \<za Ciebie,\> jeśli zastąpisz MI FQDN wpisem DNS zarządzanego wystąpienia: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Aby uzyskać więcej informacji na temat wystąpień zarządzanych i łączności, zobacz [Architektura łączności wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-connectivity-architecture.md).
