---
title: Odnajdywanie punktu końcowego zarządzania wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać wystąpienia zarządzanego Azure SQL Database management punktu końcowego publicznego adresu IP i sprawdź jej ochronę za pomocą wbudowanych zapory
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 542db0200229b4fea4ac67ad19c468f2b9dc67d1
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772783"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Określić adres IP punktu końcowego zarządzania

Klaster wirtualny wystąpienia zarządzanego Azure SQL Database zawiera punkt końcowy zarządzania, które firma Microsoft korzysta z operacji zarządzania. Punkt końcowy zarządzania jest chroniony za pomocą wbudowanych zapory weryfikację certyfikatu poziomu i wzajemne sieci na poziomie aplikacji. Można określić adres IP punktu końcowego zarządzania, ale nie masz dostępu do tego punktu końcowego.

## <a name="determine-ip-address"></a>Określić adres IP

Załóżmy, że to wystąpienie zarządzane usługi hosta `mi-demo.xxxxxx.database.windows.net`. Uruchom `nslookup` przy użyciu nazwy hosta.

![Rozpoznawanie nazwy hosta wewnętrznego](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Teraz zrobić innego `nslookup` usuwania nazwy wyróżnione `.vnet.` segmentu. Po wykonaniu tego polecenia, omijają publiczny adres IP.

![Rozpoznawanie publiczny adres IP](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wystąpienia zarządzane przez usługę i łączność, zobacz [architektura łączności usługi Azure SQL bazy danych zarządzane wystąpienia](sql-database-managed-instance-connectivity-architecture.md).
