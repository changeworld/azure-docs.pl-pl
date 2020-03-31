---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z spotkaniami cisco webex | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a spotkaniami cisco webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4e497c556bde1be4e498cd85a68282a0e3b2666
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026271"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) ze spotkaniami cisco webex

W tym samouczku dowiesz się, jak zintegrować spotkania cisco webex z usługą Azure Active Directory (Azure AD). Po zintegrowaniu spotkań cisco webex z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do spotkań Cisco Webex.
* Umożliwia automatyczne logowanie użytkowników do spotkań cisco webex za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (Logowanie jednokrotne) cisco Webex Meetings.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Cisco Webex Meetings obsługuje jednostkę SSO inicjowane przez **SP i IDP**

* Cisco Webex Meetings obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Dodawanie spotkań Cisco Webex z galerii

Aby skonfigurować integrację spotkań cisco webex z usługą Azure AD, należy dodać spotkania Cisco Webex z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Cisco Webex Meetings** w polu wyszukiwania.
1. Wybierz **pozycję Cisco Webex Meetings** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla spotkań cisco webex

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą cisco Webex Meetings przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w witrynie Cisco Webex Meetings.

Aby skonfigurować i przetestować przychylić się do usługi Azure AD sytuować za pomocą aplikacji Cisco Webex Meetings, wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj logowanie jednokrotne cisco Webex Meetings](#configure-cisco-webex-meetings-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz cisco Webex Spotkania użytkownika testowego](#create-cisco-webex-meetings-test-user)** - mieć odpowiednik B.Simon w Cisco Webex Spotkania, który jest połączony z reprezentacji usługi Azure AD użytkownika.
3. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [portalu Azure](https://portal.azure.com/)— na stronie integracji aplikacji **Cisco Webex Meetings** znajdź sekcję **Zarządzaj** i wybierz **opcję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** można skonfigurować aplikację w trybie inicjowanym **przez dostawcę** usług, przesyłając plik **metadanych dostawcy usług** w następujący sposób:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    d. Po pomyślnym zakończeniu przekazywania pliku metadanych dostawcy usług wartości **identyfikatora** i **odpowiedzi adresy URL** są automatycznie wypełniane w sekcji **Podstawowa konfiguracja SAML.**

    >[!Note]
    >Otrzymasz plik metadanych dostawcy usług z sekcji **Konfigurowanie cisco Webex Spotkania SSO** sekcji, która jest wyjaśniona w dalszej części samouczka. 

1. Jeśli chcesz skonfigurować aplikację **w** trybie inicjowanym w sp, wykonaj następujące czynności:  

    a. W sekcji **Podstawowa konfiguracja SAML** kliknij ikonę edycji/pióra.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)
    
    b. W polu tekstowym **Podpisz adres URL** wpisz adres URL, używając następującego wzorca:` https://<customername>.my.webex.com`

5. Aplikacja Cisco Webex Meetings oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

6. Oprócz powyższej aplikacji Cisco Webex Meetings oczekuje, że kilka więcej atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML. W sekcji Oświadczenia użytkownika w oknie dialogowym Atrybuty użytkownika wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli: 

    | Nazwa | Atrybut źródłowy|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   email       | user.mail |
    |   Identyfikator UID    | user.mail |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Z listy **atrybutów Źródło** wybierz wartość atrybutu wyświetlaną dla tego wiersza z listy rozwijanej.

    f. Kliknij przycisk **Zapisz**.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie spotkań cisco Webex** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do cisco Webex Spotkania.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Cisco Webex Meetings**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-cisco-webex-meetings-sso"></a>Konfigurowanie spotkania Cisco Webex — spotkanie sytograficzne

1. Przejdź `https://<customername>.webex.com/admin` do adresu URL z poświadczeniami administracyjnymi.

2. Przejdź do **pozycji Typowe ustawienia witryny** i przejdź do **konfiguracji logowania przyuśtym**.
 
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Na stronie **Administracja sieci Web należy** wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. wybierz **SAML 2.0** jako **protokół federacyjny**.

    b. Kliknij **łącze Importuj metadane SAML,** aby przekazać plik metadanych, który został pobrany z witryny Azure portal.

    d. Kliknij przycisk **Eksportuj,** aby pobrać plik metadanych dostawcy usług i przekazać go w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    d. W polu **tekstowym AuthContextClassRef** wpisz `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` i jeśli chcesz włączyć usługę MFA przy użyciu usługi Azure AD, wpisz dwie wartości, takie jak`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Wybierz **opcję Automatyczne tworzenie konta**.

    >[!NOTE]
    >Aby włączyć inicjowanie obsługi administracyjnej przez użytkowników **tylko w czasie,** należy sprawdzić **automatyczne tworzenie konta**. Oprócz tego atrybuty tokenu SAML muszą być przekazywane w odpowiedzi SAML.

    f. Kliknij przycisk **Zapisz**.

    >[!NOTE]
    >Ta konfiguracja jest tylko dla klientów, którzy używają Webex UserID w formacie poczty e-mail.

### <a name="create-cisco-webex-meetings-test-user"></a>Tworzenie użytkownika testowego Spotkań Cisco Webex

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w Cisco Webex Spotkania. Cisco Webex Meetings obsługuje **aprowizacji just-in-time,** która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w programie Cisco Webex Meetings, podczas próby uzyskania dostępu do spotkań Cisco Webex tworzony jest nowy użytkownik.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Spotkania Cisco Webex w Panelu dostępu należy automatycznie zalogować się do spotkań Cisco Webex, dla których skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę ServiceNow z usługą Azure AD](https://aad.portal.azure.com)
