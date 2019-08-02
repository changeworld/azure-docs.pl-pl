---
title: Wykrywanie zagrożeń — Azure SQL Database | Microsoft Docs
description: Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych w pojedynczej bazie danych lub puli elastycznej.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 5549d016978e8bf9491c3745e335e3c4c793212c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566337"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>Azure SQL Database wykrywanie zagrożeń dla baz danych z pojedynczym lub pulą

[Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) dla baz danych o pojedynczej i puli wykrywa nietypowe działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Wykrywanie zagrożeń może identyfikować **potencjalne iniekcje SQL**, **uzyskiwać dostęp z nietypowej lokalizacji lub centrum danych**, **uzyskiwać dostęp z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwej aplikacji**oraz wymuszać **poświadczenia SQL** — Zobacz więcej szczegółów [alerty dotyczące wykrywania zagrożeń](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Powiadomienia dotyczące wykrytych zagrożeń można odbierać za pośrednictwem [powiadomień e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) lub [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) jest częścią oferty [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), która stanowi ujednolicony pakiet zaawansowanych funkcji zabezpieczeń SQL. Do wykrywania zagrożeń można uzyskiwać dostęp i zarządzać nimi za pośrednictwem centralnego portalu SQL ADS. Pakiet Advanced Data Security jest naliczany 15 USD/miesiąc na serwer logiczny, a pierwsze 30 dni bezpłatnie.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Skonfiguruj wykrywanie zagrożeń dla bazy danych w Azure Portal

1. Uruchom Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Przejdź do strony konfiguracji serwera Azure SQL Database, który ma być chroniony. W obszarze Ustawienia zabezpieczeń wybierz pozycję **Zaawansowane zabezpieczenia danych**.
3. Na stronie Konfiguracja **zabezpieczeń danych zaawansowanych** :

   - Włącz zaawansowane zabezpieczenia danych na serwerze.
   - W obszarze **Ustawienia wykrywania zagrożeń**w polu tekstowym **Wyślij alerty do** wprowadź listę wiadomości e-mail, które mają otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   ![Konfigurowanie wykrywania zagrożeń](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Konfigurowanie wykrywania zagrożeń przy użyciu programu PowerShell

Aby zapoznać się z przykładem skryptu, zobacz [Konfigurowanie inspekcji i wykrywania zagrożeń przy użyciu programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wykrywaniu zagrożeń](sql-database-threat-detection-overview.md).
- Dowiedz się więcej o [wykrywaniu zagrożeń w wystąpieniu zarządzanym](sql-database-managed-instance-threat-detection.md).  
- Dowiedz się więcej o [zaawansowanych zabezpieczeniach danych](sql-database-advanced-data-security.md).
- Dowiedz się więcej na temat [inspekcji](sql-database-auditing.md)
- Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
