---
title: Jakie są warunki w usłudze Azure Active Directory dostępu warunkowego? | Microsoft Docs
description: Dowiedz się, jak warunki są używane w usłudze Azure Active Directory dostępu warunkowego do wyzwolenia zasad.
services: active-directory
keywords: Dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/17/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83263b3536100ce16819002c833754d18ac12816
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112610"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Jakie są warunki w usłudze Azure Active Directory dostępu warunkowego?

Można kontrolować sposób dostępu użytkowników do aplikacji w chmurze przy użyciu [dostępu warunkowego usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). W zasadach dostępu warunkowego, zdefiniuj odpowiedź ("następnie to zrobić") można Przyczyna służącą do wyzwalania zasad ("w takim przypadku").

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

* **Wszyscy użytkownicy-goście** jest przeznaczony dla zasad użytkowników-gości B2B. Ten warunek pasuje do dowolnego konta użytkownika, który ma **userType** ustawioną wartość atrybutu **gościa**. Użyj tego ustawienia, gdy zasady muszą być stosowane zaraz po utworzeniu konta w usłudze flow zaproszenie w usłudze Azure AD.

* **Role w katalogach** jest przeznaczony dla zasad na podstawie przypisania roli użytkownika. Ten warunek obsługuje role katalogu, takie jak **administratora globalnego** lub **administrator haseł**.

* **Użytkownicy i grupy** jest przeznaczony dla określonego zbiorów użytkowników. Na przykład można wybrać grupy, która zawiera wszystkie elementy członkowskie z działu KADR, po wybraniu aplikacji HR jako aplikacji w chmurze. Grupa może być dowolnego typu grupy w usłudze Azure AD, w tym dynamiczne lub przypisane grupy zabezpieczeń i dystrybucję.

Z zasad, można wykluczyć określonych użytkowników lub grup. Jeden typowy przypadek użycia jest kont usług w przypadku zasad usługi uwierzytelnianie wieloskładnikowe (MFA).

Przeznaczone dla konkretnych zestawów użytkowników jest przydatne w przypadku wdrażania nowych zasad. W nowych zasad powinien dotyczyć tylko początkowy zestaw użytkowników, aby zweryfikować zachowanie zasad.

## <a name="cloud-apps-and-actions"></a>Aplikacje w chmurze i akcje

