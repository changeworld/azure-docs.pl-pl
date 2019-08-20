---
title: 'Azure Active Directory Domain Services: Rozwiązywanie problemów z alertami | Microsoft Docs'
description: Rozwiązywanie problemów z alertami dla Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 5a8f3401de0dc452193efbcf79aef87a19aed081
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617084"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services — Rozwiązywanie problemów z alertami
W tym artykule przedstawiono przewodniki dotyczące rozwiązywania problemów z alertami, które mogą wystąpić w domenie zarządzanej.


Wybierz kroki rozwiązywania problemów, które odnoszą się do identyfikatora lub komunikatu w alercie.

| **Identyfikator alertu** | **Komunikat alertu** | **Rozdzielczość** |
| --- | --- | :--- |
| AADDS001 | *Secure LDAP przez Internet jest włączony dla domeny zarządzanej. Jednak dostęp do portu 636 nie jest zablokowany przy użyciu sieciowej grupy zabezpieczeń. Może to spowodować ujawnienie kont użytkowników w domenie zarządzanej w celu wymuszenia hasła.* | [Niepoprawna konfiguracja bezpiecznego protokołu LDAP](alert-ldaps.md) |
| AADDS100 | *Katalog usługi Azure AD skojarzony z domeną zarządzaną mógł zostać usunięty. Domena zarządzana nie jest już w obsługiwanej konfiguracji. Firma Microsoft nie może monitorować, poprawiać ani synchronizować domeny zarządzanej.* | [Brakujący katalog](#aadds100-missing-directory) |
| AADDS101 | *Nie można włączyć Azure AD Domain Services w katalogu Azure AD B2C.* | [Azure AD B2C jest uruchomiony w tym katalogu](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Nazwa główna usługi wymagana do poprawnego działania Azure AD Domain Services została usunięta z katalogu usługi Azure AD. Ta konfiguracja ma wpływ na zdolność firmy Microsoft do monitorowania, poprawiania i synchronizowania domeny zarządzanej oraz zarządzania nią.* | [Brak nazwy głównej usługi](alert-service-principal.md) |
| AADDS103 | *Zakres adresów IP dla sieci wirtualnej, w której włączono Azure AD Domain Services należy do publicznego zakresu adresów IP. Azure AD Domain Services musi być włączona w sieci wirtualnej z zakresem prywatnych adresów IP. Ta konfiguracja ma wpływ na zdolność firmy Microsoft do monitorowania, poprawiania i synchronizowania domeny zarządzanej oraz zarządzania nią.* | [Adres należy do publicznego zakresu adresów IP](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Firma Microsoft nie może nawiązać połączenia z kontrolerami domeny dla tej domeny zarządzanej. Może się tak zdarzyć, jeśli sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) skonfigurowana w sieci wirtualnej blokuje dostęp do domeny zarządzanej. Kolejną możliwą przyczyną jest to, że istnieje trasa zdefiniowana przez użytkownika, która blokuje ruch przychodzący z Internetu.* | [Błąd sieci](alert-nsg.md) |
| AADDS105 | *Nazwa główna usługi z identyfikatorem aplikacji "d87dcbc6-a371-462e-88e3-28ad15ec4e64" została usunięta, a następnie ponownie utworzona. Ponowne tworzenie jest pozostawiane za niespójnymi uprawnieniami do Azure AD Domain Services zasobów wymaganych do obsługi domeny zarządzanej. Może to wpłynąć na synchronizację haseł w domenie zarządzanej.* | [Aplikacja do synchronizacji haseł jest nieaktualna](alert-service-principal.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *Twoja subskrypcja platformy Azure skojarzona z domeną zarządzaną została usunięta.  Azure AD Domain Services wymaga aktywnej subskrypcji, aby nadal działać prawidłowo.* | [Nie znaleziono subskrypcji platformy Azure](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *Twoja subskrypcja platformy Azure skojarzona z Twoją domeną zarządzaną nie jest aktywna.  Azure AD Domain Services wymaga aktywnej subskrypcji, aby nadal działać prawidłowo.* | [Subskrypcja platformy Azure jest wyłączona](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *Subskrypcja używana przez Azure AD Domain Services została przeniesiona do innego katalogu. Azure AD Domain Services musi mieć aktywną subskrypcję w tym samym katalogu, aby działała prawidłowo.* | [Katalogi przeniesionych subskrypcji](#aadds108-subscription-moved-directories) |
| AADDS109 | *Zasób, który jest używany przez domenę zarządzaną, został usunięty. Ten zasób jest wymagany do poprawnego działania Azure AD Domain Services.* | [Zasób został usunięty](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *Podsieć wybrana do wdrożenia Azure AD Domain Services jest pełna i nie ma miejsca na dodatkowy kontroler domeny, który należy utworzyć.* | [Podsieć jest pełna](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *Jednostka usługi, której Azure AD Domain Services używa do obsługi domeny, nie ma autoryzacji do zarządzania zasobami w ramach subskrypcji platformy Azure. Jednostka usługi musi uzyskać uprawnienia do obsługi domeny zarządzanej.* | [Nazwa główna usługi nie jest autoryzowana](#aadds111-service-principal-unauthorized) |
| AADDS112 | *Zidentyfikowano, że podsieć sieci wirtualnej w tej domenie może nie mieć wystarczającej liczby adresów IP. Azure AD Domain Services potrzebuje co najmniej dwóch dostępnych adresów IP w podsieci, w której jest włączona. Zalecamy używanie co najmniej 3-5 zapasowych adresów IP w podsieci. Mogło to nastąpić, jeśli inne maszyny wirtualne zostaną wdrożone w podsieci, co spowoduje wyczerpanie liczby dostępnych adresów IP lub ograniczenie liczby dostępnych adresów IP w podsieci.* | [Za mało adresów IP](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Zasoby używane przez Azure AD Domain Services zostały wykryte w nieoczekiwanym stanie i nie można ich odzyskać.* | [Zasoby są nieodwracalne](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *Podsieć wybrana do wdrożenia Azure AD Domain Services jest nieprawidłowa i nie można jej użyć.* | [Nieprawidłowa podsieć](#aadds114-subnet-invalid) |
| AADDS115 | *Co najmniej jeden z zasobów sieciowych używany przez domenę zarządzaną nie może być obsługiwany w przypadku, gdy zakres docelowy został zablokowany.* | [Zasoby są zablokowane](#aadds115-resources-are-locked) |
| AADDS116 | *Nie można obsługiwać co najmniej jednego z zasobów sieciowych używanych przez domenę zarządzaną z powodu ograniczeń zasad.* | [Zasoby są bezużyteczne](#aadds116-resources-are-unusable) |
| AADDS500 | *Domena zarządzana została Ostatnia synchronizacja z usługą Azure AD w dniu [Date]. Użytkownicy mogą nie być w stanie zalogować się w domenie zarządzanej lub członkostwa w grupach mogą nie być zsynchronizowane z usługą Azure AD.* | [Synchronizacja nie zakończyła się w czasie](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *Utworzono ostatnią kopię zapasową domeny zarządzanej w dniu [Date].* | [Nie wykonano kopii zapasowej w czasie](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Certyfikat bezpiecznego protokołu LDAP dla domeny zarządzanej wygaśnie w dniu [Date].* | [Wygasający certyfikat bezpiecznego protokołu LDAP](alert-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Domena zarządzana jest wstrzymana, ponieważ subskrypcja platformy Azure skojarzona z domeną nie jest aktywna.* | [Zawieszenie z powodu wyłączenia subskrypcji](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Domena zarządzana jest zawieszona z powodu nieprawidłowej konfiguracji. Usługa nie była w stanie zarządzać, poprawiać ani aktualizować kontrolerów domeny dla domeny zarządzanej przez dłuższy czas.* | [Zawieszenie z powodu nieprawidłowej konfiguracji](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: Brakujący katalog
**Komunikat alertu:**

*Katalog usługi Azure AD skojarzony z domeną zarządzaną mógł zostać usunięty. Domena zarządzana nie jest już w obsługiwanej konfiguracji. Firma Microsoft nie może monitorować, poprawiać ani synchronizować domeny zarządzanej.*

**Tłumaczenia**

Ten błąd jest zwykle spowodowany przez nieprawidłowe przeniesienie subskrypcji platformy Azure do nowego katalogu usługi Azure AD i usunięcie starego katalogu usługi Azure AD, który nadal jest skojarzony z Azure AD Domain Services.

Ten błąd jest nieodwracalny. Aby rozwiązać ten problem, należy [usunąć istniejącą domenę zarządzaną](delete-aadds.md) i utworzyć ją ponownie w nowym katalogu. Jeśli masz problemy z usuwaniem, skontaktuj się z zespołem [pomocy technicznej](contact-us.md)Azure Active Directory Domain Services.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C jest uruchomiony w tym katalogu
**Komunikat alertu:**

*Nie można włączyć Azure AD Domain Services w katalogu Azure AD B2C.*

**Tłumaczenia**

>[!NOTE]
>Aby nadal używać Azure AD Domain Services, należy ponownie utworzyć wystąpienie Azure AD Domain Services w katalogu innym niż Azure AD B2C.

Aby przywrócić usługę, wykonaj następujące kroki:

1. [Usuń domenę zarządzaną](delete-aadds.md) z istniejącego katalogu usługi Azure AD.
2. Utwórz nowy katalog, który nie jest katalogiem Azure AD B2C.
3. Postępuj zgodnie z przewodnikiem [wprowadzenie](tutorial-create-instance.md) , aby odtworzyć domenę zarządzaną.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adres należy do publicznego zakresu adresów IP

**Komunikat alertu:**

*Zakres adresów IP dla sieci wirtualnej, w której włączono Azure AD Domain Services należy do publicznego zakresu adresów IP. Azure AD Domain Services musi być włączona w sieci wirtualnej z zakresem prywatnych adresów IP. Ta konfiguracja ma wpływ na zdolność firmy Microsoft do monitorowania, poprawiania i synchronizowania domeny zarządzanej oraz zarządzania nią.*

**Tłumaczenia**

> [!NOTE]
> Aby rozwiązać ten problem, należy usunąć istniejącą domenę zarządzaną i utworzyć ją ponownie w sieci wirtualnej z zakresem prywatnych adresów IP. Ten proces jest zakłócany.

Przed rozpoczęciem zapoznaj się z sekcją **przestrzeń adresów Private IP v4** w [tym artykule](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

W sieci wirtualnej maszyny mogą zgłaszać żądania do zasobów platformy Azure, które znajdują się w tym samym zakresie adresów IP, co te skonfigurowane dla podsieci. Jednak ponieważ sieć wirtualna jest skonfigurowana dla tego zakresu, te żądania będą kierowane do sieci wirtualnej i nie docierają do zamierzonych zasobów sieci Web. Ta konfiguracja może prowadzić do nieprzewidzianych błędów w Azure AD Domain Services.

**Jeśli masz własny zakres adresów IP w Internecie skonfigurowanym w sieci wirtualnej, ten alert może zostać zignorowany. Jednak w przypadku tej konfiguracji Azure AD Domain Services nie można zatwierdzić [umowy SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)], ponieważ może to prowadzić do nieprzewidywalnych błędów.**


1. [Usuń domenę zarządzaną](delete-aadds.md) z katalogu.
2. Popraw zakres adresów IP podsieci
   1. Przejdź do [strony sieci wirtualne na Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Wybierz sieć wirtualną, której chcesz użyć do Azure AD Domain Services.
   3. Kliknij **przestrzeń adresową** w obszarze Ustawienia
   4. Zaktualizuj zakres adresów, klikając istniejący zakres adresów i edytując go lub dodając dodatkowy zakres adresów. Upewnij się, że nowy zakres adresów należy do zakresu prywatnego adresów IP. Zapisz zmiany.
   5. Kliknij pozycję **podsieci** w obszarze nawigacji po lewej stronie.
   6. Kliknij podsieć, którą chcesz edytować w tabeli.
   7. Zaktualizuj zakres adresów i Zapisz zmiany.
3. Postępuj zgodnie [z wprowadzenie pomocą przewodnika Azure AD Domain Services](tutorial-create-instance.md) , aby ponownie utworzyć domenę zarządzaną. Upewnij się, że wybierasz sieć wirtualną z zakresem prywatnych adresów IP.
4. Aby przyłączyć maszyny wirtualne do nowej domeny, należy postępować zgodnie z [tym przewodnikiem](join-windows-vm.md).
8. Aby upewnić się, że alert został rozwiązany, Sprawdź kondycję domeny w ciągu dwóch godzin.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Nie znaleziono subskrypcji platformy Azure

**Komunikat alertu:**

*Twoja subskrypcja platformy Azure skojarzona z domeną zarządzaną została usunięta.  Azure AD Domain Services wymaga aktywnej subskrypcji, aby nadal działać prawidłowo.*

**Tłumaczenia**

Azure AD Domain Services wymaga subskrypcji do działania i nie można go przenieść do innej subskrypcji. Ponieważ subskrypcja platformy Azure, z którą skojarzona jest domena zarządzana, została usunięta, należy ponownie utworzyć subskrypcję platformy Azure i Azure AD Domain Services.

1. Utwórz subskrypcję platformy Azure
2. [Usuń domenę zarządzaną](delete-aadds.md) z istniejącego katalogu usługi Azure AD.
3. Postępuj zgodnie z przewodnikiem [wprowadzenie](tutorial-create-instance.md) , aby odtworzyć domenę zarządzaną.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Subskrypcja platformy Azure została wyłączona

**Komunikat alertu:**

*Twoja subskrypcja platformy Azure skojarzona z Twoją domeną zarządzaną nie jest aktywna.  Azure AD Domain Services wymaga aktywnej subskrypcji, aby nadal działać prawidłowo.*

**Tłumaczenia**


1. [Odnów subskrypcję platformy Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Po odnowieniu subskrypcji Azure AD Domain Services otrzyma powiadomienie z platformy Azure, aby ponownie włączyć domenę zarządzaną.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Katalogi przeniesionych subskrypcji

**Komunikat alertu:**

*Subskrypcja używana przez Azure AD Domain Services została przeniesiona do innego katalogu. Azure AD Domain Services musi mieć aktywną subskrypcję w tym samym katalogu, aby działała prawidłowo.*

**Tłumaczenia**

Możesz przenieść subskrypcję skojarzoną z Azure AD Domain Services z powrotem do poprzedniego katalogu lub [usunąć domenę zarządzaną](delete-aadds.md) z istniejącego katalogu i utworzyć ją ponownie w wybranym katalogu (przy użyciu nowej subskrypcji lub Zmień katalog, w którym znajduje się wystąpienie Azure AD Domain Services.

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Nie można znaleźć zasobów dla domeny zarządzanej

**Komunikat alertu:**

*Zasób, który jest używany przez domenę zarządzaną, został usunięty. Ten zasób jest wymagany do poprawnego działania Azure AD Domain Services.*

**Tłumaczenia**

Azure AD Domain Services tworzy określone zasoby podczas wdrażania, aby zapewnić prawidłowe działanie, w tym publiczne adresy IP, karty sieciowe i moduł równoważenia obciążenia. Jeśli którykolwiek z nazw zostanie usunięty, spowoduje to, że domena zarządzana będzie w nieobsługiwanym stanie i uniemożliwia zarządzanie domeną. Ten alert można znaleźć, gdy ktoś, kto może edytować zasoby Azure AD Domain Services, usuwa wymagany zasób. Poniższe kroki przedstawiają sposób przywracania domeny zarządzanej.

1. Przejdź do strony Azure AD Domain Services Health
   1.    Podróż do [strony Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) w Azure Portal.
   2.    W okienku nawigacji po lewej stronie kliknij pozycję **kondycja** .
2. Sprawdź, czy alert jest krótszy niż 4 godziny
   1.    Na stronie kondycja Kliknij alert z IDENTYFIKATORem **AADDS109**
   2.    Po jego znalezieniu alert będzie miał sygnaturę czasową. Jeśli sygnatura czasowa jest krótsza niż 4 godziny temu, istnieje możliwość, że Azure AD Domain Services może odtworzyć usunięty zasób.
3. Jeśli alert jest wcześniejszy niż 4 godziny, domena zarządzana jest w stanie nieodwracalnym. Należy usunąć i utworzyć ponownie Azure AD Domain Services.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Podsieć skojarzona z domeną zarządzaną jest pełna

**Komunikat alertu:**

*Podsieć wybrana do wdrożenia Azure AD Domain Services jest pełna i nie ma miejsca na dodatkowy kontroler domeny, który należy utworzyć.*

**Tłumaczenia**

Ten błąd jest nieodwracalny. Aby rozwiązać ten problem, należy [usunąć istniejącą domenę zarządzaną](delete-aadds.md) i [ponownie utworzyć domenę zarządzaną](tutorial-create-instance.md) .

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Nazwa główna usługi nie jest autoryzowana

**Komunikat alertu:**

*Jednostka usługi, której Azure AD Domain Services używa do obsługi domeny, nie ma autoryzacji do zarządzania zasobami w ramach subskrypcji platformy Azure. Jednostka usługi musi uzyskać uprawnienia do obsługi domeny zarządzanej.*

**Tłumaczenia**

Nasze jednostki usługi muszą mieć dostęp, aby można było zarządzać zasobami w domenie zarządzanej oraz ich tworzyć. Ktoś odmówił dostępu do nazwy głównej usługi, a teraz nie może zarządzać zasobami. Postępuj zgodnie z instrukcjami, aby udzielić dostępu do nazwy głównej usługi.

1. Przeczytaj o [kontroli RBAC i jak udzielić dostępu do aplikacji na Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Przejrzyj dostęp do nazwy głównej usługi o identyfikatorze ```abba844e-bc0e-44b0-947a-dc74e5d09022``` i Udziel dostępu, który został odrzucony w wcześniejszym terminie.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Za mało adresu IP w domenie zarządzanej

**Komunikat alertu:**

*Zidentyfikowano, że podsieć sieci wirtualnej w tej domenie może nie mieć wystarczającej liczby adresów IP. Azure AD Domain Services potrzebuje co najmniej dwóch dostępnych adresów IP w podsieci, w której jest włączona. Zalecamy używanie co najmniej 3-5 zapasowych adresów IP w podsieci. Mogło to nastąpić, jeśli inne maszyny wirtualne zostaną wdrożone w podsieci, co spowoduje wyczerpanie liczby dostępnych adresów IP lub ograniczenie liczby dostępnych adresów IP w podsieci.*

**Tłumaczenia**

1. Usuń domenę zarządzaną z dzierżawy.
2. Popraw zakres adresów IP podsieci
   1. Przejdź do [strony sieci wirtualne na Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Wybierz sieć wirtualną, której chcesz użyć do Azure AD Domain Services.
   3. Kliknij **przestrzeń adresową** w obszarze Ustawienia
   4. Zaktualizuj zakres adresów, klikając istniejący zakres adresów i edytując go lub dodając dodatkowy zakres adresów. Zapisz zmiany.
   5. Kliknij pozycję **podsieci** w obszarze nawigacji po lewej stronie.
   6. Kliknij podsieć, którą chcesz edytować w tabeli.
   7. Zaktualizuj zakres adresów i Zapisz zmiany.
3. Postępuj zgodnie [z wprowadzenie pomocą przewodnika Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) , aby ponownie utworzyć domenę zarządzaną. Upewnij się, że wybierasz sieć wirtualną z zakresem prywatnych adresów IP.
4. Aby przyłączyć maszyny wirtualne do nowej domeny, należy postępować zgodnie z [tym przewodnikiem](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. Sprawdź kondycję domeny w ciągu dwóch godzin, aby upewnić się, że kroki zostały wykonane prawidłowo.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Zasoby są nieodwracalne

**Komunikat alertu:**

*Zasoby używane przez Azure AD Domain Services zostały wykryte w nieoczekiwanym stanie i nie można ich odzyskać.*

**Tłumaczenia**

Ten błąd jest nieodwracalny. Aby rozwiązać ten problem, należy [usunąć istniejącą domenę zarządzaną](delete-aadds.md) i [ponownie utworzyć domenę zarządzaną](tutorial-create-instance.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114: Nieprawidłowa podsieć

**Komunikat alertu:**

*Podsieć wybrana do wdrożenia Azure AD Domain Services jest nieprawidłowa i nie można jej użyć.*

**Tłumaczenia**

Ten błąd jest nieodwracalny. Aby rozwiązać ten problem, należy [usunąć istniejącą domenę zarządzaną](delete-aadds.md) i [ponownie utworzyć domenę zarządzaną](tutorial-create-instance.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115: Zasoby są zablokowane

**Komunikat alertu:**

*Co najmniej jeden z zasobów sieciowych używany przez domenę zarządzaną nie może być obsługiwany w przypadku, gdy zakres docelowy został zablokowany.*

**Tłumaczenia**

1.  Zapoznaj się z dziennikami operacji Menedżer zasobów w zasobach sieciowych (należy podać informacje o tym, które blokady uniemożliwiają modyfikację).
2.  Usuń blokady zasobów, aby jednostka usługi Azure AD Domain Services mogła z nich korzystać.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Zasoby są bezużyteczne

**Komunikat alertu:**

*Nie można obsługiwać co najmniej jednego z zasobów sieciowych używanych przez domenę zarządzaną z powodu ograniczeń zasad.*

**Tłumaczenia**

1.  Zapoznaj się z dziennikami operacji Menedżer zasobów na zasobach sieciowych dla domeny zarządzanej.
2.  Obniżyć ograniczenia zasad dotyczących zasobów, aby umożliwić podmiotowi usługi AAD-DS.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synchronizacja nie została ukończona w czasie

**Komunikat alertu:**

*Domena zarządzana została Ostatnia synchronizacja z usługą Azure AD w dniu [Date]. Użytkownicy mogą nie być w stanie zalogować się w domenie zarządzanej lub członkostwa w grupach mogą nie być zsynchronizowane z usługą Azure AD.*

**Tłumaczenia**

[Sprawdź kondycję domeny](check-health.md) pod kątem alertów, które mogą wskazywać na problemy z konfiguracją domeny zarządzanej. Czasami problemy z konfiguracją mogą blokować zdolność firmy Microsoft do synchronizowania Twojej domeny zarządzanej. Jeśli możesz rozwiązać jakiekolwiek alerty, odczekaj dwie godziny i sprawdź, czy synchronizacja została ukończona.

Poniżej przedstawiono niektóre typowe przyczyny zatrzymania synchronizacji w domenach zarządzanych:
- Połączenie sieciowe jest blokowane w domenie zarządzanej. Aby dowiedzieć się więcej na temat sprawdzania sieci pod kątem problemów, zapoznaj się z tematem Rozwiązywanie problemów z [grupami zabezpieczeń sieci](alert-nsg.md) i [wymaganiami sieciowymi dla Azure AD Domain Services](network-considerations.md).
-  Synchronizacja haseł nigdy nie została skonfigurowana lub nie została ukończona. Aby skonfigurować synchronizację haseł, Przeczytaj [ten artykuł](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Nie wykonano kopii zapasowej w czasie

**Komunikat alertu:**

*Utworzono ostatnią kopię zapasową domeny zarządzanej w dniu [Date].*

**Tłumaczenia**

[Sprawdź kondycję domeny](check-health.md) pod kątem alertów, które mogą wskazywać na problemy z konfiguracją domeny zarządzanej. Czasami problemy z konfiguracją mogą blokować możliwość tworzenia kopii zapasowej domeny zarządzanej przez firmę Microsoft. Jeśli możesz rozwiązać jakiekolwiek alerty, odczekaj dwie godziny i sprawdź, czy kopia zapasowa została ukończona.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Zawieszenie z powodu wyłączenia subskrypcji

**Komunikat alertu:**

*Domena zarządzana jest wstrzymana, ponieważ subskrypcja platformy Azure skojarzona z domeną nie jest aktywna.*

**Tłumaczenia**

> [!WARNING]
> Jeśli Twoja domena zarządzana jest zawieszona przez dłuższy czas, jest to niebezpieczne. Najlepszym rozwiązaniem jest możliwie szybkie rozwiązanie zawieszenia. Aby dowiedzieć się więcej, zapoznaj się z [tym artykułem](suspension.md).

Aby przywrócić usługę, [Odnów subskrypcję platformy Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) skojarzoną z domeną zarządzaną.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Zawieszenie z powodu nieprawidłowej konfiguracji

**Komunikat alertu:**

*Domena zarządzana jest zawieszona z powodu nieprawidłowej konfiguracji. Usługa nie była w stanie zarządzać, poprawiać ani aktualizować kontrolerów domeny dla domeny zarządzanej przez dłuższy czas.*

**Tłumaczenia**

> [!WARNING]
> Jeśli Twoja domena zarządzana jest zawieszona przez dłuższy czas, jest to niebezpieczne. Najlepszym rozwiązaniem jest możliwie szybkie rozwiązanie zawieszenia. Aby dowiedzieć się więcej, zapoznaj się z [tym artykułem](suspension.md).

[Sprawdź kondycję domeny](check-health.md) pod kątem alertów, które mogą wskazywać na problemy z konfiguracją domeny zarządzanej. Jeśli możesz rozwiązać dowolny z tych alertów, zrób to. Po skontaktowaniu się z pomocą techniczną, aby ponownie włączyć subskrypcję.


## <a name="contact-us"></a>Skontaktuj się z nami
Skontaktuj się z zespołem pomocy technicznej Azure Active Directory Domain Services, aby [udostępnić opinię lub pomoc techniczną](contact-us.md).
