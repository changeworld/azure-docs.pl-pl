---
title: 'Usługa Azure Active Directory Domain Services: Zawieszone domeny | Dokumentacja firmy Microsoft'
description: Domena zarządzana zawieszenie i usunięcie
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: ergreenl
ms.openlocfilehash: 37524fbdf3cd521414a507f6fb67421708343ccd
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37934248"
---
# <a name="suspended-domains"></a>Wstrzymane domen
Gdy usług domenowych Azure AD nie będzie mógł do obsługi domeny zarządzanej przez długi czas, domena zarządzana jest umieszczana w stanie wstrzymania. Ten artykuł będzie zawierał wyjaśnienie, dlaczego domeny zarządzane są wstrzymywane, długość, zawieszenia i jak skorygować wstrzymania domeny.


## <a name="overview-of-suspended-domains"></a>Omówienie domen zawieszone

![Oś czasu wstrzymania domeny](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

Poprzedniego rysunku przedstawiono, jak domena jest wstrzymana, jak długo zostanie zawieszona, a ostatecznie, usunięcie domeny zarządzanej. W poniższych sekcjach szczegółowo powodów dlaczego domeny może zostać zawieszone i jak anulować zawieszenie użytkownika w domenie zarządzanej.


## <a name="why-are-managed-domains-suspended"></a>Dlaczego domeny zarządzane są zawieszone

Domeny zarządzane są wstrzymywane, jeśli są w stanie, w których usługi domenowe Azure AD jest w stanie zarządzać domeny. Może to być spowodowane błędną konfiguracją które blokuje dostęp do zasobów wymaganych przez usługi domenowe Azure AD lub nieaktywny subskrypcji platformy Azure. Po 15 dni nie będzie w stanie usługi domeny zarządzanej usług domenowych Azure AD zostanie zawieszone domeny.

Dokładne przyczyny, dlaczego może zostać zawieszone domeny znajdują się poniżej:
* O co najmniej jedną z poniższych alertów obecne w domenie przez 15 dni z rzędu:
   * [AADDS104: Błąd sieci](active-directory-ds-troubleshoot-nsg.md).
* Twoja subskrypcja platformy Azure ważność lub jest nieaktywna


## <a name="what-happens-when-a-domain-is-suspended"></a>Co się stanie, zawieszenia domeny?

Zawieszenia domenę usługi Azure AD Domain Services zatrzymuje maszyny wirtualne, które usługi domeny zarządzanej. Oznacza to, kopie zapasowe nie są pobierane, użytkownicy nie będą mogli zalogować się do domeny i nie jest już wykonywana jest synchronizacja z usługą Azure AD.

Domena, tylko pozostanie w stanie zawieszenia maksymalnie 15 dni. Aby zapewnić czas odzyskiwania, zalecane jest jak najszybciej rozwiązać zawieszenia.

## <a name="how-do-i-know-if-my-domain-is-suspended"></a>Jak sprawdzić, jest zawieszona mojej domeny?
Domena zarządzana będą otrzymywać [alert](active-directory-ds-troubleshoot-alerts.md) na stronie kondycji usług domenowych Azure AD w witrynie Azure portal, która deklaruje domeny zawieszone. Ponadto stan domeny będzie oznakowane "Suspended".


## <a name="unsuspending-and-restoring-domains"></a>Domeny unsuspending i przywracanie

Aby anulować zawieszenie użytkownika domeny, potrzebne są następujące czynności:

1. Przejdź do [strony usług domenowych Azure AD](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) w witrynie Azure portal
2. Kliknij domenę, którą chcesz anulować zawieszenie użytkownika
3. W okienku nawigacji po lewej stronie, kliknij przycisk **kondycji**
4. Kliknij alert zawieszenia (identyfikator alertu będzie AADDS503 lub AADDS504, w zależności od przyczyny zawieszenia).
5. Kliknij łącze rozwiązania podany w alercie, a następnie postępuj zgodnie z instrukcjami w celu rozpoznawania zawieszenia.

Domenę można przywrócić tylko od daty ostatniej kopii zapasowej. Data ostatniej kopii zapasowej jest wyświetlany na stronie kondycji domeny zarządzanej. Wszelkie zmiany wprowadzone od ostatniej kopii zapasowej nie zostaną przywrócone po unsuspension. Ponadto usług domenowych Azure AD można przechowywać kopie zapasowe dla maksymalnie 30 dni. Jeśli najnowszej kopii zapasowej jest więcej niż 30 dni, należy usunąć kopii zapasowej i usług domenowych Azure AD nie będzie można przywrócić z kopii zapasowej.

## <a name="deleting-domains"></a>Usuwanie domen

Jeśli domena jest wstrzymana przez więcej niż 15 dni, usług domenowych Azure AD powoduje usunięcie domeny zarządzanej, z powodu braku aktywności i brak możliwości usługi domeny. Użytkownik nie jest już opłata usług domenowych Azure AD. Aby przywrócić Twojej domeny zarządzanej, należy utworzyć ją ponownie.


## <a name="next-steps"></a>Kolejne kroki
- [Rozwiąż alerty w domenie zarządzanej](active-directory-ds-troubleshoot-alerts.md)
- [Więcej informacji na temat usługi Azure AD Domain Services](active-directory-ds-overview.md)
- [Skontaktuj się z zespołem produktu](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Skontaktuj się z nami

Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [Podziel się opinią lub pomocy technicznej](active-directory-ds-contact-us.md).
