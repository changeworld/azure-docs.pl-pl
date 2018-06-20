---
title: Jakie są warunki dostępu warunkowego w usłudze Azure Active Directory? | Microsoft Docs
description: Dowiedz się, jak warunki są używane w dostępu warunkowego w usłudze Azure Active Directory do wyzwalania zasad.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
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
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232471"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Jakie są warunki dostępu warunkowego w usłudze Azure Active Directory? 

Z [dostępu warunkowego w usłudze Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), można kontrolować sposób autoryzowanym użytkownikom dostępu aplikacji w chmurze. W zasadach dostępu warunkowego zdefiniowanego odpowiedzi ("tym") Przyczyna służącą do wyzwalania zasad ("w takim przypadku"). 

![Kontrola](./media/active-directory-conditional-access-conditions/10.png)


W kontekście dostępu warunkowego:

- "**w takim przypadku**" jest wywoływana **warunki**. 
- "**To zrobić**" jest wywoływana **dostęp do formantów**.

Kombinacja warunki z kontroli dostępu w sieci reprezentuje zasady dostępu warunkowego.

![Kontrola](./media/active-directory-conditional-access-conditions/61.png)


Warunki, które nie zostały skonfigurowane w zasadach dostępu warunkowego nie są stosowane. Niektóre warunki są [obowiązkowe](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work) zastosować zasady dostępu warunkowego do środowiska. 

W tym artykule przedstawiono omówienie warunki i sposób ich użycia w zasadach dostępu warunkowego. 

## <a name="users-and-groups"></a>Użytkownicy i grupy

Warunek użytkowników i grup jest obowiązkowa w zasadach dostępu warunkowego. W zasadach, można wybrać operator **wszyscy użytkownicy** lub wybierz konkretnych użytkowników i grup.

![Kontrola](./media/active-directory-conditional-access-conditions/111.png)

Po wybraniu:

- **Wszyscy użytkownicy**, zasady są stosowane do wszystkich użytkowników w katalogu. W tym gości.

- **Wybierz użytkowników i grupy**, można ustawić następujące opcje:

    - **Wszyscy użytkownicy gościa** — umożliwia kierowania zasad B2B reklamy. Ten warunek zgodny dowolnego konta użytkownika z *userType* ustawić atrybutu *gościa*. To ustawienie można użyć w sytuacjach, gdy zasady musi zastosować natychmiast po utworzeniu konta w strumieniu zaproszenia w usłudze Azure AD.

    - **Role katalogu** — umożliwia docelowy zasady oparte na przypisanie roli użytkownika. Ten warunek obsługuje katalogu role, takie jak *administratora globalnego* lub *hasło administratora*.

    - **Użytkownicy i grupy** — umożliwia określonych zestawów użytkowników docelowych. Na przykład można wybrać grupy, która zawiera wszystkie elementy członkowskie działu HR, jeśli masz aplikację HR wybrany jako aplikacji w chmurze.

Grupy, może być dowolnego typu grupy w usłudze Azure AD, w tym dynamiczne lub przypisanych grup zabezpieczeń i dystrybucji

Można również wykluczyć określonych użytkowników lub grup z zasad. Jeden przypadek użycia wspólnych są konta usług, jeśli uwierzytelnianie wieloskładnikowe (MFA) wymusza zasady. 

Przeznaczonych dla określonych zestawów użytkowników jest przydatne w przypadku wdrażania nowych zasad. W nowych zasad powinien wskazywać tylko początkowego zestawu użytkowników, aby sprawdzić poprawność zasad zachowania. 



## <a name="cloud-apps"></a>Aplikacje w chmurze 

