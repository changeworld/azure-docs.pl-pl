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
ms.openlocfilehash: 3fac86d53de4d5f519feaba2208f4682a0b38403
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600266"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Jakie są warunki dostępu warunkowego usługi Azure Active Directory? 

Można kontrolować sposób autoryzowanym użytkownikom aplikacje w chmurze przy użyciu [dostępu warunkowego usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal). W zasadach dostępu warunkowego należy zdefiniować odpowiedź Przyczyna wyzwalanie zasad. Przykładową odpowiedź jest **to zrobić**. Z powodu przykład jest **w takim przypadku**.

![Przyczyna i odpowiedzi](./media/conditions/10.png)


W kontekście dostępu warunkowego **w takim przypadku** nosi nazwę **warunek**. **Następnie można to zrobić** nosi nazwę **kontroli dostępu**. Kombinacja warunkach i usługi kontroli dostępu reprezentuje zasady dostępu warunkowego.

![Zasady dostępu warunkowego](./media/conditions/61.png)


Warunki, które nie zostały skonfigurowane w zasadach dostępu warunkowego nie są stosowane. Niektóre warunki są [obowiązkowe](best-practices.md) zastosowania zasad dostępu warunkowego do środowiska. 

W tym artykule przedstawiono warunki i sposoby ich używania w zasadach dostępu warunkowego. 

## <a name="users-and-groups"></a>Użytkownicy i grupy

Warunek użytkowników i grup jest obowiązkowe w zasadach dostępu warunkowego. W zasadach, możesz wybrać operator **wszyscy użytkownicy** lub wybierz konkretnych użytkowników i grup.

![Użytkownicy i grupy](./media/conditions/111.png)

Po wybraniu **wszyscy użytkownicy**, Twoje zasady są stosowane do wszystkich użytkowników w katalogu, w tym użytkowników-gości.

Gdy użytkownik **Wybieranie użytkowników i grup**, można ustawić następujące opcje:

* **Wszyscy użytkownicy-goście** jest przeznaczony dla zasad użytkowników-gości B2B. Ten warunek pasuje do dowolnego konta użytkownika, który ma **userType** ustawioną wartość atrybutu **gościa**. To ustawienie można użyć, gdy zasady muszą być stosowane zaraz po utworzeniu konta w usłudze flow zaproszenie w usłudze Azure AD.

* **Role w katalogach** jest przeznaczony dla zasad na podstawie przypisania roli użytkownika. Ten warunek obsługuje role katalogu, takie jak **administratora globalnego** lub **administrator haseł**.

* **Użytkownicy i grupy** jest przeznaczony dla określonego zbiorów użytkowników. Na przykład można wybrać grupy, która zawiera wszystkie elementy członkowskie z działu KADR, po wybraniu aplikacji HR jako aplikacji w chmurze. Grupa może być dowolnego typu grupy w usłudze Azure AD, w tym dynamiczne lub przypisane grupy zabezpieczeń i dystrybucję.

Z zasad, można wykluczyć określonych użytkowników lub grup. Jeden typowy przypadek użycia jest kont usług w przypadku zasad usługi uwierzytelnianie wieloskładnikowe (MFA). 

Przeznaczone dla konkretnych zestawów użytkowników jest przydatne w przypadku wdrażania nowych zasad. W nowych zasad powinien dotyczyć tylko początkowy zestaw użytkowników, aby zweryfikować zachowanie zasad. 



## <a name="cloud-apps"></a>Aplikacje w chmurze 

