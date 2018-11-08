---
title: Raport o zabezpieczeniach dotyczący użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej o raporcie o zabezpieczeniach dotyczącym użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 13d81e655b98771d985277974bd3081437e2a3e8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255783"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Rozwiązywanie problemów dotyczących użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory

Dzięki raportom o zabezpieczeniach w usłudze Azure Active Directory (Azure AD) możesz uzyskać wgląd w prawdopodobieństwo naruszenia bezpieczeństwa kont użytkowników w środowisku. Użytkownik oznaczonych flagą ryzyka jest wskaźnikiem konta użytkownika, który może być zabezpieczenia mogły zostać naruszone.

Firma Microsoft dokłada starań, aby pomóc zapewnić bezpieczne środowisko. W ramach tego zobowiązania firma Microsoft stale monitoruje działania, które są nietypowe lub zgodne ze znanymi wzorcami ataków. 


Gdy zostaną wykryte nietypowe działania, które mogą wskazywać na nieautoryzowany dostęp do pewnych kont Twoich użytkowników, otrzymasz powiadomienia umożliwiające podjęcie działań. Dostarczanie powiadomień nie oznacza, że systemy firmy Microsoft w jakikolwiek sposób zostały naruszone.
 

## <a name="access-the-users-flagged-for-risk-report"></a>Uzyskiwanie dostępu do raportu dotyczącego użytkowników oflagowanych w związku z ryzykiem

Możesz przejrzeć użytkowników oflagowanych z powodu ryzyka, korzystając z powiązanego [raportu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk) w usłudze Azure Active Directory (AD). Jeśli nie masz subskrypcji usługi Azure AD, możesz przejść przez proces subskrypcji jednorazowej bez ponoszenia kosztów na stronie [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD). W tym raporcie możesz wykonywać różne akcje:

- Wygenerować hasło tymczasowe
- Wymagać od użytkownika bezpiecznego zresetowania hasła przy następnym logowaniu
- Odrzucić ryzyko związane z użytkownikiem bez podejmowania żadnej akcji korygowania.

Aby uzyskać więcej informacji, zobacz [raport o zabezpieczeniach dotyczący użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Subskrypcja usługi Azure AD dla klientów usługi Office 365

Po wykonaniu tych czynności możesz użyć poświadczeń usługi Office 365 do uzyskania dostępu do Centrum administracyjnego platformy Azure. Po uaktywnieniu dostępu do usługi Azure AD nastąpi przekierowanie do portalu usługi Azure AD. Na poziomie subskrypcji podstawowej ilość szczegółów udostępnianych w raportach jest ograniczona. Dodatkowe dane i analizy będą dostępne dla subskrybentów platformy Azure w wersji Premium.


**Aby uzyskać dostęp do raportów o użytkownikach oflagowanych w związku z ryzykiem w Centrum administracyjnym usługi Office 365:**

1.  W menu nawigacji po lewej stronie kliknij pozycję **Centra administracyjne**. 
2.  Kliknij pozycję **Azure AD**.
3.  Zaloguj się do **Centrum administracyjnego usługi Azure Active Directory**.
4.  Jeśli u góry strony zostanie wyświetlony baner z komunikatem **Zapoznaj się z nowym portalem**, kliknij link.
4.  W menu nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**. 
5.  W okienku nawigacji w obszarze **Zabezpieczenia** kliknij pozycję **Użytkownicy oflagowani w związku z ryzykiem**.

Przejrzyj wyświetlane w tym miejscu informacje. Należy zresetować hasło dla każdego konta, które jest tutaj wymienione. 

## <a name="remediation-actions"></a>Akcje naprawcze

Wykonaj następujące akcje, aby pomóc usunąć zagrożenie z kont i zabezpieczyć swoje środowisko:

1.  [Zweryfikuj](https://aka.ms/MFAValid) poprawność informacji dla uwierzytelniania wieloskładnikowego i samoobsługowego resetowania hasła. 
2.  [Włącz](https://aka.ms/MFAuth) uwierzytelnianie wieloskładnikowe dla wszystkich użytkowników. 
3.  Za pomocą tego [skryptu korygującego](https://aka.ms/remediate) dla każdego dotkniętego konta możesz automatycznie wykonać następujące kroki: 

    a. Zresetować hasło do zabezpieczania konta i skasować aktywne sesje.

    b. Usunąć delegatów skrzynki pocztowej.

    c. Wyłączyć reguły przekazywania poczty do domen zewnętrznych.

    d. Usunąć globalną właściwość przekazywania poczty w skrzynce pocztowej.

    e. Włączyć uwierzytelnianie wieloskładnikowe na koncie użytkownika.

    f. Ustawić złożoność hasła dla konta tak, aby była wysoka.

    g. Włączyć inspekcję skrzynki pocztowej.

    h. Utworzyć dziennik inspekcji do sprawdzenia przez administratora.

4. Zbadaj dzierżawę usługi Office 365 i pozostałą infrastrukturę IT, łącznie z przeglądem wszystkich ustawień dzierżawy, kont użytkowników i ustawień konfiguracji poszczególnych użytkowników pod kątem ewentualnych modyfikacji. Sprawdź, czy wskaźniki metod trwałości oraz wskaźniki intruza mogą korzystać z początkowego stopnia w celu uzyskania poświadczeń sieci VPN lub dostępu do innych zasobów organizacji. 

5.  W ramach badania należy rozważyć, czy powinno się lub czy bezwzględnie trzeba powiadomić organy rządowe, w tym uprawnione organy ścigania.

Ponadto wykonaj następujące czynności:

- Przeczytaj i wdróż te [wskazówki](https://aka.ms/fixaccount) dotyczące rozwiązywania nietypowych działań. 
- [Włącz potok inspekcji](https://aka.ms/improvesecurity), aby pomóc analizować działania w Twojej dzierżawie. Po wykonaniu tych czynności magazyn inspekcji rozpoczyna zapełnianie wszystkich dzienników aktywności. W tym momencie możesz również wykorzystać [funkcje wyszukiwania i badania Centrum zabezpieczeń i zgodności](https://aka.ms/sccsearch). 
- Użyj tego [skryptu](https://aka.ms/mailboxaudit1), aby włączyć inspekcję skrzynki pocztowej dla wszystkich kont. 
- Przejrzyj reguły delegowania uprawnień i przekazywania poczty dla wszystkich skrzynek pocztowych. Do wykonania tego zadania możesz użyć tego [skryptu programu PowerShell](https://aka.ms/delegateforwardrules). 



## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat ochrony tożsamości w usłudze Azure Active Directory, zobacz [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md).

