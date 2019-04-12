---
title: 'Samouczek: Integracja usługi Azure Active Directory z Kampusu nieskończonej | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Campus nieskończone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 75cc92e420b08307377ab85a2d8d121303429ce5
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500043"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Samouczek: Integracja usługi Azure Active Directory z Kampusu nieskończona

W tym samouczku dowiesz się, jak zintegrować nieskończonej Campus za pomocą usługi Azure Active Directory (Azure AD).
Integrowanie nieskończonej Campus z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Campus nieskończone.
* Użytkownikom można automatycznie zalogowany nieskończonej Campus (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z Kampusu nieskończoną, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Nieskończona Campus logowanie jednokrotne włączone subskrypcji
* Co najmniej musisz być administratorem usługi Azure Active Directory i mieć rolę zabezpieczeń produktu Campus z "Student informacji systemu (SIS)" Aby ukończyć konfigurację.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje nieskończonej Campus **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-infinite-campus-from-the-gallery"></a>Dodawanie nieskończonej Campus z galerii

Aby skonfigurować integrację z Kampusu nieskończonej w usłudze Azure AD, należy dodać nieskończonej Campus z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać nieskończonej Campus z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** znajdujący się u góry okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Campus nieskończonej**, wybierz opcję **Campus nieskończonej** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Nieskończona Campus na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Campus nieskończoną, w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w nieskończonej Campus musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z Kampusu nieskończoną, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie nieskończonej Campus logowania jednokrotnego](#configure-infinite-campus-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego nieskończonej Campus](#create-infinite-campus-test-user)**  — aby odpowiednikiem Britta Simon w nieskończonej Campus, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne z Kampusu nieskończoną, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Campus nieskończonej** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji konfiguracji podstawowej SAML, wykonaj następujące czynności (należy pamiętać, że domeny będą się różnić w przypadku modelu hostingu z ale **w pełni KWALIFIKOWANA — domeny** wartość musi być zgodna instalacji nieskończonej Campus):

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Nieskończona Campus domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/sp-identifier-reply.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Konfigurowanie nieskończonej Campus logowania jednokrotnego

1. W oknie przeglądarki internetowej innej Zaloguj się w nieskończonej Campus jako Administrator zabezpieczeń.

2. W lewej części menu, kliknij polecenie **administrowania systemem**.

    ![Administrator](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Przejdź do **zabezpieczeń użytkownika** > **zarządzania SAML** > **Konfiguracja dostawcy usługi logowania jednokrotnego**.

    ![Saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Na **Konfiguracja dostawcy usługi logowania jednokrotnego** strony, wykonaj następujące czynności:

    ![Usługa rejestracji jednokrotnej](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Wybierz **Włącz SAML logowania jednokrotnego**.

    b. Edytuj **opcjonalna nazwa atrybutu** zawierać **nazwy**

    c. Na **opcję Pobierz dane serwera dostawcy tożsamości (IDP)** wybierz **adres URL metadanych**, Wklej **adres Url metadanych Federacji aplikacji** wartości, którą masz skopiowane z portalu Azure, w polu, a następnie kliknij przycisk **synchronizacji**.

    d. Po kliknięciu przycisku **synchronizacji** wartości pobrać, automatycznie wypełnione w **Konfiguracja dostawcy usługi logowania jednokrotnego** strony. Te wartości można sprawdzić do dopasowania wartości widoczne w kroku 4 powyżej.

    e. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład BrittaSimon@contoso.com.

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

> [!NOTE]
> Jeśli chcesz wyświetlić wszystkie platformy Azure użytkownicy będą mieć rejestracji jednokrotnej dostęp do nieskończonej Campus i polegać na systemie uprawnienia wewnętrzny Infinite Campus kontrolować dostęp do, można ustawić **wymagane przypisanie użytkownika** właściwości aplikacji na nie i pominęli następujące kroki.

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Campus nieskończoną.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Campus nieskończonej**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Campus nieskończonej**.

    ![Link nieskończonej Campus na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-infinite-campus-test-user"></a>Tworzenie użytkownika testowego Campus nieskończona

Nieskończona Campus ma architekturę dane demograficzne, a ich tematyka. Skontaktuj się z pomocą [zespołem pomocy technicznej nieskończonej Campus](mailto:sales@infinitecampus.com) Aby dodać użytkowników na platformie Campus nieskończoną.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Campus nieskończonej w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Campus nieskończoną, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
