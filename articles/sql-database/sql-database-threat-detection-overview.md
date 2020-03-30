---
title: Zaawansowana ochrona przed zagrożeniami
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące potencjalne zagrożenia bezpieczeństwa w bazie danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 17ca8cbb7a55e9c0d44af099f4884f71b1cd457a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124771"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Usługa Advanced Threat Protection dla usługi Azure SQL Database

Zaawansowana ochrona przed zagrożeniami dla [usługi Azure SQL Database](sql-database-technical-overview.md) i usługi Azure [Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Zaawansowana ochrona przed zagrożeniami jest częścią oferty [Zaawansowane bezpieczeństwo danych](sql-database-advanced-data-security.md) (ADS), która jest ujednoliconym pakietem dla zaawansowanych funkcji zabezpieczeń SQL. Aby uzyskiwać dostęp do funkcji Advanced Threat Protection i zarządzać nią, można korzystać centralnego portalu pakietu SQL ADS.

> [!NOTE]
> W tym temacie stosuje się do serwera SQL platformy Azure oraz do bazy danych SQL i usługi Azure Synapse, które są tworzone na serwerze SQL platformy Azure. Dla uproszczenia bazy danych SQL jest używany podczas odwoływania się do bazy danych SQL i platformy Azure Synapse.

## <a name="what-is-advanced-threat-protection"></a>Co to jest zaawansowana ochrona przed zagrożeniami

 Zaawansowana ochrona przed zagrożeniami zapewnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie, gdy występują one, udostępniając alerty zabezpieczeń dotyczące nietypowych działań. Użytkownicy otrzymują alert o podejrzanych działaniach bazy danych, potencjalnych lukach i atakach iniekcji SQL, a także o nietypowych wzorcach dostępu do bazy danych i kwerend. Zaawansowana ochrona przed zagrożeniami integruje alerty z [usługą Azure Security Center](https://azure.microsoft.com/services/security-center/), które zawierają szczegółowe informacje o podejrzanej aktywności i zalecają działania dotyczące sposobu badania i łagodzenia zagrożenia. Zaawansowana ochrona przed zagrożeniami ułatwia rozwiązywanie potencjalnych zagrożeń bazy danych bez konieczności bycia ekspertem w dziedzinie zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania zabezpieczeń.

Aby uzyskać pełne środowisko badania, zaleca się włączenie [inspekcji bazy danych SQL](sql-database-auditing.md), która zapisuje zdarzenia bazy danych do dziennika inspekcji na koncie usługi Azure storage.  

## <a name="advanced-threat-protection-alerts"></a>Zaawansowane alerty ochrony przed zagrożeniami

Zaawansowana ochrona przed zagrożeniami dla usługi Azure SQL Database wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania i może wyzwolić następujące alerty:

- **Luka w zabezpieczeniach iniekcji SQL:** Ten alert jest wyzwalany, gdy aplikacja generuje wadliwą instrukcję SQL w bazie danych. który może oznaczać możliwe luki w zabezpieczeniach umożliwiające ataki przez iniekcję kodu SQL. Istnieją dwie możliwe przyczyny generowania błędnej instrukcji:

  - Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
  - Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
- **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany w przypadku aktywnego wykorzystania zidentyfikowanej luki umożliwiającej iniekcję SQL w aplikacji. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL, korzystając z podatnego kodu aplikacji lub procedur składowanych.
- **Dostęp z nietypowej lokalizacji**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nietypowego centrum danych platformy Azure**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL z nietypowego centrum danych platformy Azure, co zostało zaobserwowane na tym serwerze w ostatnim czasie. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja na platformie Azure, usługa Power BI, edytor zapytań SQL platformy Azure). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL za pomocą nietypowego podmiotu zabezpieczeń (użytkownika SQL). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Atak siłowy na poświadczenia SQL**: Ten alert jest wyzwalany w przypadku nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Eksploruj nietypowe działania bazy danych po wykryciu podejrzanego zdarzenia

Otrzymasz powiadomienie e-mail po wykryciu nietypowych działań bazy danych. Wiadomość e-mail zawiera informacje o podejrzanym zdarzeniu zabezpieczeń, w tym charakter nietypowych działań, nazwę bazy danych, nazwę serwera, nazwę aplikacji i czas zdarzenia. Ponadto wiadomość e-mail zawiera informacje na temat możliwych przyczyn i zalecane działania w celu zbadania i złagodzenia potencjalnego zagrożenia dla bazy danych.

![Nietypowy raport aktywności](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Kliknij **łącze Wyświetl ostatnie alerty SQL** w wiadomości e-mail, aby uruchomić witrynę Azure portal i wyświetlić stronę alertów usługi Azure Security Center, która zawiera omówienie aktywnych zagrożeń wykrytych w bazie danych SQL.

   ![Zagrożenia aktywnością](./media/sql-database-threat-detection/active_threats.png)

2. Kliknij określony alert, aby uzyskać dodatkowe szczegóły i działania dotyczące badania tego zagrożenia i korygowania przyszłych zagrożeń.

   Na przykład iniekcji SQL jest jednym z najczęstszych problemów z zabezpieczeniami aplikacji sieci Web w Internecie, który jest używany do ataku na aplikacje oparte na danych. Osoby atakujące wykorzystują luki w zabezpieczeniach aplikacji, aby wstrzyknąć złośliwe instrukcje SQL do pól wprowadzania aplikacji, naruszając lub modyfikując dane w bazie danych. W przypadku alertów iniekcji SQL szczegóły alertu obejmują instrukcję SQL, która została wykorzystana.

   ![Konkretny alert](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Eksploruj alerty zaawansowanej ochrony przed zagrożeniami dla bazy danych w witrynie Azure portal

Zaawansowana ochrona przed zagrożeniami integruje swoje alerty z [centrum zabezpieczeń platformy Azure](https://azure.microsoft.com/services/security-center/). Kafelki zaawansowanej ochrony przed zagrożeniami sql sql w bazie danych i bloków SQL ADS w portalu Azure śledzą stan aktywnych zagrożeń.

Kliknij pozycję **Alert zaawansowanej ochrony przed zagrożeniami,** aby uruchomić stronę alertów usługi Azure Security Center i uzyskać omówienie aktywnych zagrożeń SQL wykrytych w bazie danych.

   ![Alert zaawansowanej ochrony przed zagrożeniami](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Alert zaawansowanej ochrony przed zagrożeniami2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zaawansowanej ochronie przed zagrożeniami w pojedynczych i pulowanych bazach danych.](sql-database-threat-detection.md)
- Dowiedz się więcej o [zaawansowanej ochronie przed zagrożeniami w wystąpieniu zarządzanym](sql-database-managed-instance-threat-detection.md).
- Dowiedz się więcej o [zaawansowanym bezpieczeństwie danych](sql-database-advanced-data-security.md).
- Dowiedz się więcej o [inspekcji bazy danych SQL usługi Azure](sql-database-auditing.md)
- Dowiedz się więcej o [Centrum zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Aby uzyskać więcej informacji na temat cen, zobacz [stronę cennik bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
