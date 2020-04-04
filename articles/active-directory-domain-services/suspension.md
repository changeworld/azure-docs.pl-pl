---
title: Zawieszone domeny w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się więcej o różnych stanach kondycji domeny zarządzanej usług Azure AD DS i o tym, jak przywrócić zawieszoną domenę.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 42b26911c12b1e7c62444a6fb2ee68720b02a56b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654606"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Opis stanów kondycji i rozwiązywania problemów z zawieszonymi domenami w usługach domenowych Usługi domenowe Active Directory platformy Azure

Gdy usługi domenowe Usługi domenowe Active Directory platformy Azure (Usługi Azure AD DS) nie mogą obsługiwać domeny zarządzanej przez długi okres czasu, domena zarządzana jest zawieszona. Jeśli domena zarządzana pozostaje w stanie wstrzymania, zostanie automatycznie usunięta. Aby utrzymać domenę zarządzaną usługą Azure AD DS w dobrej kondycji i uniknąć zawieszenia, rozwiąż wszystkie alerty tak szybko, jak to możliwe.

Z tego artykułu dowiesz się, dlaczego domeny zarządzane są zawieszone i jak odzyskać zawieszoną domenę.

## <a name="overview-of-managed-domain-states"></a>Omówienie stanów domeny zarządzanej

Za pośrednictwem cyklu życia domeny zarządzanej usług Azure AD DS istnieją różne stany, które wskazują jego kondycję. Jeśli domena zarządzana zgłasza problem, szybko rozwiąż przyczynę, aby zatrzymać stan przed dalszym pogarszaniem się.

