---
title: 'Azure Active Directory Domain Services: Zawieszone domeny | Microsoft Docs'
description: Zawieszenie i usunięcie domeny zarządzanej
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 781a81589032c290cef7342e7210ee36f388b22a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233986"
---
# <a name="understand-the-suspended-states-for-an-azure-active-directory-domain-services-managed-domain"></a>Informacje o zawieszonych Stanach dla Azure Active Directory Domain Services domeny zarządzanej

Gdy Azure Active Directory Domain Services (Azure AD DS) nie jest w stanie obsłużyć domeny zarządzanej przez długi czas, spowoduje to przełączenie domeny zarządzanej do stanu wstrzymania. W tym artykule wyjaśniono, dlaczego domeny zarządzane są zawieszone i jak skorygować zawieszoną domenę.


## <a name="states-your-managed-domain-can-be-in"></a>Stany, w których może znajdować się domena zarządzana

![Wstrzymana oś czasu domeny](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Powyższa ilustracja przedstawia możliwe stany, w których może być dostępna domena zarządzana przez platformę Azure AD DS.

### <a name="running-state"></a>Stan "uruchomiona"
Domena zarządzana, która jest skonfigurowana prawidłowo i regularnie działa, jest w stanie **uruchomienia** .

**Czego oczekiwać**
* Firma Microsoft może regularnie monitorować kondycję domeny zarządzanej.
* Kontrolery domeny dla domeny zarządzanej są poprawiane i regularnie aktualizowane.
* Zmiany z Azure Active Directory są regularnie synchronizowane z domeną zarządzaną.
* Regularne kopie zapasowe są tworzone dla domeny zarządzanej.


### <a name="needs-attention-state"></a>Stan "wymaga uwagi"
Domena zarządzana jest w stanie **uwagi** , jeśli co najmniej jeden problem wymaga od administratora podjęcia odpowiednich działań. Na stronie kondycja domeny zarządzanej znajduje się jeden lub więcej alertów w tym stanie.

Na przykład jeśli skonfigurowano restrykcyjną sieciowej grupy zabezpieczeń dla sieci wirtualnej, firma Microsoft może nie być w stanie zaktualizować i monitorować domeny zarządzanej. Ta nieprawidłowa konfiguracja wyzwala alert, który umieszcza domenę zarządzaną w stanie "wymaga uwagi".

Każdy alert zawiera zestaw kroków rozwiązania. Niektóre alerty są przejściowe i automatycznie rozwiązywane przez usługę. Inne alerty można rozwiązać, postępując zgodnie z instrukcjami w odpowiednich krokach rozwiązania dla tego alertu. W przypadku niektórych alertów krytycznych należy skontaktować się z pomocą techniczną firmy Microsoft w celu uzyskania rozwiązania.

Aby uzyskać więcej informacji, zobacz [Jak rozwiązywać problemy z alertami w domenie zarządzanej](troubleshoot-alerts.md).

**Czego oczekiwać**

W niektórych przypadkach (na przykład jeśli masz nieprawidłową konfigurację sieci), kontrolery domeny dla domeny zarządzanej mogą być nieosiągalne. Gdy domena zarządzana jest w stanie "wymaga uwagi", firma Microsoft nie może zagwarantować regularnego monitorowania, poprawek, aktualizacji lub tworzenia kopii zapasowych.

* Twoja domena zarządzana jest w złej kondycji i ciągłe monitorowanie kondycji może zostać zatrzymane do momentu rozwiązania alertu.
* Nie można uaktualnić ani zaktualizować kontrolerów domeny dla domeny zarządzanej.
* Zmiany z Azure Active Directory mogą nie być zsynchronizowane z domeną zarządzaną.
* Kopie zapasowe dla domeny zarządzanej mogą być podejmowane, jeśli jest to możliwe.
* W przypadku rozwiązywania alertów mających wpływ na domenę zarządzaną może być możliwe przywrócenie stanu "uruchomiona".
* Alerty krytyczne są wyzwalane w przypadku problemów z konfiguracją, w których firma Microsoft nie może nawiązać połączenia z kontrolerami domeny. Jeśli takie alerty nie zostaną rozwiązane w ciągu 15 dni, domena zarządzana zostanie umieszczona w stanie wstrzymania.


### <a name="the-suspended-state"></a>Stan "wstrzymane"
Domena zarządzana jest umieszczana w  stanie wstrzymania z następujących powodów:

* Nie rozwiązano co najmniej jednego alertu krytycznego w ciągu 15 dni. Alerty krytyczne mogą być spowodowane przez nieprawidłową konfigurację, która blokuje dostęp do zasobów, które są wymagane przez usługę Azure AD DS.
    * Na przykład AADDS104 alertu [: Nierozwiązano błąd](alert-nsg.md) sieci przez ponad 15 dni w domenie zarządzanej.
* Wystąpił problem z rozliczeniami związany z subskrypcją platformy Azure lub subskrypcja platformy Azure wygasła.

Domeny zarządzane są zawieszane, gdy firma Microsoft nie może stale zarządzać, monitorować, poprawiać i tworzyć kopii zapasowej domeny.

**Czego oczekiwać**
* Kontrolery domeny dla domeny zarządzanej są nieobsługiwane i nie są dostępne w sieci wirtualnej.
* Secure LDAP dostęp do domeny zarządzanej za pośrednictwem Internetu (jeśli jest włączona) przestaje działać.
* Zauważysz problemy z uwierzytelnianiem w domenie zarządzanej, logowaniem do maszyn wirtualnych przyłączonych do domeny lub Nawiązywanie połączenia za pośrednictwem protokołu LDAP/LDAPs.
* Nie są już wykonywane kopie zapasowe dla domeny zarządzanej.
* Synchronizacja z usługą Azure AD zostaje zatrzymana.

Po rozwiązaniu alertu domena zarządzana przechodzi w stan wstrzymania. Należy skontaktować się z pomocą techniczną.
Obsługa może przywrócić domenę zarządzaną, ale tylko wtedy, gdy istnieje kopia zapasowa, która jest starsza niż 30 dni.

Domena zarządzana pozostaje w stanie wstrzymania przez 15 dni. Aby odzyskać domenę zarządzaną, firma Microsoft zaleca natychmiastowe rozwiązanie alertów o kluczowym znaczeniu.


### <a name="deleted-state"></a>Stan "usunięte"
Domena zarządzana, która pozostaje w stanie wstrzymania przez 15 dni, zostanie **usunięta**.

**Czego oczekiwać**
* Wszystkie zasoby i kopie zapasowe dla domeny zarządzanej są usuwane.
* Nie można przywrócić domeny zarządzanej i trzeba utworzyć nową domenę zarządzaną do korzystania z usługi Azure AD DS.
* Po jego usunięciu nie są naliczane opłaty za domenę zarządzaną.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Jak sprawdzić, czy Twoja domena zarządzana jest zawieszona?
Zostanie wyświetlony [alert](troubleshoot-alerts.md) na stronie kondycji usługi Azure AD DS w Azure Portal, która deklaruje, że domena jest zawieszona. Stan domeny zawiera także "zawieszone".


## <a name="restore-a-suspended-domain"></a>Przywracanie zawieszonej domeny
Aby przywrócić domenę, która jest w stanie wstrzymania, wykonaj następujące czynności:

1. Przejdź do [strony Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) w Azure Portal.
2. Wybierz domenę zarządzaną.
3. W lewym panelu wybierz pozycję **kondycja**.
4. Wybierz Alert. Identyfikator alertu będzie AADDS503 lub AADDS504, w zależności od przyczyny zawieszenia.
5. Wybierz link do rozwiązania, który jest dostępny w alercie. Następnie postępuj zgodnie z instrukcjami, aby rozwiązać alert.

Twoja domena zarządzana może zostać przywrócona tylko do daty ostatniej kopii zapasowej. Data ostatniej kopii zapasowej jest wyświetlana na stronie kondycja domeny zarządzanej. Wszelkie zmiany, które wystąpiły po utworzeniu ostatniej kopii zapasowej, nie zostaną przywrócone. Kopie zapasowe dla domeny zarządzanej są przechowywane przez maksymalnie 30 dni. Usuwane są kopie zapasowe starsze niż 30 dni.


## <a name="next-steps"></a>Następne kroki
- [Rozwiązywanie alertów dla domeny zarządzanej](troubleshoot-alerts.md)
- [Przeczytaj więcej na temat Azure Active Directory Domain Services](overview.md)
- [Skontaktuj się z zespołem produktu](contact-us.md)

## <a name="contact-us"></a>Skontaktuj się z nami
Skontaktuj się z zespołem pomocy technicznej Azure Active Directory Domain Services, aby [udostępnić opinię lub pomoc techniczną](contact-us.md).
