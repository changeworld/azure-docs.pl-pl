---
title: Zaawansowana ochrona przed zagrożeniami
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 03/31/2019
ms.openlocfilehash: d09cc202c3f7e18358ec693d1cfe4bd2648811ba
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820697"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Usługa Advanced Threat Protection dla usługi Azure SQL Database

Zaawansowana ochrona przed zagrożeniami dla [Azure SQL Database](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Zaawansowana ochrona przed zagrożeniami jest częścią oferty [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń SQL. Dostęp do zaawansowanej ochrony przed zagrożeniami można uzyskać i zarządzać nimi za pośrednictwem centralnego portalu SQL ADS.

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

## <a name="what-is-advanced-threat-protection"></a>Co to jest zaawansowana ochrona przed zagrożeniami

 Zaawansowana ochrona przed zagrożeniami zapewnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku nietypowych działań. Użytkownicy otrzymują alerty o podejrzanych działaniach związanych z bazą danych, potencjalnych luk w zabezpieczeniach i atakach iniekcji SQL, a także o nietypowych wzorcach dostępu do bazy danych i zapytań. Zaawansowana ochrona przed zagrożeniami integruje alerty z [Azure Security Center](https://azure.microsoft.com/services/security-center/), które zawierają szczegółowe informacje o podejrzanych działaniach i zalecaną akcję dotyczącą badania i łagodzenia zagrożeń. Zaawansowana ochrona przed zagrożeniami pozwala łatwo rozwiązywać potencjalne zagrożenia dla bazy danych bez konieczności być specjalistą ds. zabezpieczeń ani zarządzać zaawansowanymi systemami monitorowania zabezpieczeń.

W celu pełnego badania zaleca się włączenie [inspekcji SQL Database](sql-database-auditing.md), która zapisuje zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage.  

## <a name="advanced-threat-protection-alerts"></a>Alerty zaawansowanej ochrony przed zagrożeniami

Zaawansowana ochrona przed zagrożeniami dla Azure SQL Database wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania i może wyzwolić następujące alerty:

- **Podatność na iniekcję SQL**: ten alert jest wyzwalany, gdy aplikacja generuje błędną instrukcję SQL w bazie danych. który może oznaczać możliwe luki w zabezpieczeniach umożliwiające ataki przez iniekcję kodu SQL. Istnieją dwie możliwe przyczyny generowania błędnej instrukcji:

  - Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
  - Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
- **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany w przypadku aktywnego wykorzystania zidentyfikowanej luki umożliwiającej iniekcję SQL w aplikacji. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL, korzystając z podatnego kodu aplikacji lub procedur składowanych.
- **Dostęp z nietypowej lokalizacji**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nietypowego centrum danych platformy Azure**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL z nietypowego centrum danych platformy Azure, co zostało zaobserwowane na tym serwerze w ostatnim czasie. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja na platformie Azure, usługa Power BI, edytor zapytań SQL platformy Azure). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL za pomocą nietypowego podmiotu zabezpieczeń (użytkownika SQL). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Atak siłowy na poświadczenia SQL**: Ten alert jest wyzwalany w przypadku nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Zbadaj nietypowe działania bazy danych po wykryciu podejrzanego zdarzenia

Po wykryciu nietypowych działań bazy danych otrzymasz powiadomienie e-mail. Wiadomość e-mail zawiera informacje dotyczące podejrzanego zdarzenia zabezpieczeń, w tym charakter nietypowych działań, nazwę bazy danych, nazwę serwera, nazwę aplikacji i czas zdarzenia. Ponadto wiadomość e-mail zawiera informacje o możliwych przyczynach i zalecanych działaniach związanych z badaniem i łagodzeniem potencjalnego zagrożenia dla bazy danych.

![Raport o nietypowych działaniach](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Kliknij link **Wyświetl Ostatnie alerty SQL** w wiadomości e-mail, aby uruchomić Azure Portal i wyświetlić stronę Azure Security Center alertów, która zawiera przegląd aktywnych zagrożeń wykrytych w bazie danych SQL.

   ![Zagrożenia aktywności](./media/sql-database-threat-detection/active_threats.png)

2. Kliknij konkretny alert, aby uzyskać dodatkowe szczegóły i akcje związane z badaniem tego zagrożenia i korygowaniem przyszłe zagrożenia.

   Na przykład iniekcja SQL to jeden z najczęstszych problemów z zabezpieczeniami aplikacji sieci Web w Internecie, który jest używany do ataku na aplikacje oparte na danych. Osoby atakujące wykorzystują luki w zabezpieczeniach, aby wstrzyknąć złośliwe instrukcje SQL do pól wprowadzania aplikacji, naruszania lub modyfikowania danych w bazie danych. W przypadku alertów o iniekcji SQL szczegóły alertu obejmują wykorzystaną przez lukę instrukcję SQL.

   ![Konkretny alert](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Zapoznaj się z alertami dotyczącymi zaawansowanej ochrony przed zagrożeniami dla bazy danych w Azure Portal

Zaawansowana ochrona przed zagrożeniami integruje swoje alerty z [usługą Azure Security Center](https://azure.microsoft.com/services/security-center/). Kafelki SQL Advanced Threat Protection na żywo w ramach bazy danych i bloki usługi SQL AD w Azure Portal śledzą stan aktywnych zagrożeń.

Kliknij **alert zaawansowanej ochrony przed zagrożeniami** , aby uruchomić stronę alerty Azure Security Center i zapoznać się z omówieniem aktywnych zagrożeń SQL wykrytych w bazie danych lub hurtowni danych.

   ![Alert dotyczący zaawansowanej ochrony przed zagrożeniami](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Zaawansowana ochrona przed zagrożeniami Alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat zaawansowanej ochrony przed zagrożeniami w ramach jednej i puli baz danych](sql-database-threat-detection.md).
- Dowiedz się więcej [na temat zaawansowanej ochrony przed zagrożeniami w wystąpieniu zarządzanym](sql-database-managed-instance-threat-detection.md).
- Dowiedz się więcej o [zaawansowanych zabezpieczeniach danych](sql-database-advanced-data-security.md).
- Dowiedz się więcej na temat [inspekcji Azure SQL Database](sql-database-auditing.md)
- Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
