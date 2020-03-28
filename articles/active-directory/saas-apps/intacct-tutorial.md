---
title: 'Samouczek: Integracja usługi Azure Active Directory z Sage Intacct | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Sage Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28390dba009226d493f5bfc6a5270b067f5bba0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73570563"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Samouczek: Integracja programu Sage Intacct z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Sage Intacct z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Sage Intacct z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Sage Intacct.
* Włącz użytkownikom automatyczne logowanie do Sage Intacct za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Sage Intacct jednokrotnego logowania (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Sage Intacct obsługuje zainicjowane przez **IDP** SSO

## <a name="adding-sage-intacct-from-the-gallery"></a>Dodawanie Sage Intacct z galerii

Aby skonfigurować integrację Sage Intacct z usługą Azure AD, należy dodać Sage Intacct z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Sage Intacct** w polu wyszukiwania.
1. Wybierz **Sage Intacct** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sage-intacct"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi Sage Intacct

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą Sage Intacct przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji Sage Intacct.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą Sage Intacct, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
2. **[Skonfiguruj Sage Intacct SSO](#configure-sage-intacct-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Sage Intacct](#create-sage-intacct-test-user)** — aby mieć odpowiednik B.Simon w Sage Intacct, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Sage Intacct** znajdź sekcję **Zarządzaj** i wybierz **opcję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL:`https://www.intacct.com/ia/acct/sso_response.phtml`

1. Aplikacja Sage Intacct oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj,** aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

1. Oprócz powyższej aplikacji Sage Intacct oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa  |  Atrybut źródłowy|
    | ---------------| --------------- |
    | Nazwa firmy | **Identyfikator firmy Sage Intacct** |
    | name | Wartość powinna być taka sama jak Sage Intacct **User ID**, który można wprowadzić w **sekcji Utwórz Sage Intacct test użytkownika sekcji**, który jest wyjaśniony w dalszej części samouczka |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | Wartość powinna być taka sama jak Sage Intacct **Federated SSO User ID**, który można wprowadzić w **sekcji Utwórz Sage Intacct test użytkownika sekcji**, co jest wyjaśnione w dalszej części samouczka |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij przycisk **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie sage intacct** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Sage Intacct.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Sage Intacct**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-sage-intacct-sso"></a>Konfigurowanie sydażu Sage Intacct

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Sage Intacct jako administrator.

1. Kliknij kartę **Firma,** a następnie kliknij pozycję **Informacje o firmie**.

    ![Firmy](./media/intacct-tutorial/ic790037.png "Firma")

1. Kliknij kartę **Zabezpieczenia,** a następnie kliknij pozycję **Edytuj**.

    ![Zabezpieczenia](./media/intacct-tutorial/ic790038.png "Zabezpieczenia")

1. W sekcji **Logowanie jednokrotne (Logowanie jednokrotne)** wykonaj następujące czynności:

    ![Znak jednokrotny na](./media/intacct-tutorial/ic790039.png "znak jednokrotny na")

    a. Wybierz **włącz znak jednokrotny na**.

    b. Jako **typ dostawcy tożsamości**wybierz **SAML 2.0**.

    d. W polu tekstowym **adres URL wystawcy** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.

    d. W polu tekstowym **Adres URL logowania** wklej wartość adresu URL **logowania,** który został skopiowany z witryny Azure portal.

    e. Otwórz certyfikat zakodowany **w bazie-64** w notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola **Certyfikat.**

    f. Kliknij przycisk **Zapisz**.

### <a name="create-sage-intacct-test-user"></a>Utwórz użytkownika testowego Sage Intacct

Aby skonfigurować użytkowników usługi Azure AD, aby mogli zalogować się do Sage Intacct, muszą być aprowizacji do Sage Intacct. W przypadku programu Sage Intacct inprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **Sage Intacct.**

1. Kliknij kartę **Firma,** a następnie kliknij pozycję **Użytkownicy**.

    ![Użytkownicy](./media/intacct-tutorial/ic790041.png "Użytkownicy")

1. Kliknij kartę **Dodaj.**

    ![Dodaj](./media/intacct-tutorial/ic790042.png "Dodaj")

1. W sekcji **Informacje o użytkowniku** wykonaj następujące czynności:

    ![Informacje o użytkowniku](./media/intacct-tutorial/ic790043.png "Informacje o użytkowniku")

    a. Wprowadź **identyfikator użytkownika**, **nazwisko, imię,** **adres e-mail,** **tytuł**i **telefon** konta usługi Azure AD, które chcesz udostępnić w sekcji **Informacje o użytkowniku.** **First name**

    > [!NOTE]
    > Upewnij się, że **identyfikator użytkownika** na powyższym zrzucie ekranu i wartość **atrybutu źródłowego,** która jest mapowana z atrybutem **nazwa** w sekcji **Atrybuty użytkownika** w witrynie Azure portal powinny być takie same.

    b. Wybierz **uprawnienia administratora** konta usługi Azure AD, które chcesz aprowizować.

    d. Kliknij przycisk **Zapisz**. 
    
    d. Posiadacz konta usługi Azure AD otrzymuje wiadomość e-mail i następuje łącze, aby potwierdzić swoje konto, zanim stanie się aktywne.

1. Kliknij **kartę logowania jednokrotnego** i upewnij się, że **identyfikator użytkownika SSO federacyjnego** na poniższym zrzucie ekranu i wartość **atrybutu źródłowego,** która jest mapowana za pomocą `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` sekcji **Atrybuty użytkownika** w witrynie Azure portal, powinny być takie same.

    ![Informacje o użytkowniku](./media/intacct-tutorial/ic790044.png "Informacje o użytkowniku")

> [!NOTE]
> Aby aprowizować konta użytkowników usługi Azure AD, można użyć innych narzędzi do tworzenia konta użytkownika Sage Intacct lub interfejsów API dostarczonych przez Sage Intacct.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Sage Intacct w Panelu dostępu należy automatycznie zalogować się do Sage Intacct, dla którego skonfigurowano logowanie jednośladowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

