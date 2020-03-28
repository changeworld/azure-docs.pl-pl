---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z menedżerem timeoffmanager | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06aa2ddf3e7168147ec091ef6fb9826025f23364
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561817"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeoffmanager"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z menedżerem timeoffmanager

W tym samouczku dowiesz się, jak zintegrować TimeOffManager z usługą Azure Active Directory (Azure AD). Po zintegrowaniu TimeOffManager z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do TimeOffManager.
* Włącz użytkownikom automatyczne logowanie do TimeOffManager za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego TimeOffManager.TimeOffManager single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.


* TimeOffManager obsługuje zainicjowane przez protokół SSO inicjowane przez **protokół IDP**

* TimeOffManager obsługuje inicjowanie obsługi administracyjnej użytkowników **just in time**

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.


## <a name="adding-timeoffmanager-from-the-gallery"></a>Dodawanie timeoffmanager z galerii

Aby skonfigurować integrację TimeOffManager z usługą Azure AD, należy dodać TimeOffManager z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **TimeOffManager** w polu wyszukiwania.
1. Wybierz **TimeOffManager** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-timeoffmanager"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu TimeOffManager

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą funkcji TimeOffManager przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze TimeOffManager.

Aby skonfigurować i przetestować przychylić do usługi Azure AD sytuować za pomocą timeoffmanager, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne TimeOffManager](#configure-timeoffmanager-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego TimeOffManager](#create-timeoffmanager-test-user)** — aby mieć odpowiednik B.Simon w TimeOffManager, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **TimeOffManager** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, używając następującego wzorca:`https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Możesz uzyskać tę wartość z **logowania jednokrotnego na stronie ustawień,** która jest wyjaśniona w dalszej części samouczka lub skontaktuj się z [zespołem pomocy technicznej TimeOffManager](https://www.purelyhr.com/contact-us). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja TimeOffManager oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/edit-attribute.png)

1. Oprócz powyższej aplikacji TimeOffManager oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | --- | --- |
    | Firstname |Nazwa użytkownika.givenname |
    | Lastname |User.nazwisko |
    | Adres e-mail |User.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie funkcji TimeOffManager** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając dostęp do TimeOffManager.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **opcję TimeOffManager**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-timeoffmanager-sso"></a>Konfigurowanie sytcha liczby SYK timeoffmanager

1. W innym oknie przeglądarki sieci Web zaloguj się do witryny firmy TimeOffManager jako administrator.

2. Przejdź do ** \> pozycji \> Opcje konta logowania jednokrotnego**.
   
    ![Ustawienia logowania jednokrotnego](./media/timeoffmanager-tutorial/ic795917.png "Ustawienia logowania jednokrotnego")

3. W sekcji **Ustawienia logowania jednokrotnego** wykonaj następujące czynności:
   
    ![Ustawienia logowania jednokrotnego](./media/timeoffmanager-tutorial/ic795918.png "Ustawienia logowania jednokrotnego")
   
    a. Otwórz certyfikat zakodowany w notatniku base-64, skopiuj jego zawartość do schowka, a następnie wklej cały certyfikat w **textboxie certyfikatu X.509.**
   
    b. W polu tekstowym **wystawcy Idp** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.
   
    d. W polu tekstowym **adresu URL punktu końcowego IdP** wklej wartość **adresu URL logowania** skopiowanego z witryny Azure portal.
   
    d. Jako **Wymuszaj SAML**, wybierz **nie**.
   
    e. Jako **automatyczne tworzenie użytkowników**wybierz pozycję **Tak**.
   
    f. W polu tekstowym **adresu URL wylogowania** wklej wartość **adresu URL wylogowania** skopiowanego z witryny Azure Portal.
   
    g. kliknij **pozycję Zapisz zmiany**.

4. Na stronie **Logowania jednokrotnego na** stronie ustawień skopiuj wartość **adresu URL usługi konsumenta oświadczeń** i wklej go w polu tekstowym Adres URL **odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal. 

      ![Ustawienia logowania jednokrotnego](./media/timeoffmanager-tutorial/ic795915.png "Ustawienia logowania jednokrotnego")

### <a name="create-timeoffmanager-test-user"></a>Utwórz użytkownika testowego TimeOffManager

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w TimeOffManager. TimeOffManager obsługuje just-in-time inicjowania obsługi administracyjnej użytkownika, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w TimeOffManager, nowy jest tworzony po uwierzytelnieniu.

>[!NOTE]
>Można użyć innych narzędzi do tworzenia konta użytkownika TimeOffManager lub interfejsów API dostarczonych przez TimeOffManager do aprowizowania kont użytkowników usługi Azure AD.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka TimeOffManager w Panelu dostępu należy automatycznie zalogować się do TimeOffManager, dla którego skonfigurowano logującą się jednokrotność. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj timeoffmanager z usługą Azure AD](https://aad.portal.azure.com/)

