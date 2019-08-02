---
title: Odnajdź Azure SQL Database punkt końcowy zarządzania wystąpieniami zarządzanymi | Microsoft Docs
description: Dowiedz się, jak uzyskać Azure SQL Database publiczny adres IP punktu końcowego zarządzania wystąpieniami zarządzanymi i zweryfikować jego wbudowaną ochronę zapory
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
ms.openlocfilehash: a0d47c61bf84cfb22c767fd09b3feed74f55b7fc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567487"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Określ adres IP punktu końcowego zarządzania

Klaster wirtualny Azure SQL Database wystąpienia zarządzanego zawiera punkt końcowy zarządzania wykorzystywany przez firmę Microsoft do zarządzania operacjami. Punkt końcowy zarządzania jest chroniony za pomocą wbudowanej zapory na poziomie sieci i weryfikacji certyfikatu wzajemnego na poziomie aplikacji. Możesz określić adres IP punktu końcowego zarządzania, ale nie możesz uzyskać dostępu do tego punktu końcowego.

Aby określić adres IP zarządzania, wykonaj wyszukiwanie DNS na nazwie FQDN wystąpienia zarządzanego: `mi-name.zone_id.database.windows.net`. Spowoduje to zwrócenie wpisu DNS, który jest taki `trx.region-a.worker.vnet.database.windows.net`sam. Następnie można wykonać wyszukiwanie DNS dla tej nazwy FQDN z usuniętym elementem ". VNET". Spowoduje to zwrócenie adresu IP zarządzania. 

Ten program PowerShell wykona wszystkie czynności, jeśli zamienisz \<nazwę FQDN\> z wpisem DNS w wystąpieniu zarządzanym: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Aby uzyskać więcej informacji na temat zarządzanych wystąpień i połączeń, zobacz [Azure SQL Database architektury łączności wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
