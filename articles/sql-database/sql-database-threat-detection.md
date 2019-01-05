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
ms.date: 01/03/2019
ms.openlocfilehash: 3f4a120e2aaf2925805bec26f721d5cfb4194bf1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041547"
---
# <a name="azure-sql-database-threat-detection-for-single-database"></a>Wykrywanie zagrożeń usługi Azure SQL Database dla pojedynczej bazy danych

Usługi Azure SQL [wykrywania zagrożeń](sql-database-threat-detection-overview.md) dla [bazy danych SQL](sql-database-technical-overview.md) pojedynczych baz danych, wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych. Wykrywanie zagrożeń można zidentyfikować **wstrzyknięcie kodu SQL potencjalne**, **dostęp z nietypowej lokalizacji lub danych Centrum**, **dostępu z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwe aplikacji**, i **siłowe poświadczenia SQL** — Zobacz więcej szczegółów w [alerty związane z wykrywaniem zagrożeń](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Możesz otrzymywać powiadomienia o wykrytych zagrożeniach, za pośrednictwem [wiadomości e-mail z powiadomieniami](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) lub [witryny Azure portal](sql-database-threat-detection.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) jest częścią [SQL Zaawansowana ochrona przed zagrożeniami](sql-advanced-threat-protection.md) oferty (ATP), która to ujednolicone pakiet dla zaawansowane funkcje zabezpieczeń programu SQL. Wykrywanie zagrożeń można uzyskać dostęp i zarządzane za pośrednictwem centralnej portal SQL ATP. Usługa wykrywania zagrożeń są naliczane opłaty 15$ / miesiąc na każdym serwerze logicznym z pierwszych 30 dni wolne od opłat.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Konfigurowanie wykrywania zagrożeń dla bazy danych w witrynie Azure portal

1. Uruchamianie witryny Azure portal pod [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do strony konfiguracji serwera usługi Azure SQL Database, które chcesz chronić. W ustawieniach zabezpieczeń wybierz **zaawansowanej ochrony przed zagrożeniami**.
3. Na **zaawansowanej ochrony przed zagrożeniami** strony konfiguracji:

   - Włącz zaawansowaną ochronę przed zagrożeniami na serwerze.
   - W **ustawień wykrywania zagrożeń**w **Wyślij alerty do** tekstu Podaj listę adresów e-mail, aby otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   ![Konfigurowanie wykrywania zagrożeń](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Konfigurowanie wykrywania zagrożeń przy użyciu programu PowerShell

Aby uzyskać przykładowy skrypt, zobacz [skonfigurować inspekcję i wykrywanie zagrożeń za pomocą programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [wykrywania zagrożeń](sql-database-threat-detection-overview.md).
- Dowiedz się więcej o [wykrywania zagrożeń w wystąpieniu zarządzanym](sql-database-managed-instance-threat-detection.md).  
- Dowiedz się więcej o [SQL zaawansowanej ochrony przed zagrożeniami](sql-advanced-threat-protection.md).
- Dowiedz się więcej o [inspekcji usługi Azure SQL Database](sql-database-auditing.md)
- Dowiedz się więcej o [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Aby uzyskać więcej informacji na temat cen, zobacz [stronę cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
