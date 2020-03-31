---
title: Zaawansowane zabezpieczenia danych dla usługi IaaS w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: " Dowiedz się, jak włączyć zaawansowane zabezpieczenia danych dla usługi IaaS w usłudze Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282733"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Zaawansowane zabezpieczenia danych dla serwerów SQL na maszynach wirtualnych platformy Azure (wersja zapoznawcza)
Zaawansowane zabezpieczenia danych dla serwerów SQL na maszynach wirtualnych platformy Azure to ujednolicony pakiet dla zaawansowanych funkcji zabezpieczeń SQL. Ta funkcja w wersji zapoznawczej obejmuje funkcje identyfikowania i łagodzenia potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywania nietypowych działań, które mogą wskazywać na zagrożenia dla bazy danych. 

Ta oferta zabezpieczeń dla serwerów SQL maszyn wirtualnych platformy Azure opiera się na tej samej podstawowej technologii, która jest używana w [pakiecie zaawansowanego zabezpieczenia danych usługi Azure SQL Database.](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="overview"></a>Omówienie

Zaawansowane zabezpieczenia danych zapewniają zestaw zaawansowanych funkcji zabezpieczeń SQL, składających się z oceny luk w zabezpieczeniach i zaawansowanej ochrony przed zagrożeniami.

* [Ocena luk w zabezpieczeniach](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) to łatwa w konfiguracji usługa, która może wykrywać, śledzić i pomagać w korygowania potencjalnych luk w zabezpieczeniach bazy danych. Zapewnia wgląd w stan zabezpieczeń i zawiera kroki, aby rozwiązać problemy z zabezpieczeniami i poprawić fortyfikacje bazy danych.
* [Zaawansowana ochrona przed zagrożeniami](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do serwera SQL lub ich wykorzystania. Stale monitoruje bazę danych pod kątem podejrzanych działań i udostępnia alerty zabezpieczeń zorientowane na akcję na nietypowe wzorce dostępu do bazy danych. Te alerty zawierają szczegóły podejrzanych działań i zalecane działania w celu zbadania i złagodzenia zagrożenia.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Wprowadzenie do zaawansowanego bezpieczeństwa danych dla języka SQL na maszynach wirtualnych platformy Azure

Poniższe kroki umożliwiają rozpoczęcie pracy z programem Advanced Data Security for SQL na publicznych maszynach wirtualnych platformy Azure w wersji Preview.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Konfigurowanie zaawansowanych zabezpieczeń danych dla języka SQL na maszynach wirtualnych platformy Azure

Włącz zaawansowane zabezpieczenia danych dla serwerów SQL na maszynach wirtualnych na poziomie subskrypcji/obszaru roboczego:
 
1. Na pasku bocznym centrum zabezpieczeń otwórz stronę **Ustawienia & cennika.**

1. Wybierz subskrypcję lub obszar roboczy, dla którego chcesz włączyć zaawansowane zabezpieczenia danych dla języka SQL na maszynach wirtualnych platformy Azure.

1. Przełącz opcję dla **serwerów SQL na maszynie Wirtualnej (Wersja zapoznawcza)** do włączonej. 

    (Kliknij zrzut ekranu, aby rozwinąć)

    [![Zalecenia i alerty usługi Security Center widoczne w Centrum administracyjnym systemu Windows](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Zaawansowane zabezpieczenia danych dla serwerów SQL zostaną włączone na wszystkich serwerach SQL połączonych z wybranym obszarem roboczym lub domyślnym obszarem roboczym wybranej subskrypcji.

    >[!NOTE]
    > Rozwiązanie będzie w pełni aktywne po pierwszym ponownym uruchomieniu programu SQL Server. 

Aby utworzyć nowy obszar roboczy, postępuj zgodnie z instrukcjami w [obszarze roboczym Tworzenie usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Aby połączyć hosta programu SQL Server z obszarem roboczym, postępuj zgodnie z instrukcjami w obszarze [Łączenie komputerów z systemem Windows z monitorem Azure .](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)


## <a name="set-up-email-notification-for-security-alerts"></a>Konfigurowanie powiadomień e-mail dla alertów zabezpieczeń 

Możesz ustawić listę adresatów, aby otrzymywać powiadomienia e-mail po wygenerowaniu alertów Usługi Security Center. Wiadomość e-mail zawiera bezpośrednie łącze do alertu w usłudze Azure Security Center ze wszystkimi odpowiednimi szczegółami. 

1. Przejdź do**ustawień & cennika** **usługi Security Center** > i kliknij odpowiednią subskrypcję

    ![Ustawienia subskrypcji](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. W menu Ustawienia kliknij polecenie **Powiadomienia e-mail**. 
1. W polu **tekstowym Adres e-mail** wprowadź adresy e-mail, aby otrzymywać powiadomienia. Możesz wprowadzić więcej niż jeden adres e-mail, oddzielając adresy e-mail przecinkiem (,).  Na admin1@mycompany.comprzykładadmin2@mycompany.com,admin3@mycompany.com

    ![Ustawienia poczty e-mail](./media/security-center-advanced-iaas-data/email-settings.png)

1. W ustawieniach **powiadomień e-mail** ustaw następujące opcje:
  
    * **Wysyłanie powiadomień e-mail o alertach o wysokiej ważności:** Zamiast wysyłać wiadomości e-mail dla wszystkich alertów, wysyłaj tylko alerty o wysokiej ważności.
    * **Wysyłaj również powiadomienia e-mail do właścicieli subskrypcji:** Wysyłaj powiadomienia również do właścicieli subskrypcji.

1. W górnej części ekranu **Powiadomienia e-mail** kliknij pozycję **Zapisz**.

  > [!NOTE]
  > Pamiętaj, aby kliknąć przycisk **Zapisz** przed zamknięciem okna lub nowe ustawienia **powiadomień e-mail** nie zostaną zapisane.

## <a name="explore-vulnerability-assessment-reports"></a>Zapoznaj się z raportami oceny luk w zabezpieczeniach

Pulpit nawigacyjny oceny luk w zabezpieczeniach zawiera przegląd wyników oceny we wszystkich bazach danych. Można wyświetlić dystrybucję baz danych zgodnie z wersją programu SQL Server, wraz z podsumowaniem niepowodzenia w porównaniu z przekazywaniem baz danych i ogólnym podsumowaniem nieudanych kontroli zgodnie z dystrybucją ryzyka.

Wyniki oceny luk w zabezpieczeniach można wyświetlić bezpośrednio w u centrum zabezpieczeń.

1. Na pasku bocznym centrum zabezpieczeń w obszarze HIGIENA BEZPIECZEŃSTWA ZASOBÓW wybierz pozycję **Data & Storage**.

1. Wybierz zalecenia **Luki w zabezpieczeniach baz danych SQL na maszynach wirtualnych powinny zostać naprawione (Wersja zapoznawcza).** Aby uzyskać więcej informacji, zobacz [Zalecenia centrum zabezpieczeń](security-center-recommendations.md). 

    [![**Luki w bazach danych SQL w maszynach wirtualnych powinny zostać naprawione (zalecenie podglądu)**](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Pojawi się szczegółowy widok tego zalecenia.

    [![Szczegółowy widok **Luk w bazach danych SQL w maszynach wirtualnych powinien zostać naprawiony (zalecenie Podgląd)**](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Aby przejść do szczegółów, aby uzyskać więcej informacji:

    * Aby uzyskać przegląd zeskanowanych zasobów (baz danych) i listę testowanych kontroli zabezpieczeń, kliknij serwer zainteresowania.
    [![Luki w zabezpieczeniach pogrupowane według serwera SQL](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Aby uzyskać przegląd luk w zabezpieczeniach pogrupowanych według określonej bazy danych SQL, kliknij interesującą bazę danych.
    [![Luki w zabezpieczeniach pogrupowane według serwera SQL](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    W każdym widoku kontrole zabezpieczeń są sortowane według **ważności**. Kliknij określoną kontrolę zabezpieczeń, aby wyświetlić okienko szczegółów z **opisem**, jak go **skorygować** i inne powiązane informacje, takie jak **Wpływ** lub **Benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Zaawansowana ochrona przed zagrożeniami serwerów SQL w alertach maszyn wirtualnych platformy Azure
Alerty są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania serwerów SQL. Te zdarzenia mogą wyzwolić następujące alerty:

### <a name="anomalous-access-pattern-alerts-preview"></a>Alarmy wzorca dostępu nietypowego (wersja zapoznawcza)

* **Dostęp z nietypowej lokalizacji:** Ten alert jest wyzwalany, gdy nastąpi zmiana wzorca dostępu do serwera SQL, gdzie ktoś zalogował się do serwera SQL z nietypowej lokalizacji geograficznej. Potencjalne przyczyny:
    * Osoba atakująca lub były złośliwy zatrudnić ma dostęp do programu SQL Server.
    * Uprawniony użytkownik uzyskał dostęp do programu SQL Server z nowej lokalizacji.
* **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. Potencjalne przyczyny:
    * Osoba atakująca próbująca naruszyć sql przy użyciu typowych narzędzi do ataku.
    * Uzasadnione testy penetracyjne w akcji.
* **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL za pomocą nietypowego podmiotu zabezpieczeń (użytkownika SQL). Potencjalne przyczyny:
    * Osoba atakująca lub były złośliwy zatrudnić ma dostęp do programu SQL Server. 
    * Uprawniony użytkownik uzyskał dostęp do programu SQL Server za pomocą nowego podmiotu.
* **Atak siłowy na poświadczenia SQL**: Ten alert jest wyzwalany w przypadku nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. Potencjalne przyczyny:
    * Osoba atakująca próbująca naruszyć sql przy użyciu brutalnej siły.
    * Uzasadnione testy penetracyjne w akcji.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Potencjalne ataki iniekcji SQL (obsługiwane w programie SQL Server 2019)

* **Luka w zabezpieczeniach iniekcji SQL:** Ten alert jest wyzwalany, gdy aplikacja generuje wadliwą instrukcję SQL w bazie danych. który może oznaczać możliwe luki w zabezpieczeniach umożliwiające ataki przez iniekcję kodu SQL. Potencjalne przyczyny:
    * Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
    * Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
* **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany w przypadku aktywnego wykorzystania zidentyfikowanej luki umożliwiającej iniekcję SQL w aplikacji. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL, korzystając z podatnego kodu aplikacji lub procedur składowanych.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Polecenie Niebezpieczne (obsługiwane w programie SQL Server 2019)

* **Potencjalnie niebezpieczna akcja:** Ten alert jest wyzwalany po wykonaniu wysoce uprzywilejowanego i potencjalnie niebezpiecznego polecenia. Potencjalne przyczyny:
    * Włączone jest polecenie, które zaleca się wyłączenie w celu poprawy postawy zabezpieczeń.
    * Osoba atakująca próbująca wykorzystać uprawnienia dostępu sql lub eskalacji.   


## <a name="explore-and-investigate-security-alerts"></a>Eksplorowanie i badanie alertów zabezpieczeń

Alerty zabezpieczeń danych są dostępne na stronie alertów usługi Security Center, na karcie zabezpieczeń zasobu lub za pośrednictwem bezpośredniego łącza w wiadomościach e-mail z alertami.

1. Aby wyświetlić alerty:

    * W Centrum zabezpieczeń — kliknij pozycję **Alerty zabezpieczeń** z paska bocznego i wybierz alert.
    * W zakresie zasobu — otwórz odpowiednią stronę zasobu, a na pasku bocznym kliknij pozycję **Zabezpieczenia**. 

1. Alerty są zaprojektowane tak, aby były samodzielne, ze szczegółowymi krokami korygucyjnymi i informacjami o badaniu w każdym z nich. Możesz zbadać dalej przy użyciu innych usług Azure Security Center i usługi Azure Sentinel możliwości dla szerszego widoku:

    * Włącz funkcję inspekcji programu SQL Server do dalszych badań. Jeśli jesteś użytkownikiem usługi Azure Sentinel, możesz przekazać dzienniki inspekcji SQL ze zdarzeń dziennika zabezpieczeń systemu Windows do usługi Sentinel i cieszyć się bogatym doświadczeniem badania.
    * Aby poprawić swoją postawę zabezpieczeń, użyj zaleceń centrum zabezpieczeń dla komputera hosta wskazanego w każdym alertie. Zmniejszy to ryzyko przyszłych ataków. 


## <a name="next-steps"></a>Następne kroki

Materiały pokrewne można znaleźć w następującym artykule:

- [Jak korygować zalecenia](security-center-remediate-recommendations.md)