Aplikacja w chmurze jest witryny sieci Web, usługi lub punkt końcowy jest chroniony przez serwer Proxy aplikacji usługi Azure AD. Aby uzyskać szczegółowy opis obsługiwanymi aplikacjami w chmurze, zobacz [przypisań aplikacji w chmurze](technical-reference.md#cloud-apps-assignments). **w chmurze aplikacji lub akcje** warunek jest obowiązkowe w zasadach dostępu warunkowego. W zasadach, możesz wybrać operator **wszystkie aplikacje w chmurze** lub określić aplikacji przy użyciu **Wybierz aplikacje**.

Organizacje mogą wybrać spośród następujących:

* **Wszystkie aplikacje w chmurze** po zastosowaniu wbudowanej linii bazowej zasady do zastosowania w całej organizacji. Zaznacz to pole wyboru dla zasad, które wymagają uwierzytelniania wieloskładnikowego po wykryciu ryzyka logowania dla dowolnej aplikacji w chmurze. Zasady stosowane do wszystkich aplikacji w chmurze dotyczy dostępu do wszystkich witryn sieci Web i usług. To ustawienie nie ogranicza się do aplikacji w chmurze, które pojawiają się na liście aplikacji wybierz opcję.
* **Wybierz aplikacje** do określonych usług docelowego przez zasady. Na przykład możesz wymagać od użytkowników wymagane w przypadku urządzeń zgodnych, dostęp do usługi SharePoint Online. Ta zasada jest stosowana także do innych usług, podczas uzyskiwania dostępu do zawartości programu SharePoint. Przykładem jest Microsoft Teams.

> [!NOTE]
> Z zasad można wykluczyć określone aplikacje. Te aplikacje są jednak nadal podlega procesowi zasady zastosowane do usług, do których uzyskują dostęp.

**Akcje użytkownika** zadania, które mogą być wykonywane przez użytkownika. Jedyną akcją, aktualnie obsługiwany jest **zarejestrowanie informacji o zabezpieczeniach (wersja zapoznawcza)** , co pozwala zasad dostępu warunkowego, gdy użytkownicy, którzy są włączone dla rejestracji połączone próbować zarejestrować ich zabezpieczeń informacje. Więcej informacji można znaleźć w artykule [Włącz zabezpieczenia informacji o rejestracji (wersja zapoznawcza) w połączeniu](../authentication/howto-registration-mfa-sspr-combined.md).

## <a name="sign-in-risk"></a>Ryzyko logowania

Ryzyko logowania jest wskaźnikiem prawdopodobieństwa (wysoki, średni lub niski), że logowanie nie zostało wprowadzonych przez prawowitym właścicielem konta użytkownika. Usługa Azure AD oblicza poziom ryzyka logowania przez użytkownika użytkownika logowania. Można użyć poziomu obliczeniowego ryzyka logowania jako warunku w zasadach dostępu warunkowego.

![Poziomy ryzyka logowania](./media/conditions/22.png)

Aby użyć tego warunku, musisz mieć [usługi Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable) włączone.
 
Typowe przypadki użycia dla tego warunku są zasady, które mają następujące ochrony: 

- Blokuj użytkownikom wysoki ryzyka logowania. Ta ochrona uniemożliwia potencjalnie innych wiarygodnych użytkownikom uzyskiwanie dostępu do aplikacji w chmurze. 
- Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników z średniego ryzyka logowania. Wymuszając uwierzytelnianie wieloskładnikowe, możesz podać dodatkowe pewność, że logowanie jest realizowane przez prawowitym właścicielem konta.

Aby uzyskać więcej informacji, zobacz [zablokować dostęp po wykryciu zagrożenia sesji](app-sign-in-risk.md).  

## <a name="device-platforms"></a>Platformy urządzeń

Platformy urządzeń jest określony przez system operacyjny, który działa na twoim urządzeniu. Usługa Azure AD identyfikuje platformy przy użyciu informacji dostarczonych przez urządzenia, takie jak agenta użytkownika. Te informacje są niezweryfikowanych. Zaleca się, że wszystkie platformy mają zasad zastosowanych do nich. Zasady powinny blokować dostęp, wymaga zgodności z zasadami Microsoft Intune albo wymagają, że urządzenie jest przyłączone do domeny. Wartość domyślna to na stosowanie zasad na wszystkich platformach urządzeń. 


![Konfiguruj platformy urządzeń](./media/conditions/24.png)

Aby uzyskać listę obsługiwanych platform urządzeń, zobacz [warunek platformy urządzenia](technical-reference.md#device-platform-condition).


Typowy przypadek użycia dla tego warunku jest zasadę, która ogranicza dostęp do aplikacji w chmurze do [urządzeń zarządzanych przy użyciu](require-managed-devices.md). Aby uzyskać więcej scenariuszy, w tym warunku platformy urządzeń, zobacz [dostępu warunkowego opartego na aplikacji usługi Azure Active Directory](app-based-conditional-access.md).



## <a name="device-state"></a>Stan urządzenia

Warunek stanu urządzenia wyklucza hybrydowe przyłączone do usługi Azure AD, urządzenia i oznaczone jako zgodne z zasadami dostępu warunkowego. 


![Skonfiguruj Stany urządzeń](./media/conditions/112.png)

Ten warunek jest przydatne, gdy zasady dotyczą tylko niezarządzanego urządzenia do zapewnienia bezpieczeństwa sesji. Na przykład tylko wymuszania kontroli sesji Microsoft Cloud App Security po niezarządzanego urządzenia. 

## <a name="locations"></a>Lokalizacje

Korzystając z lokalizacji, można zdefiniować warunków opartych na których podjęto próbę połączenia. 

![Konfiguruj lokalizacje](./media/conditions/25.png)

Typowe przypadki użycia dla tego warunku są zasady za pomocą następujących ochrony:

- Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników uzyskujących dostęp do usługi, gdy są one poza siecią firmową.  

- Zablokuj dostęp dla użytkowników uzyskujących dostęp do usługi z określonych krajów lub regionów. 

Aby uzyskać więcej informacji, zobacz [co to jest warunek lokalizacji w usłudze Azure Active Directory dostępu warunkowego?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Aplikacje klienckie

Domyślnie zasady dostępu warunkowego mają zastosowanie do następujących aplikacji:

- **[Aplikacje przeglądarki](technical-reference.md#supported-browsers)**  — aplikacje przeglądarki obejmują witryn sieci Web za pośrednictwem protokołu SAML, WS-Federation i OpenID Connect protokołów Usługa rejestracji Jednokrotnej w sieci web. Dotyczy to również witryny sieci Web lub sieci web usługi, która została zarejestrowana jako poufne klienta OAuth. Na przykład Office 365 SharePoint witryny sieci Web. 

- **[Aplikacje mobilne i klasyczne korzystających z nowoczesnego uwierzytelniania](technical-reference.md#supported-mobile-applications-and-desktop-clients)**  — są to aplikacje klasyczne pakietu Office i aplikacji na telefony. 


Ponadto możesz wybrać docelową zasady do aplikacji klienta właściwy, które nie korzystają z nowoczesnego uwierzytelniania, na przykład:

- **[Klienci programu Exchange ActiveSync](conditions.md#exchange-activesync-clients)**  — w przypadku bloki zasad, przy użyciu protokołu Exchange ActiveSync, których to dotyczy użytkownicy uzyskują wiadomość e-mail z kwarantanny jednego z informacji na temat przyczyny zablokowania. Jeśli to konieczne, wiadomość e-mail zawiera instrukcje dotyczące rejestrowania urządzenia w usłudze Intune.

- **[Inni klienci](block-legacy-authentication.md)**  — są to klientów korzystających z uwierzytelniania podstawowego przy użyciu protokołów poczty, takich jak IMAP, MAPI, POP, SMTP i starsze aplikacje pakietu Office, które nie korzystają z nowoczesnego uwierzytelniania. Aby uzyskać więcej informacji, zobacz [sposobu działania nowoczesnego uwierzytelniania dla aplikacji klienckich pakietu Office 2013 i Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016).

![Aplikacje klienckie](./media/conditions/41.png)

Typowe przypadki użycia dla tego warunku są zasady z następującymi wymaganiami:

- **[Wymagaj urządzenia zarządzanego](require-managed-devices.md)**  dla aplikacji mobilnych i klasycznych, które pobierania danych na urządzeniu. W tym samym czasie Zezwalaj na dostęp z dowolnego urządzenia za pomocą przeglądarki. Ten scenariusz zapobiega zapisywanie i synchronizowanie dokumentów do niezarządzanego urządzenia. Przy użyciu tej metody można zmniejszyć prawdopodobieństwo utraty danych, w przypadku utraty lub kradzieży urządzenia.

- **[Wymagaj urządzenia zarządzanego](require-managed-devices.md)**  dla aplikacji przy użyciu protokołu ActiveSync dostęp do usługi Exchange Online.

- **[Blokuj starsze uwierzytelnianie](block-legacy-authentication.md)**  do usługi Azure AD (inni klienci)

- Blokuj dostęp z aplikacji sieci web, ale zezwalaj na dostęp z aplikacji mobilnych i komputerowych.



### <a name="exchange-activesync-clients"></a>Klienci programu Exchange ActiveSync

Można wybrać tylko **klienci programu Exchange ActiveSync** jeśli:


- Microsoft Office 365 Exchange Online jest jedyną aplikacją w chmurze wybrane.

    ![Aplikacje w chmurze](./media/conditions/32.png)

- Nie masz inne warunki skonfigurowane w zasadach. Jednak można zawęzić zakres ten warunek, zastosowanie tylko do [obsługiwanych platform](technical-reference.md#device-platform-condition).
 
    ![Zastosuj zasady tylko do obsługiwanych platform](./media/conditions/33.png)


Gdy dostęp jest zablokowany, ponieważ [urządzenia zarządzanego](require-managed-devices.md) jest wymagane, narażeni użytkownicy pobieranie pojedynczego mail, która zawiera informacje na temat ich przy użyciu usługi Intune. 

Jeśli zatwierdzonej aplikacji wymagane jest, narażeni użytkownicy Uzyskaj wskazówki dotyczące instalacji i używania klienta mobilnego programu Outlook.

W innych przypadkach na przykład, jeśli usługa MFA jest wymagana, narażeni użytkownicy są zablokowane, ponieważ klienci korzystający z uwierzytelniania podstawowego nie obsługują usługi MFA.

Można jedynie docelowy to ustawienie, aby użytkownicy i grupy. Nie obsługuje on gości lub ról. Jeśli warunek maszyna gościa lub roli jest skonfigurowany, wszyscy użytkownicy są zablokowane, ponieważ dostęp warunkowy nie może określić, jeśli mają dotyczyć zasady użytkownika lub nie.


 Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie usługi SharePoint Online i usługi Exchange Online dla usługi Azure Active Directory dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Dostęp warunkowy oparty na aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 



## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Szybki Start: Wymagać uwierzytelniania Wieloskładnikowego dla określonych aplikacji przy użyciu usługi Azure Active Directory dostępu warunkowego](app-based-mfa.md).

- Aby skonfigurować zasady dostępu warunkowego dla danego środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md). 

