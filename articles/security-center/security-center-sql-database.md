---
title: Usługa Azure Security Center i Azure SQL Database | Microsoft Docs
description: W tym artykule przedstawiono sposób, w jaki Security Center może pomóc w zabezpieczaniu baz danych w programie Azure SQL Database.
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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "60704615"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Usługa Azure Security Center i Azure SQL Database
Usługa [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

W tym artykule przedstawiono sposób, w jaki Security Center może pomóc w zabezpieczaniu baz danych w programie Azure SQL Database.

## <a name="why-use-security-center"></a>Dlaczego warto korzystać z usługi Security Center?
Security Center pomaga chronić dane w SQL Database, zapewniając wgląd w zabezpieczenia wszystkich serwerów i baz danych. Za pomocą Security Center można:

* Zdefiniuj zasady dla SQL Database szyfrowania i inspekcji.
* Monitoruj zabezpieczenia SQL Database zasobów we wszystkich Twoich subskrypcjach.
* Szybkie identyfikowanie i rozwiązywanie problemów z zabezpieczeniami.
* Integruj alerty z [wykrywania Azure SQL Database zagrożeń](../sql-database/sql-database-threat-detection.md).

Oprócz ochrony zasobów SQL Database, Security Center zapewnia również monitorowanie zabezpieczeń i zarządzanie nimi dla maszyn wirtualnych platformy Azure, Cloud Services, App Services, sieci wirtualnych i innych. Dowiedz się więcej o Security Center [tym miejscu](security-center-intro.md).

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Bezpłatna warstwa Security Center jest włączona w ramach subskrypcji. Aby uzyskać więcej informacji o warstwach bezpłatna i standardowa Security Center, zobacz [Cennik usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Security Center obsługuje dostęp oparty na rolach. Aby dowiedzieć się więcej na temat kontroli dostępu opartej na rolach (RBAC) na platformie Azure, zobacz [Azure Active Directory Access Control oparte na rolach](../role-based-access-control/role-assignments-portal.md). Security Center często zadawane pytania zawierają informacje dotyczące [sposobu obsługi uprawnień w programie Security Center](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Dostęp do usługi Security Center
Usługa Security Center jest dostępna w witrynie [Azure Portal](https://azure.microsoft.com/features/azure-portal/). [Zaloguj się do portalu](https://portal.azure.com/) i wybierz **opcję Security Center**.

![Opcja Security Center][1]

Zostanie otwarty blok **Security Center** .
![Security Center blok][2]

## <a name="set-security-policy"></a>Ustawianie zasad zabezpieczeń
Zasady zabezpieczeń definiują zestaw formantów, które są zalecane dla zasobów w ramach określonej subskrypcji lub grupy zasobów. W Security Center definiujesz zasady dla subskrypcji lub grup zasobów zgodnie z potrzebami zabezpieczeń firmy i typem aplikacji lub poufnością danych w poszczególnych subskrypcjach.

Można ustawić zasady, aby wyświetlić zalecenia dotyczące inspekcji SQL i szyfrowania danych (transparental Data Encryption, TDE).

* Po włączeniu **inspekcji SQL i wykrywania zagrożeń**Security Center zaleca się, aby Inspekcja dostępu do bazy danych Azure była włączona w celu zapewnienia zgodności, zaawansowanego wykrywania i testowania.
* Po włączeniu przezroczystego **szyfrowania danych w programie SQL**Security Center zaleca się włączenie szyfrowania dla Azure SQL Database, skojarzonych kopii zapasowych i plików dziennika transakcji.

Aby ustawić zasady zabezpieczeń, wybierz kafelek **zasady** w bloku Security Center. W bloku **zasady zabezpieczeń** wybierz subskrypcję, dla której chcesz włączyć zasady zabezpieczeń. Wybierz pozycję **zasady zapobiegania** i Włącz zalecenia dotyczące zabezpieczeń, których chcesz użyć w ramach tej subskrypcji.
![Zasady zabezpieczeń][3]

Aby dowiedzieć się więcej, zobacz [Ustawianie zasad zabezpieczeń](tutorial-security-policy.md).

## <a name="manage-security-recommendation"></a>Zarządzanie zaleceniem zabezpieczeń
Usługa Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure. Po znalezieniu potencjalnych luk w zabezpieczeniach usługa Security Center tworzy odpowiednie zalecenia. Przewodnik dotyczący zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych kontrolek.

Po ustawieniu zasad zabezpieczeń Security Center analizuje stan zabezpieczeń zasobów, aby zidentyfikować potencjalne luki w zabezpieczeniach. Zalecenia są wyświetlane w formacie tabeli, w którym każdy wiersz reprezentuje jedno z określonych zaleceń. Poniższa tabela zawiera informacje ułatwiające zrozumienie dostępnych zaleceń dotyczących Azure SQL Database i jakie każde zalecenie ma zastosowanie w przypadku zastosowania go. Wybranie zalecenia spowoduje przejście do artykułu, w którym wyjaśniono, jak wdrożyć zalecenie w Security Center.

| Zalecenie | Opis |
| --- | --- |
| [Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL](security-center-enable-auditing-on-sql-servers.md) |Zaleca włączenie inspekcji i wykrywania zagrożeń dla serwerów SQL Database. (Tylko SQL Database usługi. Nie zawiera Microsoft SQL Server uruchomionych na maszynach wirtualnych.) |
| [Włącz inspekcję i wykrywanie zagrożeń w bazach danych SQL](security-center-enable-auditing-on-sql-databases.md) |Zaleca włączenie inspekcji i wykrywania zagrożeń dla baz danych SQL Database. (Tylko SQL Database usługi. Nie zawiera Microsoft SQL Server uruchomionych na maszynach wirtualnych.) |
| [Włączanie technologii Transparent Data Encryption](security-center-enable-transparent-data-encryption.md) |Zaleca włączenie szyfrowania dla baz danych SQL. (Tylko SQL Database usługi). |

Aby zapoznać się z zaleceniami dotyczącymi zasobów platformy Azure, wybierz kafelek **zalecenia** w bloku Security Center. W bloku **zalecenia** wybierz zalecenie, aby wyświetlić szczegóły. W tym przykładzie Wybierzmy pozycję **Włącz inspekcję & wykrywanie zagrożeń na serwerach SQL**.

![Zalecenia][4]

Jak pokazano poniżej, Security Center przedstawia serwery SQL, na których nie włączono inspekcji i wykrywania zagrożeń. Po włączeniu inspekcji można skonfigurować ustawienia wykrywania zagrożeń i ustawienia poczty e-mail w celu otrzymywania alertów zabezpieczeń. Wykrywanie zagrożeń powiadamia użytkownika o wykryciu nietypowych działań bazy danych wskazujących na potencjalne zagrożenia bezpieczeństwa bazy danych. Alerty są wyświetlane na pulpicie nawigacyjnym Security Center.
![Inspekcja i wykrywanie zagrożeń][5]

Wykonaj kroki opisane w temacie [SQL Database wykrywanie zagrożeń w Azure Portal](../sql-database/sql-database-threat-detection-portal.md) , aby włączyć i skonfigurować wykrywanie zagrożeń oraz skonfigurować listę wiadomości e-mail, które będą otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań.

Aby dowiedzieć się więcej na temat zaleceń, zobacz [Zarządzanie zaleceniami](security-center-recommendations.md)dotyczącymi zabezpieczeń.

## <a name="monitor-security-health"></a>Monitoruj kondycję zabezpieczeń
Po włączeniu [zasad zabezpieczeń](tutorial-security-policy.md) dla zasobów subskrypcji Centrum zabezpieczeń będzie analizować zabezpieczenia zasobów, aby zidentyfikować potencjalne luki.  Stan zabezpieczeń zasobów można wyświetlić na kafelku **kondycja zabezpieczeń zasobów** . Po kliknięciu przycisku **dane** na kafelku **kondycja zabezpieczeń zasobów** zostanie otwarty blok **zasoby danych** z zaleceniami SQL dotyczącymi problemów, takich jak Inspekcja i przezroczyste szyfrowanie danych, które nie są włączone. Przedstawia on także zalecenia dotyczące ogólnej kondycji bazy danych.
![Kondycja zabezpieczeń zasobów][6]

Aby dowiedzieć się więcej, zobacz [monitorowanie kondycji zabezpieczeń](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Zarządzaj alertami zabezpieczeń i reaguj na nie
Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z [wykrywania zagrożeń usługi Azure SQL](../sql-database/sql-database-threat-detection.md), a także inne zasoby platformy Azure, aby wykrywać prawdziwe zagrożenia i zmniejszać liczbę fałszywych pozytywnych. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku.

Aby wyświetlić alerty, wybierz kafelek **alerty zabezpieczeń** w bloku Security Center. W bloku **alerty zabezpieczeń** wybierz alert, aby dowiedzieć się więcej o zdarzeniach, które wyzwoliły alert, oraz informacje o tym, co należy zrobić, aby skorygować atak. W tym przykładzie wybierzemy potencjalną **iniekcję kodu SQL**.
![Alerty zabezpieczeń][7]

Jak pokazano poniżej, Security Center zawiera dodatkowe szczegóły, które oferują wgląd w to, co wyzwolił alert, zasób docelowy, gdy ma zastosowanie źródłowy adres IP i zalecenia dotyczące sposobu korygowania.
![Potencjalna iniekcja SQL][8]

Aby dowiedzieć się więcej, zobacz temat [Zarządzanie alertami zabezpieczeń i reagowanie na](security-center-managing-and-responding-alerts.md)nie.

## <a name="next-steps"></a>Następne kroki
* [Security Center często zadawane](security-center-faq.md) pytania — Znajdź często zadawane pytania dotyczące korzystania z usługi.
* [Security Center Przewodnik planowania i](security-center-planning-and-operations-guide.md) obsługi — postępuj zgodnie z zestawem kroków i zadań, aby zoptymalizować użycie Security Center na podstawie wymagań dotyczących zabezpieczeń i modelu zarządzania chmurą w organizacji.
* [Security Center zabezpieczenia danych](security-center-data-security.md) — Dowiedz się, jak Security Center zbiera i przetwarza dane dotyczące zasobów platformy Azure, w tym informacje o konfiguracji, metadane, dzienniki zdarzeń, pliki zrzutu awaryjnego i inne.
* [Obsługa zdarzeń](security-center-incident.md) związanych z zabezpieczeniami — Dowiedz się, jak korzystać z funkcji alertu zabezpieczeń w Security Center, aby pomóc w obsłudze zdarzeń związanych z bezpieczeństwem.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
