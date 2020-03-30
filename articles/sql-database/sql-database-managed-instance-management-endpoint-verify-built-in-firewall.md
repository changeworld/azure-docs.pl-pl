---
title: Odnajduj wbudowaną zaporę wystąpienia zarządzanego
description: Dowiedz się, jak zweryfikować wbudowaną ochronę zapory w wystąpieniu zarządzanym usługi Azure SQL Database.
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
ms.openlocfilehash: 555ef56aafa37a1e1d384f945b04f9237adc5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821802"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Weryfikowanie wbudowanej zapory wystąpienia zarządzanego

[Obowiązkowe reguły zabezpieczeń przychodzących wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) wymagają otwarcia portów zarządzania 9000, 9003, 1438, 1440, 1452 z **dowolnego źródła** w sieciowej grupie zabezpieczeń (NSG), która chroni zarządzane wystąpienie. Mimo że te porty są otwarte na poziomie sieciowej grup y sieciowej, są chronione na poziomie sieci przez wbudowaną zaporę.

## <a name="verify-firewall"></a>Sprawdź zaporę

Aby zweryfikować te porty, użyj dowolnego narzędzia skanera zabezpieczeń, aby przetestować te porty. Poniższy zrzut ekranu pokazuje, jak korzystać z jednego z tych narzędzi.

![Weryfikowanie wbudowanej zapory](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wystąpień zarządzanych i łączności, zobacz [Architektura łączności wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-connectivity-architecture.md).