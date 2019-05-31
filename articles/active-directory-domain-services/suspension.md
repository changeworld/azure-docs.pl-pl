---
title: 'Azure Active Directory Domain Services: Zawieszone domeny | Dokumentacja firmy Microsoft'
description: Domena zarządzana zawieszenie i usunięcie
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: a4e43518ce70c040b8243034c52e4b215548a47c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246722"
---
# <a name="suspended-domains"></a>Wstrzymane domeny
Jeśli Azure Active Directory Domain Services (Azure AD DS) jest w stanie zrealizować domeny zarządzanej przez długi czas, umieszcza je w domenie zarządzanej przechodzą w stan wstrzymania. W tym artykule opisano, dlaczego domeny zarządzane są wstrzymywane i jak skorygować wstrzymania domeny.


## <a name="states-your-managed-domain-can-be-in"></a>Stany Twoja domena zarządzana może znajdować się w

![Oś czasu wstrzymania domeny](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Poprzedni element graficzny opisano możliwe stany, które mogą należeć do domeny zarządzanej usług Azure AD DS.

### <a name="running-state"></a>Stanie "Uruchomiona"
Trwa domeny zarządzanej, który jest skonfigurowany prawidłowo i operacyjne regularnie **systemem** stanu.

**Czego można oczekiwać**
* Microsoft regularnie monitorować kondycję swojej domeny zarządzanej.
* Kontrolery domeny dla domeny zarządzanej są poprawkami i regularnie aktualizowane.
* Regularnie zsynchronizowaniu zmian z usługi Azure Active Directory do domeny zarządzanej.
* Regularnie Twórz kopie zapasowe są wykonywane dla domeny zarządzanej.


### <a name="needs-attention-state"></a>Stan "Wymaga uwagi"
Domena zarządzana jest w **wymaga uwagi** stanie, jeśli co najmniej jeden problem wymaga administratorowi na podjęcie działań. Na stronie kondycji domeny zarządzanej zawiera jeden lub więcej alertów, w tym stanie.

Na przykład jeśli restrykcyjne sieciowej grupy zabezpieczeń skonfigurowane dla Twojej sieci wirtualnej, Microsoft może nie można zaktualizować i monitorować Twojej domeny zarządzanej. Ta konfiguracja nieprawidłowy wyzwala alert, który polega na spakowaniu Twojej domeny zarządzanej do stanu "Wymaga uwagi".

Każdy alert ma zestaw czynności rozwiązujących problem. Niektóre alerty są przejściowe i automatycznie rozwiązać przez usługę. Aby rozwiązać niektóre inne alerty, postępując zgodnie z instrukcjami w odpowiednie kroki rozwiązania alertu. Dla niektórych alertów krytycznych należy się z pomocą techniczną firmy Microsoft, aby uzyskać rozwiązanie problemu dotyczącego.

Aby uzyskać więcej informacji, zobacz [jak rozwiązywać problemy z alertami w domenie zarządzanej](troubleshoot-alerts.md).

**Czego można oczekiwać**

W niektórych przypadkach (na przykład w przypadku konfiguracji sieci nieprawidłowy) może być nieosiągalny z kontrolerami domeny dla domeny zarządzanej. Jeśli Twoja domena zarządzana jest w stanie "Wymaga uwagi", firma Microsoft nie gwarantuje, że będzie można monitorowane, poprawkami, zaktualizowane lub kopię w górę w regularnych odstępach czasu.

* Twoja domena zarządzana jest w złej kondycji i monitorowanie kondycji trwającą zatrzymać, dopóki nie zostanie rozwiązany alert.
* Kontrolery domeny dla domeny zarządzanej nie można zastosować poprawki względem jakiegokolwiek lub aktualizowane.
* Zmiany w usłudze Azure Active Directory mogą nie zostać zsynchronizowane z domeną zarządzaną.
* Wykonywanie kopii zapasowych dla domeny zarządzanej może być podjęte, jeśli jest to możliwe.
* Jeśli możesz rozwiązać alerty, które wpływają na Twojej domeny zarządzanej, może być przywracania go w stanie "Uruchomiona".
* Alerty krytyczne są wyzwalane dla problemy z konfiguracją, w którym firma Microsoft jest nie można nawiązać połączenia z kontrolerami domeny. Jeśli takie alerty nie są rozwiązane w ciągu 15 dni, Twoja domena zarządzana jest umieszczany w stanie "Wstrzymane".


### <a name="the-suspended-state"></a>Stanu "Suspended"
Domeny zarządzanej jest umieszczany w **zawieszone** stanu z następujących powodów:

* Jeden lub więcej alertów krytycznych jeszcze nie został rozwiązany w ciągu 15 dni. Alerty krytyczne może być spowodowane błędną konfiguracją które blokuje dostęp do zasobów, które są wymagane przez usługi Azure AD DS.
    * Na przykład alert [AADDS104: Błąd sieci](alert-nsg.md) występuje więcej niż 15 dni nierozpoznane w domenie zarządzanej.
* Brak problemu z rozliczeniem z subskrypcją platformy Azure lub subskrypcji platformy Azure wygasła.

Domeny zarządzane są wstrzymywane podczas firmy Microsoft nie będzie mógł zarządzać, monitorować, patch lub kopię zapasową domeny na bieżąco.

**Czego można oczekiwać**
* Kontrolery domeny dla domeny zarządzanej są cofanie aprowizacji i nie są dostępne w ramach sieci wirtualnej.
* Dostęp do domeny zarządzanej za pośrednictwem Internetu (jeśli jest włączony) Secure LDAP przestanie działać.
* Błędy uwierzytelniania do domeny zarządzanej, logowanie się do maszyn wirtualnych przyłączonych do domeny lub połączenie za pośrednictwem protokołu LDAP/LDAPS można zauważyć.
* Wykonywanie kopii zapasowych dla domeny zarządzanej nie są pobierane.
* Zatrzymuje synchronizacji z usługą Azure AD.

Po rozwiązaniu alertów z domeną zarządzaną przechodzi do stanu "Suspended". Następnie należy się z pomocą techniczną.
Obsługa może przywrócić Twojej domeny zarządzanej, ale istnieje tylko wtedy, gdy kopii zapasowej, która jest mniejszy niż 30 dni.

Domena zarządzana tylko pozostaje w stanie wstrzymania przez 15 dni. Aby odzyskać Twojej domeny zarządzanej, firma Microsoft zaleca niezwłocznie rozwiązać alerty krytyczne.


### <a name="deleted-state"></a>Stan "Usunięta"
Domena zarządzana, która pozostaje w stanie "Wstrzymane" przez 15 dni jest **usunięte**.

**Czego można oczekiwać**
* Usuwane są wszystkie zasoby i wykonywanie kopii zapasowych dla domeny zarządzanej.
* Nie można przywrócić domeny zarządzanej i należy utworzyć nową domenę zarządzanego do użycia usługi Azure AD DS.
* Po jej usunięciu nie są rozliczane dla domeny zarządzanej.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Skąd wiadomo, Twoja domena zarządzana jest zawieszona?
Zostanie wyświetlony [alert](troubleshoot-alerts.md) na stronie programu Azure AD DS Health w witrynie Azure portal, która deklaruje, że domena została wstrzymana. Stan domeny pokazuje również "Suspended".


## <a name="restore-a-suspended-domain"></a>Przywróć wstrzymania domeny
Aby przywrócić domeny, który jest w stanie "Wstrzymane", wykonaj następujące czynności:

1. Przejdź do [strony usługi Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) w witrynie Azure portal.
2. Wybierz domeny zarządzanej.
3. W panelu po lewej stronie wybierz **kondycji**.
4. Wybierz alert. Identyfikator alertu będzie AADDS503 lub AADDS504, w zależności od przyczyny zawieszenia.
5. Wybierz łącze rozwiązania, który znajduje się w alercie. Następnie postępuj zgodnie z instrukcjami, aby rozwiązać alert.

Data ostatniej kopii zapasowej można przywrócić tylko Twojej domeny zarządzanej. Data ostatniej kopii zapasowej jest wyświetlany na stronie kondycji domeny zarządzanej. Wszelkie zmiany, które wystąpiły po ostatniej kopii zapasowej nie zostanie przywrócony. Wykonywanie kopii zapasowych dla domeny zarządzanej, są przechowywane przez maksymalnie 30 dni. Tworzenie kopii zapasowych, które są starsze niż 30 dni zostaną usunięte.


## <a name="next-steps"></a>Kolejne kroki
- [Rozwiąż alerty dotyczące Twojej domeny zarządzanej](troubleshoot-alerts.md)
- [Dowiedz się więcej o usłudze Azure Active Directory Domain Services](overview.md)
- [Skontaktuj się z zespołem produktu](contact-us.md)

## <a name="contact-us"></a>Skontaktuj się z nami
Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [Podziel się opinią lub pomocy technicznej](contact-us.md).
