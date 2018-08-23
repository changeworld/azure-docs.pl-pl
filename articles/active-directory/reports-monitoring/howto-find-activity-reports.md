---
title: Znajdowanie raportów aktywności użytkownika usługi Azure Active Directory w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, gdzie raporty aktywności użytkownika w usłudze Azure Active Directory znajdują się w witrynie Azure portal.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: report-monitor
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 182537d6f07b624f2395f591681ed4596579bde0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054536"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Znajdowanie raportów aktywności w witrynie Azure portal

W tym artykule opisano, jak znajdowanie raportów aktywności użytkownika usługi Azure Active Directory w witrynie Azure portal.

## <a name="activity-and-integrated-app-reports"></a>Działanie i raporty w zintegrowanej aplikacji

Oparte na kontekście raportowania w witrynie Azure portal, istniejące raporty są scalane w jednym widoku. Jeden podstawowy interfejs API udostępnia dane do widoku.

Aby wyświetlić ten widok na **usługi Azure Active Directory** bloku, w obszarze **działania**, wybierz opcję **dzienniki inspekcji**.

![Dzienniki inspekcji](./media/howto-find-activity-reports/482.png "Dzienniki inspekcji")

Następujące raporty i dalszych są skonsolidowane w tym widoku:

* Raport z audytu
* Działania związane z resetowaniem haseł
* Aktywność rejestracji resetowania haseł
* Aktywności grup samoobsługi
* Zmiany nazwy grupy usługi Office 365
* Działanie z aprowizacją kont
* Stan przerzucania hasła
* Błędy aprowizacji kont


Raport użycia aplikacji zostało ulepszone i wchodzi w skład **logowania** widoku. Aby wyświetlić ten widok na **usługi Azure Active Directory** bloku, w obszarze **działania**, wybierz opcję **logowania**.

![Widok logowania](./media/howto-find-activity-reports/483.png "wyświetlić logowania")

**Logowania** widok zawiera wszystkie logowania użytkownika. Aby uzyskać informacje dotyczące użycia aplikacji, można użyć tych informacji. Również wyświetlane informacje o użyciu aplikacji w **aplikacje dla przedsiębiorstw** omówienie w **ZARZĄDZAJ** sekcji.

![Aplikacje dla przedsiębiorstw](./media/howto-find-activity-reports/484.png "aplikacje dla przedsiębiorstw")

## <a name="access-a-specific-report"></a>Dostęp do konkretnego raportu

Mimo że w witrynie Azure portal zapewnia pojedynczy widok, również można sprawdzić konkretne raporty.

### <a name="audit-logs"></a>Dzienniki inspekcji

W odpowiedzi na opinie klientów, w witrynie Azure portal umożliwia filtrowanie zaawansowane uzyskuje dostęp do danych, które chcesz. Jest jeden filtr, można użyć *kategorii działań*, który zawiera różne typy działań logowania w usłudze Azure AD. Aby zawęzić wyniki do czego szukasz, możesz wybrać kategorię.

Na przykład jeśli interesuje Cię tylko działań związanych z resetowaniem haseł, możesz wybrać **samoobsługowego zarządzania hasłami** kategorii. Kategorie, widoczne są oparte na zasób, który pracuje w.  

![Opcje kategorii na stronie dzienniki inspekcji filtrowania](./media/howto-find-activity-reports/06.png "opcje kategorii na stronie dzienniki inspekcji filtrowania")

Kategorie działań:

- Katalog podstawowy
- Samoobsługowe zarządzanie hasłami
- Samoobsługowe zarządzanie grupami
- Aprowizacja kont

### <a name="application-usage"></a>Użycie aplikacji

Aby wyświetlić szczegóły dotyczące użycia aplikacji dla wszystkich aplikacji lub w ramach jednej aplikacji, w obszarze **działania**, wybierz opcję **logowania**. Aby zawęzić wyniki, można filtrować według nazwy użytkownika lub aplikacji.

![Filtr zdarzeń logowania strony](./media/howto-find-activity-reports/07.png "strony filtr zdarzeń logowania")

### <a name="security-reports"></a>Raporty dotyczące zabezpieczeń

#### <a name="azure-ad-anomalous-activity-reports"></a>Raporty usługi Azure AD nietypowych działań.

Raporty zabezpieczeń nietypowego działania usługi Azure AD, i dalszych są skonsolidowane do przedstawienia jeden centralny widok. Ten widok przedstawia wszystkie zdarzenia związane z zabezpieczeniami ryzyka, że usługi Azure AD można wykrywać i raportować.

Poniższej tabeli list usługi Azure AD nietypowych działań raporty dotyczące zabezpieczeń i odpowiednie typy zdarzeń o podwyższonym ryzyku w witrynie Azure portal.

| Raport nietypowych działań w usłudze Azure AD |  Typ zdarzenia o podwyższonym ryzyku ochrony tożsamości|
| :--- | :--- |
| Użytkownicy z ujawnionymi poświadczeniami | Ujawnione poświadczenia |
| Nieregularne działania związane z logowaniem | Niemożliwa podróż do nietypowych lokalizacji |
| Logowania z urządzeń, które mogą być zainfekowane | Logowania z zainfekowanych urządzeń|
| Logowania z nieznanych źródeł | Logowania z anonimowych adresów IP |
| Logowania z adresów IP związanych z podejrzanymi działaniami | Logowania z adresów IP związanych z podejrzanymi działaniami |
| - | Logowania z nieznanych lokalizacji |

Następujące zabezpieczeń usługi Azure AD w nietypowych działań raporty nie są uwzględniane jako zdarzeń o podwyższonym ryzyku w witrynie Azure portal:

* Logowania po wielokrotnych niepowodzeniach
* Logowania z wielu lokalizacji geograficznych

Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](concept-risk-events.md).  


#### <a name="detected-risk-events"></a>Wykrytych ryzykownych zdarzeń

W witrynie Azure portal możesz uzyskać dostęp do raportów dotyczących zdarzeń z ryzykiem wykrytych na **usługi Azure Active Directory** bloku, w obszarze **zabezpieczeń**. Zdarzenia o podwyższonym ryzyku w wykryte są śledzone w następujące raporty:   

- Narażeni użytkownicy
- Ryzykowne logowania

![Raporty dotyczące zabezpieczeń](./media/howto-find-activity-reports/04.png "raporty dotyczące zabezpieczeń")

Aby uzyskać więcej informacji na temat raporty dotyczące zabezpieczeń zobacz:

- [Raport zabezpieczeń dotyczący narażonych w portalu Azure Active Directory użytkowników](concept-user-at-risk.md)
- [Ryzykowne raporcie logowań w portalu Azure Active Directory](concept-risky-sign-ins.md)


Aby wyświetlić **użycia aplikacji** raportu na **usługi Azure Active Directory** bloku, w obszarze **ZARZĄDZAJ**, wybierz opcję **aplikacje dla przedsiębiorstw**, a następnie wybierz **logowania**.


![Raport logowań aplikacji przedsiębiorstwa](./media/howto-find-activity-reports/199.png)

## <a name="next-steps"></a>Kolejne kroki

Omówienie funkcji raportowania można znaleźć w temacie [Raporty w usłudze Azure Active Directory](overview-reports.md).
