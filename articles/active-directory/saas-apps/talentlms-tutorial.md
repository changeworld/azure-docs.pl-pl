---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą TalentLMS | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 970ab9de270b1227884a13ac578d4c439043b20c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233360"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Samouczek: Integracja usługi Azure Active Directory z usługą TalentLMS

W tym samouczku dowiesz się, jak zintegrować usługę TalentLMS z usługą Azure Active Directory (Azure AD).
Integracja usługi TalentLMS z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do usługi TalentLMS.
* Można włączyć użytkowników do automatycznego logowania się do talentlms (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą TalentLMS, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego talentlms

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* TalentLMS obsługuje sso zainicjowane przez **SP**

## <a name="adding-talentlms-from-the-gallery"></a>Dodawanie talentów MLMS z galerii

Aby skonfigurować integrację usługi TalentLMS z usługą Azure AD, należy dodać usługę TalentLMS z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać program TalentLMS z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **TalentLMS**, wybierz **TalentLMS** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![TalentLMS na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowanie jednokrotne za pomocą talentlms na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie TalentLMS.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi TalentLMS, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne programu TalentLMS](#configure-talentlms-single-sign-on)** — umożliwia skonfigurowanie ustawień logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego TalentLMS](#create-talentlms-test-user)** — aby mieć odpowiednik Britta Simon w talentlms, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą usługi TalentLMS, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **TalentLMS** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o logach jednokrotnych domen i adresów URL talentlms](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.TalentLMSapp.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta TalentLMS,](https://www.talentlms.com/contact) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **ODCISK PALCA** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Konfigurowanie talentów MLMS** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-talentlms-single-sign-on"></a>Konfigurowanie logowania jednokrotnego programu TalentLMS

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy TalentLMS jako administrator.

1. W sekcji **Ustawienia & konta** kliknij kartę **Użytkownicy.**

    ![Ustawienia & konta](./media/talentlms-tutorial/IC777296.png "Ustawienia & konta")

1. Kliknij **opcję Logowanie jednokrotne ,**

1. W sekcji Single sign-on (Logowanie jednokrotne) wykonaj następujące kroki:

    ![Logowanie jednokrotne](./media/talentlms-tutorial/IC777297.png "Logowanie jednokrotne")

    a. Z listy **typów integracji sypka** wybierz **SAML 2.0**.

    b. W polu tekstowym **dostawcy tożsamości (IDP)** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.

    d. Wklej wartość **odcisk palca** z witryny Azure portal do pola **tekstowego odcisk palca certyfikatu.**

    d.  W polu **tekstowym adres URL zdalnego logowania wklej** wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    e. W polu **tekstowym adresu URL zdalnego wylogowania** wklej wartość **adresu URL wylogowania,** który został skopiowany z witryny Azure portal.

    f. Wypełnij następujące informacje:

    * W polach **tekstowych TargetedID** wpisz`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * W polach tekstowych **Imię** wpisz`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * W polach tekstowych **Nazwisko** wpisz`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * W polach **tekstowych Wiadomości e-mail** wpisz`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Kliknij przycisk **Zapisz**.

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

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do talentów TALENTMS.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **TalentLMS**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **TalentLMS**.

    ![Łącze TalentLMS na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-talentlms-test-user"></a>Utwórz użytkownika testowego TalentLMS

Aby umożliwić użytkownikom usługi Azure AD logowanie się do usługi TalentLMS, muszą one być aprowizowana do usługi TalentLMS. W przypadku talentów MLMS inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **talentów MLMS.**

1. Kliknij **pozycję Użytkownicy**, a następnie kliknij pozycję **Dodaj użytkownika**.

1. Na stronie okna dialogowego **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Dodaj użytkownika](./media/talentlms-tutorial/IC777299.png "Dodaj użytkownika")  

    a. W polu tekstowym **Imię** wprowadź imię użytkownika, takiego jak **Britta**.

    b. W polu tekstowym **Last name (Nazwisko)** wprowadź nazwisko użytkownika, na przykład **Simon**.
 
    d. W polu tekstowym **Email address** (Adres e-mail) wprowadź adres e-mail użytkownika, np. `brittasimon\@contoso.com`.

    d. Kliknij **pozycję Dodaj użytkownika**.

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure AD można użyć innych narzędzi do tworzenia kont użytkowników programu TalentLMS lub interfejsów API udostępnianych przez program TalentLMS.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka TalentLMS w Panelu dostępu należy automatycznie zalogować się do programu TalentLMS, dla którego skonfigurowano logującą się logującą logującą logującą log. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

