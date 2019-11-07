---
title: Skonfiguruj zaawansowaną ochronę przed zagrożeniami — Azure SQL Database wystąpienia zarządzanego
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych w wystąpieniu zarządzanym.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 8e419031c34a693bac7ab9f9825c24576e80ba14
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687885"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami w Azure SQL Database wystąpieniu zarządzanym

[Zaawansowana ochrona przed zagrożeniami](sql-database-threat-detection-overview.md) dla [wystąpienia zarządzanego](sql-database-managed-instance-index.yml) wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Zaawansowana ochrona przed zagrożeniami może identyfikować **potencjalne iniekcje SQL**, **uzyskiwać dostęp z nietypowej lokalizacji lub centrum danych**, **uzyskiwać dostęp z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwej aplikacji**oraz **wymuszać poświadczenia SQL** — Zobacz Więcej szczegółów na temat [alertów dotyczących zaawansowanej ochrony przed zagrożeniami](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Powiadomienia dotyczące wykrytych zagrożeń można odbierać za pośrednictwem [powiadomień e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) lub [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Zaawansowana ochrona przed zagrożeniami](sql-database-threat-detection-overview.md) jest częścią oferty [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń SQL. Dostęp do zaawansowanej ochrony przed zagrożeniami można uzyskać i zarządzać nimi za pośrednictwem centralnego portalu SQL ADS.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami w Azure Portal

1. Uruchom Azure Portal w [https://portal.azure.com](https://portal.azure.com).
2. Przejdź do strony konfiguracji wystąpienia zarządzanego, które chcesz chronić. Na stronie **Ustawienia** wybierz pozycję **Zaawansowane zabezpieczenia danych**.
3. Na stronie Konfiguracja zabezpieczeń danych zaawansowanych
   - Włącz **Zaawansowane** zabezpieczenia danych.
   - Skonfiguruj **listę wiadomości e-mail** , aby otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
   - Wybierz **konto usługi Azure Storage** , w którym są zapisywane rekordy nietypowych zagrożeń inspekcji.
   - Wybierz **typy zaawansowanej ochrony przed zagrożeniami** , które chcesz skonfigurować. Dowiedz się więcej o [alertach dotyczących zaawansowanej ochrony przed zagrożeniami](sql-database-threat-detection-overview.md).
4. Kliknij przycisk **Zapisz** , aby zapisać nowe lub zaktualizowane zasady zaawansowanej ochrony danych.

   ![Zaawansowana ochrona przed zagrożeniami](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Ceny na zrzutach ekranu nie zawsze odzwierciedlają bieżącą cenę i są przykładem.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [zaawansowanej ochrony przed zagrożeniami](sql-database-threat-detection-overview.md).
- Więcej informacji o wystąpieniach zarządzanych można znaleźć w temacie [co to jest wystąpienie zarządzane](sql-database-managed-instance.md).
- Dowiedz się więcej [na temat zaawansowanej ochrony przed zagrożeniami dla pojedynczej bazy danych](sql-database-threat-detection.md).
- Dowiedz się więcej na temat [inspekcji wystąpienia zarządzanego](https://go.microsoft.com/fwlink/?linkid=869430).
- Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
