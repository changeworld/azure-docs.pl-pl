---
title: Konfigurowanie zaawansowanej ochrony przed zagrożeniami
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące potencjalne zagrożenia bezpieczeństwa bazy danych w jednej bazie danych lub puli elastycznej.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822506"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Zaawansowana ochrona przed zagrożeniami w usłudze Azure SQL Database dla pojedynczych lub pulowanych baz danych

[Zaawansowana ochrona przed zagrożeniami](sql-database-threat-detection-overview.md) dla pojedynczych i pulowanych baz danych wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Zaawansowana ochrona przed zagrożeniami może identyfikować **potencjalne iniekcje SQL,** **dostęp z nietypowej lokalizacji lub centrum danych,** **dostęp z nieznanej głównej lub potencjalnie szkodliwej aplikacji,** a **poświadczenia Brute force SQL** — więcej szczegółów znajdziesz w [alertach zaawansowanej ochrony przed zagrożeniami.](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)

Powiadomienia o wykrytych zagrożeniach można otrzymywać za pośrednictwem [powiadomień e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) lub [witryny Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Zaawansowana ochrona przed zagrożeniami](sql-database-threat-detection-overview.md) jest częścią oferty [zaawansowanego bezpieczeństwa danych](sql-database-advanced-data-security.md) (ADS), która jest ujednoliconym pakietem dla zaawansowanych funkcji zabezpieczeń SQL. Aby uzyskiwać dostęp do funkcji Advanced Threat Protection i zarządzać nią, można korzystać centralnego portalu pakietu SQL ADS.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Konfigurowanie zaawansowanej ochrony przed zagrożeniami w witrynie Azure portal

1. Uruchom portal Platformy [https://portal.azure.com](https://portal.azure.com)Azure pod adresem .
2. Przejdź do strony konfiguracji serwera bazy danych SQL azure, który chcesz chronić. W ustawieniach zabezpieczeń wybierz pozycję **Zaawansowane zabezpieczenia danych**.
3. Na stronie konfiguracji **Zaawansowane zabezpieczenia danych:**

   - Włącz zaawansowane zabezpieczenia danych na serwerze.
   - W **obszarze Zaawansowane ustawienia ochrony przed zagrożeniami**w polu tekstowym Wyślij **alerty do** podaj listę wiadomości e-mail, które będą otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   ![Konfigurowanie zaawansowanej ochrony przed zagrożeniami](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Ceny na zrzutach ekranu nie zawsze odzwierciedlają aktualną cenę i są przykładem.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Konfigurowanie usługi Advanced Threat Protection przy użyciu programu PowerShell

Przykładowy skrypt można znaleźć w temacie [Konfigurowanie inspekcji i zaawansowanej ochrony przed zagrożeniami przy użyciu programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zaawansowanej ochronie przed zagrożeniami](sql-database-threat-detection-overview.md).
- Dowiedz się więcej o [zaawansowanej ochronie przed zagrożeniami w wystąpieniu zarządzanym](sql-database-managed-instance-threat-detection.md).  
- Dowiedz się więcej o [zaawansowanym bezpieczeństwie danych](sql-database-advanced-data-security.md).
- Dowiedz się więcej o [inspekcji](sql-database-auditing.md)
- Dowiedz się więcej o [Centrum zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Aby uzyskać więcej informacji na temat cen, zobacz [stronę cennik bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