Aplikacja w chmurze jest witryny sieci Web lub usługi. Witryny sieci Web chronionych przez serwer Proxy aplikacji usługi AD systemu Azure są również aplikacje w chmurze. Aby uzyskać szczegółowy opis obsługiwanymi aplikacjami w chmurze, zobacz [przypisań aplikacji w chmurze](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

**Aplikacje w chmurze** warunek jest obowiązkowe w zasadach dostępu warunkowego. W zasadach, możesz wybrać operator **wszystkie aplikacje w chmurze** lub wybierz określone aplikacje.

![Obejmują aplikacje w chmurze](./media/conditions/03.png)

- Wybierz **wszystkie aplikacje w chmurze** do linii bazowej zasady do zastosowania w całej organizacji. Zaznacz to pole wyboru dla zasad, które wymagają uwierzytelniania wieloskładnikowego po wykryciu ryzyka logowania dla dowolnej aplikacji w chmurze. Zasady stosowane do **wszystkie aplikacje w chmurze** dotyczy dostępu do wszystkich witryn sieci Web i usług. To ustawienie nie ogranicza się do aplikacji w chmurze, które pojawiają się na **Wybierz aplikacje** listy. 

- Wybierz aplikacje w chmurze poszczególnych do określonych usług docelowego przez zasady. Na przykład, możesz wymagać od użytkowników mają [zgodnego urządzenia](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) dostęp do usługi SharePoint Online. Ta zasada jest stosowana także do innych usług, podczas uzyskiwania dostępu do zawartości programu SharePoint. Przykładem jest Microsoft Teams. 

Z zasad można wykluczyć określone aplikacje. Te aplikacje są jednak nadal podlega procesowi zasady zastosowane do usług, do których uzyskują dostęp. 



## <a name="sign-in-risk"></a>Ryzyko logowania

Ryzyko logowania jest wskaźnikiem wysoki, średni lub niski prawdopodobieństwo, że próba logowania nie zostało wprowadzone przez prawowitym właścicielem konta użytkownika. Usługa Azure AD oblicza poziom ryzyka logowania przez użytkownika użytkownika logowania. Poziom ryzyka obliczeniowe logowania może być warunku w zasadach dostępu warunkowego. 

![Poziomy ryzyka logowania](./media/conditions/22.png)

Aby użyć tego warunku, musisz mieć [usługi Azure Active Directory Identity Protection](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection-enable) włączone.
 
Typowe przypadki użycia dla tego warunku są zasady, które mają następujące ochrony: 

- Blokuj użytkownikom wysoki ryzyka logowania. Ta ochrona uniemożliwia potencjalnie innych wiarygodnych użytkownikom uzyskiwanie dostępu do aplikacji w chmurze. 
- Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników z średniego ryzyka logowania. Wymuszając uwierzytelnianie wieloskładnikowe, możesz podać dodatkowe pewność, że logowanie jest realizowane przez prawowitym właścicielem konta.

Aby uzyskać więcej informacji, zobacz [Ryzykowne logowania](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-security-risky-sign-ins).  

## <a name="device-platforms"></a>Platformy urządzeń

Platformy urządzeń jest określony przez system operacyjny, który działa na twoim urządzeniu. Usługa Azure AD identyfikuje platformy przy użyciu informacji dostarczonych przez urządzenia, takie jak agenta użytkownika. Te informacje są niezweryfikowanych. Zaleca się, że wszystkie platformy mają zasad zastosowanych do nich. Zasady powinny blokować dostęp, wymaga zgodności z zasadami Microsoft Intune albo wymagają, że urządzenie jest przyłączone do domeny. Wartość domyślna to na stosowanie zasad na wszystkich platformach urządzeń. 


![Konfiguruj platformy urządzeń](./media/conditions/24.png)

Aby uzyskać listę obsługiwanych platform urządzeń, zobacz [warunek platformy urządzenia](technical-reference.md#device-platform-condition).


Typowy przypadek użycia dla tego warunku jest zasadę, która ogranicza dostęp do aplikacji w chmurze do [urządzeń zarządzanych przy użyciu](require-managed-devices.md). Aby uzyskać więcej scenariuszy, w tym warunku platformy urządzeń, zobacz [dostępu warunkowego opartego na aplikacji usługi Azure Active Directory](app-based-conditional-access.md).



## <a name="device-state"></a>Stan urządzenia

Warunek stanu urządzenia wyklucza hybrydowe przyłączone do usługi Azure AD, urządzenia i oznaczone jako zgodne z zasadami dostępu warunkowego. Ten warunek jest przydatne, gdy zasady dotyczą tylko niezarządzanego urządzenia do zapewnienia bezpieczeństwa sesji. Na przykład tylko wymuszania kontroli sesji Microsoft Cloud App Security po niezarządzanego urządzenia. 


![Skonfiguruj Stany urządzeń](./media/conditions/112.png)

Jeśli chcesz zablokować dostęp dla urządzeń niezarządzanych, zaimplementować [dostępu warunkowego opartego na urządzeniu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).


## <a name="locations"></a>Lokalizacje

Korzystając z lokalizacji, można zdefiniować warunków opartych na których podjęto próbę połączenia. 

![Skonfiguruj lokalizacje](./media/conditions/25.png)

Typowe przypadki użycia dla tego warunku są zasady za pomocą następujących ochrony:

- Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników uzyskujących dostęp do usługi, gdy są one poza siecią firmową.  

- Zablokuj dostęp dla użytkowników uzyskujących dostęp do usługi z określonych krajów lub regionów. 

Aby uzyskać więcej informacji, zobacz [co to jest warunek lokalizacji w funkcji dostępu warunkowego usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Aplikacje klienckie

Korzystając z warunku aplikacji klienta, można zastosować zasady do różnych typów aplikacji. Przykładami są witryn sieci Web, usług, aplikacji mobilnych i klasycznych. 



Aplikacja jest klasyfikowany w następujący sposób:

- Witryny sieci Web lub usługi za pomocą protokołów Usługa rejestracji Jednokrotnej w sieci web, SAML, WS-Fed lub OpenID Connect dla poufnych klienta.

- Aplikacji mobilnej lub aplikacji pulpitu, jeśli klient natywny używa aplikacji mobilnej, OpenID Connect.

Aby uzyskać listę aplikacji klienckich, w których można używać w zasadach dostępu warunkowego, zobacz [warunek aplikacje klienckie](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#client-apps-condition) w techniczne dotyczące usługi Azure Active Directory dostępu warunkowego.

Typowe przypadki użycia dla tego warunku są zasady za pomocą następujących ochrony: 

- Wymagaj [zgodnego urządzenia](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) dla aplikacji mobilnych i klasycznych, które pobieranie dużych ilości danych na urządzeniu. W tym samym czasie Zezwalaj na dostęp z dowolnego urządzenia za pomocą przeglądarki.

- Blokuj dostęp z aplikacji sieci web, ale zezwalaj na dostęp z aplikacji mobilnych i komputerowych.

Można zastosować ten stan do protokołów nowoczesnego uwierzytelniania i logowania jednokrotnego w sieci web. Można również zastosować do aplikacji Poczta, które używają programu Microsoft Exchange ActiveSync. Przykładami są aplikacji natywnego programu pocztowego w większości smartfony. 

Warunek aplikacje klienta można wybrać tylko, jeśli program Microsoft Office 365 Exchange Online jest jedyną aplikacją w chmurze wybrane.

![Aplikacje w chmurze](./media/conditions/32.png)

Wybieranie **programu Exchange ActiveSync** jako klient stan aplikacji jest obsługiwany tylko wtedy, gdy nie masz inne warunki skonfigurowane w zasadach. Jednak można zawęzić zakres ten warunek, aby zastosować tylko do obsługiwanych platform.

 
![Zastosuj zasady tylko do obsługiwanych platform](./media/conditions/33.png)

Zastosowanie tego warunku tylko do obsługiwanych platform jest taki sam dla wszystkich platform urządzeń w [warunek platformy urządzenia](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).

![Konfiguruj platformy urządzeń](./media/conditions/34.png)


 Więcej informacji można znaleźć w tych artykułach:

- [Konfigurowanie usługi SharePoint Online i usługi Exchange Online dla dostępu warunkowego usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Usługa Azure Active Directory na podstawie aplikacji dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam). 


### <a name="legacy-authentication"></a>Starsze uwierzytelnianie  

Dostęp warunkowy dotyczy teraz starszych klientów programu Microsoft Office, które nie obsługują nowoczesnego uwierzytelniania. Ma również zastosowanie do klientów, którzy używają protokoły poczty e-mail, np. SMTP, POP i IMAP. Przy użyciu starszej wersji uwierzytelniania, można skonfigurować zasady, takie jak **zablokować dostęp z innych klientów**.


![Konfigurowanie aplikacji klienta](./media/conditions/160.png)  


#### <a name="known-issues"></a>Znane problemy

- Konfigurowanie zasad dla **inni klienci** blokuje całą organizację, z niektórych klientów, takich jak SPConnect. Ten blok wynika z faktu, starsi klienci uwierzytelnić się w nieoczekiwany sposób. Ten problem nie ma zastosowania do głównej aplikacji pakietu Office, takich jak starsi klienci pakietu Office. 

- Może upłynąć do 24 godzin, aż zasady zaczną obowiązywać. 


#### <a name="frequently-asked-questions"></a>Często zadawane pytania

**P: czy** tego uwierzytelnienia zablokuje usług sieci Web programu Microsoft Exchange?

Zależy to protokół uwierzytelniania, który korzysta z usług sieci Web programu Exchange. Jeśli aplikacja usługi sieci Web programu Exchange korzysta z nowoczesnego uwierzytelniania, jest ona objęta **aplikacje mobilne i klienci stacjonarni** aplikacji klienckiej. Uwierzytelnianie podstawowe jest objęta **inni klienci** aplikacji klienckiej.


**P: czy** kontrolki można użyć dla **inni klienci**?

Można skonfigurować dla dowolnej kontrolki **inni klienci**. Jednak środowisko użytkownika końcowego będzie zablokowany dostęp do wszystkich przypadkach. **Inni klienci** nie obsługują formanty, takie jak uwierzytelnianie wieloskładnikowe, zgodne urządzenie i przyłączanie do domeny. 
 
**P:** jakimi warunkami można użyć dla **inni klienci**?

Dowolny warunek można skonfigurować dla **inni klienci**.

**P: czy** jest program Exchange ActiveSync obsługuje wszystkie warunki i kontrolki?

Nie. Na poniższej liście podsumowano obsługę programu Exchange ActiveSync: 

- Program Exchange ActiveSync obsługuje tylko użytkownika i przeznaczone dla grupy. Nie obsługuje on gości lub ról. Jeśli warunek maszyna gościa lub roli jest skonfigurowany, wszyscy użytkownicy są blokowane. Program Exchange ActiveSync blokuje wszystkich użytkowników, ponieważ nie może określić, jeśli mają dotyczyć zasady użytkownika lub nie.

- Program Exchange ActiveSync działa tylko z usługą Microsoft Exchange Online jako aplikacji w chmurze. 

- Program Exchange ActiveSync nie obsługuje dowolny warunek z wyjątkiem samej aplikacji klienta. 

- Program Exchange ActiveSync można skonfigurować przy użyciu dowolnej kontrolki. Wszystkie kontrolki z wyjątkiem zgodność urządzenia prowadzić do bloku.

**P: czy** czy zasady mają zastosowanie do wszystkich aplikacji klienckich domyślnie przyszłości?

Nie. Nie ma zmian w zachowaniu zasady domyślne. Zasady w dalszym ciągu są domyślnie stosowane do przeglądarki i aplikacje mobilne i klienci stacjonarni.



## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Szybki Start: Wymagaj uwierzytelniania Wieloskładnikowego dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](app-based-mfa.md).

- Aby skonfigurować zasady dostępu warunkowego dla danego środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md). 

