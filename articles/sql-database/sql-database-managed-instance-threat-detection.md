---
title: Konfigurowanie zaawansowanej ochrony przed zagrożeniami — wystąpienie zarządzane
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące potencjalne zagrożenia bezpieczeństwa dla bazy danych w wystąpieniu zarządzanym.
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
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822553"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Konfigurowanie zaawansowanej ochrony przed zagrożeniami w wystąpieniu zarządzanym usługi Azure SQL Database

[Zaawansowana ochrona przed zagrożeniami](sql-database-threat-detection-overview.md) dla [wystąpienia zarządzanego](sql-database-managed-instance-index.yml) wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Zaawansowana ochrona przed zagrożeniami może identyfikować **potencjalne iniekcje SQL,** **dostęp z nietypowej lokalizacji lub centrum danych,** **dostęp z nieznanej głównej lub potencjalnie szkodliwej aplikacji,** a **poświadczenia Brute force SQL** — więcej szczegółów znajdziesz w [alertach zaawansowanej ochrony przed zagrożeniami.](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)

Powiadomienia o wykrytych zagrożeniach można otrzymywać za pośrednictwem [powiadomień e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) lub [witryny Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Zaawansowana ochrona przed zagrożeniami](sql-database-threat-detection-overview.md) jest częścią oferty [zaawansowanego bezpieczeństwa danych](sql-database-advanced-data-security.md) (ADS), która jest ujednoliconym pakietem dla zaawansowanych funkcji zabezpieczeń SQL. Aby uzyskiwać dostęp do funkcji Advanced Threat Protection i zarządzać nią, można korzystać centralnego portalu pakietu SQL ADS.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Konfigurowanie zaawansowanej ochrony przed zagrożeniami w witrynie Azure portal

1. Uruchom portal Platformy [https://portal.azure.com](https://portal.azure.com)Azure pod adresem .
2. Przejdź do strony konfiguracji wystąpienia zarządzanego, które chcesz chronić. Na stronie **Ustawienia** wybierz pozycję **Zaawansowane zabezpieczenia danych**.
3. Na stronie konfiguracji Zaawansowane zabezpieczenia danych
   - Włącz **zaawansowane** zabezpieczenia danych.
   - Skonfiguruj **listę wiadomości e-mail,** aby otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
   - Wybierz **konto magazynu platformy Azure,** na którym są zapisywane nietypowe rekordy inspekcji zagrożeń.
   - Wybierz **typy zaawansowanej ochrony przed zagrożeniami,** które chcesz skonfigurować. Dowiedz się więcej o [alertach zaawansowanej ochrony przed zagrożeniami](sql-database-threat-detection-overview.md).
4. Kliknij **przycisk Zapisz,** aby zapisać nową lub zaktualizowaną zasadę zaawansowanego zabezpieczenia danych.

   ![Zaawansowana ochrona przed zagrożeniami](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Ceny na zrzutach ekranu nie zawsze odzwierciedlają aktualną cenę i są przykładem.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zaawansowanej ochronie przed zagrożeniami](sql-database-threat-detection-overview.md).
- Dowiedz się więcej o wystąpieniach zarządzanych, zobacz [Co to jest wystąpienie zarządzane](sql-database-managed-instance.md).
- Dowiedz się więcej o [zaawansowanej ochronie przed zagrożeniami dla pojedynczej bazy danych](sql-database-threat-detection.md).
- Dowiedz się więcej o [inspekcji wystąpienia zarządzanego](https://go.microsoft.com/fwlink/?linkid=869430).
- Dowiedz się więcej o [Centrum zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
