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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fab94088d1d54012a955b0663b078d03b13d6299
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624916"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Znajdowanie raportów aktywności w witrynie Azure portal

W tym artykule dowiesz się, jak znajdowanie raportów aktywności użytkownika usługi Azure Active Directory (Azure AD) w witrynie Azure portal.

## <a name="audit-logs-report"></a>Raport dotyczący dzienników inspekcji

Raport dotyczący dzienników inspekcji łączy kilka raportów w całym działania aplikacji w jednym widoku na podstawie kontekstu raportowania. Dostęp do raportu dzienników inspekcji:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz swój katalog w prawym górnym rogu, a następnie wybierz **usługi Azure Active Directory** bloku, w okienku nawigacji po lewej stronie.
3. Wybierz **dzienniki inspekcji** z **działania** części bloku Azure Active Directory. 

    ![Dzienniki inspekcji](./media/howto-find-activity-reports/482.png "Dzienniki inspekcji")

Raport dotyczący dzienników inspekcji konsoliduje następujące raporty:

* Raport z inspekcji
* Działania związane z resetowaniem haseł
* Aktywność rejestracji resetowania haseł
* Aktywności grup samoobsługi
* Zmiany nazwy grupy usługi Office 365
* Działanie z aprowizacją kont
* Stan przerzucania hasła
* Błędy aprowizacji kont

### <a name="filtering-on-audit-logs"></a>Filtrowanie według dzienników inspekcji

Umożliwia filtrowanie Zaawansowane w raporcie inspekcji dostępu do określonej kategorii danych inspekcji, określając ją w **kategorii działań** filtru. Na przykład, aby wyświetlić wszystkie działania związane z samoobsługowego resetowania hasła, wybierz **Samoobsługowe zarządzanie hasłami** kategorii. 

    ![Category options on the Filter Audit Logs page](./media/howto-find-activity-reports/06.png "Category options on the Filter Audit Logs page")

Kategorie działań:

- Katalog podstawowy
- Samoobsługowe zarządzanie hasłami
- Samoobsługowe zarządzanie grupami
- Aprowizacja kont


## <a name="sign-ins-report"></a>Raport logowań 

**Logowania** widok zawiera wszystkie logowania użytkownika, jak również **użycia aplikacji** raportu. Również wyświetlane informacje o użyciu aplikacji w **Zarządzaj** części **aplikacje dla przedsiębiorstw** Przegląd.

    ![Enterprise applications](./media/howto-find-activity-reports/484.png "Enterprise applications")

Dostęp do raportów logowania:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz swój katalog w prawym górnym rogu, a następnie wybierz **usługi Azure Active Directory** bloku, w okienku nawigacji po lewej stronie.
3. Wybierz **plik logowań do** z **działania** części bloku Azure Active Directory. 

    ![Widok logowania](./media/howto-find-activity-reports/483.png "wyświetlić logowania")


### <a name="filtering-on-application-name"></a>Filtrowanie według nazwy aplikacji

W raporcie logowań można użyć, aby wyświetlić szczegóły dotyczące użycia aplikacji przez filtrowanie według nazwy użytkownika lub aplikacji.

![Filtr zdarzeń logowania strony](./media/howto-find-activity-reports/07.png "strony filtr zdarzeń logowania")

## <a name="security-reports"></a>Raporty dotyczące zabezpieczeń

### <a name="anomalous-activity-reports"></a>Raporty nietypowych działań

Nietypowe działanie raporty zawierają informacje dotyczące zdarzeń ryzyka związanych z zabezpieczeniami usługi Azure AD można wykrywać i raportować.

Poniższej tabeli list usługi Azure AD nietypowych działań raporty dotyczące zabezpieczeń i odpowiednie typy zdarzeń o podwyższonym ryzyku w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](concept-risk-events.md).  


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


### <a name="detected-risk-events"></a>Wykrytych ryzykownych zdarzeń

Raporty o zdarzeniach zagrożenia wykryte w **zabezpieczeń** części **usługi Azure Active Directory** bloku [witryny Azure portal](https://portal.azure.com). Zdarzenia o podwyższonym ryzyku w wykryte są śledzone w następujące raporty:   

- [Narażeni użytkownicy](concept-user-at-risk.md)
- [Ryzykowne logowania](concept-risky-sign-ins.md)

    ![Raporty dotyczące zabezpieczeń](./media/howto-find-activity-reports/04.png "raporty dotyczące zabezpieczeń")

## <a name="next-steps"></a>Kolejne kroki

* [Przegląd dzienników inspekcji](concept-audit-logs.md)
* [Omówienie logowania](concept-sign-ins.md)
* [Przegląd ryzykownych zdarzeń](concept-risk-events.md)
