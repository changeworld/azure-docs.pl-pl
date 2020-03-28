---
title: 'Samouczek: Integracja usługi Azure Active Directory z Percolate | Dokumenty firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a Percolate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094594"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Samouczek: Integracja usługi Azure Active Directory z percolate

W tym samouczku dowiesz się, jak zintegrować Percolate z usługą Azure Active Directory (Azure AD).

Integracja ta zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do Percolate.
* Można włączyć użytkowników, aby automatycznie zalogować się do Percolate (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Percolate, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja Percolate z włączoną funkcją logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Percolate obsługuje identyfikatory SSO inicjowane przez sp i idp.

## <a name="add-percolate-from-the-gallery"></a>Dodaj Percolate z galerii

Aby skonfigurować integrację Percolate do usługi Azure AD, należy dodać Percolate z galerii do listy zarządzanych aplikacji SaaS.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory:**

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji dla przedsiębiorstw** > **Wszystkie aplikacje:**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Percolate**. W wynikach wyszukiwania wybierz **pozycję Percolate,** a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD za pomocą Percolate przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD a odpowiednim użytkownikiem w Percolate.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą Percolate, należy wykonać następujące kroki:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby włączyć tę funkcję dla użytkowników.
2. **[Konfigurowanie percolate logowania jednokrotnego](#configure-percolate-single-sign-on)** po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednookrotne usługi Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć logowanie jednookrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego Percolate,](#create-a-percolate-test-user)** który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą Percolate, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Percolate** wybierz pozycję **Logowanie jednokrotne:**

    ![Wybieranie logowania jednokrotnego](common/select-sso.png)

2. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne:

    ![Wybieranie metody logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę **Edycja,** aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML:**

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Konfiguracja podstawowego SAML** nie trzeba podejmować żadnych działań w celu skonfigurowania aplikacji w trybie inicjowanym przez IdP. Aplikacja jest już zintegrowana z platformą Azure.

    ![Przenikanie informacji o logowaniach do domeny i adresów URL](common/preintegrated.png)

5. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp. zgiełku, wybierz pozycję **Ustaw dodatkowe adresy URL,** a w polu **Zaloguj się na adres URL** wprowadź: **https://percolate.com/app/login**

   ![Przenikanie informacji o logowaniach do domeny i adresów URL](common/metadata-upload-additional-signon.png)
6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** wybierz ikonę **Kopiuj,** aby skopiować **adres URL metadanych federacji aplikacji**. Zapisz ten adres URL.

    ![Kopiowanie adresu URL metadanych federacji aplikacji](common/copy-metadataurl.png)

7. W sekcji **Konfigurowanie percolate** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowania**.

### <a name="configure-percolate-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Percolate

1. W nowym oknie przeglądarki internetowej zaloguj się do Percolate jako administrator.

2. Po lewej stronie strony głównej wybierz pozycję **Ustawienia:**
    
    ![Wybranie pozycji Ustawienia](./media/percolate-tutorial/configure01.png)

3. W lewym okienku wybierz pozycję **SSO** w obszarze **Organizacja**:

    ![Wybierz opcję jeden na szycie w obszarze Organizacja](./media/percolate-tutorial/configure02.png)

    1. W polu **Adres URL logowania** wklej wartość adresu URL **logowania** skopiowaną z witryny Azure portal.

    1. W polu **Identyfikator jednostki** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure portal.

    1. W Notatniku otwórz certyfikat zakodowany base-64 pobrany z witryny Azure portal. Skopiuj jego zawartość i wklej ją do pola **certyfikatów x509.**

    1. W polu **Atrybut e-mail** wpisz **adres e-mail**.

    1. Pole **adresu URL metadanych dostawcy tożsamości** jest polem opcjonalnym. Jeśli **adres URL metadanych federacji aplikacji** został skopiowany z witryny Azure Portal, możesz wkleić go do tego pola.

    1. Na liście **Czy AuthNRequests należy podpisać?** **No**

    1. Na liście **Włączanie automatycznego inicjowania obsługi administracyjnej funkcji SSO** wybierz pozycję **Nie**.

    1. Wybierz **pozycję Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure portal.

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory** w lewym okienku, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy:**

    ![Wybierz pozycję Wszyscy użytkownicy](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu:

    ![Wybierz pozycję Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **Użytkownik** należy wykonać następujące czynności.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź BrittaSimon@ **\<> swojej>.\<>rozszerzenia **. (Na przykład BrittaSimon@contoso.com.)

    1. Wybierz **pozycję Pokaż hasło**, a następnie zapisz wartość w polu **Hasło.**

    1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz Britta Simon do korzystania z usługi Azure AD logowania jednokrotnego, przyznając jej dostęp do Percolate.

1. W portalu Azure wybierz **pozycję Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Percolate**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **percolate**.

    ![Lista wniosków](common/all-applications.png)

3. W lewym okienku wybierz **pozycję Użytkownicy i grupy:**

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Użytkownicy i grupy](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-percolate-test-user"></a>Tworzenie użytkownika testowego Percolate

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Percolate, należy dodać je do Percolate. Należy dodać je ręcznie.

Aby utworzyć konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Percolate jako administrator.

2. W lewym okienku wybierz pozycję **Użytkownicy** w obszarze **Organizacja**. Wybierz **nowych użytkowników:**

    ![Wybierz nowych użytkowników](./media/percolate-tutorial/configure03.png)

3. Na stronie **Tworzenie użytkowników** należy wykonać następujące czynności.

    ![Strona Tworzenie użytkowników](./media/percolate-tutorial/configure04.png)

    1. W polu **Poczta e-mail** wprowadź adres e-mail użytkownika. Na przykład brittasimon@contoso.com.

    1. W polu **Pełna nazwa** wprowadź nazwę użytkownika. Na przykład **Brittasimon**.

    1. Wybierz **pozycję Utwórz użytkowników**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Percolate w Panelu dostępu należy automatycznie zalogować się do wystąpienia Percolate, dla którego skonfigurowano logowanie logujące się. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)