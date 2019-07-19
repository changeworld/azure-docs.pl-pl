---
title: Zaawansowane zabezpieczenia danych dla IaaS w Azure Security Center | Microsoft Docs
description: " Dowiedz się, jak włączyć zaawansowane zabezpieczenia danych dla IaaS w Azure Security Center. "
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
ms.author: v-mohabe
ms.openlocfilehash: 0b83575baa2221f0b502abbf919654492c7ab6cf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295752"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Zaawansowane zabezpieczenia danych dla serwerów SQL w systemie Azure Virtual Machines (publiczna wersja zapoznawcza)
Zaawansowane zabezpieczenia danych dla serwerów SQL w systemie Azure Virtual Machines to ujednolicony pakiet dla zaawansowanych funkcji zabezpieczeń SQL. Obecnie (w publicznej wersji zapoznawczej) zawiera funkcje umożliwiające wyświetlanie i łagodzenie potencjalnych luk w zabezpieczeniach i wykrywanie nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych. 

Ta oferta zabezpieczeń dla serwerów SQL maszyn wirtualnych platformy Azure jest oparta na tej samej podstawowej technologii, która jest używana w [pakiecie Azure SQL Database Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Przegląd

Zaawansowane zabezpieczenia danych zapewniają zestaw zaawansowanych funkcji zabezpieczeń SQL, które obejmują ocenę luk w zabezpieczeniach i zaawansowaną ochronę przed zagrożeniami.

* [Ocena luk](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) w zabezpieczeniach to prosta konfiguracja usługi, która umożliwia odnajdywanie, śledzenie i rozwiązywanie problemów z potencjalnymi lukami w zabezpieczeniach bazy danych. Zapewnia wgląd w stan zabezpieczeń i zawiera kroki rozwiązywania problemów z zabezpieczeniami i ulepszania bazy danych FORTIFICATIONS.
* [Zaawansowana ochrona przed zagrożeniami](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do programu SQL Server lub korzystania z niego. Nieustannie monitoruje bazę danych pod kątem podejrzanych działań i zapewnia zorientowane na działania alerty zabezpieczeń dotyczące nietypowych wzorców dostępu do bazy danych. Te alerty zawierają szczegóły podejrzanych działań i zalecane akcje do zbadania i ograniczenia zagrożenia.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Wprowadzenie do zaawansowanych zabezpieczeń danych SQL na maszynach wirtualnych platformy Azure

Poniższe kroki ułatwiają rozpoczęcie pracy z zaawansowanymi zabezpieczeniami danych SQL na maszynach wirtualnych platformy Azure w publicznej wersji zapoznawczej.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Konfigurowanie zaawansowanych zabezpieczeń danych SQL na maszynach wirtualnych platformy Azure

**Przed rozpoczęciem**: Do przechowywania analizowanych dzienników zabezpieczeń potrzebny jest obszar roboczy Log Analytics. Jeśli go nie masz, możesz go utworzyć łatwo, zgodnie z opisem w temacie [Tworzenie obszaru roboczego log Analytics w Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Połącz maszynę wirtualną obsługującą program SQL Server z obszarem roboczym Log Analytics. Aby uzyskać instrukcje, zobacz [łączenie komputerów z systemem Windows w celu Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. W portalu Azure Marketplace przejdź do [rozwiązania SQL Advanced Data Security](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Można go znaleźć za pomocą opcji wyszukiwania w witrynie Marketplace, jak widać na poniższej ilustracji). Zostanie otwarta strona **SQL Advanced Data Security** .

    ![Zaawansowane zabezpieczenia danych dla IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Kliknij przycisk **Utwórz**. Zostaną wyświetlone miejsca pracy.

    ![Zaawansowane zabezpieczenia danych — Tworzenie](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Wybierz obszar roboczy, którego chcesz użyć, a następnie kliknij przycisk **Utwórz**.

   ![Wybór obszaru roboczego](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Uruchom ponownie [program SQL Server maszyny wirtualnej](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Eksplorowanie i badanie alertów zabezpieczeń

Możesz wyświetlać bieżące alerty zabezpieczeń i zarządzać nimi.

1. Kliknij pozycję **Security Center** > **alerty zabezpieczeń**, a następnie kliknij alert.

    ![Znajdź alert](./media/security-center-advanced-iaas-data/find-alert.png)

1. W kolumnie **zaatakowany zasób** kliknij zaatakowany zasób.

1. Aby wyświetlić szczegóły alertu i akcje związane z badaniem bieżącego zagrożenia i rozwiązywać przyszłe zagrożenia, przewiń w dół stronę **Informacje ogólne** , a następnie w sekcji **kroki zaradcze** kliknij link **kroki badania** .

    ![Kroki zaradcze](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Aby wyświetlić dzienniki skojarzone z wyzwalaczem alertu, przejdź do **obszaru roboczego usługi log Analytics** i wykonaj następujące czynności:

     > [!NOTE]
     > Jeśli **obszary robocze usługi log Analytics** nie są wyświetlane w menu po lewej stronie, kliknij pozycję **wszystkie usługi**i Wyszukaj **obszary robocze analizy dzienników**.

    1. Upewnij się, że kolumny wyświetlają **warstwę cenową** i kolumny **Identyfikator obszaru roboczego** . (**Obszary robocze** > usługi log Analytics**Edytuj kolumny**, Dodaj **warstwę cenową** i **Identyfikator obszaru roboczego**).

     ![Edytuj kolumny](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Kliknij obszar roboczy, w którym znajdują się dzienniki alertów.

    1. W menu **Ogólne** kliknij pozycję **dzienniki** .

    1. Kliknij oczy obok tabeli **SQLAdvancedThreatProtection** . Dzienniki znajdują się na liście.

     ![Wyświetlanie dzienników](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Konfigurowanie powiadomień E-mail dla alertów ATP 

Po wygenerowaniu alertów ASC można ustawić listę adresatów, którzy otrzymają powiadomienie e-mail. Wiadomość e-mail zawiera bezpośredni link do alertu w Azure Security Center ze wszystkimi odpowiednimi szczegółami. 

1. Przejdź do **Security Center** > **Cennik & Ustawienia** i kliknij odpowiednią subskrypcję

    ![Ustawienia subskrypcji](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. W menu **Ustawienia** kliknij pozycję **powiadomienia e-mail**. 
1. W polu tekstowym **adres e-mail** wprowadź adresy e-mail, na które mają być wysyłane powiadomienia. Można wprowadzić więcej niż jeden adres e-mail, oddzielając adresy e-mail przecinkami (,).  Na przykład admin1@mycompany.com,admin2@mycompany.comadmin3@mycompany.com

      ![Ustawienia poczty e-mail](./media/security-center-advanced-iaas-data/email-settings.png)

1. W ustawieniach **powiadomień e-mail** ustaw następujące opcje:
  
    * **Wyślij powiadomienie e-mail dla alertów o wysokiej ważności**: Zamiast wysyłać wiadomości e-mail dla wszystkich alertów, wyślij je tylko w przypadku alertów o wysokiej ważności.
    * **Wysyłaj także powiadomienia e-mail do właścicieli subskrypcji**:  Wyślij powiadomienia również do właścicieli subskrypcji.

1. W górnej części ekranu **powiadomienia e-mail** kliknij pozycję **Zapisz**.

  > [!NOTE]
  > Pamiętaj, aby kliknąć przycisk **Zapisz** przed zamknięciem okna lub nowe ustawienia **powiadomień e-mail** nie zostaną zapisane.

## <a name="explore-vulnerability-assessment-reports"></a>Poznaj raporty oceny luk w zabezpieczeniach

Pulpit nawigacyjny oceny luk w zabezpieczeniach zawiera przegląd wyników oceny dla wszystkich baz danych. Można wyświetlić dystrybucję baz danych zgodnie z wersją SQL Server, wraz z podsumowaniem niepowodzenia i przekazywania baz danych oraz ogólnym podsumowaniem niepowodzenia kontroli w zależności od dystrybucji ryzyka.

Wyniki oceny luk w zabezpieczeniach i raporty można wyświetlić bezpośrednio z Log Analytics.

1. Przejdź do obszaru roboczego Log Analytics przy użyciu zaawansowanego rozwiązania do zabezpieczania danych.
1. Przejdź do **rozwiązań** i wybierz rozwiązanie do **oceny luk w zabezpieczeniach SQL** .
1. W okienku **Podsumowanie** kliknij pozycję **Wyświetl podsumowanie** i wybierz **raport oceny luk w zabezpieczeniach SQL**.

    ![Raport SQL Assessment](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Pulpit nawigacyjny raport ładuje. Upewnij się, że przedział czasu jest ustawiony na co najmniej **7 ostatnich dni** od momentu, gdy skany oceny luk w zabezpieczeniach są uruchamiane w bazach danych zgodnie z ustalonym harmonogramem na 7 dni.

    ![Ustaw ostatnie 7 dni](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Aby przejść do szczegółów, kliknij dowolny z elementów pulpitu nawigacyjnego. Na przykład:

   1. Kliknij pozycję Sprawdzanie luk w zabezpieczeniach w sekcji **Podsumowanie testów zakończonych niepowodzeniem** , aby wyświetlić tabelę log Analytics z wynikami tego sprawdzania we wszystkich bazach danych. Te, które mają wyniki, są wyświetlane jako pierwsze.

   1. Następnie kliknij pozycję przez, aby wyświetlić szczegóły każdej luki w zabezpieczeniach, w tym opis luki w zabezpieczeniach, stan, powiązane ryzyko i rzeczywiste wyniki w tej bazie danych. Możesz również wyświetlić rzeczywiste zapytanie, które zostało uruchomione, aby wykonać to sprawdzenie, oraz informacje korygujące dotyczące rozwiązania tych luk w zabezpieczeniach.

    ![Wybór obszaru roboczego](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Wybór obszaru roboczego](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Możesz uruchomić dowolne Log Analytics zapytań dotyczących danych wyników oceny luk w zabezpieczeniach, aby wydzielić dane i zaindeksować je zgodnie z potrzebami.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Zaawansowana ochrona przed zagrożeniami dla serwerów SQL na maszynach wirtualnych platformy Azure
Alerty są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do serwerów SQL lub korzystania z nich. Zdarzenia te mogą wyzwalać następujące alerty:

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>Alerty nietypowego wzorca dostępu (obsługiwane w publicznej wersji zapoznawczej)

* **Dostęp z nietypowej lokalizacji:** Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do programu SQL Server, w którym ktoś zalogował się do serwera SQL z nietypowej lokalizacji geograficznej. Możliwe przyczyny:
     * Osoba atakująca lub poprzednie złośliwe oprogramowanie uzyskało dostęp do SQL Server.
     * Uprawniony użytkownik uzyskał dostęp do SQL Server z nowej lokalizacji.
* **Dostęp z potencjalnie szkodliwej aplikacji**: alert jest wyzwalany, gdy do uzyskiwania dostępu do bazy danych jest używana potencjalnie szkodliwa aplikacja. Możliwe przyczyny:
     * Osoba atakująca próbująca naruszyć kod SQL przy użyciu popularnych narzędzi ataku.
     * W działaniu legalne testowanie penetracji.
* **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do programu SQL Server, w którym ktoś zalogował się do programu SQL Server przy użyciu nietypowego podmiotu zabezpieczeń (użytkownika SQL). Możliwe przyczyny:
     * Osoba atakująca lub poprzednie złośliwe oprogramowanie uzyskało dostęp do SQL Server. 
     * Uprawniony użytkownik uzyskał dostęp do SQL Server z nowego podmiotu zabezpieczeń.
* **Wymuszanie poświadczeń SQL**: Ten alert jest wyzwalany, gdy występuje nietypowa duża liczba nieudanych logowań z innymi poświadczeniami. Możliwe przyczyny:
     * Osoba atakująca próbująca naruszyć kod SQL przy użyciu funkcji pełnego wymuszania.
     * W działaniu legalne testowanie penetracji.

### <a name="potential-sql-injection-attacks-coming"></a>Potencjalne ataki na wstrzyknięcie kodu SQL (z możliwością wejścia)

* **Podatność na iniekcję SQL**: Ten alert jest wyzwalany, gdy aplikacja generuje błędną instrukcję SQL w bazie danych. który może oznaczać możliwe luki w zabezpieczeniach umożliwiające ataki przez iniekcję kodu SQL. Możliwe przyczyny:
     * Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
     * Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
* **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany, gdy aktywna Luka w zabezpieczeniach nastąpi w odniesieniu do wskazanej aplikacji iniekcja SQL. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL, korzystając z podatnego kodu aplikacji lub procedur składowanych.
