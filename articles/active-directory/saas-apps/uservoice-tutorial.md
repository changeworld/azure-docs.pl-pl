---
title: 'Samouczek: Integracja usługi Azure Active Directory z uservoice | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a uservoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 7a3302f1ca615fe5005be9ed1f09995ebf432eb7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232001"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Samouczek: Integracja usługi Azure Active Directory z uservoice

W tym samouczku dowiesz się, jak zintegrować uservoice z usługą Azure Active Directory (Azure AD).
Integracja uservoice z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do UserVoice.
* Można włączyć użytkowników, aby automatycznie zalogować się do UserVoice (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z uservoice, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną rejestracją jednokrotną UserVoice

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* UserVoice obsługuje zainicjowane przez **usługę SP** wprowadzenie SSO

## <a name="adding-uservoice-from-the-gallery"></a>Dodawanie uservoice z galerii

Aby skonfigurować integrację uservoice do usługi Azure AD, należy dodać UserVoice z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać uservoice z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **UserVoice**, wybierz **uservoice** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![UserVoice na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z UserVoice na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w uservoice.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą uservoice, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne uservoice](#configure-uservoice-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz userVoice użytkownika testowego](#create-uservoice-test-user)** - mieć odpowiednik Britta Simon w UserVoice, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą uservoice, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **UserVoice** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie uservoice i adresach URL logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenantname>.UserVoice.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta UserVoice,](https://www.uservoice.com/) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj **odcisk palca** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Konfigurowanie uservoice** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-uservoice-single-sign-on"></a>Konfigurowanie logowania jednokrotnego uservoice

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy UserVoice jako administrator.

2. Na pasku narzędzi u góry kliknij pozycję **Ustawienia**, a następnie wybierz z menu **pozycję Portal sieci Web.**
   
    ![Sekcja Ustawienia po stronie aplikacji](./media/uservoice-tutorial/ic777519.png "Ustawienia")

3. Na karcie **Portal sieci Web** w sekcji **Uwierzytelnianie użytkowników** kliknij pozycję **Edytuj,** aby otworzyć stronę dialogową **Edytowanie uwierzytelniania użytkowników.**
   
    ![Karta Portal sieci Web](./media/uservoice-tutorial/ic777520.png "Portal internetowy")

4. Na stronie okna dialogowego **Edytowanie uwierzytelniania użytkowników** wykonaj następujące czynności:
   
    ![Edytowanie uwierzytelniania użytkowników](./media/uservoice-tutorial/ic777521.png "Edytowanie uwierzytelniania użytkowników")
   
    a. Kliknij **pozycję Logowanie jednokrotne .SSO).**
 
    b. Wklej wartość **adresu URL logowania,** która została skopiowana z witryny Azure portal do skrzynki tekstowej **logowania zdalnego** logowania.

    d. Wklej wartość **adresu URL wylogowania,** która została skopiowana z witryny Azure portal do **skrzynki tekstowej zdalnego wylogowania logowania**logowania.
 
    d. Wklej **thumbprint** wartość , który został skopiowany z witryny Azure portal do **bieżącego certyfikatu SHA1 odcisk palca** pole tekstowe.
    
    e. Kliknij **pozycję Zapisz ustawienia uwierzytelniania**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** brittasimon@yourcompanydomain.extensionużytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do UserVoice.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz **pozycję UserVoice**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **uservoice**.

    ![Łącze UserVoice na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-uservoice-test-user"></a>Utwórz użytkownika testowego UserVoice

Aby umożliwić użytkownikom usługi Azure AD zalogować się do UserVoice, muszą one być aprowidzone do UserVoice. W przypadku UserVoice inicjowania obsługi administracyjnej jest zadanie ręczne.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do dzierżawy **UserVoice.**

2. Przejdź do obszaru **Settings** (Ustawienia).
   
    ![Ustawienia](./media/uservoice-tutorial/ic777811.png "Ustawienia")

3. Kliknij **pozycję Ogólne**.

4. Kliknij **pozycję Agenci i uprawnienia**.
   
    ![Agenci i uprawnienia](./media/uservoice-tutorial/ic777812.png "Agenci i uprawnienia")

5. Kliknij **pozycję Dodaj administratorów**.
   
    ![Dodawanie administratorów](./media/uservoice-tutorial/ic777813.png "Dodawanie administratorów")

6. W oknie dialogowym **Zaproś administratorów** wykonaj następujące czynności:
   
    ![Zapraszanie administratorów](./media/uservoice-tutorial/ic777814.png "Zapraszanie administratorów")
   
    a. W polach tekstowych Wiadomości e-mail wpisz adres e-mail konta, które chcesz udostępnić, a następnie kliknij przycisk **Dodaj**.
   
    b. Kliknij pozycję **Invite** (Zaproś).

> [!NOTE]
> Można użyć innych narzędzi do tworzenia konta użytkownika UserVoice lub interfejsów API dostarczonych przez UserVoice do aprowizowania kont użytkowników usługi Azure AD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka UserVoice w Panelu dostępu należy automatycznie zalogować się do uservoice, dla którego skonfigurowano logowanie jednośmiętne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

