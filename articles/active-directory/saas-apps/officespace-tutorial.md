---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z oprogramowaniem OfficeSpace | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a oprogramowaniem OfficeSpace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80712c7f59845287006c1699524573c6094498b3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561715"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z oprogramowaniem OfficeSpace

W tym samouczku dowiesz się, jak zintegrować oprogramowanie OfficeSpace z usługą Azure Active Directory (Azure AD). Po zintegrowaniu oprogramowania OfficeSpace z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do oprogramowania OfficeSpace.
* Włącz użytkownikom automatyczne logowanie do oprogramowania OfficeSpace za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego oprogramowania OfficeSpace.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Oprogramowanie OfficeSpace obsługuje funkcję SSO inicjowane przez usługę **SP**


* Oprogramowanie OfficeSpace obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time


## <a name="adding-officespace-software-from-the-gallery"></a>Dodawanie oprogramowania OfficeSpace z galerii

Aby skonfigurować integrację oprogramowania OfficeSpace z usługą Azure AD, należy dodać oprogramowanie OfficeSpace z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **oprogramowanie OfficeSpace** w polu wyszukiwania.
1. Wybierz **pozycję Oprogramowanie OfficeSpace** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla oprogramowania OfficeSpace

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą oprogramowania OfficeSpace przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuował działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w oprogramowaniu OfficeSpace.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą oprogramowania OfficeSpace, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne oprogramowania OfficeSpace](#configure-officespace-software-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego oprogramowania OfficeSpace](#create-officespace-software-test-user)** — aby mieć odpowiednik B.Simon w oprogramowaniu OfficeSpace, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **officespace** znajdź sekcję **Zarządzanie** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta oprogramowania OfficeSpace,](mailto:support@officespacesoftware.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja OfficeSpace software oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja OfficeSpace software **oczekuje,** że identyfikator nazw zostanie zamapowany na **user.mail,** więc musisz edytować mapowanie atrybutów, klikając ikonę **Edytuj** i zmienić mapowanie atrybutów.

    ![image](common/edit-attribute.png)

1. Oprócz powyższej aplikacji OfficeSpace software oczekuje, że kilka więcej atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | email | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

1. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

1. W sekcji **Certyfikat podpisywania SAML** skopiuj **wartość odcisku palca** i zapisz ją na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

1. W sekcji **Konfigurowanie oprogramowania OfficeSpace** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do oprogramowania OfficeSpace.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Oprogramowanie OfficeSpace**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="configure-officespace-software-sso"></a>Konfigurowanie funkcjiomomom systemu OfficeSpace

1. W innym oknie przeglądarki sieci Web zaloguj się do dzierżawy oprogramowania OfficeSpace jako administrator.

2. Przejdź do **pozycji Ustawienia** i kliknij pozycję **Łączniki**.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Kliknij **pozycję Uwierzytelnianie SAML**.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/officespace-tutorial/tutorial_officespace_003.png)

4. W sekcji **SAML Authentication** (Uwierzytelnianie SAML) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. W polu tekstowym **url dostawcy wylogowania** wklej wartość **adresu URL wylogowania** skopiowanego z witryny Azure Portal.

    b. W polu tekstowym **docelowy adres URL klienta idp** wklej wartość **adresu URL logowania** skopiowanego z witryny Azure portal.

    d. Wklej wartość **Odcisk palca** skopiowaną z witryny Azure portal do pola **tekstowego odcisków palców certyfikatu IDP klienta.** 

    d. Kliknij **pozycję Zapisz ustawienia**.

### <a name="create-officespace-software-test-user"></a>Tworzenie użytkownika testowego oprogramowania OfficeSpace

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w oprogramowaniu OfficeSpace. Oprogramowanie OfficeSpace obsługuje just-in-time inicjowania obsługi administracyjnej, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w oprogramowaniu OfficeSpace, nowy jest tworzony po uwierzytelnieniu.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej programu OfficeSpace Software](mailto:support@officespacesoftware.com).

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Oprogramowanie OfficeSpace w Panelu dostępu należy automatycznie zalogować się do oprogramowania OfficeSpace, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj oprogramowanie OfficeSpace z usługą Azure AD](https://aad.portal.azure.com/)

