---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Sprawdź łuk Dome9 CloudGuard punktu | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i sprawdź łuk Dome9 CloudGuard punktu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdaaab8257d3a79130902e1ba0466f9cf15484f4
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147206"
---
# <a name="tutorial-integrate-check-point-cloudguard-dome9-arc-with-azure-active-directory"></a>Samouczek: Integrowanie wyboru punktu CloudGuard Dome9 łuku z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Sprawdź łuk Dome9 CloudGuard punktu z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Sprawdź łuk Dome9 CloudGuard punktu z usługą Azure AD możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do sprawdzanie punktu CloudGuard Dome9 łuku.
* Umożliwianie użytkownikom można automatycznie zalogowanego na sprawdzanie punktu CloudGuard Dome9 łuk za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Sprawdź, czy punkt CloudGuard Dome9 łuk logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Sprawdź obsługuje punkt CloudGuard Dome9 łuk **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Dodawanie sprawdzanie punktu CloudGuard Dome9 łuku z galerii

Aby skonfigurować integrację Sprawdź łuku Dome9 CloudGuard punktu w usłudze Azure AD, należy dodać sprawdzanie punktu CloudGuard Dome9 łuku z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Sprawdź łuk Dome9 CloudGuard punktu** w polu wyszukiwania.
1. Wybierz **Sprawdź łuk Dome9 CloudGuard punktu** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą sprawdzanie punktu CloudGuard Dome9 łuk za pomocą użytkownika testu o nazwie **B.Simon**. Logowanie Jednokrotne do pracy należy ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Sprawdź łuk Dome9 CloudGuard punktu.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą Sprawdź łuk Dome9 CloudGuard punktu, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Sprawdź łuk Dome9 CloudGuard punktu](#configure-check-point-cloudguard-dome9-arc)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B.Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające B.Simon do użycia usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Sprawdź łuk Dome9 CloudGuard punktu](#create-check-point-cloudguard-dome9-arc-test-user)**  mieć odpowiednikiem B.Simon sprawdzanie punktu CloudGuard Dome9 łuk połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Sprawdź łuk Dome9 CloudGuard punktu** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne** .
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://secure.dome9.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Wartość nazwy firmy wybierzesz w portalu administracyjnym aplikacji Dome9, co objaśniono w dalszej części tego samouczka.

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej sprawdzanie punktu CloudGuard Dome9 łuk klienta](mailto:Dome9@checkpoint.com) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Check Point łuk Dome9 CloudGuard aplikacja oczekuje twierdzenia SAML, w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

7. Ponadto powyżej Sprawdź łuk Dome9 CloudGuard punktu aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli: 

    | Name (Nazwa) |  Atrybut źródłowy|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

1. Na **Konfigurowanie Sprawdź łuk Dome9 CloudGuard punktu** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-check-point-cloudguard-dome9-arc"></a>Konfigurowanie wyboru punktu CloudGuard Dome9 łuk

1. W oknie przeglądarki innej witryny sieci web należy zalogować się do witryny firmy Sprawdź łuk Dome9 CloudGuard punktu jako administrator.

2. Kliknij pozycję **Profile Settings** (Ustawienia profilu) w prawym górnym rogu, a następnie kliknij przycisk **Account Settings** (Ustawienia konta). 

    ![Sprawdź konfigurację łuk Dome9 CloudGuard punktu](./media/dome9arc-tutorial/configure1.png)

3. Przejdź na kartę **SSO** (Logowanie jednokrotne), a następnie kliknij przycisk **ENABLE** (WŁĄCZ).

    ![Sprawdź konfigurację łuk Dome9 CloudGuard punktu](./media/dome9arc-tutorial/configure2.png)

4. W sekcji SSO Configuration (Konfiguracja logowania jednokrotnego) wykonaj następujące czynności:

    ![Sprawdź konfigurację łuk Dome9 CloudGuard punktu](./media/dome9arc-tutorial/configure3.png)

    a. Wprowadź nazwę firmy w polu tekstowym **Account ID** (Identyfikator konta). Ta wartość ma być używana w adresie URL odpowiedzi, wymienione w witrynie Azure portal **podstawową konfigurację protokołu SAML** sekcji.

    b. W **wystawcy** pola tekstowego, Wklej wartość **usługi Azure AD identyfikator**, które zostały skopiowane witryny Azure Portal.

    c. W polu tekstowym **Idp endpoint url** (Adres URL punktu końcowego dostawcy tożsamości) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. Otwórz w Notatniku pobrany certyfikat zakodowany w formacie Base64, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **SAML X.509 certificate** (Certyfikat X.509 SAML).

    e. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B.Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić B.Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Sprawdź łuk Dome9 CloudGuard punktu.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Sprawdź łuk Dome9 CloudGuard punktu**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Tworzenie użytkownika testowego sprawdzanie punktu CloudGuard Dome9 łuk

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się na sprawdzanie punktu CloudGuard Dome9 łuk, musi być obsługiwana w aplikacji. Zaznacz punkt CloudGuard Dome9 łuk obsługę just-in-time, ale w tym działało poprawnie, użytkownik musi wybrać określonego **roli** i Przypisz te same użytkownikowi.

   >[!Note]
   >Dla **roli** tworzenia i inne szczegóły kontaktu [zespołem pomocy technicznej sprawdzanie klienta łuk Dome9 CloudGuard punktu](mailto:Dome9@checkpoint.com).

**Aby ręcznie aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Sprawdź łuk Dome9 CloudGuard punktu jako administrator.

2. Kliknij pozycję **Users & Roles** (Użytkownicy i role), a następnie kliknij pozycję **Users** (Użytkownicy).

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user1.png)

3. Kliknij przycisk **ADD USER** (DODAJ UŻYTKOWNIKA).

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user2.png)

4. W sekcji **Create User** (Tworzenie użytkownika) wykonaj następujące kroki:

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user3.png)

    a. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład B.Simon@contoso.com.

    b. W **imię** polu tekstowym wpisz imię użytkownika, takich jak B.

    c. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak Simon.

    d. W polu **SSO User** (Użytkownik logowania jednokrotnego) wybierz ustawienie **On** (Włączone).

    e. Kliknij przycisk **UTWÓRZ**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Sprawdź łuk Dome9 CloudGuard punktu w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze sprawdzanie punktu CloudGuard Dome9 łuk dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)