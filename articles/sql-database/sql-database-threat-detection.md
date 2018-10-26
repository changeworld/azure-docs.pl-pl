---
title: Wykrywanie zagrożeń — usługa Azure SQL Database | Dokumentacja firmy Microsoft
description: Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych w jednej bazie danych lub elastycznej puli.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 10/25/2018
ms.openlocfilehash: 2882bd782359697cf714214e68166c9f997f52e4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093551"
---
# <a name="azure-sql-database-threat-detection"></a>Wykrywanie zagrożeń w usłudze Azure SQL Database

Wykrywania zagrożeń usługi Azure SQL dla [bazy danych SQL](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych.

Wykrywanie zagrożeń jest częścią [SQL Zaawansowana ochrona przed zagrożeniami](sql-advanced-threat-protection.md) oferty (ATP), która to ujednolicone pakiet dla zaawansowane funkcje zabezpieczeń programu SQL. Wykrywanie zagrożeń można uzyskać dostęp i zarządzane za pośrednictwem centralnej portal SQL ATP.


> [!NOTE] 
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.


## <a name="what-is-threat-detection"></a>Co to jest wykrywanie zagrożeń?

Funkcja wykrywania zagrożeń SQL stanowi nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie i odpowiadanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku wystąpienia nietypowych działań. Użytkownicy otrzymują alert po podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach i wstrzykiwanie kodu SQL, ataki, a także dostęp do nietypowych bazy danych i wzorców zapytań. Funkcja wykrywania zagrożeń SQL integruje alerty z usługą [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/), zawierający szczegółowe informacje o podejrzanych działań i zaleca się akcję sposobu badania i ograniczenia zagrożenia. Funkcja wykrywania zagrożeń SQL upraszcza reagowanie na potencjalne zagrożenia bazy danych bez konieczności dysponowania eksperta zabezpieczeń lub zarządzania zabezpieczeniami zaawansowanymi systemami monitorowania. 

Badanie pełne środowisko pracy, zalecane jest włączanie [inspekcji usługi SQL Database](sql-database-auditing.md), która zapisuje zdarzenia bazy danych inspekcji logowania na koncie magazynu platformy Azure.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Konfigurowanie wykrywania zagrożeń dla bazy danych w witrynie Azure portal
1. Uruchamianie witryny Azure portal pod [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do strony konfiguracji serwera usługi Azure SQL Database, które chcesz chronić. W ustawieniach zabezpieczeń wybierz **zaawansowanej ochrony przed zagrożeniami**.
3. Na **zaawansowanej ochrony przed zagrożeniami** strony konfiguracji:

   - Włącz zaawansowaną ochronę przed zagrożeniami na serwerze.
   - W **ustawień wykrywania zagrożeń**w **Wyślij alerty do** tekstu Podaj listę adresów e-mail, aby otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   ![Konfigurowanie wykrywania zagrożeń](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Konfigurowanie wykrywania zagrożeń przy użyciu programu PowerShell

Aby uzyskać przykładowy skrypt, zobacz [skonfigurować inspekcję i wykrywanie zagrożeń za pomocą programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Zapoznaj się z nietypowe działania bazy danych w przypadku wykrycia podejrzanych zdarzeń

Otrzymasz wiadomość e-mail z powiadomieniem po wykryciu nietypowych działań bazy danych. Wiadomość e-mail zawiera informacje o podejrzanych zdarzeniach dotyczących zabezpieczeń tym o naturze nietypowych działań, nazwę bazy danych, nazwę serwera, nazwę aplikacji i czas zdarzenia. Ponadto wiadomość e-mail zawiera informacje o możliwych przyczynach i rekomendowanych działań umożliwiających sprawdzenie i ograniczenie potencjalnych zagrożeń w bazie danych.

![Raport nietypowych działań](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. Kliknij przycisk **przeglądania ostatnich alertów SQL** linku w wiadomości e-mail do uruchomienia witryny Azure portal i wyświetlić na stronie alertów usługi Azure Security Center, które zawiera omówienie aktywne zagrożenia wykryte w bazie danych SQL.

   ![Działania zagrożenia](./media/sql-database-threat-detection/active_threats.png)

2. Kliknij określony alert, aby uzyskać dodatkowe informacje i akcje dla tego typu zagrożenia badanie i korygowanie przez przyszłymi zagrożeniami.

   Na przykład wstrzykiwanie kodu SQL jest jednym z typowych problemów zabezpieczeń aplikacji sieci Web w Internecie, który jest używany do atakowania aplikacji opartych na danych. Osoby atakujące wykorzystują luki w zabezpieczeniach aplikacji iniekcję złośliwych instrukcji SQL do pól wejścia aplikacji, co umożliwia naruszenie lub zmodyfikowanie danych w bazie danych. Dla alertów wstrzyknięcie kodu SQL szczegóły alertu zawierają narażone instrukcji SQL, które zostało wykorzystane.

   ![Określony alert](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Zapoznaj się z alerty wykrywania zagrożeń dla bazy danych w witrynie Azure portal

Wykrywanie zagrożeń SQL Database integruje jego alerty z usługą [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/). Żywe Kafelki wykrywania zagrożeń SQL na w ramach bazy danych i bloków SQL zaawansowanej ochrony przed zagrożeniami w witrynie Azure portal umożliwia śledzenie stanu aktywne zagrożenia.

Kliknij przycisk **alert wykrycia zagrożeń** można uruchomić usługi Azure Security Center alerty strony i zapoznaj się z omówieniem aktywne zagrożenia SQL wykryte na bazę danych ani na magazyn danych.

   ![Alert wykrycia zagrożeń](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![Alert2 wykrywanie zagrożeń](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Alerty wykrywania zagrożeń bazy danych SQL platformy Azure 
Wykrywanie zagrożeń dla bazy danych SQL Azure wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych i może wywołać następujące alerty:
- **Podatność na iniekcję SQL**: Ten alert jest wyzwalany, gdy aplikacja generuje błędną instrukcję SQL w bazie danych. Może to wskazywać lukę w zabezpieczeniach umożliwiającą ataki przez iniekcję SQL. Istnieją dwie możliwe przyczyny generowania błędnej instrukcji:
   - Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
   - Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
- **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany w przypadku aktywnego wykorzystania zidentyfikowanej luki umożliwiającej iniekcję SQL w aplikacji. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL, korzystając z podatnego kodu aplikacji lub procedur składowanych.
- **Dostęp z nietypowej lokalizacji**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nietypowego centrum danych platformy Azure**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL z nietypowego centrum danych platformy Azure, co zostało zaobserwowane na tym serwerze w ostatnim czasie. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja na platformie Azure, usługa Power BI, edytor zapytań SQL platformy Azure). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL za pomocą nietypowego podmiotu zabezpieczeń (użytkownika SQL). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Atak siłowy na poświadczenia SQL**: Ten alert jest wyzwalany w przypadku nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [SQL zaawansowanej ochrony przed zagrożeniami](sql-advanced-threat-protection.md). 
* Dowiedz się więcej o [inspekcji usługi Azure SQL Database](sql-database-auditing.md)
* Dowiedz się więcej o [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Aby uzyskać więcej informacji na temat cen, zobacz [stronę cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
