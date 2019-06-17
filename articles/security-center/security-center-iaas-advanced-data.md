---
title: Zaawansowane zabezpieczenia danych dla modelu IaaS w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak włączyć zabezpieczeń zaawansowanych danych dla modelu IaaS w usłudze Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e7420adfe1608df39ef72124817f1d6dadf07db8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66400161"
---
# <a name="advanced-data-security-for-sql-servers-on-iaas"></a>Zabezpieczenia zaawansowanych danych dla serwerów SQL iaas
Zabezpieczeń zaawansowanych danych dla serwerów SQL w usłudze Azure Virtual Machines to ujednolicone pakiet dla zaawansowane funkcje zabezpieczeń programu SQL. Obecnie zawiera funkcje, dzięki czemu są ujawniane łagodzenia potencjalne luki bazy danych i wykrywanie nietypowych działań wskazujących zagrożenie z bazą danych. 

Zabezpieczenia, tej oferty dla serwerów SQL maszyn wirtualnych platformy Azure są oparte na tych samych podstawowych technologii używanej do [pakietu zaawansowane bezpieczeństwo danych Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Omówienie

Bezpieczeństwo danych zaawansowane zawiera zbiór zaawansowane funkcje zabezpieczeń programu SQL, składające się z oceny luk w zabezpieczeniach i Zaawansowana ochrona przed zagrożeniami.

* [Ocena luk w zabezpieczeniach](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) jest łatwa do skonfigurowania usługi odnajdywania, śledzenie i ułatwić korygowanie potencjalnych luk w bazie danych. Zapewnia wgląd w stan zabezpieczeń i zawiera kroki, aby rozwiązać problemy z zabezpieczeniami i poprawić funkcjonalność z fortifications swojej bazy danych.
* [Zaawansowana ochrona przed zagrożeniami](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystać program SQL server. Stale monitoruje bazę danych pod kątem podejrzanych działań i zawiera alerty zabezpieczeń zorientowanych na akcje dotyczące bazy danych nietypowe wzorce dostępu. Te alerty zawierają szczegółowe informacje podejrzanych działań i rekomendowanych działań umożliwiających zbadania i wyeliminowania zagrożenia.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Rozpoczynanie pracy z zabezpieczeniami zaawansowanymi danych SQL na maszynach wirtualnych platformy Azure

Poniższe kroki ułatwiające rozpoczęcie pracy z zabezpieczeniami zaawansowanymi danych SQL na maszynach wirtualnych platformy Azure.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Skonfiguruj zaawansowane zabezpieczenia danych SQL na maszynach wirtualnych platformy Azure

**Przed rozpoczęciem**: Należy obszar roboczy usługi Log Analytics do przechowywania dzienników zabezpieczeń analizowane. Jeśli nie masz, a następnie możesz utworzyć je łatwo, jak wyjaśniono w [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Połącz z maszyną Wirtualną hostowany program SQL server do obszaru roboczego usługi Log Analytics. Aby uzyskać instrukcje, zobacz [Windows łączenie komputerów do usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. W witrynie Azure Marketplace, przejdź do [rozwiązania SQL zaawansowanych danych zabezpieczeń](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Można znaleźć za pomocą opcji wyszukiwania w witrynie marketplace, patrz na poniższej ilustracji.) **Danych zaawansowanych SQL** zostanie otwarta strona.

    ![Zabezpieczenia zaawansowanych danych IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Kliknij pozycję **Utwórz**. Miejsca pracy są wyświetlane.

    ![Advanced Data Security Create](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Wybierz obszar roboczy, a następnie kliknij przycisk **Utwórz**.

   ![Wybór obszaru roboczego](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Uruchom ponownie [maszyny Wirtualnej programu SQL server](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Eksploruj i badanie alertów zabezpieczeń

Można wyświetlać i zarządzać bieżące alerty zabezpieczeń.

1. Kliknij przycisk **usługi Security Center** > **alerty zabezpieczeń**i kliknij alert.

    ![Znajdź Alert](./media/security-center-advanced-iaas-data/find-alert.png)

1. Z **zaatakowany zasób** kolumny, kliknij zasób, który został ataku.

1. Aby wyświetlić szczegóły alertu i akcje w przypadku badania bieżące zagrożenia i adresowania przyszłymi zagrożeniami, przewiń w dół **ogólne informacje** strony, a następnie w **czynności zaradczych** sekcji, kliknij pozycję  **KROKI badania** łącza.

    ![Instrukcje rozwiązania problemu](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Aby wyświetlić dzienniki, które są skojarzone z wyzwalania alertu, przejdź do **dziennika obszarów roboczych usługi analytics** i wykonaj następujące czynności:

     > [!NOTE]
     > Jeśli **dziennika obszarów roboczych usługi analytics** nie są wyświetlane w menu po lewej stronie kliknij pozycję **wszystkich usług**i wyszukaj **dziennika obszarów roboczych usługi analytics**.

    1. Pamiętaj, że są wyświetlane kolumny **warstwa cenowa** i **WorkspaceID** kolumn. (**Dziennika obszarów roboczych usługi analytics** > **Upravit sloupce**, Dodaj **warstwa cenowa** i **WorkspaceID**.)

     ![Edytuj kolumny](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Kliknij obszar roboczy, który ma alertów dzienników.

    1. W obszarze **ogólne** menu, kliknij przycisk **dzienników**

    1. Kliknij oka **SQLAdvancedThreatProtection** tabeli. Dzienniki są wyświetlane.

     ![Wyświetl dzienniki](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Skonfiguruj powiadomienia E-mail dla alertów zaawansowanej ochrony przed zagrożeniami 

Możesz ustawić listę adresatów otrzymujących wiadomość e-mail z powiadomieniem, gdy zostaną wygenerowane alerty usługi ASC. Wiadomość e-mail zawiera bezpośredni link do alertu w usłudze Azure Security Center z wszystkich odpowiednich szczegółów. 

1. Przejdź do **usługi Security Center** > **zasady zabezpieczeń** i w wierszu kliknij odpowiednie subskrypcji **Edytuj ustawienia >** .

    ![Ustawienia subskrypcji](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Z **ustawienia** kliknij menu **wiadomości E-mail z powiadomieniami**. 
1. W **adres E-mail** tekstu wprowadź adresy e-mail, aby otrzymywać powiadomienia. Aby wprowadzić więcej niż jeden adres e-mail, adresy e-mail oddziel przecinkami (,).  Na przykład admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

      ![Ustawienia poczty e-mail](./media/security-center-advanced-iaas-data/email-settings.png)

1. W **powiadomienia E-mail** ustawienia, z następujących opcji:
  
    * **Wyślij powiadomienie e-mail dla alertów o wysokiej ważności**: Zamiast wysyłania wiadomości e-mail dla wszystkich alertów, należy wysłać tylko w przypadku alertów o wysokiej ważności.
    * **Również wysyłać powiadomienia e-mail do właścicieli subskrypcji**:  Za wysyłanie powiadomień do właścicieli subskrypcji.

1. W górnej części **wiadomości E-mail z powiadomieniami** ekranu, kliknij przycisk **Zapisz**.

  > [!NOTE]
  > Należy kliknąć przycisk **Zapisz** przed zamknięciem okna lub do nowej **powiadomienia E-mail** nie można zapisać ustawień.

## <a name="explore-vulnerability-assessment-reports"></a>Poznaj raporty z oceny luk w zabezpieczeniach

Pulpit nawigacyjny z oceny luk w zabezpieczeniach omówienie wyników oceny we wszystkich bazach danych. Możesz zobaczyć rozkład baz danych zgodnie z wersją programu SQL Server, wraz z podsumowaniem Niepowodzenie w porównaniu z przekazywaniem baz danych i zbiorcze podsumowanie operacji sprawdzania zgodnie z ryzykiem dystrybucji zakończonych niepowodzeniem.

Można wyświetlić Twoje wyniki oceny luk w zabezpieczeniach i raporty bezpośrednio z usługi Log Analytics.

1. Przejdź do obszaru roboczego usługi Log Analytics za pomocą rozwiązania w zakresie zaawansowanych zabezpieczeń danych.
1. Przejdź do **rozwiązania** i wybierz **oceny luk w zabezpieczeniach SQL** rozwiązania.
1. W **Podsumowanie** okienku kliknij **Wyświetl podsumowanie** i wybierz swoje **raport z oceny luk w zabezpieczeniach SQL**.

    ![Raport z oceny SQL](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Ładuje raportu pulpitu nawigacyjnego. Upewnij się, przedział czasu jest ustawiony do co najmniej **ciągu ostatnich 7 dni** ponieważ skanowania oceny luk w zabezpieczeniach są uruchamiane w bazach danych zgodnie z ustalonym harmonogramem co 7 dni.

    ![Ustaw ostatnich 7 dni](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Aby przejść do szczegółów, aby uzyskać więcej informacji, kliknij dowolny z elementów pulpitu nawigacyjnego. Na przykład:

   1. Kliknij znacznik wyboru luk w zabezpieczeniach w **sprawdza, czy podsumowanie** sekcji, aby wyświetlić tabelę usługi Log Analytics z wynikami dla tego sprawdzenia we wszystkich bazach danych. Mają wyniki są wymienione jako pierwsze.

   1. Następnie kliknij, aby wyświetlić szczegóły dotyczące każdej luk w zabezpieczeniach, w tym opis luk w zabezpieczeniach i wpływu, stan, skojarzone o podwyższonym ryzyku i rzeczywiste wyniki dla tej bazy danych. Rzeczywiste zapytanie, które zostało uruchomione do wykonania tego wyboru, a informacje o korygowaniu rozpoznawania tę lukę w zabezpieczeniach może być też widoczny.

    ![Wybór obszaru roboczego](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Wybór obszaru roboczego](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Możesz uruchomić żadnych zapytań usługi Log Analytics z danymi wyniki oceny luk w zabezpieczeniach, aby kątami danych zgodnie z potrzebami.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Zaawansowana ochrona przed zagrożeniami, serwerami SQL dotyczących alertów maszyny wirtualne platformy Azure
Alerty są generowane na podstawie nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystać serwerami programu SQL Server. Zdarzenia te można uruchomić następujące alerty:

### <a name="anomalous-access-pattern-alerts"></a>Dostęp z nietypowego wzorca alertów

* **Dostęp z nietypowej lokalizacji:** Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do programu SQL server, gdy ktoś zalogował się z serwerem SQL z nietypowej lokalizacji geograficznej. Potencjalne przyczyny:
     * Osoba atakująca lub byłego stosują złośliwego uzyskał dostęp do programu SQL Server.
     * Przez wiarygodnego użytkownika ma dostęp do programu SQL Server z nowej lokalizacji.
* **Dostęp z potencjalnie szkodliwej aplikacji**: jego alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana do dostępu do bazy danych. Potencjalne przyczyny:
     * Osoba atakująca próby naruszenia SQL przy użyciu typowych narzędzi ataku.
     * Autoryzowanych testów penetracyjnych ich działanie.
* **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do programu SQL server, gdy ktoś zalogował się do programu SQL server za pomocą nietypowego podmiotu zabezpieczeń (użytkownika SQL). Potencjalne przyczyny:
     * Osoba atakująca lub byłego stosują złośliwego uzyskał dostęp do programu SQL Server. 
     * Przez wiarygodnego użytkownika uzyskał dostęp do serwera SQL z przy użyciu nowej jednostki.
* **Atak siłowy na poświadczenia SQL**: Ten alert jest wyzwalany, gdy ma nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. Potencjalne przyczyny:
     * Osoba atakująca próby naruszenia SQL przy użyciu atak siłowy.
     * Autoryzowanych testów penetracyjnych ich działanie.

### <a name="potential-sql-injection-attacks-coming"></a>Potencjalna iniekcja SQL, ataki (wejścia)

* **Podatność na iniekcję SQL**: Ten alert jest wyzwalany, jeśli aplikacja generuje błędnej instrukcji SQL w bazie danych. który może oznaczać możliwe luki w zabezpieczeniach umożliwiające ataki przez iniekcję kodu SQL. Potencjalne przyczyny:
     * Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
     * Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
* **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany w przypadku aktywnego wykorzystania przed luką w zabezpieczeniach aplikacji zidentyfikowanego na iniekcję SQL. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL, korzystając z podatnego kodu aplikacji lub procedur składowanych.
