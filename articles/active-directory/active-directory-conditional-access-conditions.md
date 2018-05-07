---
title: Warunki dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przypisania są używane w dostępu warunkowego w usłudze Azure Active Directory do wyzwalania zasad.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 3cb8e598864bccfbea24a2aec5d9387ff903e51c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Warunki dostępu warunkowego w usłudze Azure Active Directory 

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



## <a name="sign-in-risk"></a>Ryzyko związane z logowaniem

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

Aby uzyskać więcej informacji, zobacz [lokalizacji warunki dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-locations.md).


## <a name="client-apps"></a>Aplikacje klienckie

Warunek aplikacji klienta umożliwia stosowanie zasad do różnych typów aplikacji, takich jak:

- Witryny sieci Web i usług
- Aplikacje mobilne i aplikacje komputerowe. 

![Warunki](./media/active-directory-conditional-access-conditions/04.png)

Aplikacja zostanie sklasyfikowany jako:

- Witryny sieci web lub usługi korzysta z protokołów Usługa rejestracji Jednokrotnej w sieci web, SAML, WS-Fed lub OpenID Connect poufne klienta.

- A aplikację mobilną lub aplikację, gdy jest używana dla natywnego klienta aplikacji mobilnej, OpenID Connect.

Aby uzyskać pełną listę aplikacji klienckich, których można używać w zasadach dostępu warunkowego, zobacz [informacje techniczne dotyczące usługi Azure Active Directory dostępu warunkowego](active-directory-conditional-access-technical-reference.md#client-apps-condition).

Typowe przypadki użycia tego warunku są zasadami, które:

- Wymagaj [zgodnego urządzenia](active-directory-conditional-access-policy-connected-applications.md) dla aplikacji mobilnych i klasycznych, które pobierać dużych ilości danych na urządzeniu, umożliwiając dostęp z dowolnego urządzenia za pomocą przeglądarki.

- Zablokuj dostęp z aplikacji sieci web, ale zezwalaj na dostęp z aplikacji mobilnych i klasycznych.

Oprócz przy użyciu protokołów nowoczesnego uwierzytelniania i logowania jednokrotnego w sieci web, ten warunek można zastosować do aplikacji poczty, które używają programu Exchange ActiveSync, takich jak aplikacje natywnego programu pocztowego w większości smartfonów. Obecnie aplikacji klienta przy użyciu starszej wersji protokołów muszą być zabezpieczone za pomocą usług AD FS.

 Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie programu SharePoint Online i Exchange Online dla usługi Azure Active Directory dostępu warunkowego](active-directory-conditional-access-no-modern-authentication.md)
 
- [Azure Active Directory na podstawie aplikacji dostępu warunkowego](active-directory-conditional-access-mam.md) 








## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wprowadzenie dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 

