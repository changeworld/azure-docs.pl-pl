---
title: Konfigurowanie wykrywania zagrożeń — wystąpienie zarządzane usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych w wystąpieniu zarządzanym.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: f87b7093a732639db8a53ebec68fcae8f29735f3
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158673"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Konfigurowanie wykrywania zagrożeń (wersja zapoznawcza) w wystąpieniu zarządzanym bazy danych Azure SQL

Usługi Azure SQL [wykrywania zagrożeń](sql-database-threat-detection-overview.md) dla [wystąpienie zarządzane usługi SQL Database](sql-database-managed-instance-index.yml) wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych. Wykrywanie zagrożeń można zidentyfikować **wstrzyknięcie kodu SQL potencjalne**, **dostęp z nietypowej lokalizacji lub danych Centrum**, **dostępu z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwe aplikacji**, i **siłowe poświadczenia SQL** — Zobacz więcej szczegółów w [alerty związane z wykrywaniem zagrożeń](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Możesz otrzymywać powiadomienia o wykrytych zagrożeniach, za pośrednictwem [wiadomości e-mail z powiadomieniami](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) lub [witryny Azure portal](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) jest częścią [SQL Zaawansowana ochrona przed zagrożeniami](sql-advanced-threat-protection.md) oferty (ATP), która to ujednolicone pakiet dla zaawansowane funkcje zabezpieczeń programu SQL. Wykrywanie zagrożeń można uzyskać dostęp i zarządzane za pośrednictwem centralnej portal SQL ATP. Usługa wykrywania zagrożeń są naliczane opłaty 15$ / miesiąc na wystąpieniu zarządzanym przy użyciu ciągu pierwszych 30 dni wolne od opłat.

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

- Dowiedz się więcej o [wykrywania zagrożeń](sql-database-threat-detection-overview.md).
- Dowiedz się więcej o wystąpieniu zarządzanym, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md).
- Dowiedz się więcej o [wykrywania zagrożeń dla pojedynczej bazy danych](sql-database-threat-detection.md).
- Dowiedz się więcej o [inspekcji wystąpienie zarządzane](https://go.microsoft.com/fwlink/?linkid=869430).
- Dowiedz się więcej o [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
