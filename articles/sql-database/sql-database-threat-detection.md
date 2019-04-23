---
title: Wykrywanie zagrożeń — usługa Azure SQL Database | Dokumentacja firmy Microsoft
description: Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych w jednej bazie danych lub elastycznej puli.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 60839fa06d8436d3d2202d1d97735c42c5768e9e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797213"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>Wykrywania zagrożeń usługi Azure SQL Database dla jednego lub pulami baz danych

[Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) dla pojedynczych i puli baz danych wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych. Wykrywanie zagrożeń można zidentyfikować **wstrzyknięcie kodu SQL potencjalne**, **dostęp z nietypowej lokalizacji lub danych Centrum**, **dostępu z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwe aplikacji**, i **siłowe poświadczenia SQL** — Zobacz więcej szczegółów w [alerty związane z wykrywaniem zagrożeń](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Możesz otrzymywać powiadomienia o wykrytych zagrożeniach, za pośrednictwem [wiadomości e-mail z powiadomieniami](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) lub [witryny Azure portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) jest częścią [zaawansowanych zabezpieczeń danych](sql-database-advanced-data-security.md) (ADS) oferty, która to ujednolicone pakiet dla zaawansowane funkcje zabezpieczeń programu SQL. Wykrywanie zagrożeń można uzyskać dostęp i zarządzane za pośrednictwem portalu centralnej REKLAM SQL. Pakiet zabezpieczeń zaawansowanych danych są naliczane opłaty 15$ / miesiąc na każdym serwerze logicznym z pierwszych 30 dni wolne od opłat.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Konfigurowanie wykrywania zagrożeń dla bazy danych w witrynie Azure portal

1. Uruchamianie witryny Azure portal pod [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do strony konfiguracji serwera usługi Azure SQL Database, które chcesz chronić. W ustawieniach zabezpieczeń wybierz **zaawansowanych danych**.
3. Na **zaawansowanych danych** strony konfiguracji:

   - Włącz zabezpieczenia zaawansowanych danych na serwerze.
   - W **ustawień wykrywania zagrożeń**w **Wyślij alerty do** tekstu Podaj listę adresów e-mail, aby otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   ![Konfigurowanie wykrywania zagrożeń](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Konfigurowanie wykrywania zagrożeń przy użyciu programu PowerShell

Aby uzyskać przykładowy skrypt, zobacz [skonfigurować inspekcję i wykrywanie zagrożeń za pomocą programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [wykrywanie zagrożeń](sql-database-threat-detection-overview.md).
- Dowiedz się więcej o [wykrywanie zagrożeń w wystąpieniu zarządzanym](sql-database-managed-instance-threat-detection.md).  
- Dowiedz się więcej o [zaawansowanych zabezpieczeń danych](sql-database-advanced-data-security.md).
- Dowiedz się więcej o [inspekcji](sql-database-auditing.md)
- Dowiedz się więcej o [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Aby uzyskać więcej informacji na temat cen, zobacz [stronę cennika usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
