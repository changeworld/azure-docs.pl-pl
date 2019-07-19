---
title: Azure SQL Database niestandardowego serwera DNS wystąpienia zarządzanego | Microsoft Docs
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
manager: craigg
ms.date: 07/17/2019
ms.openlocfilehash: 674c5d48dad5d3cfd138853d7ea38ae4a216c93d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309860"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurowanie niestandardowego serwera DNS dla Azure SQL Database wystąpienia zarządzanego

Wystąpienie zarządzane Azure SQL Database musi być wdrożone w ramach [sieci wirtualnej](../virtual-network/virtual-networks-overview.md)platformy Azure. Istnieje kilka scenariuszy (na przykład poczta DB, połączone serwery do innych wystąpień SQL w środowisku chmury lub hybrydowej), które wymagają rozpoznawania prywatnych nazw hostów z wystąpienia zarządzanego. W takim przypadku należy skonfigurować niestandardowy serwer DNS na platformie Azure. 

Ponieważ wystąpienie zarządzane używa tego samego systemu DNS do obsługi wewnętrznych czynności, należy skonfigurować niestandardowy serwer DNS, aby można było rozpoznać nazwy domen publicznych.

   > [!IMPORTANT]
   > Zawsze używaj w pełni kwalifikowanych nazw domen (FQDN) dla serwerów poczty, serwerów SQL i innych usług, nawet jeśli znajdują się w prywatnej strefie DNS. Na przykład użycie `smtp.contoso.com` dla serwera poczty, ponieważ `smtp` proste nie zostanie prawidłowo rozwiązane.

   > [!IMPORTANT]
   > Aktualizacja serwerów DNS sieci wirtualnej nie ma natychmiastowego wpływu na wystąpienie zarządzane. Konfiguracja usługi DNS wystąpienie zarządzane zostanie zaktualizowana po wygaśnięciu dzierżawy DHCP lub po upgarade platformy, niezależnie od tego, co nastąpi wcześniej. **Przed utworzeniem pierwszego wystąpienia zarządzanego użytkownicy są zalecani do ustawienia konfiguracji DNS sieci wirtualnej.**

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Samouczek pokazujący, jak utworzyć nowe wystąpienie zarządzane, można znaleźć w temacie [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej dla wystąpienia zarządzanego, zobacz [Konfiguracja sieci wirtualnej dla wystąpień zarządzanych](sql-database-managed-instance-connectivity-architecture.md)
