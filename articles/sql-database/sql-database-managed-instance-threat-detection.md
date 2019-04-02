---
title: Konfigurowanie wykrywania zagrożeń — usługa Azure SQL Database, zarządzanego wystąpienia | Dokumentacja firmy Microsoft
description: Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych w wystąpieniu zarządzanym.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: a8e9dfe70e300e6b1d0d50aae60660644f2ab31d
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793054"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Konfigurowanie wykrywania zagrożeń (wersja zapoznawcza) w usłudze Azure SQL Database, wystąpienia zarządzanego

[Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) dla [wystąpienia zarządzanego](sql-database-managed-instance-index.yml) wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych. Wykrywanie zagrożeń można zidentyfikować **wstrzyknięcie kodu SQL potencjalne**, **dostęp z nietypowej lokalizacji lub danych Centrum**, **dostępu z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwe aplikacji**, i **siłowe poświadczenia SQL** — Zobacz więcej szczegółów w [alerty związane z wykrywaniem zagrożeń](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Możesz otrzymywać powiadomienia o wykrytych zagrożeniach, za pośrednictwem [wiadomości e-mail z powiadomieniami](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) lub [witryny Azure portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) jest częścią [zaawansowanych zabezpieczeń danych](sql-database-advanced-data-security.md) (ADS) oferty, która to ujednolicone pakiet dla zaawansowane funkcje zabezpieczeń programu SQL. Wykrywanie zagrożeń można uzyskać dostęp i zarządzane za pośrednictwem portalu centralnej REKLAM SQL. Usługa wykrywania zagrożeń są naliczane opłaty 15$ / miesiąc na wystąpienie zarządzane przy użyciu ciągu pierwszych 30 dni wolne od opłat.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Konfigurowanie wykrywania zagrożeń wystąpienia zarządzanego w witrynie Azure portal

1. Uruchamianie witryny Azure portal pod [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do strony konfiguracji wystąpienia zarządzanego, który chcesz chronić. W **ustawienia** wybierz opcję **wykrywania zagrożeń**.
3. Na stronie konfiguracji wykrywania zagrożeń
   - Włącz **ON** wykrywanie zagrożeń.
   - Konfigurowanie **listę adresów e-mail** otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
   - Wybierz **konta usługi Azure storage** gdzie nietypowe zagrożeń rekordów inspekcji są zapisywane.
4. Kliknij przycisk **Zapisz** można zapisać zasad wykrywania nowych lub zaktualizowanych przed zagrożeniami.

   ![Wykrywanie zagrożeń](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [wykrywanie zagrożeń](sql-database-threat-detection-overview.md).
- Dowiedz się więcej o wystąpieniach zarządzanych, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md).
- Dowiedz się więcej o [zagrożeń wykrywania dla pojedynczej bazy danych](sql-database-threat-detection.md).
- Dowiedz się więcej o [zarządzane wystąpienia inspekcji](https://go.microsoft.com/fwlink/?linkid=869430).
- Dowiedz się więcej o [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
