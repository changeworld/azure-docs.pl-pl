---
title: Niestandardowa usługa DNS Azure SQL Database wystąpienia zarządzanego
description: W tym temacie opisano opcje konfiguracji niestandardowego systemu DNS z wystąpieniem zarządzanym Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: b44c2b1fdf27f285868c5681c38fe3ffa6e5e86d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688154"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurowanie niestandardowego serwera DNS dla Azure SQL Database wystąpienia zarządzanego

Wystąpienie zarządzane Azure SQL Database musi być wdrożone w ramach [sieci wirtualnej](../virtual-network/virtual-networks-overview.md)platformy Azure. Istnieje kilka scenariuszy (na przykład poczta DB, połączone serwery do innych wystąpień SQL w środowisku chmury lub hybrydowej), które wymagają rozpoznawania prywatnych nazw hostów z wystąpienia zarządzanego. W takim przypadku należy skonfigurować niestandardowy serwer DNS na platformie Azure. 

Ponieważ wystąpienie zarządzane używa tego samego systemu DNS do obsługi wewnętrznych elementów roboczych, należy skonfigurować niestandardowy serwer DNS, aby można było rozpoznać nazwy domen publicznych.

> [!IMPORTANT]
> Zawsze używaj w pełni kwalifikowanej nazwy domeny (FQDN) dla serwera poczty, wystąpienia SQL Server i innych usług, nawet jeśli znajdują się w prywatnej strefie DNS. Na przykład użyj `smtp.contoso.com` dla serwera poczty e-mail, ponieważ `smtp` nie rozwiąże się poprawnie. Tworzenie połączonego serwera lub replikacji odwołującej się do maszyn wirtualnych SQL w tej samej sieci wirtualnej wymaga również nazwy FQDN i domyślnego sufiksu DNS. Na przykład `SQLVM.internal.cloudapp.net`. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw używające własnego serwera DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Aktualizowanie serwerów DNS sieci wirtualnej nie będzie miało wpływu na wystąpienie zarządzane. Konfiguracja usługi DNS wystąpienia zarządzanego jest aktualizowana po wygaśnięciu dzierżawy DHCP lub po upgarade platformy, w zależności od tego, co nastąpi wcześniej. **Przed utworzeniem pierwszego wystąpienia zarządzanego użytkownicy są zalecani do ustawienia konfiguracji DNS sieci wirtualnej.**

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Samouczek pokazujący, jak utworzyć nowe wystąpienie zarządzane, można znaleźć w temacie [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej dla wystąpienia zarządzanego, zobacz [Konfiguracja sieci wirtualnej dla wystąpień zarządzanych](sql-database-managed-instance-connectivity-architecture.md)
