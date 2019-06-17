---
title: Usługa Azure Security Center i Azure SQL Database | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak usługa Security Center może pomóc w ochronie baz danych w usłudze Azure SQL Database.
services: sql-database
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rkarlin
ms.openlocfilehash: 0a889de79b6a5921007614dac8d610c1be0222d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60704615"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Usługa Azure Security Center i Azure SQL Database
Usługa [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

W tym artykule pokazano, jak usługa Security Center może pomóc w ochronie baz danych w usłudze Azure SQL Database.

## <a name="why-use-security-center"></a>Dlaczego warto korzystać z usługi Security Center?
Usługa Security Center pomaga chronić dane w bazie danych SQL, zapewniając wgląd w zabezpieczenia serwerów i baz danych. Usługa Security Center możesz wykonywać następujące czynności:

* Definiuj zasady szyfrowania bazy danych SQL i inspekcji.
* Monitorowanie zabezpieczeń zasobów bazy danych SQL dla wszystkich swoich subskrypcji.
* Szybkie identyfikowanie i rozwiązywanie problemów dotyczących bezpieczeństwa.
* Integruj alerty z [wykrywania zagrożeń usługi Azure SQL Database](../sql-database/sql-database-threat-detection.md).

Oprócz wspomagania ochrony zasobów bazy danych SQL, Security Center zapewnia także monitorowanie zabezpieczeń i zarządzania dla maszyn wirtualnych platformy Azure, usług Cloud Services, App Services i sieciami wirtualnymi. Dowiedz się więcej o usłudze Security Center [tutaj](security-center-intro.md).

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Usługa Security Center w warstwie bezpłatna jest włączona w ramach subskrypcji. Aby uzyskać więcej informacji na temat Centrum zabezpieczeń warstwami bezpłatna i standardowa, zobacz [cennik usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Usługa Security Center obsługuje dostępu opartej na rolach. Aby dowiedzieć się więcej na temat kontroli dostępu opartej na rolach (RBAC) na platformie Azure, zobacz [kontroli dostępu opartej na roli Azure Active Directory](../role-based-access-control/role-assignments-portal.md). Często zadawane pytania dla Centrum zabezpieczeń zawiera informacje na temat [jak uprawnienia są obsługiwane w usłudze Security Center](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Dostęp do usługi Security Center
Usługa Security Center jest dostępna w witrynie [Azure Portal](https://azure.microsoft.com/features/azure-portal/). [Zaloguj się do portalu](https://portal.azure.com/) i wybierz **opcji usługi Security Center**.

![Opcja usługi Security Center][1]

**Usługi Security Center** zostanie otwarty blok.
![Blok Centrum zabezpieczeń][2]

## <a name="set-security-policy"></a>Ustawianie zasad zabezpieczeń
Zasady zabezpieczeń określają zestaw mechanizmów kontrolnych, które są zalecane dla zasobów w ramach określonej subskrypcji lub grupy zasobów. W usłudze Security Center możesz zdefiniować zasady dla subskrypcji lub grupy zasobów zgodnie z potrzebami zabezpieczeń firmy i typem aplikacji oraz poufnością danych w poszczególnych subskrypcjach.

Można ustawić zasady, aby wyświetlić zalecenia dotyczące inspekcji SQL i niewidoczne szyfrowanie danych SQL (TDE).

* Po ponownym włączeniu **SQL inspekcji i wykrywania zagrożeń**, usługa Security Center zaleca Inspekcja dostępu do bazy danych Azure była włączona dla zapewnienia zgodności, zaawansowanego wykrywania i na potrzeby analizy.
* Po ponownym włączeniu **niewidoczne szyfrowanie danych SQL**, usługa Security Center zaleca, aby funkcja szyfrowania można włączyć dla usługi Azure SQL Database, skojarzonych kopii zapasowych i plików dziennika transakcji.

Aby ustawić zasady zabezpieczeń, wybierz **zasad** Kafelek do bloku usługi Security Center. Na **zasady zabezpieczeń** bloku, wybierz subskrypcję, dla którego chcesz włączyć zasady zabezpieczeń. Wybierz **zasady zapobiegania** i włączyć **na** zalecenia dotyczące zabezpieczeń, które chcesz użyć dla tej subskrypcji.
![Zasady zabezpieczeń][3]

Aby dowiedzieć się więcej, zobacz [Ustawianie zasad zabezpieczeń](tutorial-security-policy.md).

## <a name="manage-security-recommendation"></a>Zarządzanie zalecenie dotyczące zabezpieczeń
Usługa Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure. Po znalezieniu potencjalnych luk w zabezpieczeniach usługa Security Center tworzy odpowiednie zalecenia. Przewodnik dotyczący zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych kontrolek.

Po ustawieniu zasad zabezpieczeń usługa Security Center analizuje stan zabezpieczeń zasobów w celu identyfikowania potencjalnych luk w zabezpieczeniach. Zalecenia są wyświetlane w formacie tabeli, gdzie każdy wiersz reprezentuje jedno zalecenie. Skorzystaj z poniższej tabeli jako odwołanie, aby lepiej zrozumieć dostępne zalecenia dotyczące usługi Azure SQL Database, a każde zalecenie, działanie w przypadku zastosowania. Wybranie zalecenia spowoduje przeniesienie użytkownika do artykułu, który wyjaśnia sposób realizacji zalecenia w usłudze Security Center.

| Zalecenie | Opis |
| --- | --- |
| [Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL](security-center-enable-auditing-on-sql-servers.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń na serwerach bazy danych SQL. (Tylko w przypadku usługi SQL Database. Nie obejmują programu Microsoft SQL Server uruchomionej na maszynach wirtualnych.) |
| [Włączanie inspekcji i wykrywania zagrożeń w bazach danych SQL](security-center-enable-auditing-on-sql-databases.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń dla baz danych SQL Database. (Tylko w przypadku usługi SQL Database. Nie obejmują programu Microsoft SQL Server uruchomionej na maszynach wirtualnych.) |
| [Włączanie technologii Transparent Data Encryption](security-center-enable-transparent-data-encryption.md) |Zaleca włączenie szyfrowania baz danych SQL. (Tylko usługa SQL Database). |

Aby wyświetlić zalecenia dotyczące zasobów platformy Azure, wybierz **zalecenia** Kafelek do bloku usługi Security Center. Na **zalecenia** bloku, wybierz zalecenie, aby wyświetlić szczegóły. W tym przykładzie wybierzemy **Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL**.

![Zalecenia][4]

Jak pokazano poniżej, usługa Security Center pokazuje serwery SQL, w którym nie włączono inspekcji i wykrywania zagrożeń. Po włączeniu inspekcji można skonfigurować ustawień wykrywania zagrożeń i ustawienia poczty e-mail, aby otrzymywać alerty zabezpieczeń. Wykrywanie zagrożeń ostrzega o tym, gdy wykryje nietypowe działania bazy danych, które wskazują potencjalne zagrożenia bezpieczeństwa w bazie danych. Alerty są wyświetlane na pulpicie nawigacyjnym usługi Security Center.
![Inspekcja i wykrywanie zagrożeń][5]

Postępuj zgodnie z instrukcjami w [wykrywania zagrożeń bazy danych SQL w witrynie Azure portal](../sql-database/sql-database-threat-detection-portal.md) można włączać i konfigurować wykrywanie zagrożeń i skonfigurować listę adresów e-mail, które będą otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań.

Aby dowiedzieć się więcej o zaleceniach, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Monitoruj kondycję zabezpieczeń
Po włączeniu [zasad zabezpieczeń](tutorial-security-policy.md) dla zasobów subskrypcji Centrum zabezpieczeń będzie analizować zabezpieczenia zasobów, aby zidentyfikować potencjalne luki.  Można wyświetlić stan zabezpieczeń zasobów w **kondycja zabezpieczeń zasobów** kafelka. Po kliknięciu **danych** w **kondycja zabezpieczeń zasobów** kafelka **zasoby danych** zostanie otwarty blok zawierający zalecenia dotyczące SQL w przypadku problemów, takich jak dane inspekcji lub przezroczystego nie włączono szyfrowanie. Przedstawia on także zalecenia dotyczące ogólnej kondycji bazy danych.
![Kondycja zabezpieczeń zasobów][6]

Aby dowiedzieć się więcej, zobacz [monitorowanie kondycji zabezpieczeń](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Zarządzanie alertami zabezpieczeń i reagowanie na nie
Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z [wykrywania zagrożeń SQL Azure](../sql-database/sql-database-threat-detection.md), jak również innych zasobów platformy Azure, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku.

Aby wyświetlić alerty, wybierz **alerty zabezpieczeń** Kafelek do bloku usługi Security Center. Na **alerty zabezpieczeń** bloku, wybierz alert, aby więcej informacji na temat zdarzeń, które wywołały oraz co, jeśli istnieją, prowadzi należy wykonać w celu wyeliminowania skutków ataku. W tym przykładzie wybierzemy **wstrzyknięcie kodu SQL potencjalne**.
![Alerty zabezpieczeń][7]

Jak pokazano poniżej, usługa Security Center zapewnia dodatkowe informacje, które oferują wgląd w przyczyny ich wyzwolenia alertu, zasób docelowy, jeśli ma to zastosowanie źródłowy adres IP oraz zalecenia dotyczące sposobu rozwiązania.
![Potencjalna iniekcja SQL][8]

Aby dowiedzieć się więcej, zobacz [reagowanie na alerty zabezpieczeń i zarządzanie nimi](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Kolejne kroki
* [Usługa Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Przewodnik planowania i obsługi Centrum zabezpieczeń](security-center-planning-and-operations-guide.md) — Wykonaj zestaw kroków i zadań, aby zoptymalizować korzystanie z usługi Security Center w oparciu wymagań dotyczących zabezpieczeń oraz modelu zarządzania chmurą w organizacji.
* [Security Center — bezpieczeństwo danych](security-center-data-security.md) — Dowiedz się, jak usługa Security Center zbiera i przetwarza dane dotyczące zasobów platformy Azure, w tym informacje o konfiguracji, metadane, dzienniki zdarzeń i pliki zrzutu awaryjnego.
* [Obsługa zdarzeń naruszenia zabezpieczeń](security-center-incident.md) — informacje o sposobie użycia funkcji alertu zabezpieczeń w usłudze Security Center, aby ułatwić obsługę zdarzeń naruszenia zabezpieczeń.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
