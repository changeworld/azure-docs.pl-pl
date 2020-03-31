---
title: Użytkownicy oflagowani pod kątem ryzyka w portalu usługi Azure Active Directory | Dokumenty firmy Microsoft
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
ms.openlocfilehash: 30d02c5484ea4cce2953eac6b1b7b26a17c142bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68989696"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Rozwiązywanie problemów dotyczących użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory

Za pomocą raportów zabezpieczeń w usłudze Azure Active Directory (Azure AD) można ocenić prawdopodobieństwo naruszenia zabezpieczeń kont użytkowników w twoim środowisku. Użytkownik oflagowany w związku z ryzykiem jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Firma Microsoft dokłada starań, aby pomóc zapewnić bezpieczne środowisko. W ramach tego zobowiązania firma Microsoft stale monitoruje działania, które są nietypowe lub zgodne ze znanymi wzorcami ataków. 

Jeśli zostaną wykryte nietypowe działania, które mogą wskazywać na nieautoryzowany dostęp do niektórych kont użytkowników, otrzymasz powiadomienia umożliwiające podjęcie działań. Nie oznacza to, że własne systemy Firmy Microsoft zostały naruszone.

## <a name="access-the-users-flagged-for-risk-report"></a>Uzyskiwanie dostępu do raportu dotyczącego użytkowników oflagowanych w związku z ryzykiem

Możesz przejrzeć użytkowników oflagowanych pod kątem ryzyka za pośrednictwem [raportu o ryzyku użytkowników](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) w witrynie Azure portal. Jeśli nie masz usługi Azure AD, możesz zarejestrować się bezpłatnie w witrynie [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD). 

Od użytkowników oflagowanych do raportu o ryzyku można podjąć następujące działania dla każdego użytkownika:

- Wygenerować hasło tymczasowe
- Wymagać od użytkownika bezpiecznego zresetowania hasła przy następnym logowaniu
- Odrzucić ryzyko związane z użytkownikiem bez podejmowania żadnej akcji korygowania.

Aby uzyskać więcej informacji, zobacz [Użytkownicy oflagowani raportem zabezpieczeń ryzyka](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Subskrypcja usługi Azure AD dla klientów usługi Office 365

Możesz również użyć poświadczeń usługi Office 365, aby uzyskać dostęp do **Centrum administracyjnego platformy Azure**. Po uaktywnieniu dostępu do usługi Azure AD nastąpi przekierowanie do portalu usługi Azure AD. Na poziomie subskrypcji podstawowej ilość szczegółów udostępnianych w raportach jest ograniczona. Dodatkowe dane i analizy będą dostępne dla subskrybentów platformy Azure w wersji Premium.

Aby uzyskać dostęp **do użytkowników oflagowanych dla** raportów o ryzyku w centrum administracyjnym usługi Microsoft 365:

1.  Z menu nawigacji po lewej stronie wybierz pozycję **Centra administracyjne**. 
2.  Wybierz **usługę Azure AD**.
3.  Zaloguj się do **Centrum administracyjnego usługi Azure Active Directory**.
4.  Jeśli w górnej części strony zostanie wyświetlony baner z **napisem Wyewidencjonuj nowy portal,** wybierz łącze.
4.  W menu nawigacyjnym po lewej stronie wybierz pozycję **Azure Active Directory**. 
5.  W okienku nawigacji wybierz **pozycję Użytkownicy oflagowani pod kątem ryzyka** w sekcji **Zabezpieczenia.**

## <a name="remediation-actions"></a>Akcje naprawcze

Wykonaj następujące akcje, aby pomóc usunąć zagrożenie z kont i zabezpieczyć swoje środowisko:

1.  [Sprawdź poprawność poprawnych informacji](https://aka.ms/MFAValid) dotyczących uwierzytelniania wieloskładnikowego i resetowania hasła samoobsługowego. 
2.  [Włącz uwierzytelnianie wieloskładnikowe](https://aka.ms/MFAuth) dla wszystkich użytkowników. 
3.  Ten [skrypt korygowania](https://aka.ms/remediate) służy do wykonywania wszystkich kont, których dotyczy problem, aby automatycznie wykonać następujące czynności: 

    a. Resetowanie hasła w celu zabezpieczenia konta i zabicie aktywnych sesji.

    b. Usunąć delegatów skrzynki pocztowej.

    d. Wyłączyć reguły przekazywania poczty do domen zewnętrznych.

    d. Usunąć globalną właściwość przekazywania poczty w skrzynce pocztowej.

    e. Włączyć uwierzytelnianie wieloskładnikowe na koncie użytkownika.

    f. Ustawić złożoność hasła dla konta tak, aby była wysoka.

    g. Włączyć inspekcję skrzynki pocztowej.

    h. Tworzenie dziennika inspekcji dla administratora do przejrzenia.

4. Zbadaj dzierżawę usługi Office 365 i pozostałą infrastrukturę IT, łącznie z przeglądem wszystkich ustawień dzierżawy, kont użytkowników i ustawień konfiguracji poszczególnych użytkowników pod kątem ewentualnych modyfikacji. Sprawdź, czy wskaźniki metod trwałości oraz wskaźniki intruza mogą korzystać z początkowego stopnia w celu uzyskania poświadczeń sieci VPN lub dostępu do innych zasobów organizacji. 

5.  W ramach dochodzenia zastanów się, czy powinieneś powiadomić o tym organy rządowe, w tym organy ścigania.

Ponadto wykonaj następujące czynności:

- Przeczytaj i zaimplementuj [niniejsze wytyczne dotyczące rozwiązywania nietypowych działań](https://aka.ms/fixaccount). 
- [Włącz potok inspekcji,](https://aka.ms/improvesecurity) aby ułatwić analizowanie działania w dzierżawie. Po zakończeniu magazynu inspekcji rozpoczyna wypełnianie dzienników aktywności. W tym momencie można również wykorzystać [zasoby wyszukiwania i badania Centrum zabezpieczeń i zgodności](https://aka.ms/sccsearch). 
- Ten skrypt służy [do włączania inspekcji skrzynek pocztowych](https://aka.ms/mailboxaudit1) dla wszystkich kont. 
- Przejrzyj reguły delegowania uprawnień i przekazywania poczty dla wszystkich skrzynek pocztowych. Do wykonania tego zadania możesz użyć tego [skryptu programu PowerShell](https://aka.ms/delegateforwardrules). 

## <a name="next-steps"></a>Następne kroki

* [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
* [Użytkownicy oflagowani w związku z ryzykiem](concept-user-at-risk.md)
