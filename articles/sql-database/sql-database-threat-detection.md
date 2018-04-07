---
title: Wykrywanie — baza danych Azure SQL zagrożeń | Dokumentacja firmy Microsoft
description: Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych, które wskazują możliwe zagrożenia bezpieczeństwa bazy danych.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: ronmat
ms.openlocfilehash: c4a94ab9c7e0dab9e8c25e54fdd0a30b28b7a8a3
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="sql-database-threat-detection"></a>Wykrywanie zagrożeń bazy danych SQL

SQL wykrywanie zagrożeń wykrywa nietypowe działania wskazują nietypowe i potencjalnie szkodliwe próby dostępu lub wykorzystać baz danych.

## <a name="overview"></a>Przegląd

Wykrywanie zagrożeń SQL zawiera nową warstwę zabezpieczeń, co umożliwia klientom wykrywanie i odpowiadanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń w nietypowych działań.  Użytkownicy otrzymują alert po bazy danych podejrzanych działań, potencjalnych luk i ataki, a także bazy danych nietypowe wzorce dostępu. Wykrywanie zagrożeń SQL alerty zawierają szczegółowe informacje o podejrzanych działaniach i zalecane działania dotyczące sposobu badania i ograniczyć zagrożenie. Użytkownicy mogą Eksploruj podejrzane zdarzenia przy użyciu [SQL Database Auditing](sql-database-auditing.md) ustalenie, jeśli są one wynikiem próby dostępu, naruszenia lub wykorzystać w bazie danych. Wykrywanie zagrożeń upraszcza reagowanie na potencjalne zagrożenia bazy danych bez konieczności dysponowania wiedzą na poziomie eksperta zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania bezpieczeństwa.

Na przykład iniekcja kodu SQL jest jednym z typowych problemów zabezpieczeń aplikacji sieci Web w Internecie, używane do ataków opartych na danych aplikacji. Osoby atakujące wykorzystać luki w zabezpieczeniach aplikacji do dodania złośliwego instrukcje SQL do pola wejścia aplikacji, naruszenia lub modyfikowanie danych w bazie danych.

Wykrywanie zagrożeń SQL integruje alerty z [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/), i jest on rozliczany każdego chronionego serwera bazy danych SQL w tej samej cenie warstwy standardowa Centrum zabezpieczeń Azure, w $15 węzła/miesięcznie, gdzie każdy chronione bazy danych SQL serwer jest liczone jako jeden węzeł.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Skonfiguruj wykrywanie zagrożeń dla bazy danych w portalu Azure
1. Uruchamianie portalu Azure pod adresem [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do strony konfiguracji bazy danych SQL, który chcesz monitorować. Na stronie ustawień wybierz **Inspekcja i wykrywanie zagrożeń**. 
    ![Okienko nawigacji][1]
3. W **Inspekcja i wykrywanie zagrożeń** strony konfiguracji, Włącz **ON** inspekcji, w którym są wyświetlane ustawienia wykrywania zagrożeń.
  
    ![Okienko nawigacji][2]
4. Włącz **ON** wykrywanie zagrożeń.
5. Skonfiguruj listę wiadomości e-mail, aby otrzymywać alerty zabezpieczeń po wykryciu nietypowe działania bazy danych.
6. Kliknij przycisk **zapisać** w **Inspekcja i wykrywanie zagrożeń** strony, aby zapisać ustawienia wykrywania nowych lub zaktualizowanych inspekcji i zagrożeń.
       
    ![Okienko nawigacji][3]

## <a name="set-up-threat-detection-using-powershell"></a>Skonfiguruj wykrywanie zagrożeń przy użyciu programu PowerShell

Na przykład skryptu, zobacz [konfigurowania inspekcji i wykrywania zagrożeń przy użyciu programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Eksploruj nietypowe działania bazy danych w przypadku wykrycia podejrzanych zdarzeń
1. Otrzymasz wiadomość e-mail z powiadomieniem po wykryciu nietypowe działania bazy danych. <br/>
   Wiadomości e-mail zawiera informacje dotyczące zdarzeń podejrzane zabezpieczeń, w tym charakter nietypowych działań, nazwa bazy danych, nazwę serwera, nazwa aplikacji i czas trwania zdarzenia. Ponadto wiadomości e-mail zawiera informacje na temat możliwych przyczyn i zalecane akcje do sprawdzania i ograniczyć potencjalne zagrożenia w bazie danych.<br/>
     
    ![Okienko nawigacji][4]
2. Alerty e-mail zawiera łącze do dziennika inspekcji SQL. Kliknięcie tego łącza spowoduje uruchomienie portalu Azure i otwiera rekordów inspekcji SQL w czasie zbliżonym do podejrzane zdarzenia. Rekord inspekcji, aby wyświetlić więcej informacji na działania podejrzane bazy danych, co ułatwia znajdowanie instrukcji SQL, które zostały wykonane, kliknij polecenie (kto uzyskiwał dostęp do, co zostało i kiedy) i określenie, czy zdarzenie uzasadnionych lub złośliwymi (np. aplikacji Aby iniekcja kodu SQL luki, kogoś naruszone poufnych danych itp.).<br/>
   ![Okienko nawigacji][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Eksploruj alertów wykrywania zagrożeń dla bazy danych w portalu Azure

Wykrywanie zagrożeń bazy danych SQL integruje się jego alerty z [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/). Na żywo Kafelek zabezpieczenia SQL, na stronie bazy danych w portalu Azure śledzi stan aktywny zagrożeń. 

   ![Okienko nawigacji][6]
   
1. Kliknięcie SQL Kafelek zabezpieczenia uruchamia strony alerty Centrum zabezpieczeń Azure i zawiera omówienie active zagrożeń SQL wykryte w bazie danych. 

  ![Okienko nawigacji][7]

2. Kliknij określony alert zawiera dodatkowe szczegóły oraz do badania tego zagrożenia i korygowania przyszłych zagrożenia.

  ![Okienko nawigacji][8]


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o wykrywanie zagrożeń, odwiedź stronę [Azure blog](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/) 
* Dowiedz się więcej o [inspekcja bazy danych SQL Azure](sql-database-auditing.md)
* Dowiedz się więcej o [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Aby uzyskać więcej informacji o cenach, zobacz [stronie cennika bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
* Na przykład skryptu programu PowerShell, zobacz [konfigurowania inspekcji i wykrywania zagrożeń przy użyciu programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md)



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png


