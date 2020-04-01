---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z OpenAthens | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fc2272a81672ea613bdcbe17c5381e99cafbff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73053214"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z OpenAthens

W tym samouczku dowiesz się, jak zintegrować OpenAthens z usługą Azure Active Directory (Azure AD). Po zintegrowaniu OpenAthens z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do OpenAthens.
* Włącz użytkownikom automatyczne logowanie do OpenAthens za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* OpenAthens subskrypcji z włączoną funkcją logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Usługa OpenAthens obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**
* Usługa OpenAthens obsługuje aprowizację użytkowników typu **Just In Time**

## <a name="adding-openathens-from-the-gallery"></a>Dodawanie usługi OpenAthens z galerii

Aby skonfigurować integrację usługi OpenAthens z usługą Azure AD, musisz dodać usługę OpenAthens z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Polecenie Otwórz Ateny** w polu wyszukiwania.
1. Wybierz **pozycję OpenAthens** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla openathens

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą openathens przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik sytuować dla użytkownika sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w OpenAthens.

Aby skonfigurować i przetestować usługę Azure AD SSO przy za pomocą openathens, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj openathens SSO](#configure-openathens-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego OpenAthens](#create-openathens-test-user)** — aby mieć odpowiednik B.Simon w OpenAthens, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **OpenAthens** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** przekaż **plik metadanych dostawcy usług**, dla którego kroki zostały wymienione w dalszej części tego samouczka.

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie metadanych usługi Openathens](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![Przeglądanie i przekazywanie metadanych usługi Openathens](common/browse-upload-metadata.png)

    d. Po pomyślnym przekazaniu pliku metadanych wartość **Identyfikator** zostanie automatycznie wypełniona w polu tekstowym sekcji **Podstawowa konfiguracja protokołu SAML**:

    ![Domena i adresy URL usługi OpenAthens — informacje o logowaniu jednokrotnym](common/idp-identifier.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie otwartych ateli** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do OpenAthens.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **OpenAthens**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-openathens-sso"></a>Konfigurowanie sytcha syd w openathens

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy OpenAthens jako administrator.

1. Wybierz pozycję **Connections** (Połączenia) z listy na karcie **Management** (Zarządzanie).

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Wybierz pozycję **SAML 1.1/2.0**, a następnie wybierz przycisk **Configure** (Konfiguruj).

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. W celu dodania konfiguracji wybierz przycisk **Browse** (Przeglądaj), aby przekazać plik xml metadanych pobrany z witryny Azure Portal, a następnie wybierz przycisk **Add** (Dodaj).

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Wykonaj poniższe kroki na karcie **Details** (Szczegóły).

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. W obszarze **Display name mapping** (Mapowanie nazwy wyświetlanej) wybierz opcję **Use attribute** (Użyj atrybutu).

    b. W polu tekstowym **Display name attribute** (Atrybut nazwy wyświetlanej) wprowadź wartość `http://schema.microsoft.com/identity/claims/displayname`.

    d. W obszarze **Unique user mapping** (Mapowanie unikatowego użytkownika) wybierz opcję **Use attribute** (Użyj atrybutu).

    d. W polu tekstowym **Unique user attribute** (Atrybut unikatowego użytkownika) wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. W obszarze **Status** (Stan) zaznacz wszystkie trzy pola wyboru.

    f. W obszarze **Create local accounts** (Tworzenie kont lokalnych) wybierz opcję **automatically** (automatycznie).

    g. Wybierz **pozycję Zapisz zmiany**.

    h. Z karty **</> Relying Party** (</> Jednostka uzależniona) skopiuj adres **Metadata URL** (Adres URL metadanych) i otwórz go w przeglądarce, aby pobrać plik **XML metadanych dostawcy usług**. Przekaż ten plik metadanych dostawcy usług w sekcji **Podstawowa konfiguracja protokołu SAML** w usłudze Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Tworzenie użytkownika testowego usługi OpenAthens

W tej sekcji w usłudze OpenAthens jest tworzony użytkownik o nazwie Britta Simon. Usługa OpenAthens obsługuje **aprowizację użytkowników just in time**, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w usłudze OpenAthens, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka OpenAthens w panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi OpenAthens, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj openathens za pomocą usługi Azure AD](https://aad.portal.azure.com/)