Aplikacji w chmurze jest witryn sieci web lub usługi. W tym witryn sieci web chronionych przez serwer Proxy aplikacji Azure. Aby uzyskać szczegółowy opis aplikacji w chmurze obsługiwane, zobacz [przypisania aplikacji w chmurze](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

Warunek aplikacji w chmurze jest obowiązkowa w zasadach dostępu warunkowego. W zasadach, można wybrać operator **wszystkich aplikacji w chmurze** lub wybranie określonych aplikacji.

![Kontrola](./media/active-directory-conditional-access-conditions/03.png)

Możesz wybrać:

- **Wszystkie aplikacje w chmurze** do linii bazowej zasady do zastosowania w całej organizacji. Przypadek użycia wspólnych dla tego zaznaczenia jest zasadę, która wymaga usługi Multi-Factor authentication po wykryciu zagrożenia logowania dla dowolnej aplikacji w chmurze. Zasady stosowane do **wszystkich aplikacji w chmurze** ma zastosowanie do dostępu do wszystkich witryn sieci web i usług. To ustawienie nie jest ograniczona do aplikacji w chmurze, które znajdują się w **aplikacji w chmurze wybierz** listy.

- Aplikacje w chmurze poszczególnych do określonych usług docelowych przez zasady. Na przykład można wymagać od użytkowników [zgodnego urządzenia](active-directory-conditional-access-policy-connected-applications.md) można uzyskać dostępu do usługi SharePoint Online. Ta zasada dotyczy również inne usługi przy uzyskiwaniu dostępu do zawartości programu SharePoint, na przykład Teams firmy Microsoft. 

Można też wykluczyć określone aplikacje z zasad; Jednak te aplikacje będą nadal może ulec zasady stosowane do uzyskiwania dostępu do usługi. 



## <a name="sign-in-risk"></a>Ryzyko logowania

Ryzyko logowania jest wskaźnik prawdopodobieństwo (wysoki, średni lub niski) próba logowania nie została wykonana przez wiarygodnego właściciela konta użytkownika. Usługi Azure AD oblicza poziom ryzyka logowania podczas logowania użytkownika. Poziom ryzyka obliczeniowej logowania można użyć jako warunek w zasadach dostępu warunkowego. 

![Warunki](./media/active-directory-conditional-access-conditions/22.png)

Aby użyć tego warunku, musisz mieć [Azure Active Directory Identity Protection](active-directory-identityprotection.md) włączone.
 
Typowe przypadki użycia tego warunku są zasadami, które:

- Blokowanie użytkowników rejestrowania wysokiego ryzyka, aby uniemożliwić użytkownikom potencjalnie prawnie niedozwolonych dostęp do aplikacji w chmurze. 
- Wymusić uwierzytelnianie wieloskładnikowe dla użytkowników z średnie ryzyko logowania. Wymuszając uwierzytelnianie wieloskładnikowe, możesz podać dodatkowe pewność, że logowanie jest wykonywane przez wiarygodnego właściciela konta.

Aby uzyskać więcej informacji, zobacz [Ryzykowne logowania](active-directory-identityprotection.md#risky-sign-ins).  

## <a name="device-platforms"></a>Platformy urządzeń

Platformy urządzeń charakteryzuje się systemu operacyjnego, który działa na urządzeniu. Usługi Azure AD identyfikuje platformy, korzystając z informacji podanych przez urządzenie, takie jak agent użytkownika. Ponieważ te informacje są niezweryfikowane, jest zalecane wszystkich platform zasad zastosowanych do nich, blokuje dostęp, wymagające zgodności z zasadami usługi Intune lub wymagających się, że urządzenie jest przyłączony do domeny. Wartość domyślna to zastosować zasady do wszystkich platform urządzeń. 


![Warunki](./media/active-directory-conditional-access-conditions/24.png)

Aby uzyskać pełną listę obsługiwanych platform urządzeń, zobacz [warunku platformy urządzenia](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Typowy przypadek użycia dla tego warunku jest zasada, która ogranicza dostęp do aplikacji w chmurze do [zarządzanych urządzeń](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Aby uzyskać więcej scenariuszy, w tym warunku platformy urządzeń, zobacz [dostępu warunkowego na podstawie aplikacji usługi Azure Active Directory](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Stan urządzenia

Warunek stanu urządzeń umożliwia połączenia hybrydowe usługi Azure AD i oznaczone jako zgodne mają być wykluczone z zasad dostępu warunkowego urządzeń. Jest to przydatne, gdy zasady dotyczą tylko urządzeniu niezarządzanym, aby zapewnić dodatkową sesję zabezpieczeń. Na przykład tylko wymuszać kontroli sesji Microsoft Cloud App Security, gdy urządzenie jest niezarządzany. 


![Warunki](./media/active-directory-conditional-access-conditions/112.png)

Jeśli chcesz blokowały dostęp dla niezarządzanych urządzeń, należy zaimplementować [dostępu warunkowego opartego na urządzeniu](active-directory-conditional-access-policy-connected-applications.md).


## <a name="locations"></a>Lokalizacje

Lokalizacje istnieje możliwość zdefiniowania warunków, które są oparte na której zainicjowano próby połączenia z. 
     
![Warunki](./media/active-directory-conditional-access-conditions/25.png)

Typowe przypadki użycia tego warunku są zasadami, które:

- Wymusić uwierzytelnianie wieloskładnikowe dla użytkowników uzyskujących dostęp do usługi, gdy znajdują się poza siecią firmową.  

- Zablokuj dostęp użytkowników uzyskujących dostęp do usługi z określonego krajach lub regionach. 

Aby uzyskać więcej informacji, zobacz [co to jest warunek lokalizację w usłudze Azure Active Directory dostępu warunkowego?](active-directory-conditional-access-locations.md)


## <a name="client-apps"></a>Aplikacje klienckie

Warunek aplikacji klienta umożliwia stosowanie zasad do różnych typów aplikacji, takich jak:

- Witryny sieci Web i usług
- Aplikacje mobilne i aplikacje komputerowe. 



Aplikacja zostanie sklasyfikowany jako:

- Witryny sieci web lub usługi korzysta z protokołów Usługa rejestracji Jednokrotnej w sieci web, SAML, WS-Fed lub OpenID Connect poufne klienta.

- A aplikację mobilną lub aplikację, gdy jest używana dla natywnego klienta aplikacji mobilnej, OpenID Connect.

Aby uzyskać pełną listę aplikacji klienckich, których można używać w zasadach dostępu warunkowego, zobacz [warunku aplikacje klienckie](active-directory-conditional-access-technical-reference.md#client-apps-condition) technicznymi Azure Active Directory dostępu warunkowego.

Typowe przypadki użycia tego warunku są zasadami, które:

- Wymagaj [zgodnego urządzenia](active-directory-conditional-access-policy-connected-applications.md) dla aplikacji mobilnych i klasycznych, które pobierać dużych ilości danych na urządzeniu, umożliwiając dostęp z dowolnego urządzenia za pomocą przeglądarki.

- Zablokuj dostęp z aplikacji sieci web, ale zezwalaj na dostęp z aplikacji mobilnych i klasycznych.

Oprócz przy użyciu protokołów nowoczesnego uwierzytelniania i logowania jednokrotnego w sieci web, ten warunek można zastosować do aplikacji poczty, które używają programu Exchange ActiveSync, takich jak aplikacje natywnego programu pocztowego w większości smartfonów. Obecnie aplikacji klienta przy użyciu starszej wersji protokołów muszą być zabezpieczone za pomocą usług AD FS.

Ten stan można wybrać tylko, jeśli **Office 365 usługi Exchange Online** jest tylko aplikacji w chmurze wybrano.

![Aplikacje w chmurze](./media/active-directory-conditional-access-conditions/32.png)

Wybieranie **programu Exchange ActiveSync** jako aplikacje klienckie warunku jest obsługiwana tylko jeśli nie masz inne warunki zasady skonfigurowane. Można jednak zawęzić zakres ten stan do mają zastosowanie tylko do obsługiwanych platform.

 
![Obsługiwane platformy](./media/active-directory-conditional-access-conditions/33.png)

Zastosowanie tylko do obsługiwanych platform ten stan jest odpowiednikiem do wszystkich platform urządzeń w [warunku platformy urządzenia](active-directory-conditional-access-conditions.md#device-platforms).

![Obsługiwane platformy](./media/active-directory-conditional-access-conditions/34.png)


 Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie programu SharePoint Online i Exchange Online dla usługi Azure Active Directory dostępu warunkowego](active-directory-conditional-access-no-modern-authentication.md)
 
- [Azure Active Directory na podstawie aplikacji dostępu warunkowego](active-directory-conditional-access-mam.md) 


### <a name="legacy-authentication"></a>Starsze uwierzytelnianie  

Dostęp warunkowy dotyczy teraz starszych klientów pakietu Office, które nie obsługują nowoczesnego uwierzytelniania, a także klientów, którzy używają poczty protokoły POP, IMAP, SMTP itd. Dzięki temu można skonfigurować zasady, takie jak **zablokować dostęp z innych klientów**.


![Starsze uwierzytelnianie](./media/active-directory-conditional-access-conditions/160.png)
 



#### <a name="known-issues"></a>Znane problemy

- Konfigurowanie zasad dla **innych klientów** blokuje z niektórych klientów, takie jak SPConnect całej organizacji. Jest to spowodowane starszych klientów uwierzytelniania w nieoczekiwany sposób. Ten problem nie dotyczy głównych aplikacji pakietu Office, takich jak starszych klientów pakietu Office. 

- Może potrwać do 24 godzin zasad zaczęły obowiązywać. 


#### <a name="frequently-asked-questions"></a>Często zadawane pytania

**Spowoduje to zablokowanie usług sieci Web programu Exchange (EWS)**

Protokół uwierzytelniania, który używa EWS to zależy. Jeśli EWS aplikacji korzystających z nowoczesnego uwierzytelniania, zostaną objęte aplikacji klienckiej "aplikacji mobilnych i klasycznych klientów". Jeśli aplikacja EWS używa uwierzytelniania podstawowego, zostaną objęte aplikacji klienckiej "Innych klientów".


**Kontrolki można użyć dla innych klientów**

Żadnego formantu, można skonfigurować dla "Innych klientów". Środowisko użytkownika końcowego będzie jednak blokowanie dostępu we wszystkich przypadkach. "Innych klientów" nie obsługują formantach, takich jak uwierzytelnianie wieloskładnikowe zgodnego urządzenia, przyłączanie do domeny,... itd. 
 
**Jakie warunki można używać dla innych klientów?**

Wszelkie warunki, można skonfigurować dla "Innych klientów".

**Program Exchange ActiveSync obsługuje wszystkie warunki i formanty?**

Nie. Poniżej przedstawiono podsumowanie obsługi protokołu Exchange ActiveSync (EAS):

- EAS obsługuje tylko użytkowników i grup elementów docelowych. Nie obsługuje on gościa, role. Jeśli warunek gościa/roli jest skonfigurowany, wszyscy użytkownicy będą zablokowania, ponieważ nie można określić, czy dla użytkownika mają dotyczyć zasady, czy nie.

- EAS działa tylko z programem Exchange jako aplikacji w chmurze. 

- EAS nie obsługuje żadnych stan, z wyjątkiem samej aplikacji klienta.

- EAS można skonfigurować z żadnym formantem (wszystkie z wyjątkiem zgodności urządzeń doprowadzi do bloku).

**Czy zasady mają zastosowanie do wszystkich aplikacji klienckich domyślnie idąc dalej?**

Nie. Nie została zmieniona w domyślne zachowanie zasady. Zasady nadal mają zastosowanie do przeglądarki i klientów mobilnych aplikacji/pulpitu domyślnie.



## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 

