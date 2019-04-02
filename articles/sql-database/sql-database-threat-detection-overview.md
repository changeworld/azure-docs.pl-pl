---
title: Zaawansowana ochrona przed zagrożeniami — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: 710a94c919f4262c3f572f28d03c79b77e658287
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793913"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Zaawansowana ochrona przed zagrożeniami dla bazy danych Azure SQL

Zaawansowana ochrona przed zagrożeniami dla [usługi Azure SQL Database](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych.

Zaawansowana ochrona przed zagrożeniami jest częścią [zaawansowanych zabezpieczeń danych](sql-database-advanced-data-security.md) (ADS) oferty, która to ujednolicone pakiet dla zaawansowane funkcje zabezpieczeń programu SQL. Zaawansowana ochrona przed zagrożeniami, można uzyskać dostępu do i zarządzane za pośrednictwem portalu centralnej REKLAM SQL.

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

## <a name="what-is-advanced-threat-protection"></a>Co to jest Zaawansowana ochrona przed zagrożeniami

 Zaawansowana ochrona przed zagrożeniami stanowi nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie i odpowiadanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku wystąpienia nietypowych działań. Użytkownicy otrzymują alert po podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach i wstrzykiwanie kodu SQL, ataki, a także dostęp do nietypowych bazy danych i wzorców zapytań. Zaawansowana ochrona przed zagrożeniami integruje alerty z usługą [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/), które zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia. Zaawansowana ochrona przed zagrożeniami upraszcza reagowanie na potencjalne zagrożenia bazy danych bez konieczności dysponowania eksperta zabezpieczeń lub zarządzania zabezpieczeniami zaawansowanymi systemami monitorowania.

Badanie pełne środowisko pracy, zalecane jest włączanie [inspekcji usługi SQL Database](sql-database-auditing.md), która zapisuje zdarzenia bazy danych inspekcji logowania na koncie magazynu platformy Azure.  

## <a name="advanced-threat-protection-alerts"></a>Alerty zaawansowanej ochrony przed zagrożeniami

Zaawansowana ochrona przed zagrożeniami dla bazy danych SQL Azure wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych i może wywołać następujące alerty:

- **Podatność na iniekcję SQL**: Ten alert jest wyzwalany, jeśli aplikacja generuje błędnej instrukcji SQL w bazie danych. który może oznaczać możliwe luki w zabezpieczeniach umożliwiające ataki przez iniekcję kodu SQL. Istnieją dwie możliwe przyczyny generowania błędnej instrukcji:

  - Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
  - Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
- **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany w przypadku aktywnego wykorzystania przed luką w zabezpieczeniach aplikacji zidentyfikowanego na iniekcję SQL. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL, korzystając z podatnego kodu aplikacji lub procedur składowanych.
- **Dostęp z nietypowej lokalizacji**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do programu SQL server, gdy ktoś zalogował się z serwerem SQL z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z centrum danych platformy Azure nietypowe**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do programu SQL server, gdy ktoś zalogował się do programu SQL server w centrum danych platformy Azure, co zostało zaobserwowane na tym serwerze w ostatnim czasie. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja na platformie Azure, usługa Power BI, edytor zapytań SQL platformy Azure). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do programu SQL server, gdy ktoś zalogował się do programu SQL server za pomocą nietypowego podmiotu zabezpieczeń (użytkownika SQL). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana do dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Atak siłowy na poświadczenia SQL**: Ten alert jest wyzwalany, gdy ma nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Zapoznaj się z nietypowe działania bazy danych w przypadku wykrycia podejrzanych zdarzeń

Otrzymasz wiadomość e-mail z powiadomieniem po wykryciu nietypowych działań bazy danych. Wiadomość e-mail zawiera informacje o podejrzanych zdarzeniach dotyczących zabezpieczeń tym o naturze nietypowych działań, nazwę bazy danych, nazwę serwera, nazwę aplikacji i czas zdarzenia. Ponadto wiadomość e-mail zawiera informacje o możliwych przyczynach i rekomendowanych działań umożliwiających sprawdzenie i ograniczenie potencjalnych zagrożeń w bazie danych.

![Raport nietypowych działań](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Kliknij przycisk **przeglądania ostatnich alertów SQL** linku w wiadomości e-mail do uruchomienia witryny Azure portal i wyświetlić na stronie alertów usługi Azure Security Center, które zawiera omówienie aktywne zagrożenia wykryte w bazie danych SQL.

   ![Działanie zagrożenia](./media/sql-database-threat-detection/active_threats.png)

2. Kliknij określony alert, aby uzyskać dodatkowe informacje i akcje dla tego typu zagrożenia badanie i korygowanie przez przyszłymi zagrożeniami.

   Na przykład wstrzykiwanie kodu SQL jest jednym z typowych problemów zabezpieczeń aplikacji sieci Web w Internecie, który jest używany do atakowania aplikacji opartych na danych. Osoby atakujące wykorzystują luki w zabezpieczeniach aplikacji iniekcję złośliwych instrukcji SQL do pól wejścia aplikacji, co umożliwia naruszenie lub zmodyfikowanie danych w bazie danych. Dla alertów wstrzyknięcie kodu SQL szczegóły alertu zawierają narażone instrukcji SQL, które zostało wykorzystane.

   ![Określony alert](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Zapoznaj się z alertami zaawansowanej ochrony przed zagrożeniami dla bazy danych w witrynie Azure portal

Zaawansowana ochrona przed zagrożeniami integruje jego alerty z usługą [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/). Żywe Kafelki SQL zaawansowanej ochrony przed zagrożeniami w ramach bazy danych i REKLAM SQL bloków w witrynie Azure portal śledzenia stanu aktywne zagrożenia.

Kliknij przycisk **alert zaawansowanej ochrony przed zagrożeniami** można uruchomić usługi Azure Security Center alerty strony i zapoznaj się z omówieniem aktywne zagrożenia SQL wykryte na bazę danych ani na magazyn danych.

   ![Zaawansowana ochrona przed zagrożeniami alertu](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Advanced Threat Protection alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zaawansowanej ochrony przed zagrożeniami w bazach danych pojedyncze i zbiorcze](sql-database-threat-detection.md).
- Dowiedz się więcej o [zaawansowanej ochrony przed zagrożeniami w wystąpieniu zarządzanym](sql-database-managed-instance-threat-detection.md).
- Dowiedz się więcej o [zaawansowanych zabezpieczeń danych](sql-database-advanced-data-security.md).
- Dowiedz się więcej o [inspekcji usługi Azure SQL Database](sql-database-auditing.md)
- Dowiedz się więcej o [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Aby uzyskać więcej informacji na temat cen, zobacz [stronę cennika usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
