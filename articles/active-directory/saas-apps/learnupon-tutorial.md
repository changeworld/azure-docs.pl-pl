---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą LearnUpon | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a8b894a5b790321c0af8527bf77e07b9923d577
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686760"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą LearnUpon

W tym samouczku dowiesz się, jak zintegrować LearnUpon w usłudze Azure Active Directory (Azure AD).
Integrowanie LearnUpon z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do LearnUpon.
* Aby umożliwić użytkownikom można automatycznie zalogowany do LearnUpon (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą LearnUpon, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* LearnUpon logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.


* Obsługuje LearnUpon **tożsamości** jednokrotne logowanie inicjowane przez

* Obsługuje LearnUpon **Just In Time** aprowizacji użytkowników


## <a name="adding-learnupon-from-the-gallery"></a>Dodawanie LearnUpon z galerii

Aby skonfigurować integrację LearnUpon w usłudze Azure AD, należy dodać LearnUpon z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać LearnUpon z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **LearnUpon**, wybierz opcję **LearnUpon** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![LearnUpon na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą LearnUpon w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w LearnUpon musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą LearnUpon, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie LearnUpon logowania jednokrotnego](#configure-learnupon-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego LearnUpon](#create-learnupon-test-user)**  — aby odpowiednikiem Britta Simon w LearnUpon połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z LearnUpon, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **LearnUpon** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![LearnUpon domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-reply.png)

    W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta LearnUpon](https://www.learnupon.com/features/support/) można uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony, odszukaj **odcisk PALCA** — zostanie ona dodana do LearnUpon ustawień protokołu SAML.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

6. Na **Konfigurowanie LearnUpon** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-learnupon-single-sign-on"></a>Konfigurowanie LearnUpon logowanie jednokrotne

1. Otwórz inne wystąpienie przeglądarki i zaloguj się do LearnUpon przy użyciu konta administratora.

1. Kliknij przycisk **ustawienia** kartę.

    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Kliknij przycisk **Rejestracja jednokrotna — SAML**, a następnie kliknij przycisk **ustawienia ogólne** do konfigurowania ustawień protokołu SAML.
   
    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. W **ustawienia ogólne** sekcji, wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Wybierz **włączone**.

    b. Wybierz **wersji** jako **2.0**.

    c. Wybierz **pominąć warunki** jako **nie**.

    d. W **wpis tokenu SAML nazwa param** polu tekstowym wpisz nazwę parametru post żądania na adres URL klienta SAML wskazany powyżej, zawierający potwierdzenie SAML, która ma zostać zweryfikowane i uwierzytelniony — na przykład **SAMLResponse** .

    e. W **Format identyfikatora nazwy** pole tekstowe, wpisz wartość, która wskazuje, gdzie w swojej potwierdzenie SAML identyfikator użytkowników (adres E-mail) znajduje się — na przykład `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.
  
    f. W **identyfikowania lokalizacji dostawcy** polu tekstowym wpisz wartość, która wskazuje, gdzie użytkownicy są wysyłane do, jeśli kliknij ikona przekazanych z ekranu logowania do portalu Azure.
  
    g. W **adres URL wylogowania** pola tekstowego, Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal.

    h. Kliknij przycisk **Zarządzanie drukuje finger**, a następnie przekaż odcisk palca certyfikatu pobrany.

1. Kliknij przycisk **ustawienia użytkownika**, a następnie wykonaj następujące czynności:

     ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. W **Format identyfikatora nazwy pierwszej** pole tekstowe, wpisz wartość, która informuje NAS, gdzie znajdują się w Twojej potwierdzenie SAML firstname użytkowników znajduje się — na przykład: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
  
    b. W **ostatniego Format identyfikatora nazwy** pole tekstowe, wpisz wartość, która informuje NAS, gdzie znajdują się w Twojej potwierdzenie SAML lastname użytkowników znajduje się — na przykład: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do LearnUpon.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **LearnUpon**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **LearnUpon**.

    ![Link LearnUpon na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-learnupon-test-user"></a>Tworzenie użytkownika testowego LearnUpon

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w LearnUpon. LearnUpon obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w LearnUpon, nowy katalog jest tworzony po uwierzytelnieniu. Jeśli musisz utworzyć ręcznie przez użytkownika, musisz skontaktować się z [zespołem pomocy technicznej LearnUpon](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka LearnUpon w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze LearnUpon, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)