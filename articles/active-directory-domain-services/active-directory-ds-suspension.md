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
ms.date: 07/16/2018
ms.author: ergreenl
ms.openlocfilehash: 5f350bacdc21ec4e5077c93ecc9d97f2fe6c39a5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090913"
---
# <a name="suspended-domains"></a>Wstrzymane domen
Gdy usług domenowych Azure AD nie będzie mógł do obsługi domeny zarządzanej przez długi czas, domena zarządzana jest umieszczana w stanie wstrzymania. Ten artykuł będzie zawierał wyjaśnienie, dlaczego domeny zarządzane są wstrzymywane, długość, zawieszenia i jak skorygować wstrzymania domeny.


## <a name="states-your-managed-domain-can-be-in"></a>Stany Twoja domena zarządzana może znajdować się w

![Oś czasu wstrzymania domeny](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

Poprzedniego rysunku przedstawiono, jak domena jest wstrzymana, jak długo zostanie zawieszona, a ostatecznie, usunięcie domeny zarządzanej. W poniższych sekcjach szczegółowo powodów dlaczego domeny może zostać zawieszone i jak anulować zawieszenie użytkownika w domenie zarządzanej.

### <a name="running-state"></a>Stan "Uruchomiona"
Trwa domeny zarządzanej, który jest skonfigurowany prawidłowo i operacyjne regularnie **systemem** stanu.

**Czego można oczekiwać:**
* Firma Microsoft jest regularnie monitorować kondycję swojej domeny zarządzanej.
* Kontrolery domeny dla domeny zarządzanej są poprawkami i regularnie aktualizowane.
* Regularnie zsynchronizowaniu zmian z usługi Azure Active Directory do domeny zarządzanej.
* Regularnie Twórz kopie zapasowe są wykonywane dla domeny zarządzanej.


### <a name="needs-attention-state"></a>Stan "Wymaga uwagi"
Domena zarządzana jest w **wymaga uwagi** stanie, jeśli co najmniej jeden problem wymaga administratorowi na podjęcie działań. Na stronie kondycji domeny zarządzanej, spowoduje wyświetlenie listy jeden lub więcej alertów, w tym stanie. Na przykład jeśli skonfigurowano restrykcyjne sieciowej grupy zabezpieczeń dla sieci wirtualnej, Microsoft może być nie można zaktualizować i monitorować Twojej domeny zarządzanej. To nieprawidłowa konfiguracja powoduje wygenerowanie alertu, a Twoja domena zarządzana jest umieszczany w stanu wymaga uwagi.

Każdy alert ma zestaw czynności rozwiązujących problem. Niektóre alerty są przejściowe i zostanie automatycznie rozwiązać przez usługę. Aby rozwiązać niektóre inne alerty, postępując zgodnie z instrukcjami w odpowiednie kroki rozwiązania alertu. Aby rozwiązać niektóre alerty krytyczne, należy się z pomocą techniczną firmy Microsoft.

Aby uzyskać więcej informacji, zobacz [jak rozwiązywać problemy z alertami w domenie zarządzanej](active-directory-ds-troubleshoot-alerts.md).

**Czego można oczekiwać:**

W niektórych przypadkach (na przykład w przypadku konfiguracji sieci nieprawidłowy) może być nieosiągalny z kontrolerami domeny dla domeny zarządzanej. W związku z tym firma Microsoft nie gwarantuje, Twoja domena zarządzana jest monitorowane, poprawkami, zaktualizowane lub kopię w górę w regularnych odstępach czasu, w tym stanie.

* Twoja domena zarządzana jest w złej kondycji i kondycji ciągłego monitorowania może mieć wpływ, dopóki nie zostanie rozwiązany alert.
* Kontrolery domeny dla domeny zarządzanej, nie może zastosować poprawki względem jakiegokolwiek lub aktualizowane.
* Nie można zsynchronizować zmiany z usługi Azure Active Directory do domeny zarządzanej.
* Wykonywanie kopii zapasowych dla domeny zarządzanej może zostać podjęta, jeśli jest to możliwe.
* Jeśli możesz rozwiązać alerty wpływające na Twojej domeny zarządzanej, może być możliwe przywrócenie z domeną zarządzaną do stanu "Uruchomiona".
* Alerty krytyczne są wyzwalane dla problemy z konfiguracją, w którym firma Microsoft jest nie można nawiązać połączenia z kontrolerami domeny. Jeśli takie alerty nie są rozpoznawane w ciągu 15 dni, Twoja domena zarządzana będzie umieszczana w stanie "Zawieszono".


### <a name="suspended-state"></a>Stan "Wstrzymane"
Domeny zarządzanej jest umieszczany w **zawieszone** stanu z następujących powodów:
* Nieusuniętych jeden lub więcej alertów krytycznych w ciągu 15 dni. Może to być spowodowane błędną konfiguracją które blokuje dostęp do zasobów wymaganych przez usługi domenowe Azure AD.
    * Na przykład, jeśli domena zarządzana ma alert [AADDS104: błąd sieci](active-directory-ds-troubleshoot-nsg.md) nierozpoznanych przez ponad 15 dni.
* Brak problemu z rozliczeniem z subskrypcją platformy Azure lub jeśli subskrypcja platformy Azure wygasła.

Domeny zarządzane są wstrzymywane podczas firmy Microsoft nie będzie mógł zarządzać, monitorować, patch lub utworzyć kopię zapasową domeny na bieżąco.

**Czego można oczekiwać:**
* Kontrolery domeny dla domeny zarządzanej są cofanie aprowizacji i nie są dostępne w ramach sieci wirtualnej.
* Dostęp do domeny zarządzanej za pośrednictwem Internetu (jeśli jest włączona) Secure LDAP przestanie działać.
* Pojawi się błędy podczas uwierzytelniania do domeny zarządzanej, logowanie do domeny przyłączone do maszyn wirtualnych, łączenie za pośrednictwem protokołu LDAP/LDAPS itp.
* Wykonywanie kopii zapasowych dla domeny zarządzanej nie są pobierane.
* Musisz rozwiązać alert, powodując Twojej domeny zarządzanej być w stanie "Zawieszono", a następnie skontaktuj się z pomocą techniczną.
* Obsługa można przywrócić Twojej domeny zarządzanej, tylko wtedy, gdy istniejącej kopii zapasowej, który jest mniej niż 30 dni.


### <a name="deleted-state"></a>Stan 'Usunięto'
Domena zarządzana, która pozostaje w stanie "Zawieszono" przez 15 dni jest **usunięte**.

**Czego można oczekiwać:**
* Usuwane są wszystkie zasoby i wykonywanie kopii zapasowych dla domeny zarządzanej.
* Nie można przywrócić domeną zarządzaną i konieczne jest utworzenie nowej domeny zarządzanej do korzystania z usług domenowych Azure AD.
* Użytkownik nie jest naliczana dla domeny zarządzanej.


## <a name="what-happens-when-a-managed-domain-is-suspended"></a>Co się stanie, zawieszenia domeny zarządzanej?
Zawieszenia domeny usług domenowych Azure AD zatrzymuje i anulować aprowizację z kontrolerami domeny dla domeny zarządzanej. W rezultacie kopie zapasowe nie są pobierane, użytkownicy nie będą mogli zalogować się do domeny i zatrzymuje synchronizacji z usługą Azure AD.

Domeny zarządzanej, tylko pozostanie w stanie wstrzymania maksymalnie 15 dni. Aby zapewnić czas odzyskiwania, zalecane jest jak najszybciej rozwiązać zawieszenia.


## <a name="how-do-i-know-if-my-managed-domain-is-suspended"></a>Jak wiadomo, jeśli Moja domena zarządzana jest wstrzymana
Zostanie wyświetlony [alert](active-directory-ds-troubleshoot-alerts.md) na stronie kondycji usług domenowych Azure AD w witrynie Azure portal, która deklaruje domeny zawieszone. Ponadto stan domeny pokazuje "Suspended".


## <a name="how-do-i-restore-a-suspended-domain"></a>Jak przywrócić wstrzymania domeny?
Aby przywrócić domeny w stanie "Zawieszono", wykonaj następujące czynności:

1. Przejdź do [strony usług domenowych Azure AD](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) w witrynie Azure portal
2. Kliknij domenę, którą chcesz anulować zawieszenie użytkownika
3. W okienku nawigacji po lewej stronie, kliknij przycisk **kondycji**
4. Kliknij alert. Identyfikator alertu będzie AADDS503 lub AADDS504, w zależności od przyczyny zawieszenia.
5. Kliknij łącze rozwiązania podany w alercie, a następnie postępuj zgodnie z instrukcjami, aby rozwiązać alert.

Data ostatniej kopii zapasowej można przywrócić tylko domenę. Data ostatniej kopii zapasowej jest wyświetlany na stronie kondycji domeny zarządzanej. Wszelkie zmiany, które wystąpiły po ostatniej kopii zapasowej nie zostaną przywrócone. Wykonywanie kopii zapasowych dla domeny zarządzanej, są przechowywane przez maksymalnie 30 dni. Kopie zapasowe starsze niż 30 dni zostaną usunięte.


## <a name="deleting-domains"></a>Usuwanie domen
Jeśli domena jest wstrzymana przez więcej niż 15 dni, usług domenowych Azure AD powoduje usunięcie domeny zarządzanej, z powodu braku aktywności i brak możliwości usługi domeny. Użytkownik nie jest już opłata usług domenowych Azure AD. W tym momencie nie można przywrócić Twojej domeny zarządzanej i trzeba utworzyć ją ponownie.


## <a name="next-steps"></a>Kolejne kroki
- [Rozwiąż alerty w domenie zarządzanej](active-directory-ds-troubleshoot-alerts.md)
- [Więcej informacji na temat usługi Azure AD Domain Services](active-directory-ds-overview.md)
- [Skontaktuj się z zespołem produktu](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Skontaktuj się z nami
Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [Podziel się opinią lub pomocy technicznej](active-directory-ds-contact-us.md).
