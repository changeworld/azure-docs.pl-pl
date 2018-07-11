---
title: Jakie są warunki dostępu warunkowego usługi Azure Active Directory? | Microsoft Docs
description: Dowiedz się, jak używane warunków do wyzwolenia zasad dostępu warunkowego usługi Azure Active Directory.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 42792170593dbd94d0eae9b408c70f326891508a
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "36232471"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Jakie są warunki dostępu warunkowego usługi Azure Active Directory? 

Za pomocą [dostępu warunkowego usługi Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), jak autoryzowanego dostępu użytkowników można kontrolować swoje aplikacje w chmurze. W zasadach dostępu warunkowego zdefiniujesz odpowiedzi ("to") można Przyczyna służącą do wyzwalania zasad ("w takim przypadku"). 

![Kontrola](./media/active-directory-conditional-access-conditions/10.png)


W kontekście dostępu warunkowego:

- "**w takim przypadku**" nosi nazwę **warunki**. 
- "**To zrobić**" nosi nazwę **kontrole dostępu**.

Kombinacja warunkach przy użyciu usługi kontroli dostępu reprezentuje zasady dostępu warunkowego.

![Kontrola](./media/active-directory-conditional-access-conditions/61.png)


Warunki, które nie zostały skonfigurowane w zasadach dostępu warunkowego nie są stosowane. Niektóre warunki są [obowiązkowe](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work) zastosowania zasad dostępu warunkowego do środowiska. 

Ten artykuł zawiera omówienie warunki i jak są używane w zasadach dostępu warunkowego. 

## <a name="users-and-groups"></a>Użytkownicy i grupy

Warunek użytkowników i grup jest obowiązkowe w zasadach dostępu warunkowego. W zasadach, możesz wybrać operator **wszyscy użytkownicy** lub wybierz konkretnych użytkowników i grup.

![Kontrola](./media/active-directory-conditional-access-conditions/111.png)

Po wybraniu:

- **Wszyscy użytkownicy**, zasady są stosowane do wszystkich użytkowników w katalogu. Obejmuje to użytkowników-gości.

- **Wybierz użytkowników i grupy**, można ustawić następujące opcje:

    - **Wszyscy użytkownicy-goście** — pozwala na docelowe zasad użytkowników-gości B2B. Ten warunek pasuje do dowolnego konta użytkownika z *userType* ustawioną wartość atrybutu *gościa*. W przypadkach, gdy zasady musi zostać zastosowana zaraz po utworzeniu konta w usłudze flow zaproszenie w usłudze Azure AD, możesz użyć tego ustawienia.

    - **Role w katalogach** — pozwala na docelowe dla zasad, w oparciu o przypisania roli użytkownika. Ten warunek obsługuje ról w katalogu, takie jak *administratora globalnego* lub *administrator haseł*.

    - **Użytkownicy i grupy** — pozwala na konkretnych zestawów docelowych użytkowników. Na przykład można wybrać grupy, która zawiera wszystkie elementy członkowskie dział KADR, jeśli masz aplikację HR wybrany jako aplikacja w chmurze.

Grupy, może być dowolnego typu grupy w usłudze Azure AD, w tym dynamiczne lub przypisane grupy zabezpieczeń i dystrybucji

Z zasad, można wykluczyć określonych użytkowników lub grup. Jeden typowy przypadek użycia są kont usług w przypadku zasady uwierzytelniania wieloskładnikowego (MFA). 

Przeznaczone dla konkretnych zestawów użytkowników jest przydatne w przypadku wdrażania nowych zasad. W nowych zasad powinien dotyczyć tylko początkowy zestaw użytkowników, aby zweryfikować zachowanie zasad. 



## <a name="cloud-apps"></a>Aplikacje w chmurze 

