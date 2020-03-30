---
title: Niestandardowy system DNS wystąpienia zarządzanego
description: W tym temacie opisano opcje konfiguracji niestandardowego systemu DNS z wystąpieniem zarządzanym bazy danych SQL azure.
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
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247082"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurowanie niestandardowego systemu nazw domen dla wystąpienia zarządzanego usługi Azure SQL Database

Wystąpienie zarządzanego bazy danych SQL platformy Azure musi zostać wdrożone w [sieci wirtualnej platformy](../virtual-network/virtual-networks-overview.md)Azure . Istnieje kilka scenariuszy (na przykład poczta db, połączone serwery z innymi wystąpieniami SQL w chmurze lub środowisku hybrydowym), które wymagają, aby nazwy hostów prywatnych były rozpoznawane z wystąpienia zarządzanego. W takim przypadku należy skonfigurować niestandardowy system DNS na platformie Azure. 

Ponieważ wystąpienie zarządzane używa tego samego systemu DNS do swoich wewnętrznych działań, skonfiguruj niestandardowy serwer DNS, aby mógł rozpoznać nazwy domen publicznych.

> [!IMPORTANT]
> Zawsze używaj w pełni kwalifikowanej nazwy domeny (FQDN) dla serwera poczty, wystąpienia programu SQL Server i innych usług, nawet jeśli znajdują się one w prywatnej strefie DNS. Na przykład `smtp.contoso.com` użyj dla serwera `smtp` poczty, ponieważ nie zostanie poprawnie rozpoznany. Utworzenie połączonego serwera lub replikacji, która odwołuje się do maszyn wirtualnych SQL w tej samej sieci wirtualnej, wymaga również nazwy FQDN i domyślnego sufiksu DNS. Na przykład `SQLVM.internal.cloudapp.net`. Aby uzyskać więcej informacji, zobacz [Rozpoznawanie nazw, które używa własnego serwera DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Aktualizowanie serwerów DNS w sieci wirtualnej nie wpłynie natychmiast na wystąpienie zarządzane. Konfiguracja DNS wystąpienia zarządzanego jest aktualizowana po wygaśnięciu dzierżawy DHCP lub po uaktualnieniu platformy, w zależności od tego, co nastąpi wcześniej. **Użytkownicy powinni ustawić konfigurację DNS sieci wirtualnej przed utworzeniem pierwszego wystąpienia zarządzanego.**

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie, zobacz [Co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Aby zapoznać się z samouczkiem przedstawiającym sposób tworzenia nowego wystąpienia [zarządzanego,](sql-database-managed-instance-get-started.md)zobacz Tworzenie wystąpienia zarządzanego .
- Aby uzyskać informacje dotyczące konfigurowania sieci wirtualnej dla wystąpienia zarządzanego, zobacz [Konfiguracja sieci wirtualnej dla wystąpień zarządzanych](sql-database-managed-instance-connectivity-architecture.md)
