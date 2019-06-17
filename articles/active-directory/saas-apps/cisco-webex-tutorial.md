---
title: 'Samouczek: Integracja usługi Azure Active Directory z spotkań Webex Cisco | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Cisco Webex spotkania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec8c2fc2c2dbbfd2c98184215f696aaf18a87193
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105548"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex-meetings"></a>Samouczek: Integracja usługi Azure Active Directory z spotkań Webex Cisco

W tym samouczku dowiesz się, jak zintegrować Cisco Webex spotkania z usługą Azure Active Directory (Azure AD).
Integrowanie Cisco Webex spotkania z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do spotkań Webex firmy Cisco.
* Użytkownikom można automatycznie zalogowany do spotkań Webex Cisco (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z Cisco Webex spotkań, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Spotkania Webex Cisco pojedynczego logowania jednokrotnego włączonych subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje spotkań Webex Cisco **SP** jednokrotne logowanie inicjowane przez

* Obsługuje spotkań Webex Cisco **Just In Time** aprowizacji użytkowników

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Dodawanie Cisco Webex spotkania z galerii

Aby skonfigurować integrację Cisco Webex spotkań w usłudze Azure AD, należy dodać Cisco Webex spotkania z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Cisco Webex spotkania z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **spotkań Webex Cisco**, wybierz opcję **spotkań Webex Cisco** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

     ![Cisco Webex spotkań na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Cisco Webex spotkań w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w spotkaniach Webex Cisco musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Cisco Webex spotkań, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Cisco Webex spotkań logowania jednokrotnego](#configure-cisco-webex-meetings-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego spotkań Webex Cisco](#create-cisco-webex-meetings-test-user)**  — aby odpowiednikiem Britta Simon w spotkaniach Webex Cisco, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Cisco Webex spotkań, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **spotkań Webex Cisco** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. W witrynie Azure Portal na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** przekaż pobrany plik **metadanych dostawcy usług** i skonfiguruj aplikację, wykonując następujące kroki:

    >[!Note]
    >Zostanie wyświetlony plik metadanych dostawcy usługi, który zostało wyjaśnione w dalszej części w **skonfigurować Cisco Webex spotkań logowania jednokrotnego** części samouczka. 

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym ukończeniu przekazywania pliku metadanych dostawcy usług wartości **Identyfikator** i **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** zostaną wypełnione automatycznie:

    ![Cisco Webex spotkań domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier-reply.png)

    W **adres URL logowania** pola tekstowego, Wklej wartość **adres URL odpowiedzi** pobiera automatycznie wypełniony przez przekazywanie pliku metadanych SP.

5. Aplikacja spotkań Webex Cisco oczekuje twierdzenia SAML, w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby dodać atrybuty.

    ![image](common/edit-attribute.png)

6. Usuwanie atrybutów domyślnych z **oświadczenia użytkownika** sekcji i spotkania Cisco Webex aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:
    
    | Name (Nazwa) | Atrybut źródłowy|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   email       | user.mail |
    |   Identyfikator UID    | user.mail |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](./media/cisco-webex-tutorial/tutorial-cisco-webex-addnewclaim.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

8. Na **Konfigurowanie spotkań Webex Cisco** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-cisco-webex-meetings-single-sign-on"></a>Konfigurowanie Cisco Webex spotkań logowania jednokrotnego

1. Przejdź do [Zarządzanie współpracy w chmurze firmy Cisco](https://www.webex.com/go/connectadmin) przy użyciu poświadczeń administracyjnych.

2. Przejdź do **ustawienia zabezpieczeń** i przejdź do **Konfiguracja logowania jednokrotnego w sieci Web federacyjnego**.
 
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

3. Na **Konfiguracja logowania jednokrotnego w sieci Web federacyjnego** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

    a. W polu tekstowym protokołu Federation wpisz nazwę użytkownika protokołu.

    b. Kliknij pozycję **Importowanie metadanych SAML** link, aby przekazać plik metadanych, który został pobrany z witryny Azure portal.

    c. Kliknij pozycję **wyeksportować** przycisk, aby pobrać plik metadanych dostawcy usługi i przekaż go w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    d. W **AuthContextClassRef** polu tekstowym wpisz `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` i jeśli chcesz włączyć uwierzytelnianie wieloskładnikowe przy użyciu usługi Azure AD wpisz dwie wartości, np. `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Wybierz **automatyczne tworzenie konta**.

    >[!NOTE]
    >Włączania **just-in-time** aprowizacji użytkownika, które muszą sprawdzać **automatycznego tworzenia konta**. Oprócz tego atrybuty tokenu języka SAML muszą być przekazywane w odpowiedzi SAML.

    f. Kliknij pozycję **Zapisz**. 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do spotkań Webex firmy Cisco.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **spotkań Webex Cisco**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **spotkań Webex Cisco**.

    ![Link Cisco Webex spotkań na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-cisco-webex-meetings-test-user"></a>Tworzenie użytkownika testowego Cisco Webex spotkania

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w spotkaniach Webex firmy Cisco. Obsługuje spotkań Webex Cisco **just-in-time** inicjowania obsługi, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w spotkaniach Webex Cisco, nowy jest tworzony podczas próby dostępu Cisco Webex spotkania.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Cisco Webex spotkań w panelu dostępu, możesz powinny być automatycznie zalogowany do spotkań Webex Cisco, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

