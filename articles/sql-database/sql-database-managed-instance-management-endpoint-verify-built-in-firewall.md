---
title: Odkryj wbudowanej zapory wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprawdzić ochronę za pomocą wbudowanych zapory w wystąpieniu zarządzanym usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab
manager: digimobile
origin.date: 12/04/2018
ms.date: 12/31/2018
ms.openlocfilehash: 774455a2901782ef52b213c6a13c17636e28b1a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699654"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Weryfikowanie wbudowanej zapory wystąpienia zarządzanego

Wystąpienie zarządzane [reguły zabezpieczeń ruchu przychodzącego obowiązkowe](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) wymaga portów zarządzania 9000, 9003, 1438, 1440, 1452 być otwarte z **dowolnego źródła** na grupy zabezpieczeń sieci (NSG), chroniące zarządzane Wystąpienie. Mimo że te porty zostały otwarte na poziomie sieciowych grup zabezpieczeń, są chronione na poziomie sieci przez zapory wbudowanych.

## <a name="verify-firewall"></a>Sprawdź zapory

Aby sprawdzić, czy te porty, należy użyć dowolnego narzędzia skanera zabezpieczeń do przetestowania tych portów. Poniższy zrzut ekranu pokazuje, jak użyć jednej z tych narzędzi.

![Weryfikowanie wbudowanej zapory](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wystąpienia zarządzane przez usługę i łączność, zobacz [architektura łączności usługi Azure SQL bazy danych zarządzane wystąpienia](sql-database-managed-instance-connectivity-architecture.md).