---
title: 'Usługa Azure Active Directory Domain Services: Rozwiązywanie problemów z alertami | Dokumentacja firmy Microsoft'
description: Rozwiązywanie problemów dotyczących alertów dla usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: ergreenl
ms.openlocfilehash: a6928b5a849f35456a6fb7699acd7720f686c2aa
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243065"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services — Rozwiązywanie problemów z alertami
Ten artykuł zawiera przewodniki dotyczące rozwiązywania problemów w ramach wszystkich alertów, które mogą występować w domenie zarządzanej.


Wybierz kroki rozwiązywania problemów, które odnoszą się do Identyfikatora lub wiadomości w alercie.

| **Identyfikator alertu** | **Komunikat alertu** | **Rozdzielczość** |
| --- | --- | :--- |
| AADDS001 | *Dla domeny zarządzanej włączono protokół Secure LDAP przez internet. Dostęp do portu 636 nie jest zablokowany za pomocą sieciowej grupy zabezpieczeń. Może to wystawić konta użytkowników w domenie zarządzanej na próby ataków siłowych hasła.* | [Niepoprawna konfiguracja protokołu secure LDAP](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *Katalog usługi Azure AD skojarzonego z Twoją domeną zarządzaną mógł zostać usunięty. Domena zarządzana nie jest już w obsługiwanej konfiguracji. Microsoft nie może monitorować, zarządzanie, patch i synchronizacji z domeną zarządzaną.* | [Brakujący katalog](#aadds100-missing-directory) |
| AADDS101 | *Nie można włączyć usługi domenowe Azure AD w katalogu usługi Azure AD B2C.* | [Usługa Azure AD B2C jest uruchomiona w tym katalogu](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Wymagane dla usług domenowych Azure AD do prawidłowej nazwy głównej usługi został usunięty z katalogu usługi Azure AD. Ta konfiguracja wpływa negatywnie na możliwości firmy Microsoft, aby monitorować oraz zarządzać nimi, poprawki i synchronizować swojej domeny zarządzanej.* | [Brak nazwy głównej usługi](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *Zakres adresów IP dla sieci wirtualnej, w której włączono usługi domenowe Azure AD jest w zakresie publicznych adresów IP. Azure AD Domain Services musi być włączona w sieci wirtualnej przy użyciu zakresu prywatnych adresów IP. Ta konfiguracja wpływa negatywnie na możliwości firmy Microsoft, aby monitorować oraz zarządzać nimi, poprawki i synchronizować swojej domeny zarządzanej.* | [Adres znajduje się w zakresie adresów IP](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft nie może nawiązać połączenia z kontrolerami domeny dla tej domeny zarządzanej. Może to nastąpić, jeśli sieciowa grupa zabezpieczeń (NSG) skonfigurowane w Twojej sieci wirtualnej blokuje dostęp do domeny zarządzanej. Inny z możliwych przyczyn jest istnienie trasy zdefiniowanej przez użytkownika tego blokuje ruch przychodzący z Internetu.* | [Błąd sieci](active-directory-ds-troubleshoot-nsg.md) |
| AADDS105 | *Jednostki usługi o identyfikatorze aplikacji "d87dcbc6-a371-462e-88e3-28ad15ec4e64" został usunięty i tworzony ponownie. Odtworzenie pozostawia za niespójne uprawnień zasobów usługi Azure AD Domain Services, potrzebne do obsługi Twojej domeny zarządzanej. Może mieć wpływ na synchronizację haseł w domenie zarządzanej.* | [Aplikacja synchronizacji haseł jest nieaktualna](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *Twoja subskrypcja platformy Azure skojarzone z Twoją domeną zarządzaną została usunięta.  Usługi domenowe Azure AD wymaga aktywnej subskrypcji działał poprawnie.* | [Nie można odnaleźć subskrypcji platformy Azure](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *Twoja subskrypcja platformy Azure skojarzone z Twoją domeną zarządzaną nie jest aktywny.  Usługi domenowe Azure AD wymaga aktywnej subskrypcji działał poprawnie.* | [Subskrypcja platformy Azure jest wyłączona.](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *Usunięto zasób, który jest używany na potrzeby domeny zarządzanej. Ten zasób jest wymagany dla usług domenowych Azure AD działać prawidłowo.* | [Zasób został usunięty.](#aadds108-resources-for-your-managed-domain-cannot-be-found) |
| AADDS109 | *Podsieć wybrana dla wdrożenia usług domenowych Azure AD jest pełny i nie ma miejsca na dodatkowy kontroler domeny, musi zostać utworzona.* | [Podsieć jest pełna](#aadds109-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS110 | *Odkryliśmy, że podsieć sieci wirtualnej w tej domenie może nie mieć wystarczającej liczby adresów IP. Azure AD Domain Services wymaga co najmniej dwóch dostępnych adresów IP w podsieci, w której jest włączone w. Firma Microsoft zaleca posiadanie co najmniej 3 – 5 Alokacja adresów IP w obrębie podsieci. To może wystąpić, jeśli inne maszyny wirtualne są wdrażane w obrębie podsieci, w związku z tym wyczerpaniem liczby dostępnych adresów IP lub czy istnieje ograniczenie liczby dostępnych adresów IP w podsieci.* | [Nie ma wystarczającej liczby adresów IP](#aadds110-not-enough-ip-address-in-the-managed-domain) |
| AADDS111 | *Co najmniej jeden z zasobów sieciowych, używane przez domena zarządzana nie może być obsługiwany na jak zakres docelowy został zablokowany.* | [Zasoby są zablokowane.](#aadds111-resources-are-locked) |
| AADDS112 | *Co najmniej jeden z zasobów sieciowych, używane przez domena zarządzana nie może działać z powodu restriction(s) zasad.* | [Zasoby są bezużyteczne](#aadds112-resources-are-unusable) |
| AADDS113 | *Zasoby używane przez usługi Azure AD Domain Services zostały wykryte w nieoczekiwanym stanie i nie można go odzyskać.* | [Zasoby są nie do odzyskania](#aadds113-resources-are-unrecoverable) |
| AADDS114 | * Usługa azure AD Domain Services, które kontrolery domeny nie są w stanie uzyskać dostęp do portu 443. Jest on potrzebny do usługi, zarządzanie i aktualizowanie domeny zarządzanej. * | [Port 442 zablokowane](#aadds114-port-443-blocked) |
| AADDS500 | *Domena zarządzana ostatniej synchronizacji z usługą Azure AD w dniu [date]. Użytkownicy nie mieć możliwości logowania się w domenie zarządzanej lub członkostwa w grupie może nie być zsynchronizowany z usługą Azure AD.* | [Synchronizacja nie wystąpił w chwilę](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *Domena zarządzana utworzenia ostatniej kopii zapasowej na [date].* | [Tworzenie kopii zapasowej nie został przełączony w chwilę](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Certyfikat secure LDAP dla domeny zarządzanej wygaśnie w dniu [date].* | [Certyfikat secure LDAP wygasa](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Domena zarządzana jest zawieszona, ponieważ nie jest aktywna subskrypcja platformy Azure skojarzony z domeną.* | [Zawieszenie z powodu wyłączonej subskrypcji](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Domena zarządzana jest wstrzymana z powodu nieprawidłowej konfiguracji. Usługa nie mogła zarządzać poprawkami, lub zaktualizuj z kontrolerami domeny dla domeny zarządzanej przez długi czas.* | [Zawieszenie z powodu nieprawidłowej konfiguracji](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: Brak katalogu
**Komunikat alertu:**

*Katalog usługi Azure AD skojarzonego z Twoją domeną zarządzaną mógł zostać usunięty. Domena zarządzana nie jest już w obsługiwanej konfiguracji. Microsoft nie może monitorować, zarządzanie, patch i synchronizacji z domeną zarządzaną.*

**Rozwiązanie:**

Przyczyną tego błędu jest zwykle nieprawidłowo przenoszenie Twojej subskrypcji platformy Azure do nowej usługi Azure AD directory i usuwania starych katalog usługi Azure AD, która nadal jest skojarzona z usług domenowych Azure AD.

Ten błąd jest nieodwracalny. Aby rozwiązać problem, należy najpierw [Usuń istniejąca domena zarządzana](active-directory-ds-disable-aadds.md) i utwórz go ponownie w nowym katalogu. Jeśli występują problemy, usuwanie, skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services [obsługi](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: W tym katalogu jest uruchomiona usługa Azure AD B2C
**Komunikat alertu:**

*Nie można włączyć usługi domenowe Azure AD w katalogu usługi Azure AD B2C.*

**Rozwiązanie:**

>[!NOTE]
>Aby nadal korzystać z usług domenowych Azure AD, należy ponownie utworzyć wystąpienia usług domenowych Azure AD w katalogu — z usługi Azure AD B2C.

Aby przywrócić usługę, wykonaj następujące kroki:

1. [Usuwanie domeny zarządzanej](active-directory-ds-disable-aadds.md) z istniejącego katalogu usługi Azure AD.
2. Utwórz nowy katalog, który nie jest katalog usługi Azure AD B2C.
3. Postępuj zgodnie z [wprowadzenie](active-directory-ds-getting-started.md) przewodnika, aby ponownie utworzyć domeny zarządzanej.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adres znajduje się w zakresie adresów IP

**Komunikat alertu:**

*Zakres adresów IP dla sieci wirtualnej, w której włączono usługi domenowe Azure AD jest w zakresie publicznych adresów IP. Azure AD Domain Services musi być włączona w sieci wirtualnej przy użyciu zakresu prywatnych adresów IP. Ta konfiguracja wpływa negatywnie na możliwości firmy Microsoft, aby monitorować oraz zarządzać nimi, poprawki i synchronizować swojej domeny zarządzanej.*

**Rozwiązanie:**

> [!NOTE]
> Aby rozwiązać ten problem, należy usunąć istniejąca domena zarządzana i ponownie utworzyć w sieci wirtualnej z zakresu prywatnych adresów IP. Ten proces jest szkodliwe.

Przed rozpoczęciem przeczytaj **prywatnej przestrzeni adresowej v4 IP** sekcji [w tym artykule](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

W sieci wirtualnej maszyn mogą wysyłać żądania do zasobów platformy Azure, które należą do tego samego zakresu adresów IP, jak porty skonfigurowane dla podsieci. Jednak ponieważ sieć wirtualna jest skonfigurowana dla tego zakresu, te żądania będą kierowane do sieci wirtualnej i nie będzie sięgać do zasobów internetowych zamierzone. Ta konfiguracja może prowadzić do nieprzewidywalnych problemów z usług domenowych Azure AD.

**Jeśli jesteś właścicielem zakres adresów IP w Internecie, który jest skonfigurowany w sieci wirtualnej, można zignorować ten alert. Jednak nie można zatwierdzić usług domenowych Azure AD [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] w przypadku tej konfiguracji, ponieważ może to prowadzić do nieprzewidywalnych błędów.**


1. [Usuwanie domeny zarządzanej](active-directory-ds-disable-aadds.md) z katalogu.
2. Usuń zakres adresów IP w podsieci
  1. Przejdź do [stronie sieci wirtualnej w witrynie Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Wybierz sieć wirtualną, której planujesz używać dla usług domenowych Azure AD.
  3. Kliknij pozycję **przestrzeń adresowa** w obszarze Ustawienia
  4. Aktualizuj zakres adresów, klikając na istniejący zakres adresów i jej edytowanie lub dodawanie dodatkowy zakres adresów. Upewnij się, że nowy zakres adresów w zakresie prywatnych adresów IP. Zapisz zmiany.
  5. Kliknij pozycję **podsieci** w obszarze nawigacji po lewej stronie.
  6. Kliknij podsieć, którą chcesz edytować w tabeli.
  7. Aktualizuj zakres adresów, a następnie zapisz zmiany.
3. Postępuj zgodnie z [przewodnik wprowadzenie pracę przy użyciu usług domenowych Azure AD](active-directory-ds-getting-started.md) ponownie utworzyć domeny zarządzanej. Upewnij się, że możesz wybrać sieć wirtualną z zakresu prywatnych adresów IP.
4. Aby przyłączania do domeny maszyn wirtualnych do nowej domeny, wykonaj [tego przewodnika](active-directory-ds-admin-guide-join-windows-vm-portal.md).
8. Aby upewnić się, że alert nie zostanie rozwiązany, Sprawdź kondycję Twojej domeny w ciągu dwóch godzin.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Twoja subskrypcja platformy Azure nie można odnaleźć

**Komunikat alertu:**

*Twoja subskrypcja platformy Azure skojarzone z Twoją domeną zarządzaną została usunięta.  Usługi domenowe Azure AD wymaga aktywnej subskrypcji działał poprawnie.*

**Rozwiązanie:**

Azure AD Domain Services wymaga subskrypcji do funkcji i nie można przenieść do innej subskrypcji. Ponieważ subskrypcji platformy Azure, który został skojarzony z domeną zarządzaną została usunięta, należy ponownie utworzyć subskrypcję platformy Azure i usługi Azure AD Domain Services.

1. Tworzenie subskrypcji platformy Azure
2. [Usuwanie domeny zarządzanej](active-directory-ds-disable-aadds.md) z istniejącego katalogu usługi Azure AD.
3. Postępuj zgodnie z [wprowadzenie](active-directory-ds-getting-started.md) przewodnika, aby ponownie utworzyć domeny zarządzanej.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Twoja subskrypcja platformy Azure jest wyłączona.

**Komunikat alertu:**

*Twoja subskrypcja platformy Azure skojarzone z Twoją domeną zarządzaną nie jest aktywny.  Usługi domenowe Azure AD wymaga aktywnej subskrypcji działał poprawnie.*

**Rozwiązanie:**


1. [Odnów swoją subskrypcję platformy Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Po odnowieniu subskrypcji usługi Azure AD Domain Services otrzymasz powiadomienie z platformy Azure, aby ponownie włączyć domenę zarządzaną.

## <a name="aadds108-resources-for-your-managed-domain-cannot-be-found"></a>AADDS108: Nie można odnaleźć zasobów dla domeny zarządzanej

**Komunikat alertu:**

*Usunięto zasób, który jest używany na potrzeby domeny zarządzanej. Ten zasób jest wymagany dla usług domenowych Azure AD działać prawidłowo.*

**Rozwiązanie:**

Azure AD Domain Services tworzy określonych zasobów podczas wdrażania, aby działał prawidłowo, łącznie z publicznymi adresami IP, kart sieciowych i modułu równoważenia obciążenia. Jeśli dowolny nazwany zostaną usunięte, to powoduje, że Twojej domeny zarządzanej, znajdować się w nieobsługiwanym stanie i zapobiega Twojej domeny zarządzanej. Ten alert zostanie znaleziony, gdy osoba, która jest w stanie edytować zasoby usługi Azure AD Domain Services usuwa niezbędnych zasobów. Następujące kroki przedstawiają sposób przywracania Twojej domeny zarządzanej.

1.  Przejdź do strony kondycji usług domenowych Azure AD
  1.    Podróży [strony usług domenowych Azure AD]() w witrynie Azure portal.
  2.    W obszarze nawigacji po lewej stronie, kliknij przycisk **kondycji**
2.  Zaznacz, aby sprawdzić, czy alert jest mniejszy niż 4 godziny
  1.    Na stronie kondycji, kliknij alert o identyfikatorze **AADDS108**
  2.    Alert będzie mieć sygnaturę czasową dla po raz pierwszy został znaleziony. Tej sygnatury czasowej jest mniejszy niż 4 godz. temu, czy istnieje ryzyko, czy usługi domenowe Azure AD można odtworzyć usuwanego zasobu.
3.  Jeśli alert jest więcej niż 4 godziny, domena zarządzana jest w stanie odzyskać. Należy usunąć i ponownie utwórz usług domenowych Azure AD.


## <a name="aadds109-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS109: Podsieć skojarzona z Twoją domeną zarządzaną jest pełny

**Komunikat alertu:**

*Podsieć wybrana dla wdrożenia usług domenowych Azure AD jest pełny i nie ma miejsca na dodatkowy kontroler domeny, musi zostać utworzona.*

**Rozwiązanie:**

Ten błąd jest nieodwracalny. Aby rozwiązać problem, należy najpierw [Usuń istniejąca domena zarządzana](active-directory-ds-disable-aadds.md) i [ponownie utworzyć domeny zarządzanej](active-directory-ds-getting-started.md)


## <a name="aadds110-not-enough-ip-address-in-the-managed-domain"></a>AADDS110: Za mało adresu IP w domenie zarządzanej

**Komunikat alertu:**

*Odkryliśmy, że podsieć sieci wirtualnej w tej domenie może nie mieć wystarczającej liczby adresów IP. Azure AD Domain Services wymaga co najmniej dwóch dostępnych adresów IP w podsieci, w której jest włączone w. Firma Microsoft zaleca posiadanie co najmniej 3 – 5 Alokacja adresów IP w obrębie podsieci. To może wystąpić, jeśli inne maszyny wirtualne są wdrażane w obrębie podsieci, w związku z tym wyczerpaniem liczby dostępnych adresów IP lub czy istnieje ograniczenie liczby dostępnych adresów IP w podsieci.*

**Rozwiązanie:**

1. [Usuwanie domeny zarządzanej](#active-directory-ds-disable-aadds.md) z dzierżawy.
2. Usuń zakres adresów IP w podsieci
  1. Przejdź do [stronie sieci wirtualnej w witrynie Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Wybierz sieć wirtualną, której planujesz używać dla usług domenowych Azure AD.
  3. Kliknij pozycję **przestrzeń adresowa** w obszarze Ustawienia
  4. Aktualizuj zakres adresów, klikając na istniejący zakres adresów i jej edytowanie lub dodawanie dodatkowy zakres adresów. Zapisz zmiany.
  5. Kliknij pozycję **podsieci** w obszarze nawigacji po lewej stronie.
  6. Kliknij podsieć, którą chcesz edytować w tabeli.
  7. Aktualizuj zakres adresów, a następnie zapisz zmiany.
3. Postępuj zgodnie z [przewodnik wprowadzenie pracę przy użyciu usług domenowych Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) ponownie utworzyć domeny zarządzanej. Upewnij się, że możesz wybrać sieć wirtualną z zakresu prywatnych adresów IP.
4. Aby przyłączania do domeny maszyn wirtualnych do nowej domeny, wykonaj [tego przewodnika](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. Sprawdzanie kondycji domeny w ciągu dwóch godzin, aby upewnić się, że czynności zostały wykonane poprawnie.

## <a name="aadds111-resources-are-locked"></a>AADDS111: Zasoby są zablokowane.

**Komunikat alertu:**

*Co najmniej jeden z zasobów sieciowych, używane przez domena zarządzana nie może być obsługiwany na jak zakres docelowy został zablokowany.*

**Rozwiązanie:**

1.  Działania przeglądu Resource Manager dzienników z zasobów sieciowych (ten powinien zapewnić informacje na które blokada uniemożliwia modyfikowanie).
2.  Usuń blokady zasobów i nazwy głównej usługi Azure AD Domain Services może działać na nich.


## <a name="aadds112-resources-are-unusable"></a>AADDS112: Zasoby są bezużyteczne

**Komunikat alertu:**

*Co najmniej jeden z zasobów sieciowych, używane przez domena zarządzana nie może działać z powodu restriction(s) zasad.*

**Rozwiązanie:**

1.  Działania przeglądu Resource Manager dzienników z zasobów sieciowych dla domeny zarządzanej
2.  Tak, aby nazwy głównej usługi AAD DS może działać na nich, obniżyć poziom ograniczeń zasad na zasoby.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Zasoby są nie do odzyskania

**Komunikat alertu:**

*Zasoby używane przez usługi Azure AD Domain Services zostały wykryte w nieoczekiwanym stanie i nie można go odzyskać.*

**Rozwiązanie:**

Ten błąd jest nieodwracalny. Aby rozwiązać problem, należy najpierw [Usuń istniejąca domena zarządzana](active-directory-ds-disable-aadds.md) i [ponownie utworzyć domeny zarządzanej](active-directory-ds-getting-started.md)

## <a name="aadds114-port-443-blocked"></a>AADDS114: Port 443, zablokowane

**Komunikat alertu:**

*Azure AD Domain Services, które kontrolery domeny nie są w stanie uzyskać dostęp do portu 443. Jest on potrzebny do usługi, zarządzanie i aktualizowanie domeny zarządzanej.*

**Rozwiązanie:**

Zezwalać na dostęp dla ruchu przychodzącego przez port 443 na sieciową grupę zabezpieczeń dla usług domenowych Azure AD.


## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synchronizacji nie zostało ukończone w chwilę

**Komunikat alertu:**

*Domena zarządzana ostatniej synchronizacji z usługą Azure AD w dniu [date]. Użytkownicy nie mieć możliwości logowania się w domenie zarządzanej lub członkostwa w grupie może nie być zsynchronizowany z usługą Azure AD.*

**Rozwiązanie:**

[Sprawdzanie kondycji domeny](active-directory-ds-check-health.md) w ramach wszystkich alertów, które mogą wskazywać problemy w konfiguracji domeny zarządzanej. Czasami problemy z konfiguracją może zablokować możliwości firmy Microsoft do synchronizacji z domeną zarządzaną. Jeśli jest to możliwe rozwiązać alerty, Zaczekaj dwie godziny i sprawdź z powrotem do zobaczyć, jeśli synchronizacja została ukończona.

Poniżej przedstawiono niektóre typowe przyczyny, dlaczego synchronizacji zatrzymuje się w domenach zarządzanych:
- Połączenie sieciowe jest zablokowany w domenie zarządzanej. Aby dowiedzieć się więcej na temat sprawdzania sieci pod kątem problemów, przeczytaj, jak do [Rozwiązywanie problemów z sieciowymi grupami zabezpieczeń](active-directory-ds-troubleshoot-nsg.md) i [sieci wymagania dotyczące usługi Azure AD Domain Services](active-directory-ds-networking.md).
-  Synchronizacja haseł nigdy nie został Konfigurowanie lub ukończone. Aby skonfigurować synchronizację haseł, przeczytaj [w tym artykule](active-directory-ds-getting-started-password-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Tworzenie kopii zapasowej nie została wykonana w chwilę

**Komunikat alertu:**

*Domena zarządzana utworzenia ostatniej kopii zapasowej na [date].*

**Rozwiązanie:**

[Sprawdzanie kondycji domeny](active-directory-ds-check-health.md) w ramach wszystkich alertów, które mogą wskazywać problemy w konfiguracji domeny zarządzanej. Czasami problemy z konfiguracją może zablokować możliwości firmy Microsoft do synchronizacji z domeną zarządzaną. Jeśli jest to możliwe rozwiązać alerty, Zaczekaj dwie godziny i sprawdź z powrotem do zobaczyć, jeśli synchronizacja została ukończona.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Zawieszenie z powodu wyłączonej subskrypcji

**Komunikat alertu:**

*Domena zarządzana jest zawieszona, ponieważ nie jest aktywna subskrypcja platformy Azure skojarzony z domeną.*

**Rozwiązanie:**

> [!WARNING]
> Jeśli Twoja domena zarządzana jest wstrzymana przez dłuższy czas, jego jest usuwana. Zaleca się możliwie jak najszybciej rozwiązać zawieszenia. Aby dowiedzieć się więcej, odwiedź stronę [w tym artykule](active-directory-ds-suspension.md).

Aby przywrócić usługi [odnowić swoją subskrypcję platformy Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) skojarzonego z Twoją domeną zarządzaną.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Zawieszenie z powodu nieprawidłowej konfiguracji

**Komunikat alertu:**

*Domena zarządzana jest wstrzymana z powodu nieprawidłowej konfiguracji. Usługa nie mogła zarządzać poprawkami, lub zaktualizuj z kontrolerami domeny dla domeny zarządzanej przez długi czas.*

**Rozwiązanie:**

> [!WARNING]
> Jeśli Twoja domena zarządzana jest wstrzymana przez dłuższy czas, jego jest usuwana. Zaleca się możliwie jak najszybciej rozwiązać zawieszenia. Aby dowiedzieć się więcej, odwiedź stronę [w tym artykule](active-directory-ds-suspension.md).

[Sprawdzanie kondycji domeny](active-directory-ds-check-health.md) w ramach wszystkich alertów, które mogą wskazywać problemy w konfiguracji domeny zarządzanej. Jeśli te alerty mogą rozwiązania, należy to zrobić. Po skontaktuj się z pomocą techniczną, aby ponownie włączyć swoją subskrypcję.


## <a name="contact-us"></a>Skontaktuj się z nami
Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [Podziel się opinią lub pomocy technicznej](active-directory-ds-contact-us.md).
