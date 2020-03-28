---
title: 'Samouczek: Integracja usługi Azure Active Directory z bonusowo | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea9c88f8eb8ac7b72f11ff286d2294df8cb70860
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232058"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Samouczek: Integracja usługi Azure Active Directory z bonusowo

W tym samouczku dowiesz się, jak zintegrować bonusowo z usługą Azure Active Directory (Azure AD).
Integracja bonusowo z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do bonusly.
* Można włączyć użytkowników, aby automatycznie zalogować się do bonusowo (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z bonusem, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą pojedynczego logowania

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Bonusowo obsługuje zainicjowane **przez IDP jednostkę** SSO

## <a name="adding-bonusly-from-the-gallery"></a>Dodawanie bonusu z galerii

Aby skonfigurować integrację bonusowo z usługą Azure AD, należy dodać bonusowo z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać bonusowo z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Bonusly**, wybierz **Bonusly** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Bonusowo na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Bonusly na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w bonusly.

Aby skonfigurować i przetestować usługę Azure AD jednokrotnego logowania z Bonusly, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj bonusowe logowanie jednokrotne](#configure-bonusly-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego bonusly](#create-bonusly-test-user)** — mieć odpowiednik Britta Simon w bonusly, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą bonusly, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Bonusly** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie bonusowej i adresach URL z logami jednokrotnymi](common/idp-reply.png)

    W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, używając następującego wzorca:`https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta Bonusly,](https://bonus.ly/contact) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **ODCISK PALCA** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Konfigurowanie bonusów** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-bonusly-single-sign-on"></a>Konfigurowanie automatycznego logowania jednokrotnego

1. W innym oknie przeglądarki zaloguj się do **dzierżawy Bonusly.**

1. Na pasku narzędzi u góry kliknij pozycję **Ustawienia,** a następnie wybierz pozycję **Integracje i aplikacje**.

    ![Sekcja społecznościowa bonusowo](./media/bonus-tutorial/ic773686.png "Bonusly")
1. W obszarze **Logowanie jednokrotne**wybierz **saml**.

1. Na stronie okna dialogowego **SAML** wykonaj następujące czynności:

    ![Strona okno dialogowe Saml bonusly](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. W polu tekstowym **docelowego adresu URL logowania IdP** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    b. W polu tekstowym **adresu URL logowania IdP** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    d. W polu tekstowym **wystawcy IdP** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.
    
    d. Wklej wartość **odcisk palca** skopiowaną z witryny Azure portal do pola tekstowego **Cert Fingerprint.**
    
    e. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** `brittasimon@yourcompanydomain.extension`użytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do bonusly.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **opcję Bonusly**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **opcję Bonusly**.

    ![Link Bonusly na liście Aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-bonusly-test-user"></a>Utwórz użytkownika testowego Bonusly

Aby umożliwić użytkownikom usługi Azure AD zalogować się do bonusly, muszą one być aprowidywne do bonusly. W przypadku bonusly inicjowania obsługi administracyjnej jest zadanie ręczne.

> [!NOTE]
> Można użyć innych narzędzi do tworzenia konta użytkownika bonusowego lub interfejsów API dostarczonych przez bonusly do aprowizowania kont użytkowników usługi Azure AD. 

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. W oknie przeglądarki internetowej zaloguj się do dzierżawy Bonusly.

1. Kliknij przycisk **Ustawienia**.

    ![Ustawienia](./media/bonus-tutorial/ic781041.png "Ustawienia")

1. Kliknij kartę **Użytkownicy i bonusy.**

    ![Użytkownicy i bonusy](./media/bonus-tutorial/ic781042.png "Użytkownicy i bonusy")

1. Kliknij **pozycję Zarządzaj użytkownikami**.

    ![Zarządzanie użytkownikami](./media/bonus-tutorial/ic781043.png "Zarządzanie użytkownikami")

1. Kliknij **pozycję Dodaj użytkownika**.

    ![Dodaj użytkownika](./media/bonus-tutorial/ic781044.png "Dodaj użytkownika")

1. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:

    ![Dodaj użytkownika](./media/bonus-tutorial/ic781045.png "Dodaj użytkownika")  

    a. W polu tekstowym **Imię** wprowadź imię użytkownika, takiego jak **Britta**.

    b. W polu tekstowym **Last name (Nazwisko)** wprowadź nazwisko użytkownika, na przykład **Simon**.

    d. W polu tekstowym **E-mail** wprowadź adres e-mail użytkownika, na przykład `brittasimon\@contoso.com`.

    d. Kliknij przycisk **Zapisz**.

    > [!NOTE]
    > Posiadacz konta usługi Azure AD otrzymuje wiadomość e-mail zawierającą łącze potwierdzające konto, zanim stanie się aktywne.  

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Bonusly w Panelu dostępu, powinieneś zostać automatycznie zalogowany do Bonusly, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
