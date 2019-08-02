---
title: Konfigurowanie wykrywania zagrożeń-Azure SQL Database wystąpienia zarządzanego | Microsoft Docs
description: Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych w wystąpieniu zarządzanym.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 02/04/2019
ms.openlocfilehash: 6d1f2a9547f01de91a8e7739d827a91154842d7b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567302"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Skonfiguruj wykrywanie zagrożeń (wersja zapoznawcza) w Azure SQL Database wystąpieniu zarządzanym

[Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) dla [wystąpienia zarządzanego](sql-database-managed-instance-index.yml) wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Wykrywanie zagrożeń może identyfikować **potencjalne iniekcje SQL**, **uzyskiwać dostęp z nietypowej lokalizacji lub centrum danych**, **uzyskiwać dostęp z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwej aplikacji**oraz wymuszać **poświadczenia SQL** — Zobacz więcej szczegółów [alerty dotyczące wykrywania zagrożeń](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Powiadomienia dotyczące wykrytych zagrożeń można odbierać za pośrednictwem [powiadomień e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) lub [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) jest częścią oferty [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), która stanowi ujednolicony pakiet zaawansowanych funkcji zabezpieczeń SQL. Do wykrywania zagrożeń można uzyskiwać dostęp i zarządzać nimi za pośrednictwem centralnego portalu SQL ADS. Usługa wykrywania zagrożeń opłata jest naliczana 15 USD/miesiąc na wystąpienie zarządzane, a pierwsze 30 dni bezpłatnie.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Skonfiguruj wykrywanie zagrożeń dla wystąpienia zarządzanego w Azure Portal

1. Uruchom Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Przejdź do strony konfiguracji wystąpienia zarządzanego, które chcesz chronić. Na stronie **Ustawienia** wybierz pozycję **wykrywanie zagrożeń**.
3. Na stronie Konfiguracja wykrywania zagrożeń
   - Włącz **wykrywanie** zagrożeń.
   - Skonfiguruj **listę wiadomości e-mail** , aby otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
   - Wybierz **konto usługi Azure Storage** , w którym są zapisywane rekordy nietypowych zagrożeń inspekcji.
4. Kliknij przycisk **Zapisz** , aby zapisać nowe lub zaktualizowane zasady wykrywania zagrożeń.

   ![wykrywanie zagrożeń](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wykrywaniu zagrożeń](sql-database-threat-detection-overview.md).
- Więcej informacji o wystąpieniach zarządzanych można znaleźć w temacie [co to jest wystąpienie zarządzane](sql-database-managed-instance.md).
- Dowiedz się więcej [na temat wykrywania zagrożeń dla pojedynczej bazy danych](sql-database-threat-detection.md).
- Dowiedz się więcej na temat [inspekcji wystąpienia zarządzanego](https://go.microsoft.com/fwlink/?linkid=869430).
- Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
