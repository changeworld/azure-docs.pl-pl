---
title: Federacja tożsamości w Australii platformy Azure
description: Wskazówki dotyczące konfigurowania federacji tożsamości w regionach australijskich w celu spełnienia określonych wymagań australijskich zasad, przepisów i przepisów obowiązujących dla instytucji rządowych.
author: galey801
ms.service: azure-australia
ms.topic: quickstart
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e161e36ee7b403381b65f52a6f416be09025d0a2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570859"
---
# <a name="identity-federation-in-azure-australia"></a>Federacja tożsamości w Australii platformy Azure

Zarządzanie tożsamościami i Federacji z ofertami w chmurze publicznej jest jednym z najważniejszych kroków do korzystania z chmury. Usługa Azure Active Directory firmy Microsoft przechowuje informacje o użytkownikach w celu umożliwienia dostępu do usług w chmurze i jest warunkiem wstępnym korzystania z innych usług platformy Azure.

W tym artykule opisano kluczowe punkty projektowania dotyczące implementowania Azure Active Directory, synchronizacji użytkowników z domeny Active Directory Domain Services i implementowania bezpiecznego uwierzytelniania. Szczególny fokus jest umieszczany na zaleceniach zawartych w Security Center cybernetycznymi australijskie informacje o zabezpieczeniach firmy Microsoft (ISM) i w raportach certyfikacji platformy Azure.

