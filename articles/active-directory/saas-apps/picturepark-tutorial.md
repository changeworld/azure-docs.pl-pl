---
title: 'Samouczek: Integracja usługi Azure Active Directory z picturepark | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 08c5bd8da0dda74156b2d44c8106ed345ef749dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73177016"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Samouczek: Integracja usługi Azure Active Directory z picturepark

W tym samouczku dowiesz się, jak zintegrować picturepark z usługą Azure Active Directory (Azure AD).
Integracja picturepark z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do Picturepark.
* Można włączyć użytkowników, aby automatycznie zalogować się do Picturepark (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pictureparkiem, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego Picturepark

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Picturepark obsługuje zainicjowane przez **usługę SP** SSO

## <a name="adding-picturepark-from-the-gallery"></a>Dodawanie Picturepark z galerii

Aby skonfigurować integrację Picturepark z usługą Azure AD, należy dodać Picturepark z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Picturepark z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Picturepark**, wybierz **Picturepark** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Picturepark na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Picturepark na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Picturepark.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą Picturepark, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie picturepark logowanie jednokrotne](#configure-picturepark-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Picturepark](#create-picturepark-test-user)** — aby mieć odpowiednik Britta Simon w Picturepark, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą picturepark, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Picturepark** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL Picturepark](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.picturepark.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: 

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Picturepark,](https://picturepark.com/company/picturepark-customer-support) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj **odcisk palca** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Konfigurowanie pictureparku** skopiuj odpowiednie adresy URL zgodnie z wymaganiami. W przypadku **adresu URL logowania**użyj wartości z następującym wzorcem:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ jest identyfikatorem dzierżawy subskrypcji usługi Azure AD.

    ![Kopiowanie adresów URL konfiguracji](./media/picturepark-tutorial/configurls.png)

    a. Identyfikator usługi Azure AD

    b. Adres URL wylogowywania

### <a name="configure-picturepark-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Picturepark

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Picturepark jako administrator.

2. Na pasku narzędzi u góry kliknij pozycję **Narzędzia administracyjne**, a następnie kliknij pozycję **Konsola zarządzania**.
   
    ![Konsola zarządzania](./media/picturepark-tutorial/ic795062.png "Konsola zarządzania")

3. Kliknij **pozycję Uwierzytelnianie**, a następnie kliknij pozycję **Dostawcy tożsamości**.
   
    ![Uwierzytelnianie](./media/picturepark-tutorial/ic795063.png "Uwierzytelnianie")

4. W sekcji **Konfiguracja dostawcy tożsamości** wykonaj następujące kroki:
   
    ![Konfiguracja dostawcy tożsamości](./media/picturepark-tutorial/ic795064.png "Konfiguracja dostawcy tożsamości")
   
    a. Kliknij przycisk **Dodaj**.
  
    b. Wpisz nazwę konfiguracji.
   
    d. Wybierz **pozycję Ustaw jako domyślną**.
   
    d. W polu tekstowym **identyfikatora URI wystawcy** wklej wartość **adresu URL logowania** skopiowanego z witryny Azure portal.
   
    e. W polu **tekstowym Drukowanie kciuka zaufanego wystawcy** wklej wartość **odcisku palca** skopiowaną z sekcji **Certyfikat podpisywania SAML.** 

5. Kliknij pozycję **JoinDefaultUsersGroup**.

6. Aby ustawić atrybut **Emailaddress** w pola tekstowym **Oświadczenie,** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` i kliknij przycisk **Zapisz**.

      ![Konfiguracja](./media/picturepark-tutorial/ic795065.png "Konfigurowanie")

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

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do Picturepark.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz **picturepark**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **picturepark**.

    ![Łącze Picturepark na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-picturepark-test-user"></a>Utwórz użytkownika testowego Picturepark

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Picturepark, muszą być aprowied do Picturepark. W przypadku Picturepark inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **Picturepark.**

1. Na pasku narzędzi u góry kliknij pozycję **Narzędzia administracyjne**, a następnie kliknij pozycję **Użytkownicy**.
   
    ![Użytkownicy](./media/picturepark-tutorial/ic795067.png "Użytkownicy")

1. Na karcie **Przegląd użytkowników** kliknij pozycję **Nowy**.
   
    ![Zarządzanie użytkownikami](./media/picturepark-tutorial/ic795068.png "Zarządzanie użytkownikami")

1. W oknie dialogowym **Tworzenie użytkownika** wykonaj następujące kroki prawidłowego użytkownika usługi Azure Active Directory, który chcesz aprowizować:
   
    ![Tworzenie użytkownika](./media/picturepark-tutorial/ic795069.png "Utwórz użytkownika")
   
    a. W polach **tekstowych Adres e-mail** `BrittaSimon@contoso.com`wpisz **adres e-mail** użytkownika .  
   
    b. W polach tekstowych **Hasło** i **Potwierdź hasło** wpisz **hasło** BrittaSimon. 
   
    d. W polach tekstowych **Imię** wpisz **imię** użytkownika **Britta**. 
   
    d. W polach tekstowych **Nazwisko** wpisz **nazwisko** użytkownika **Simon**.
   
    e. W polach tekstowych **Firma** wpisz **nazwę firmy** użytkownika. 
   
    f. W **polach** tekstowych Kraj wybierz **kraj/region** użytkownika.
  
    g. W skrzynce **tekstowej wpisz** **kod pocztowy** miasta.
   
    h. W polach tekstowych **Miasto** wpisz **nazwę miasta** użytkownika.

    i. Wybierz **język**.
   
    j. Kliknij przycisk **Utwórz**.

>[!NOTE]
>Do aprowizowania kont użytkowników usługi Azure AD można użyć dowolnych innych narzędzi do tworzenia kont użytkowników picturepark lub interfejsów API udostępnianych przez program Picturepark.
>

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Picturepark w Panelu dostępu należy automatycznie zalogować się do pictureparku, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

