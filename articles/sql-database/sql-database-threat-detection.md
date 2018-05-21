---
title: Wykrywanie — baza danych Azure SQL zagrożeń | Dokumentacja firmy Microsoft
description: Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych, które wskazują możliwe zagrożenia bezpieczeństwa bazy danych.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/17/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: db10bbdd39920b05d9fd8c3907f22c3ee5d08b02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="azure-sql-database-threat-detection"></a>Wykrywanie zagrożeń bazy danych Azure SQL

Wykrywanie zagrożeń bazy danych w usłudze Azure SQL wykrywa nietypowe działania wskazują nietypowe i potencjalnie szkodliwe próby dostępu lub wykorzystać baz danych.

Wykrywanie zagrożeń jest częścią [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) oferty (ATP), która jest ujednoliconego pakietu dla zaawansowane funkcje zabezpieczeń programu SQL. Wykrywanie zagrożeń można uzyskać dostępu do i zarządzane za pośrednictwem portalu centralnej SQL ATP.

## <a name="what-is-threat-detection"></a>Co to jest wykrywanie zagrożeń?

Wykrywanie zagrożeń SQL zawiera nową warstwę zabezpieczeń, co umożliwia klientom wykrywanie i odpowiadanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń w nietypowych działań. Użytkownicy otrzymują alert po bazy danych podejrzanych działań, potencjalnych luk w zabezpieczeniach, i iniekcja kodu SQL ataków, a także dostęp do nietypowych bazy danych i zapytanie wzorce. Wykrywanie zagrożeń SQL integruje alerty z [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/), która obejmuje szczegóły podejrzane działania i zaleca akcji na temat sposobu badania i ograniczyć zagrożenie. Wykrywanie zagrożeń SQL upraszcza potencjalne zagrożenia do bazy danych bez konieczności ekspertów zabezpieczeń lub zarządzać zabezpieczeniami zaawansowanymi systemy monitorowania. 

Środowisko pełnego postępowania, zalecane jest umożliwienie [SQL Database Auditing](sql-database-auditing.md), która zapisuje zdarzenia bazy danych inspekcji logowania na koncie magazynu Azure.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Skonfiguruj wykrywanie zagrożeń dla bazy danych w portalu Azure
1. Uruchamianie portalu Azure pod adresem [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do strony konfiguracji serwera bazy danych SQL Azure, który chcesz chronić. W ustawieniach zabezpieczeń, wybierz **Advanced Threat Protection**.
3. Na **Advanced Threat Protection** na stronie konfiguracji:

   - Włącz Advanced Threat Protection na serwerze.
   - W **ustawienia wykrywania zagrożeń**w **wysyłania alertów do** tekstu Podaj listę wiadomości e-mail, aby otrzymywać alerty zabezpieczeń po wykryciu nietypowe działania bazy danych.
  
   ![Skonfiguruj wykrywanie zagrożeń](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Skonfiguruj wykrywanie zagrożeń przy użyciu programu PowerShell

Na przykład skryptu, zobacz [konfigurowania inspekcji i wykrywania zagrożeń przy użyciu programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Eksploruj nietypowe działania bazy danych w przypadku wykrycia podejrzanych zdarzeń

Otrzymasz wiadomość e-mail z powiadomieniem po wykryciu nietypowe działania bazy danych. Wiadomości e-mail zawiera informacje dotyczące zdarzeń podejrzane zabezpieczeń, w tym charakter nietypowych działań, nazwa bazy danych, nazwę serwera, nazwa aplikacji i czas trwania zdarzenia. Ponadto wiadomości e-mail zawiera informacje na temat możliwych przyczyn i zalecane akcje do sprawdzania i ograniczyć potencjalne zagrożenia w bazie danych.

![Raport nietypowych działań](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. Kliknij przycisk **przeglądania ostatnich alertów SQL** link w wiadomości e-mail do uruchomienia portalu Azure i pokazać na stronie alerty Centrum zabezpieczeń Azure, które zawiera omówienie active zagrożeń wykryte w bazie danych SQL.

   ![Activty zagrożeń](./media/sql-database-threat-detection/active_threats.png)

2. Kliknij określony alert, aby uzyskać dodatkowe szczegóły i akcje badanie tego zagrożenia i korygowania przyszłych zagrożeń.

   Na przykład iniekcja kodu SQL jest jednym z najbardziej typowe problemy zabezpieczeń aplikacji sieci Web w Internecie, który służy do przeprowadzania ataków opartych na danych aplikacji. Osoby atakujące wykorzystać luki w zabezpieczeniach aplikacji do dodania złośliwego instrukcje SQL do pola wejścia aplikacji, naruszenia lub modyfikowanie danych w bazie danych. Dla alertów iniekcja kodu SQL szczegóły alertu zawierają narażone instrukcji SQL, która została wykorzystana.

   ![Określony alert](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Eksploruj alertów wykrywania zagrożeń dla bazy danych w portalu Azure

Wykrywanie zagrożeń bazy danych SQL integruje się jego alerty z [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/). Dynamiczne Kafelki wykrywania zagrożeń SQL na w bazie danych i bloki SQL ATP w portalu Azure śledzi stan aktywny zagrożeń.

Kliknij przycisk **alert wykrycia zagrożeń** można uruchomić Centrum zabezpieczeń Azure alerty strony i zapoznaj się z omówieniem active zagrożeń SQL wykryte w bazie danych.

   ![Alert wykrycia zagrożeń](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![Alert2 wykrywania zagrożeń](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Alerty wykrywanie zagrożeń bazy danych SQL Azure 
Wykrywanie zagrożeń bazy danych SQL Azure wykrywa nietypowe działania wskazują nietypowe i potencjalnie szkodliwe próby dostępu lub wykorzystać baz danych i mogą wyzwalać następujące alerty:
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

* Dowiedz się więcej o [SQL Advanced Threat Protection](sql-advanced-threat-protection.md). 
* Dowiedz się więcej o [inspekcja bazy danych SQL Azure](sql-database-auditing.md)
* Dowiedz się więcej o [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Aby uzyskać więcej informacji o cenach, zobacz [stronie cennika bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
