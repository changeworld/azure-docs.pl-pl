---
title: 'Samouczek: integracja Azure Active Directory z nowymi Relic | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i nowymi Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: dfa5fbcf5df8ee314aab3e4a8228d81e7e14565b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233515"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Samouczek: integracja Azure Active Directory z nowymi Relic

W tym samouczku dowiesz się, jak zintegrować nowe Relic z Azure Active Directory (Azure AD).
Integracja nowych Relic z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do nowego Relic.
* Możesz umożliwić użytkownikom automatyczne logowanie do nowego Relic (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD przy użyciu nowego Relic, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Nowa subskrypcja z włączonym logowaniem jednokrotnym w usłudze Relic

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Nowość Relic obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-new-relic-from-the-gallery"></a>Dodawanie nowego Relic z galerii

Aby skonfigurować integrację nowych Relic z usługą Azure AD, musisz dodać nową Relic z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać nowe Relic z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **New Relic**, wybierz pozycję **Nowy Relic** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Nowe Relic na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą nowego Relic na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w nowym Relic.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu nowego Relic, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj nowe logowanie](#configure-new-relic-single-sign-on)** jednokrotne w usłudze Relic, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz nowego użytkownika testowego Relic](#create-new-relic-test-user)** — Aby uzyskać odpowiednik Britta Simon w nowym Relic, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu nowego Relic, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie **Nowa** integracja aplikacji Relic wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Nowa domena Relic i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` — Pamiętaj, aby zastąpić własny nowy identyfikator konta Relic.

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `rpm.newrelic.com`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie nowego Relic** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-new-relic-single-sign-on"></a>Konfiguruj nowe Logowanie jednokrotne w usłudze Relic

1. W innym oknie przeglądarki sieci Web Zaloguj się do nowej witryny firmowej **Relic** jako administrator.

2. W menu u góry kliknij pozycję **Ustawienia konta**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797036.png "Ustawienia konta")

3. Kliknij kartę **zabezpieczenia i uwierzytelnianie** , a następnie kliknij kartę **Logowanie** jednokrotne.
   
    ![Logowanie jednokrotne](./media/new-relic-tutorial/ic797037.png "Logowanie jednokrotne")

4. Na stronie okno dialogowe SAML wykonaj następujące czynności:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Kliknij pozycję **Wybierz plik** , aby przekazać pobrany certyfikat Azure Active Directory.

    b. W polu tekstowym **adres URL logowania zdalnego** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.
   
    c. W polu tekstowym **Wyloguj adres URL** , wklej wartość **adres URL wylogowania**, który został skopiowany z Azure Portal.

    d. Kliknij przycisk **Zapisz moje zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon\@yourcompanydomain. Extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu nowemu Relic.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Nowy Relic**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Nowy Relic**.

    ![Nowe łącze Relic na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-new-relic-test-user"></a>Utwórz nowego użytkownika testowego Relic

Aby umożliwić użytkownikom Azure Active Directory logowanie się do nowych Relic, muszą one być obsługiwane w nowych Relic. W przypadku nowych Relic Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby udostępnić konto użytkownika nowemu Relic, wykonaj następujące czynności:**

1. Zaloguj się do nowej witryny firmowej **Relic** jako administrator.

2. W menu u góry kliknij pozycję **Ustawienia konta**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797040.png "Ustawienia konta")

3. W okienku **konto** po lewej stronie kliknij pozycję **Podsumowanie**, a następnie kliknij pozycję **Dodaj użytkownika**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797041.png "Ustawienia konta")

4. W oknie dialogowym **aktywni użytkownicy** wykonaj następujące czynności:
   
    ![Aktywni użytkownicy](./media/new-relic-tutorial/ic797042.png "Aktywni użytkownicy")
   
    a. W polu tekstowym **adres e-mail** wpisz adres e-mail prawidłowego użytkownika Azure Active Directory, który chcesz udostępnić.

    b. Jako **rola** wybierz **użytkownika**.

    c. Kliknij pozycję **Dodaj tego użytkownika**.

>[!NOTE]
>Do obsługi kont użytkowników usługi Azure AD można używać innych nowych narzędzi do tworzenia kont użytkowników Relic i interfejsów API udostępnionych przez nowe Relic.
> 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka nowy Relic w panelu dostępu należy automatycznie zalogować się do nowego Relic, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

