---
title: 'Samouczek: Integracja usługi Azure Active Directory z nimblex | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a Nimblex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d3e165a5-f062-4b50-ac0b-b400838e99cd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: jeedes
ms.openlocfilehash: cd7199e94a58b3f0c121a0fd9401bff94406ed84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67096097"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Samouczek: Integracja usługi Azure Active Directory z nimblex

W tym samouczku dowiesz się, jak zintegrować Nimblex z usługą Azure Active Directory (Azure AD).
Integracja Nimblex z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do Nimblex.
* Można włączyć użytkowników do automatycznego logowania do Nimblex (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z nimblex, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą jednokrotnego logowania Nimblex

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Nimblex obsługuje sso inicjowane przez **SP**

* Nimblex obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-nimblex-from-the-gallery"></a>Dodawanie Nimblex z galerii

Aby skonfigurować integrację Nimblex z usługą Azure AD, należy dodać Nimblex z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Nimblex z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Nimblex**, wybierz **Nimblex** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Nimblex na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Nimblex na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Nimblex.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą nimblex, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj jednokrotne logowanie Nimblex](#configure-nimblex-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Nimblex](#create-nimblex-test-user)** — aby mieć odpowiednik Britta Simon w Nimblex, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą nimblex, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Nimblex** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o logach jednokrotnych w domenie Nimblex i adresach URL](common/sp-identifier-reply.png)

    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<YOUR APPLICATION PATH>/Login.aspx`

    b. W polu **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<YOUR APPLICATION PATH>/`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<path-to-application>/SamlReply.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta Nimblex,](mailto:support@ebms.com.au) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie nimblex** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-nimblex-single-sign-on"></a>Konfigurowanie jednokrotnego logowania Nimblex

1. W innym oknie przeglądarki internetowej zaloguj się do Nimblex jako administrator zabezpieczeń.

2. W prawym górnym rogu strony kliknij logo **Ustawienia.**

    ![Ustawienia Nimblex](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

3. Na stronie **Panel sterowania** w sekcji **Zabezpieczenia** kliknij pozycję **Logowanie jednokrotne**.

    ![Ustawienia Nimblex](./media/nimblex-tutorial/tutorial_nimblex_single.png)

4. Na stronie **Zarządzanie logiem jednokrotnym** wybierz nazwę wystąpienia i kliknij przycisk **Edytuj**.

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

5. Na stronie **Edytuj dostawcę ujednu** dla osób ujednych wykonaj następujące czynności:

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. W polu tekstowym **Opis** wpisz nazwę wystąpienia.

    b. W Notatniku otwórz certyfikat zakodowany base-64 pobrany z witryny Azure Portal, skopiuj jego zawartość, a następnie wklej go do pola **Certyfikat.**

    d. W polu tekstowym **docelowy adresu docelowego dostawcy tożsamości** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    d. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź pole **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz**brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do Nimblex.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **nimblex**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **nimblex**.

    ![Łącze Nimblex na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-nimblex-test-user"></a>Utwórz użytkownika testowego Nimblex

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Nimblex. Nimblex obsługuje just-in-time inicjowania obsługi administracyjnej użytkownika, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Nimblex, nowy jest tworzony po uwierzytelnieniu.

>[!Note]
>Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta Nimblex](mailto:support@ebms.com.au).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Nimblex w Panelu dostępu należy automatycznie zalogować się do nimblex, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

