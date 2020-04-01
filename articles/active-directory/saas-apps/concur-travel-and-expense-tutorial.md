---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z concur travel and expense | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a konsytującymi podróżami i wydatkami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373415"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z concur travel and expense

W tym samouczku dowiesz się, jak zintegrować concur podróży i wydatków z usługi Azure Active Directory (Azure AD). Po zintegrowaniu concur podróży i wydatków z usługi Azure AD, można:

* Kontrola w usłudze Azure AD, który ma dostęp do Concur Travel i wydatków.
* Włącz użytkownikom automatyczne logowanie do concur travel i expense za pomocą swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Konsypce subskrypcje podróży i wydatków.
* Rola "Administrator firmy" w ramach twojego konta użytkownika Concur. Możesz sprawdzić, czy masz odpowiedni dostęp, przechodząc do [Concur SSO Self-Service Tool](https://www.concursolutions.com/nui/authadmin/ssoadmin). Jeśli nie masz dostępu, skontaktuj się z pomocą techniczną concur lub kierownikiem projektu wdrożeniowego. 

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD.

* Concur Travel and Expense obsługuje **jednostki SSO** inicjowane przez IDP i **SP**
* Concur Travel and Expense obsługuje testowanie jednosojnika jednosołowego zarówno w środowisku produkcyjnym, jak i wdrożeniowym 

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu dla każdego z trzech regionów: USA, EMEA i Chiny. Więc tylko jedno wystąpienie można skonfigurować dla każdego regionu w jednej dzierżawie. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Dodawanie concur travel and expense z galerii

Aby skonfigurować integrację concur podróży i wydatków do usługi Azure AD, należy dodać Concur Travel i wydatków z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Concur Travel i Expense** w polu wyszukiwania.
1. Wybierz **pozycję Concur Travel and Expense** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD pod kątem konkurujących podróży i wydatków

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą concur travel and expense przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby identyfikator YSO działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w concur travel and expense.

Aby skonfigurować i przetestować usługę Azure AD SSO przy obliczu konkursowej podróży i wydatków, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne i koszty concur](#configure-concur-travel-and-expense-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testu Konwentu podróży i wydatków](#create-concur-travel-and-expense-test-user)** — aby mieć odpowiednik B.Simon w Concur Travel i expense, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Concur Travel i Expense** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja jest wstępnie skonfigurowana w trybie inicjowanym **przez usługę IDP,** a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz.**

    > [!NOTE]
    > Identyfikator (identyfikator jednostki) i adres URL odpowiedzi (adres URL usługi konsumenta oświadczeń) są specyficzne dla regionu. Wybierz na podstawie centrum danych encji Concur. Jeśli nie znasz centrum danych jednostki Concur, skontaktuj się z pomocą techniczną Concur. 

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra **dla atrybutu użytkownika,** aby edytować ustawienia. Unikatowy identyfikator użytkownika musi być zgodny z login_id użytkownika Concur. Zazwyczaj należy zmienić **user.userprincipalname** na **user.mail**.

    ![Edytuj atrybut użytkownika](common/edit-attribute.png)

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą SAML** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać metadane i zapisać je na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Concur Travel i wydatków.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **pozycję Concur Travel and Expense**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-concur-travel-and-expense-sso"></a>Konfigurowanie przysłów ysk

1. Aby skonfigurować logowanie jednokrotne po stronie **Koncur Travel i Expense,** należy przekazać pobrany **kod XML metadanych federacji** do [narzędzia samoobsługowego logowania jednokrotnego](https://www.concursolutions.com/nui/authadmin/ssoadmin) i zalogować się za pomocą konta z rolą "Administrator firmy". 

1. Kliknij przycisk **Dodaj**.
1. Wprowadź niestandardową nazwę dla swojego idp, na przykład "Azure AD (US)". 
1. Kliknij **pozycję Przekaż plik XML** i dołącz pobrany wcześniej kod **XML metadanych federacji.**
1. Kliknij **pozycję Dodaj metadane,** aby zapisać zmianę.

    ![Zrzut ekranu przedstawiający narzędzie samoobsługowe Concur SSO](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Utwórz użytkownika testowego Concur Travel and Expense

W tej sekcji utworzysz użytkownika o nazwie B.Simon w Concur Travel and Expense. Praca z zespołem pomocy technicznej Concur, aby dodać użytkowników na platformie Concur Travel and Expense. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

> [!NOTE]
> Identyfikator logowania concur B.Simon musi być zgodny z unikatowym identyfikatorem B.Simona w usłudze Azure AD. Na przykład, jeśli B.Simon's Azure AD `B.Simon@contoso.com`unikatowy identyfikator jest . B.Simon's Concur identyfikator logowania `B.Simon@contoso.com` musi być również. 

## <a name="configure-concur-mobile-sso"></a>Konfigurowanie urządzenia sytłego Concur Mobile
Aby włączyć identyfikator SSO dla urządzeń przenośnych Concur, musisz podać **adres URL dostępu użytkownika do**zespołu pomocy technicznej Concur . Wykonaj poniższe czynności, aby uzyskać **adres URL dostępu użytkownika** z usługi Azure AD:
1. Przejdź do **aplikacji Enterprise**
1. Kliknij **pozycję Zbieżne podróże i wydatki**
1. Kliknij **pozycję Właściwości**
1. Kopiuj **adres URL dostępu użytkownika** i podaj ten adres URL do pomocy technicznej concur

> [!NOTE]
> Opcja samoobsługowa do skonfigurowania usługi SSO nie jest dostępna, więc pracuj z zespołem pomocy technicznej Concur, aby włączyć mobilny plik SSO. 

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Konkuruj podróże i wydatki w Panelu dostępu należy automatycznie zalogować się do konkuruj podróży i wydatków, dla których skonfigurowano logowanie jednośmiękowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj zbieżne podróże i wydatki dzięki usłudze Azure AD](https://aad.portal.azure.com/)

