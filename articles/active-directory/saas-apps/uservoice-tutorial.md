---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem UserVoice | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi UserVoice.
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
ms.openlocfilehash: dbd7189b1761a9ea88ce32dae3d7b45a88301ff6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60338771"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Samouczek: Integracja usługi Azure Active Directory z programem UserVoice

W tym samouczku dowiesz się, jak zintegrować UserVoice w usłudze Azure Active Directory (Azure AD).
Integrowanie usługi UserVoice z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi UserVoice.
* Aby umożliwić użytkownikom można automatycznie zalogowany do UserVoice (logowanie jednokrotne) za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi UserVoice, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* UserVoice logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje UserVoice **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-uservoice-from-the-gallery"></a>Dodawanie usługi UserVoice z galerii

Aby skonfigurować integrację usługi UserVoice w usłudze Azure AD, należy dodać UserVoice z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi UserVoice w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **UserVoice**, wybierz opcję **UserVoice** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![UserVoice na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi UserVoice w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze UserVoice musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi UserVoice, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie usługi UserVoice logowania jednokrotnego](#configure-uservoice-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego UserVoice](#create-uservoice-test-user)**  — aby odpowiednikiem Britta Simon w usłudze UserVoice, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowania jednokrotnego z usługi UserVoice, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **UserVoice** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena usługi UserVoice i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenantname>.UserVoice.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta UserVoice](https://www.uservoice.com/) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **Odcisk palca** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. Na **Konfigurowanie UserVoice** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-uservoice-single-sign-on"></a>Konfigurowanie usługi UserVoice logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się w witrynie UserVoice firmy jako administrator.

2. Na pasku narzędzi u góry kliknij **ustawienia**, a następnie wybierz pozycję **portalu sieci Web** z menu.
   
    ![W sekcji Ustawienia na stronie aplikacji](./media/uservoice-tutorial/ic777519.png "ustawienia")

3. Na **portalu sieci Web** na karcie **uwierzytelnianie użytkownika** , kliknij przycisk **Edytuj** otworzyć **Edytuj uwierzytelnianie użytkownika** strony okna dialogowego.
   
    ![Portal sieci Web kartę](./media/uservoice-tutorial/ic777520.png "portalu sieci Web")

4. Na **Edytuj uwierzytelnianie użytkownika** okna dialogowego strony, wykonaj następujące czynności:
   
    ![Edytuj uwierzytelnianie użytkownika](./media/uservoice-tutorial/ic777521.png "Edytuj uwierzytelnianie użytkownika")
   
    a. Kliknij przycisk **logowanie jednokrotne (SSO)**.
 
    b. Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal do **logowania jednokrotnego zdalnego logowania** pola tekstowego.

    c. Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal do **wylogowania zdalnego logowania jednokrotnego w polu tekstowym**.
 
    d. Wklej **odcisk palca** wartości, które zostały skopiowane z witryny Azure portal do **bieżącego odcisk palca certyfikatu SHA1** pola tekstowego.
    
    e. Kliknij przycisk **zapisywanie ustawień uwierzytelniania**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do usługi UserVoice.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **UserVoice**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **UserVoice**.

    ![Link usługi UserVoice, na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-uservoice-test-user"></a>Tworzenie użytkownika testowego usługi UserVoice

Aby umożliwić użytkownikom usługi Azure AD zarejestrować się w usłudze UserVoice, musi być obsługiwana w usłudze UserVoice. W przypadku usługi UserVoice Inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Twojej **UserVoice** dzierżawy.

2. Przejdź do obszaru **Settings** (Ustawienia).
   
    ![Ustawienia](./media/uservoice-tutorial/ic777811.png "Ustawienia")

3. Kliknij przycisk **ogólne**.

4. Kliknij przycisk **agentów i uprawnienia**.
   
    ![Uprawnienia i agenci](./media/uservoice-tutorial/ic777812.png "agentów i uprawnienia")

5. Kliknij przycisk **dodać administratorów**.
   
    ![Dodaj administratorów](./media/uservoice-tutorial/ic777813.png "dodać administratorów")

6. Na **zaprosić Administratorzy** okno dialogowe, należy wykonać następujące czynności:
   
    ![Zaproś Administratorzy](./media/uservoice-tutorial/ic777814.png "zaproszenie administratorów")
   
    a. W polu tekstowym wiadomości E-mail, wpisz adres e-mail konta, do aprowizowania, a następnie kliknij przycisk **Dodaj**.
   
    b. Kliknij pozycję **Invite** (Zaproś).

> [!NOTE]
> Można użyć jakichkolwiek innych UserVoice użytkownika konta tworzenie narzędzi lub interfejsów API udostępniane przez usługę UserVoice aprowizacji kont użytkowników usługi AAD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi UserVoice w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze UserVoice, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

