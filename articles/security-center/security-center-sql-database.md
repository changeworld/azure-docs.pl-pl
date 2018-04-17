---
title: Usługa Centrum zabezpieczeń Azure i usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak Centrum zabezpieczeń można zabezpieczyć baz danych w bazie danych SQL Azure.
services: sql-database
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: b507a62db9a80866005cb63d2008fb14612b516f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Usługa Centrum zabezpieczeń Azure i bazy danych SQL Azure
Usługa [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

W tym artykule opisano, jak Centrum zabezpieczeń można zabezpieczyć baz danych w bazie danych SQL Azure.

## <a name="why-use-security-center"></a>Dlaczego warto korzystać z usługi Security Center?
Centrum zabezpieczeń pomaga chronić dane w bazie danych SQL, zapewniając wgląd w zabezpieczenia serwerów i baz danych. Centrum zabezpieczeń można:

* Definiowanie zasad szyfrowania bazy danych SQL i inspekcji.
* Monitorowanie zabezpieczeń zasobów bazy danych SQL za pośrednictwem wszystkich subskrypcji.
* Szybkie identyfikowanie i Korygowanie problemów z zabezpieczeniami.
* Integracja alertów z [wykrywanie zagrożeń bazy danych SQL Azure](../sql-database/sql-database-threat-detection.md).

Oprócz ochrony zasobów bazy danych SQL, Centrum zabezpieczeń zawiera także monitorowanie zabezpieczeń i zarządzanie nim dla maszyn wirtualnych platformy Azure, usługi w chmurze, aplikacji, usług i sieci wirtualnych. Dowiedz się więcej na temat Centrum zabezpieczeń [tutaj](security-center-intro.md).

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Warstwa bezpłatna Centrum zabezpieczeń jest włączone w ramach subskrypcji. Aby uzyskać więcej informacji na wolne Centrum zabezpieczeń i warstwy standardowa, zobacz [cennik Centrum zabezpieczeń](https://azure.microsoft.com/pricing/details/security-center/).

Centrum zabezpieczeń obsługuje dostęp opartej na rolach. Aby dowiedzieć się więcej na temat kontroli dostępu opartej na rolach (RBAC) na platformie Azure, zobacz [kontroli dostępu opartej na roli Azure Active Directory](../role-based-access-control/role-assignments-portal.md). Często zadawane pytania z Centrum zabezpieczeń zawiera informacje na temat [obsługi uprawnienia w Centrum zabezpieczeń](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Dostęp do usługi Security Center
Usługa Security Center jest dostępna w witrynie [Azure Portal](https://azure.microsoft.com/features/azure-portal/). [Zaloguj się do portalu](https://portal.azure.com/) i wybierz **opcji Centrum zabezpieczeń**.

![Opcja Centrum zabezpieczeń][1]

**Centrum zabezpieczeń** zostanie otwarty blok.
![Blok Centrum zabezpieczeń][2]

## <a name="set-security-policy"></a>Ustawianie zasad zabezpieczeń
Zasady zabezpieczeń określają zestaw kontrolek, które są zalecane dla zasobów w określonej subskrypcji lub grupy zasobów. W Centrum zabezpieczeń można zdefiniować zasady dla subskrypcji lub grupy zasobów zgodnie z potrzebami zabezpieczeń firmy i typem aplikacji oraz poufności danych w każdej subskrypcji.

Można ustawić zasady, aby wyświetlić zalecenia dotyczące inspekcji SQL i niewidoczne szyfrowanie danych SQL (funkcji TDE).

* Po ponownym włączeniu **inspekcję SQL i wykrywania zagrożeń**, Centrum zabezpieczeń zaleca przeprowadzanie inspekcji dostępu do bazy danych Azure włączenia dla zapewnienia zgodności, zaawansowanego wykrywania i badania celów.
* Po ponownym włączeniu **niewidoczne szyfrowanie danych SQL**, że szyfrowanie magazynowane zaleca Centrum zabezpieczeń jest włączone dla bazy danych SQL Azure, skojarzonych kopii zapasowych i plików dziennika transakcji.

Aby skonfigurować zasady zabezpieczeń, wybierz **zasad** kafelka w bloku Centrum zabezpieczeń. Na **zasady zabezpieczeń** bloku, wybierz subskrypcję, na którym chcesz włączyć zasady zabezpieczeń. Wybierz **zasady zapobiegania** i włączyć **na** zalecenia dotyczące zabezpieczeń, które chcesz użyć dla tej subskrypcji.
![Zasady zabezpieczeń][3]

Aby dowiedzieć się więcej, zobacz [ustawiania zasad zabezpieczeń](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Zarządzanie zalecana ze względów bezpieczeństwa
Usługa Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure. Po znalezieniu potencjalnych luk w zabezpieczeniach usługa Security Center tworzy odpowiednie zalecenia. Przewodnik dotyczący zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych kontrolek.

Po ustawieniu zasad zabezpieczeń Centrum zabezpieczeń analizuje stan zabezpieczeń zasobów, aby zidentyfikować potencjalne luki w zabezpieczeniach. Zalecenia są wyświetlane w formacie tabeli, w którym każdy wiersz zawiera jeden zaleceń. Aby lepiej zrozumieć dostępne zalecenia dotyczące bazy danych SQL Azure, a każde zalecenie działania w przypadku zastosowania, skorzystaj z poniższej tabeli jako odwołanie. Wybieranie zaleceń prowadzi do artykułu, w którym opisano sposób wykonania zalecenia w Centrum zabezpieczeń.

| Zalecenie | Opis |
| --- | --- |
| [Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL](security-center-enable-auditing-on-sql-servers.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń dla serwerów baz danych SQL. (Tylko w przypadku usługi baza danych SQL. Nie obejmuje programu Microsoft SQL Server uruchomiony na maszynach wirtualnych). |
| [Włączanie inspekcji i wykrywania zagrożeń baz danych SQL](security-center-enable-auditing-on-sql-databases.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń dla baz danych SQL Database. (Tylko w przypadku usługi baza danych SQL. Nie obejmuje programu Microsoft SQL Server uruchomiony na maszynach wirtualnych). |
| [Włączanie technologii Transparent Data Encryption](security-center-enable-transparent-data-encryption.md) |Zaleca się, aby włączyć szyfrowanie dla bazy danych SQL. (Tylko usługa SQL Database). |

Aby wyświetlić zalecenia dotyczące zasobów platformy Azure, wybierz **zalecenia** kafelka w bloku Centrum zabezpieczeń. Na **zalecenia** bloku, wybierz zalecenie, aby wyświetlić szczegóły. W tym przykładzie załóżmy wybierz **Włączanie inspekcji i wykrywanie zagrożeń na serwerach SQL**.

![Zalecenia][4]

Jak pokazano poniżej, Centrum zabezpieczeń zawiera serwerów SQL, których nie włączono inspekcji i wykrywania zagrożeń. Po włączeniu inspekcji można skonfigurować ustawienia wykrywanie zagrożeń i ustawienia poczty e-mail, aby otrzymywać alerty zabezpieczeń. Wykrywanie zagrożeń alerty po wykryciu nietypowe działania bazy danych, które wskazują możliwe zagrożenia bezpieczeństwa bazy danych. Alerty są wyświetlane na pulpicie nawigacyjnym Centrum zabezpieczeń.
![Inspekcja i wykrywanie zagrożeń][5]

Postępuj zgodnie z instrukcjami [wykrywanie zagrożeń bazy danych SQL w portalu Azure](../sql-database/sql-database-threat-detection-portal.md) włączać i konfigurować wykrywanie zagrożeń i skonfigurować listę wiadomości e-mail, które będą wysyłane alerty zabezpieczeń po wykryciu nietypowych działań.

Aby dowiedzieć się więcej na temat zalecenia, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Monitoruj kondycję zabezpieczeń
Po włączeniu [zasad zabezpieczeń](security-center-policies.md) dla zasobów subskrypcji Centrum zabezpieczeń będzie analizować zabezpieczenia zasobów, aby zidentyfikować potencjalne luki.  Można wyświetlić stan zabezpieczeń zasobów w **kondycja zabezpieczeń zasobów** kafelka. Po kliknięciu **danych** w **kondycja zabezpieczeń zasobów** Kafelek, **zasobów danych** zostanie otwarty blok z SQL zalecenia dotyczące problemów, takich jak dane inspekcji i przezroczyste nie włączono szyfrowania. Przedstawia on także zalecenia dotyczące ogólnej kondycji bazy danych.
![Kondycja zabezpieczeń zasobów][6]

Aby dowiedzieć się więcej, zobacz [monitorowanie kondycji zabezpieczeń](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Zarządzanie alertami zabezpieczeń i reagowanie na nie
Centrum zabezpieczeń automatycznie gromadzi, analizuje i integruje dane dzienników z [wykrywanie zagrożeń SQL Azure](../sql-database/sql-database-threat-detection.md)oraz innych zasobów platformy Azure, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku.

Aby wyświetlić alerty, wybierz **alerty zabezpieczeń** kafelka w bloku Centrum zabezpieczeń. Na **alerty zabezpieczeń** bloku, wybierz alert, aby więcej informacji na temat zdarzeń, które wywołały oraz co, jeśli kroki, możesz konieczne skorygować atak. W tym przykładzie załóżmy wybierz **iniekcja kodu SQL potencjalne**.
![Alerty zabezpieczeń][7]

Jak pokazano poniżej, Centrum zabezpieczeń zawiera dodatkowe szczegóły, które oferują przyczyn wyzwolenia alertu, zasobu docelowego, jeśli ma to zastosowanie źródłowego adresu IP i zaleceń dotyczących sposobu rozwiązania.
![Potencjalne iniekcja kodu SQL][8]

Aby dowiedzieć się więcej, zobacz [reagowanie na alerty zabezpieczeń i zarządzanie nimi](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Kolejne kroki
* [Często zadawane pytania dotyczące Centrum zabezpieczeń](security-center-faq.md) — często zadawane pytania dotyczące korzystania z usługi wyszukiwania.
* [Przewodnik dotyczący planowania i operacji Centrum zabezpieczeń](security-center-planning-and-operations-guide.md) — Wykonaj zestaw kroków i zadań, aby zoptymalizować korzystanie z Centrum zabezpieczeń w oparciu o wymagania dotyczące zabezpieczeń oraz modelu zarządzania chmurą w organizacji.
* [Bezpieczeństwo danych Centrum zabezpieczeń](security-center-data-security.md) — Dowiedz się, jak Centrum zabezpieczeń zbiera i przetwarza dane dotyczące zasobów platformy Azure, w tym informacje o konfiguracji, metadane, dzienniki zdarzeń i pliki zrzutu awaryjnego.
* [Obsługi zdarzeń zabezpieczeń](security-center-incident.md) — używanie możliwości alertów zabezpieczeń w Centrum zabezpieczeń, aby pomóc w obsługi zdarzeń zabezpieczeń.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
