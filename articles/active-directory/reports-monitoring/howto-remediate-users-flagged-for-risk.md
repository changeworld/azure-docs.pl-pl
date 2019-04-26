---
title: Raport o zabezpieczeniach dotyczący użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej o raporcie o zabezpieczeniach dotyczącym użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7209f468f493e226fae22ccd260e8ceb2e570494
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60286669"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Rozwiązywanie problemów dotyczących użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory

Dzięki raportom o zabezpieczeniach w usłudze Azure Active Directory (Azure AD) można mierzyć prawdopodobieństwo naruszenia bezpieczeństwa kont użytkowników w danym środowisku. Użytkownik oznaczonych flagą ryzyka jest wskaźnikiem konta użytkownika, który może być zabezpieczenia mogły zostać naruszone.

Firma Microsoft dokłada starań, aby pomóc zapewnić bezpieczne środowisko. W ramach tego zobowiązania firma Microsoft stale monitoruje działania, które są nietypowe lub zgodne ze znanymi wzorcami ataków. 

W przypadku wykrycia nietypowych działań, które mogą wskazywać na nieautoryzowany dostęp do pewnych kont Twoich użytkowników, otrzymasz powiadomienia umożliwiające podjęcie działań. Nie oznacza to, że systemy firmy Microsoft zostały złamane.

## <a name="access-the-users-flagged-for-risk-report"></a>Uzyskiwanie dostępu do raportu dotyczącego użytkowników oflagowanych w związku z ryzykiem

Możesz przejrzeć użytkowników oznaczonych flagą ryzyka za pośrednictwem [raport dotyczący narażonych użytkowników](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) w witrynie Azure portal. Jeśli nie masz usługi Azure AD, możesz zapisać się za darmo w [ https://aka.ms/AccessAAD ](https://aka.ms/AccessAAD). 

Od użytkowników oflagowanych w raporcie o podwyższonym ryzyku można wykonać następujące czynności dla każdego użytkownika:

- Wygenerować hasło tymczasowe
- Wymagać od użytkownika bezpiecznego zresetowania hasła przy następnym logowaniu
- Odrzucić ryzyko związane z użytkownikiem bez podejmowania żadnej akcji korygowania.

Aby uzyskać więcej informacji, zobacz [użytkowników oflagowanych w raporcie o zabezpieczeniach ryzyka](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Subskrypcja usługi Azure AD dla klientów usługi Office 365

Można również użyć poświadczeń usługi Office 365 dostęp do **Centrum administracyjnego platformy Azure**. Po uaktywnieniu dostępu do usługi Azure AD nastąpi przekierowanie do portalu usługi Azure AD. Na poziomie subskrypcji podstawowej ilość szczegółów udostępnianych w raportach jest ograniczona. Dodatkowe dane i analizy będą dostępne dla subskrybentów platformy Azure w wersji Premium.

Aby uzyskać dostęp do **użytkownicy oflagowani w związku z ryzykiem** raportów w Centrum administracyjnym usługi Microsoft 365:

1.  W menu nawigacji po lewej stronie wybierz **centra administracyjne**. 
2.  Wybierz **usługi Azure AD**.
3.  Zaloguj się do **Centrum administracyjnego usługi Azure Active Directory**.
4.  Jeśli w górnej części strony, który jest wyświetlany komunikat zostanie wyświetlony Baner z **zapoznaj się z nowym portalem**, wybierz link.
4.  W menu nawigacji po lewej stronie wybierz **usługi Azure Active Directory**. 
5.  W okienku nawigacji wybierz **użytkownicy oflagowani w związku z ryzykiem** z **zabezpieczeń** sekcji.

## <a name="remediation-actions"></a>Akcje naprawcze

Wykonaj następujące akcje, aby pomóc usunąć zagrożenie z kont i zabezpieczyć swoje środowisko:

1.  [Sprawdź poprawność poprawnych informacji](https://aka.ms/MFAValid) uwierzytelniania wieloskładnikowego i samoobsługowego hasła zresetować. 
2.  [Włącz uwierzytelnianie wieloskładnikowe](https://aka.ms/MFAuth) dla wszystkich użytkowników. 
3.  Użyj tego [skrypt korygujący](https://aka.ms/remediate) dla każdego dotkniętego konta, aby automatycznie wykonać następujące czynności: 

    a. Zresetuj hasło do zabezpieczania konta i skasować aktywne sesje.

    b. Usunąć delegatów skrzynki pocztowej.

    c. Wyłączyć reguły przekazywania poczty do domen zewnętrznych.

    d. Usunąć globalną właściwość przekazywania poczty w skrzynce pocztowej.

    e. Włączyć uwierzytelnianie wieloskładnikowe na koncie użytkownika.

    f. Ustawić złożoność hasła dla konta tak, aby była wysoka.

    g. Włączyć inspekcję skrzynki pocztowej.

    h. Utworzyć dziennik inspekcji dla administratora przejrzeć.

4. Zbadaj dzierżawę usługi Office 365 i pozostałą infrastrukturę IT, łącznie z przeglądem wszystkich ustawień dzierżawy, kont użytkowników i ustawień konfiguracji poszczególnych użytkowników pod kątem ewentualnych modyfikacji. Sprawdź, czy wskaźniki metod trwałości oraz wskaźniki intruza mogą korzystać z początkowego stopnia w celu uzyskania poświadczeń sieci VPN lub dostępu do innych zasobów organizacji. 

5.  W ramach badania rozważ, czy należy powiadomić organy rządowe, w tym organom ścigania.

Ponadto wykonaj następujące czynności:

- Przeczytaj i wdróż te [wskazówki dotyczące rozwiązywania nietypowych działań](https://aka.ms/fixaccount). 
- [Włącz potok inspekcji](https://aka.ms/improvesecurity) aby pomóc analizować działania w ramach dzierżawy. Po wykonaniu tych czynności, Magazyn inspekcji rozpoczyna zapełnianie dzienników aktywności. W tym momencie można również korzystać z [zasobów wyszukiwania i badania Centrum zabezpieczeń i zgodności firmy](https://aka.ms/sccsearch). 
- Użyj tego [skrypt, aby włączyć inspekcję skrzynki pocztowej](https://aka.ms/mailboxaudit1) dla wszystkich kont. 
- Przejrzyj reguły delegowania uprawnień i przekazywania poczty dla wszystkich skrzynek pocztowych. Do wykonania tego zadania możesz użyć tego [skryptu programu PowerShell](https://aka.ms/delegateforwardrules). 

## <a name="next-steps"></a>Kolejne kroki

* [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
* [Użytkowników oznaczonych flagą ryzyka](concept-user-at-risk.md)