Klasyfikacja informacji przechowywanych w Azure Active Directory powinna informować o tym, jak została zaprojektowana. Poniższy fragment jest dostępny w [raporcie certyfikacji ACSC — Microsoft Azure](https://aka.ms/au-irap):

>**Raport certyfikacji ACSC — Microsoft Azure** Azure Active Directory (Azure AD) należy skonfigurować za pomocą usługi Active Directory Federation Services, gdy jednostki Wspólnoty klasyfikują użycie i zawartość danych Active Directory w trybie CHRONIONYm. Chociaż Active Directory dane w niesklasyfikowanej klasyfikacji oznaczeń (UDLM) nie wymagają Federacji, jednostki Wspólnoty nadal mogą zaimplementować Federacji, aby ograniczyć ryzyko związane z usługą dostarczaną z zewnątrz z Australii.

W związku z tym te informacje są zsynchronizowane i mechanizm, za pomocą którego użytkownicy są uwierzytelniani, są tymi, które zostały omówione w tym miejscu.

## <a name="key-design-considerations"></a>Podstawowe zagadnienia dotyczące projektowania

### <a name="user-synchronisation"></a>Synchronizacja użytkownika

Podczas wdrażania Azure AD Connect istnieje kilka decyzji, które należy wykonać, aby uzyskać informacje na temat danych, które zostaną zsynchronizowane. Azure AD Connect jest oparta na Microsoft Identity Manager i oferuje niezawodny zestaw funkcji służący do [przekształcania](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-best-practices-changing-default-configuration) danych między katalogami.

Usługi doradcze firmy Microsoft mogą być zaangażowane w ADRAP ocenę istniejących Active Directory systemu Windows Server. ADRAP pomaga w ustaleniu wszelkich problemów, które mogą wymagać skorygowania przed zsynchronizowaniem z Azure Active Directory. Umowy Microsoft pomoc techniczna Premier Agreement zazwyczaj obejmują tę usługę.

[Narzędzie IdFix](https://docs.microsoft.com/office365/enterprise/install-and-run-idfix) skanuje lokalną domenę Active Directory pod kątem problemów przed rozpoczęciem synchronizacji z usługą Azure AD. IDFix to klucz pierwszego kroku przed wdrożeniem Azure AD Connect. Chociaż skanowanie IDFix może identyfikować dużą liczbę problemów, wiele z tych problemów może być szybko rozwiązywana ze skryptami lub działaniem przy użyciu transformacji danych w Azure AD Connect.

Usługa Azure AD wymaga, aby użytkownicy mieli zewnętrznie trasowane domeny najwyższego poziomu w celu włączenia uwierzytelniania. Jeśli domena ma sufiks nazwy UPN, który nie jest obsługiwany zewnętrznie, należy ustawić [alternatywny identyfikator logowania](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname) w programie AD Connect na atrybut poczty użytkownika. Użytkownicy logują się do usług platformy Azure przy użyciu adresu e-mail, a nie do logowania się do domeny.

Sufiks nazwy UPN na kontach użytkowników można także zmienić za pomocą takich narzędzi, jak program PowerShell. może mieć nieprzewidziane konsekwencje dla innych połączonych systemów i nie jest już uważane za najlepsze rozwiązanie.

Przy wyborze atrybutów do Azure Active Directory, najbezpieczniejszym jest założenie, że wszystkie atrybuty są wymagane. W katalogu nie może znajdować się faktyczne chronione dane, jednak inspekcja jest zalecana. Jeśli chronione dane znajdują się w katalogu, należy ocenić wpływ pomijania lub transformacji atrybutu. W ramach przydatnego przewodnika istnieje lista atrybutów [wymaganych](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized)przez usługi Microsoft Cloud.

### <a name="authentication"></a>Authentication

Ważne jest zapoznanie się z dostępnymi opcjami oraz sposób ich użycia w celu zapewnienia bezpieczeństwa użytkownikom końcowym.
Firma Microsoft oferuje [trzy natywne rozwiązania](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) do uwierzytelniania użytkowników przed Azure Active Directory:

* Synchronizacja skrótów haseł — hasła z Active Directory Domain Services są synchronizowane przez Azure AD Connect do Azure Active Directory.
* [Uwierzytelnianie przekazywane](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) — hasła pozostają w Active Directory Domain Services. Użytkownicy są uwierzytelniani względem Active Directory Domain Services za pośrednictwem agenta. Żadne hasła nie są przechowywane w usłudze Azure AD.
* [Federacyjnego logowanie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-whatis) jednokrotne — Azure Active Directory jest federacyjne z Active Directory Federation Services, podczas logowania system Azure kieruje użytkowników do Active Directory Federation Services uwierzytelniania. Żadne hasła nie są przechowywane w usłudze Azure AD.

Synchronizacja skrótów haseł może być używana w scenariuszach, w których oficjalne: dane poufne i poniżej są przechowywane w katalogu. Scenariusze, w których przechowywane są chronione dane, będą wymagały jednej z dwóch pozostałych opcji.

Wszystkie trzy z tych opcji obsługują [zapisywanie zwrotne haseł](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback), które [Przewodnik po klientach ACSC](https://aka.ms/au-irap) zaleca się wyłączyć. Ale organizacje powinny oszacować ryzyko wyłączenia funkcji zapisywania zwrotnego haseł przed wzrostem produktywności i krótszym nakładem pracy związanym z korzystaniem z samoobsługowego resetowania haseł.

#### <a name="pass-through-authentication-pta"></a>Uwierzytelnianie przekazywane (PTA)

Uwierzytelnianie przekazywane zostało wydane po zakończeniu oceny IRAP i w związku z tym; należy indywidualnie ocenić, aby określić, jak rozwiązanie pasuje do profilu ryzyka organizacji. Uwierzytelnianie przekazywane jest preferowane w porównaniu z Federacją firmy Microsoft ze względu na ulepszony stan zabezpieczeń.

![Uwierzytelnianie przekazywane](media/pta1.png)

Uwierzytelnianie przekazywane przedstawia kilka czynników projektowych, które należy wziąć pod uwagę:

* Agent uwierzytelniania przekazywanego musi mieć możliwość nawiązywania połączeń wychodzących z usługami Microsoft Cloud.
* Zainstalowanie więcej niż jednego agenta, aby upewnić się, że usługa będzie wysoce dostępna. Najlepszym rozwiązaniem jest wdrożenie co najmniej trzech agentów i maksymalnie 12 agentów.
* Najlepszym rozwiązaniem jest uniknięcie instalacji agenta bezpośrednio na kontrolerach domena usługi Active Directory. Domyślnie podczas wdrażania Azure AD Connect z użyciem uwierzytelniania przekazywanego Agent zainstaluje agenta na serwerze programu AD Connect.
* Uwierzytelnianie przekazywane jest niższą opcją konserwacji niż Active Directory Federation Services, ponieważ nie wymaga dedykowanej infrastruktury serwera, zarządzania certyfikatami lub reguł zapory dla ruchu przychodzącego.

#### <a name="active-directory-federation-services-adfs"></a>Active Directory Federation Services (ADFS)

Active Directory Federation Services został uwzględniony w ocenie IRAP i jest zatwierdzony do użycia w środowiskach chronionych.

![Federacja](media/federated-identity.png)

Active Directory Federation Services przedstawia kilka czynników projektowych, które należy wziąć pod uwagę:

* Usługi federacyjne wymagają ruchu przychodzącego z Internetu przez Internet lub z minimalnymi punktami końcowymi usługi firmy Microsoft.
* Usługi federacyjne używają infrastruktury PKI i certyfikatów, które wymagają ciągłego zarządzania i odnawiania.
* Usługi federacyjne należy wdrożyć na dedykowanych serwerach i wymagać odpowiedniej infrastruktury sieciowej, aby zapewnić jej bezpieczną dostępność na zewnątrz.

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

Sekcja ISM w ramach usługi uwierzytelnianie wieloskładnikowe zaleca wdrożenie jej w następujących scenariuszach w oparciu o profil ryzyka:

* Uwierzytelnianie użytkowników standardowych
* Uwierzytelnianie uprzywilejowanych kont
* Uwierzytelnianie dostępu zdalnego użytkowników
* Użytkownicy wykonujący uprzywilejowane akcje

Azure Active Directory zapewnia uwierzytelnianie wieloskładnikowe, które może być włączone dla wszystkich lub podzbioru użytkowników (na przykład tylko konta uprzywilejowane). Firma Microsoft udostępnia również rozwiązanie o nazwie dostęp warunkowy, które zapewnia bardziej szczegółową kontrolę nad sposobem stosowania uwierzytelniania wieloskładnikowego (na przykład tylko wtedy, gdy użytkownicy logują się ze zdalnych zakresów adresów IP).

Usługa Azure MFA Authentication obsługuje następujące, akceptowalne formy weryfikacji:

* Połączenie telefoniczne
* Wiadomość SMS
* Aplikacja Microsoft Authenticator
* Obsługiwane tokeny sprzętowe

Privileged Identity Management składnik Azure Active Directory służy do wymuszania użycia uwierzytelniania wieloskładnikowego, gdy użytkownicy podnieśą swoje uprawnienia, aby spełnić czwarte zalecenie.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem na temat [kontroli dostępu opartej na rolach i Privileged Identity Management](role-privileged.md).