![Postęp stanów, które domena zarządzana usługą Azure AD DS ma w kierunku zawieszenia](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Domena zarządzana usługą Azure AD DS może znajdować się w jednym z następujących stanów:

* [Uruchomiono](#running-state)
* [Wymaga uwagi](#needs-attention-state)
* [Suspended](#suspended-state)
* [Usunięta](#deleted-state)

## <a name="running-state"></a>Stan uruchomienia

Domena zarządzana usługą Azure AD DS, która jest poprawnie skonfigurowana i działa bez problemów, jest w stanie *uruchomionym.* Jest to żądany stan dla domeny zarządzanej.

### <a name="what-to-expect"></a>Czego się spodziewać

* Platforma Azure może regularnie monitorować kondycję domeny zarządzanej.
* Kontrolery domeny dla domeny zarządzanej są regularnie poprawiane i aktualizowane.
* Zmiany z usługi Azure Active Directory są regularnie synchronizowane z domeną zarządzaną.
* Regularne kopie zapasowe są pobierane dla domeny zarządzanej.

## <a name="needs-attention-state"></a>Wymaga stanu uwagi

Domena zarządzana usługą Azure AD DS z co najmniej jednym problemem, które należy rozwiązać, jest w stanie *Wymaga uwagi.* Strona kondycji domeny zarządzanej zawiera listę alertów i wskazuje, gdzie występuje problem. Niektóre alerty są przejściowe i są automatycznie rozpoznawane przez platformę Azure. W przypadku innych alertów można rozwiązać ten problem, wykonując podane kroki rozwiązywania problemów. Jest to alert krytyczny, [otwórz żądanie pomocy technicznej platformy Azure][azure-support] w celu uzyskania dodatkowej pomocy w rozwiązywaniu problemów.

Jednym z przykładów alertu jest, gdy istnieje restrykcyjna grupa zabezpieczeń sieci. W tej konfiguracji platforma Azure może nie być w stanie zaktualizować i monitorować domeny zarządzanej. Alert jest generowany, a stan zmienia się *na Wymaga uwagi*.

Aby uzyskać więcej informacji, zobacz [Jak rozwiązywać problemy z alertami dotyczącymi domeny zarządzanej usług Azure AD DS][resolve-alerts].

### <a name="what-to-expect"></a>Czego się spodziewać

Gdy domena zarządzana usługą Azure AD DS jest w stanie *wymaga uwagi,* platforma Azure może nie być w stanie regularnie monitorować, łatać, aktualizować lub uzupełniać danymi. W niektórych przypadkach, na przykład w przypadku nieprawidłowej konfiguracji sieciowej, kontrolery domeny dla domeny zarządzanej mogą być nieosiągalne.

* Domena zarządzana jest w stanie złej kondycji i bieżące monitorowanie kondycji może zostać zatrzymane, dopóki alert nie zostanie rozwiązany.
* Kontrolerów domeny dla domeny zarządzanej nie można załatać ani zaktualizować.
* Zmiany z usługi Azure Active Directory mogą nie być synchronizowane z domeną zarządzana.
* Kopie zapasowe domeny zarządzanej mogą nie być pobierane.
* Jeśli rozpoznasz alerty niekrytyczne, które mają wpływ na domenę zarządzaną, kondycja powinna powrócić do stanu *Uruchomione.*
* Alerty krytyczne są wyzwalane w przypadku problemów z konfiguracją, w których platforma Azure nie może dotrzeć do kontrolerów domeny. Jeśli te alerty krytyczne nie zostaną rozwiązane w ciągu 15 dni, domena zarządzana wejdzie w stan *Wstrzymane.*

## <a name="suspended-state"></a>Stan zawieszenia

Domena zarządzana usługą Azure AD DS wchodzi w stan **wstrzymania** z jednego z następujących powodów:

* Co najmniej jeden alert krytyczny nie został rozwiązany w ciągu 15 dni.
    * Alerty krytyczne mogą być spowodowane błędną konfiguracją, która blokuje dostęp do zasobów, które są potrzebne przez usługi Azure AD DS. Na przykład alert [AADDS104: Błąd sieci][alert-nsg] został rozwiązany przez ponad 15 dni w domenie zarządzanej.
* Wystąpił problem z rozliczeniami z subskrypcją platformy Azure lub wygasła subskrypcja platformy Azure.

Domeny zarządzane są zawieszone, gdy platforma Azure nie może zarządzać domeną, monitorować, łatać ani jej nie wywsuć kopii zapasowej. Domena zarządzana pozostaje w stanie *wstrzymanym* przez 15 dni. Aby zachować dostęp do domeny zarządzanej, należy natychmiast rozwiązać alerty krytyczne.

### <a name="what-to-expect"></a>Czego się spodziewać

Następujące zachowanie jest wystąpić, gdy domena zarządzana usługą Azure AD DS jest w stanie *wstrzymane:*

* Kontrolery domeny dla domeny zarządzanej są usuwane z aprowizowania i nie są osiągalne w sieci wirtualnej.
* Bezpieczny dostęp LDAP do domeny zarządzanej przez Internet, jeśli jest włączona, przestaje działać.
* Występują błędy w uwierzytelnianiu w domenie zarządzanej, logowaniu się do maszyn wirtualnych przyłączonych do domeny lub łączeniu się za pomocą protokołu LDAP/LDAPS.
* Kopie zapasowe domeny zarządzanej nie są już pobierane.
* Synchronizacja z przystankami usługi Azure AD.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Skąd wiesz, czy twoja domena zarządzana jest zawieszona?

Zostanie wyświetlony [alert][resolve-alerts] na stronie Usługi Azure AD DS health w witrynie Azure Portal, który zauważa, że domena jest zawieszona. Stan domeny pokazuje również *zawieszone*.

### <a name="restore-a-suspended-domain"></a>Przywracanie zawieszonej domeny

Aby przywrócić kondycję domeny zarządzanej usług Azure AD DS, która jest w stanie *wstrzymanym,* wykonaj następujące kroki:

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Usługi domeny**.
1. Wybierz domenę zarządzana usługą Azure AD DS z listy, na przykład *aaddscontoso.com*, a następnie wybierz pozycję **Kondycja**.
1. Wybierz alert, taki jak *AADDS503* lub *AADDS504*, w zależności od przyczyny zawieszenia.
1. Wybierz łącze rozpoznawania, które znajduje się w alercie i wykonaj kroki, aby go rozwiązać.

Domenę zarządzaną można przywrócić tylko do daty ostatniej kopii zapasowej. Data ostatniej kopii zapasowej jest wyświetlana na stronie **Kondycja** domeny zarządzanej. Wszelkie zmiany, które wystąpiły po ostatniej kopii zapasowej nie zostaną przywrócone. Kopie zapasowe domeny zarządzanej są przechowywane przez maksymalnie 30 dni. Kopie zapasowe starsze niż 30 dni są usuwane.

Po rozpoznaniu alertów, gdy domena zarządzana jest w stanie *Suspended* [wstrzymane, otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby powrócić do stanu w dobrej kondycji. Jeśli istnieje kopia zapasowa mniej niż 30 dni, pomoc techniczna platformy Azure może przywrócić domenę zarządzaną.

## <a name="deleted-state"></a>Stan usunięty

Jeśli domena zarządzana usługą Azure AD DS pozostaje w stanie *wstrzymane* przez 15 dni, zostanie usunięta. Ten proces jest nie do odzyskania.

### <a name="what-to-expect"></a>Czego się spodziewać

Gdy domena zarządzana usługą Azure AD DS wchodzi w stan *usunięte,* widoczne jest następujące zachowanie:

* Wszystkie zasoby i kopie zapasowe domeny zarządzanej zostaną usunięte.
* Nie można przywrócić domeny zarządzanej i trzeba utworzyć zastępczą domenę zarządzaną, aby ponownie użyć usług Azure AD DS.
* Po usunięciu nie są naliczane naliczane płatności za domenę zarządzana.

## <a name="next-steps"></a>Następne kroki

Aby zapewnić właściwość domeny zarządzanej usługą Azure AD DS i zminimalizować ryzyko jej zawieszenia, dowiedz się, jak [rozwiązywać alerty dotyczące domeny zarządzanej.][resolve-alerts]

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