Aplikacja w chmurze jest witryn sieci web lub usługi. W tym witryny sieci web chronionych przez serwer Proxy aplikacji platformy Azure. Aby uzyskać szczegółowy opis obsługiwanymi aplikacjami w chmurze, zobacz [przypisania aplikacji w chmurze](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

Warunek aplikacje chmury jest obowiązkowe w zasadach dostępu warunkowego. W zasadach, możesz wybrać operator **wszystkie aplikacje w chmurze** lub wybierz określone aplikacje.

![Kontrola](./media/active-directory-conditional-access-conditions/03.png)

Można wybrać:

- **Wszystkie aplikacje w chmurze** do linii bazowej zasad mają być stosowane do całej organizacji. Typowy przypadek użycia dla tego zaznaczenia jest zasada, która wymaga uwierzytelniania wieloskładnikowego w przypadku wykrycia ryzyka logowania dla dowolnej aplikacji w chmurze. Zasady stosowane do **wszystkie aplikacje w chmurze** dotyczy dostępu do wszystkich witryn sieci web i usług. To ustawienie nie jest ograniczony do aplikacji w chmurze, które pojawiają się na **aplikacji w chmurze wybierz** listy.

- Aplikacje w chmurze poszczególnych do określonych usług docelowego przez zasady. Na przykład, możesz wymagać od użytkowników mają [zgodnego urządzenia](active-directory-conditional-access-policy-connected-applications.md) dostęp do usługi SharePoint Online. Ta zasada jest stosowana także do innych usług, podczas uzyskiwania dostępu do zawartości programu SharePoint, na przykład, Microsoft Teams. 

Można również wykluczyć określone aplikacje z zasad; te aplikacje są jednak nadal podlega procesowi zasady zastosowane do usług, do których uzyskują dostęp. 



## <a name="sign-in-risk"></a>Ryzyko logowania

Ryzyko logowania jest wskaźnikiem prawdopodobieństwo (wysoki, średni lub niski), czy próba logowania nie było wykonywane przez prawowitym właścicielem konta użytkownika. Usługa Azure AD oblicza poziom ryzyka logowania podczas logowania użytkownika. Można użyć poziomu obliczeniowego ryzyka logowania jako warunku w zasadach dostępu warunkowego. 

![Warunki](./media/active-directory-conditional-access-conditions/22.png)

Aby użyć tego warunku, musisz mieć [usługi Azure Active Directory Identity Protection](active-directory-identityprotection.md) włączone.
 
Typowe przypadki użycia dla tego warunku są zasadami, które:

- Blok użytkownicy z wysoki ryzyka logowania, aby uniemożliwić potencjalnie innych wiarygodnych użytkownikom dostęp do aplikacji w chmurze. 
- Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników z średniego ryzyka logowania. Wymuszając uwierzytelnianie wieloskładnikowe, możesz podać dodatkowe pewność, że logowanie odbywa się przez prawowitym właścicielem konta.

Aby uzyskać więcej informacji, zobacz [Ryzykowne logowania](active-directory-identityprotection.md#risky-sign-ins).  

## <a name="device-platforms"></a>Platformy urządzeń

Platformy urządzeń jest określony przez system operacyjny, który jest uruchomiony na twoim urządzeniu. Usługa Azure AD identyfikuje platformy przy użyciu informacji dostarczonych przez urządzenia, takie jak agenta użytkownika. Ponieważ te informacje są niezweryfikowanych, zaleca się, że wszystkie platformy mają zasad zastosowanych do nich przez blokowanie dostępu, wymagające zgodności z zasadami usługi Intune lub konieczności, że urządzenie jest przyłączone do domeny. Wartość domyślna to, aby zastosować zasady do wszystkich platform urządzeń. 


![Warunki](./media/active-directory-conditional-access-conditions/24.png)

Aby uzyskać pełną listę obsługiwanych platform urządzeń, zobacz [warunek platformy urządzenia](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Typowy przypadek użycia dla tego warunku jest zasadę, która ogranicza dostęp do aplikacji w chmurze do [urządzeń zarządzanych przy użyciu](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Aby uzyskać więcej scenariuszy, w tym warunku platformy urządzeń, zobacz [dostępu warunkowego opartego na aplikacji usługi Azure Active Directory](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Stan urządzenia

Warunek stanu urządzenia umożliwia przyłączone do hybrydowej usługi Azure AD i urządzenia oznaczone jako zgodne, które mają być wykluczone z zasad dostępu warunkowego. Jest to przydatne, gdy zasady dotyczą tylko niezarządzanego urządzenia do zapewnienia bezpieczeństwa sesji. Na przykład tylko wymuszania kontroli sesji Microsoft Cloud App Security po niezarządzanego urządzenia. 


![Warunki](./media/active-directory-conditional-access-conditions/112.png)

Jeśli chcesz zablokować dostęp dla niezarządzanych urządzeń, należy zaimplementować [dostępu warunkowego opartego na urządzeniu](active-directory-conditional-access-policy-connected-applications.md).


## <a name="locations"></a>Lokalizacje

Z lokalizacji masz możliwość definiowania warunków, które są oparte na której zainicjowano próby nawiązania połączenia z. 
     
![Warunki](./media/active-directory-conditional-access-conditions/25.png)

Typowe przypadki użycia dla tego warunku są zasadami, które:

- Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników uzyskujących dostęp do usługi, gdy znajdują się poza siecią firmową.  

- Zablokuj dostęp dla użytkowników uzyskujących dostęp do usługi z określonych krajów lub regionów. 

Aby uzyskać więcej informacji, zobacz [co to jest warunek lokalizacji w funkcji dostępu warunkowego usługi Azure Active Directory?](active-directory-conditional-access-locations.md)


## <a name="client-apps"></a>Aplikacje klienckie

Stan aplikacji klienta umożliwia stosowanie zasad do różnych typów aplikacji, takich jak:

- Witryny sieci Web i usług
- Aplikacje mobilne i aplikacje klasyczne. 



Aplikacja jest klasyfikowany jako:

- Witryny sieci web lub usługi za pomocą protokołów Usługa rejestracji Jednokrotnej w sieci web, SAML, WS-Fed lub OpenID Connect dla poufnych klienta.

- A aplikacji mobilnej lub aplikacji pulpitu, jeśli klient natywny używa aplikacji mobilnej, OpenID Connect.

Aby uzyskać pełną listę aplikacji klienckich, w których można używać w zasadach dostępu warunkowego, zobacz [warunek aplikacje klienckie](active-directory-conditional-access-technical-reference.md#client-apps-condition) w techniczne dotyczące usługi Azure Active Directory dostępu warunkowego.

Typowe przypadki użycia dla tego warunku są zasadami, które:

- Wymagaj [zgodnego urządzenia](active-directory-conditional-access-policy-connected-applications.md) dla aplikacji mobilnych i klasycznych, które pobieranie dużych ilości danych na urządzeniu, zezwalając na dostęp z dowolnego urządzenia za pomocą przeglądarki.

- Blokuj dostęp z aplikacji sieci web, ale zezwalaj na dostęp z aplikacji mobilnych i komputerowych.

Oprócz przy użyciu rejestracji Jednokrotnej w sieci web i protokoły nowoczesnego uwierzytelniania, można zastosować ten warunek do aplikacji Poczta, które używają programu Exchange ActiveSync, takich jak aplikacje natywnego programu pocztowego w większości smartfony. Obecnie aplikacje klienckie przy użyciu starszych protokołów muszą być chronione przy użyciu usług AD FS.

Ten problem można wybrać tylko, jeśli **Office 365 Exchange Online** jest jedyną aplikacją w chmurze wybrano.

![Aplikacje w chmurze](./media/active-directory-conditional-access-conditions/32.png)

Wybieranie **programu Exchange ActiveSync** jako aplikacje klienckie warunek jest obsługiwana tylko jeśli nie masz inne warunki w zasadach skonfigurowano. Jednak można zawęzić zakres ten stan do mają zastosowanie tylko do obsługiwanych platform.

 
![Obsługiwane platformy](./media/active-directory-conditional-access-conditions/33.png)

Zastosowanie tego warunku tylko do obsługiwanych platform jest to równoważne do wszystkich platform urządzeń w [warunek platformy urządzenia](active-directory-conditional-access-conditions.md#device-platforms).

![Obsługiwane platformy](./media/active-directory-conditional-access-conditions/34.png)


 Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie usługi SharePoint Online i usługi Exchange Online dla dostępu warunkowego usługi Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md)
 
- [Usługa Azure Active Directory na podstawie aplikacji dostępu warunkowego](active-directory-conditional-access-mam.md) 


### <a name="legacy-authentication"></a>Starsze uwierzytelnianie  

Dostęp warunkowy dotyczy teraz starsi klienci pakietu Office, które nie obsługują nowoczesnego uwierzytelniania, a także klientów, którzy używają protokołów poczty, takich jak POP, IMAP, SMTP itd. Dzięki temu można skonfigurować zasady, takie jak **zablokować dostęp z innych klientów**.


![Starsze uwierzytelnianie](./media/active-directory-conditional-access-conditions/160.png)
 



#### <a name="known-issues"></a>Znane problemy

- Konfigurowanie zasad dla **inni klienci** blokuje całą organizację, z niektórych klientów, takich jak SPConnect. Jest to spowodowane starszych klientów uwierzytelniających się w nieoczekiwany sposób. Ten problem nie ma zastosowania do głównej aplikacji pakietu Office, takich jak starsi klienci pakietu Office. 

- Może upłynąć do 24 godzin dla zasad, które zostały wprowadzone. 


#### <a name="frequently-asked-questions"></a>Często zadawane pytania

**Spowoduje to zablokowanie usług sieci Web programu Exchange (EWS)**

Zależy to protokół uwierzytelniania, która używa EWS. Aplikacja EWS korzystających z nowoczesnego uwierzytelniania, zostaną objęte po wybraniu aplikacji klienckiej "aplikacje mobilne i klienci stacjonarni". Aplikacja EWS używa uwierzytelniania podstawowego, zostaną objęte po wybraniu aplikacji klienckiej "Inni klienci".


**Kontrolki można używać dla innych klientów**

Można skonfigurować dowolną kontrolkę dla "Innych klientów". Jednak środowisko użytkownika końcowego będzie Zablokuj dostęp we wszystkich przypadkach. "Innych klientów" nie obsługują formanty, takie jak uwierzytelnianie wieloskładnikowe, zgodne urządzenie, przyłączanie do domeny, itp. 
 
**Jakie warunki można używać dla innych klientów?**

Wprowadź wszystkie jego warunki można skonfigurować dla "Innych klientów".

**Program Exchange ActiveSync obsługuje wszystkie warunki i kontrolki?**

Nie. Poniżej przedstawiono podsumowanie obsługi protokołu Exchange ActiveSync (EAS):

- Program EAS obsługuje tylko użytkowników i grupy określania wartości docelowej. Gość, ról nie jest obsługiwana. Jeśli warunek gościa/roli jest skonfigurowany, ponieważ nie można określić, jeśli mają dotyczyć zasady użytkownika lub nie zostanie zablokowany wszystkich użytkowników.

- Program EAS działa tylko z programem Exchange jako aplikacji w chmurze. 

- Program EAS nie obsługuje dowolny warunek z wyjątkiem samej aplikacji klienta.

- Program EAS można skonfigurować dowolną kontrolkę (wszystkie z wyjątkiem zgodność urządzenia doprowadzi do bloku).

**Czy zasady mają zastosowanie do wszystkich aplikacji klienckich domyślnie przyszłości?**

Nie. Nie ma zmian w zachowaniu zasady domyślne. Zasady w dalszym ciągu są domyślnie stosowane do przeglądarki i klientów mobilnych aplikacji/pulpitu.



## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 

