---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją AirWatch | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e20a298c34a7b2723963396f8ccaafaa9472e19a
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65899201"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Samouczek: integracja usługi Azure Active Directory z aplikacją AirWatch

Z tego samouczka dowiesz się, jak zintegrować aplikację AirWatch z usługą Azure Active Directory (Azure AD).
Integracja aplikacji AirWatch z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować za pomocą usługi Azure AD, kto ma dostęp do aplikacji AirWatch.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji AirWatch (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją AirWatch, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji AirWatch z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja AirWatch obsługuje logowanie jednokrotne inicjowane przez **SP**

## <a name="adding-airwatch-from-the-gallery"></a>Dodawanie aplikacji AirWatch z galerii

Aby skonfigurować integrację aplikacji AirWatch z usługą Azure AD, musisz dodać aplikację AirWatch z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację AirWatch z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **AirWatch**, wybierz pozycję **AirWatch** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja AirWatch na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją AirWatch, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji AirWatch.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją AirWatch, należy ukończyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji AirWatch](#configure-airwatch-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Tworzenie użytkownika testowego aplikacji AirWatch](#create-airwatch-test-user)** — aby mieć w aplikacji AirWatch odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
5. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji AirWatch, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **AirWatch** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji AirWatch](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz wartość: `AirWatch`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać tę wartość, skontaktuj się z [zespołem obsługi klienta firmy AirWatch](https://www.air-watch.com/company/contact-us/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja AirWatch oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności:

    | Name (Nazwa) |  Atrybut źródłowy|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

8. W sekcji **Skonfiguruj aplikację AirWatch** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-airwatch-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji AirWatch

1. W innym oknie przeglądarki zaloguj się jako administrator do witryny firmowej aplikacji AirWatch.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Accounts** (Konta), a następnie kliknij pozycję **Administrators** (Administratorzy).

   ![Administratorzy](./media/airwatch-tutorial/ic791920.png "Administratorzy")

3. Rozwiń menu **Settings** (Ustawienia), a następnie kliknij pozycję **Directory Services** (Usługi katalogowe).

   ![Ustawienia](./media/airwatch-tutorial/ic791921.png "Ustawienia")

4. Kliknij kartę **User** (Użytkownik), w polu tekstowym **Base DN** (Bazowa nazwa domeny) wpisz nazwę domeny, a następnie kliknij pozycję **Save** (Zapisz).

   ![Użytkownik](./media/airwatch-tutorial/ic791922.png "Użytkownik")

5. Kliknij kartę **Server** (Serwer).

   ![Serwer](./media/airwatch-tutorial/ic791923.png "Serwer")

6. Wykonaj poniższe czynności:

    ![Przekazywanie](./media/airwatch-tutorial/ic791924.png "Przekazywanie")   

    a. W polu **Directory Type** (Typ katalogu) wybierz pozycję **None** (Brak).

    b. Wybierz pozycję **Use SAML For Authentication** (Użyj protokołu SAML na potrzeby uwierzytelniania).

    c. Aby przekazać pobrany certyfikat, kliknij pozycję **Upload** (Przekaż).

7. W sekcji **Request** (Żądanie) wykonaj następujące czynności:

    ![Żądanie](./media/airwatch-tutorial/ic791925.png "Żądanie")  

    a. W polu **Request Binding Type** (Typ powiązania żądania) wybierz opcję **POST**.

    b. W witrynie Azure Portal na stronie **Konfigurowanie logowania jednokrotnego w aplikacji Airwatch** skopiuj wartość **Adres URL logowania**, a następnie wklej ją w polu tekstowym **Identity Provider Single Sign On URL** (Adres URL logowania jednokrotnego dostawcy tożsamości).

    c. W polu **NameID Format** (Format identyfikatora nazwy) wybierz opcję **Email Address** (Adres e-mail).

    d. Kliknij pozycję **Zapisz**.

8. Ponownie kliknij kartę **User** (Użytkownik).

    ![Użytkownik](./media/airwatch-tutorial/ic791926.png "Użytkownik")

9. W sekcji **Attribute** (Atrybut) wykonaj następujące czynności:

    ![Atrybut](./media/airwatch-tutorial/ic791927.png "Atrybut")

    a. W polu tekstowym **Object Identifier** (Identyfikator obiektu) wpisz `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. W polu tekstowym **Username** (Nazwa użytkownika) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. W polu tekstowym **Display Name** (Nazwa wyświetlana) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. W polu tekstowym **First Name** (Imię) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. W polu tekstowym **Last Name** (Nazwisko) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. W polu tekstowym **Email** (Adres e-mail) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Kliknij pozycję **Zapisz**.

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

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji AirWatch.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **AirWatch**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **AirWatch**.

    ![Link aplikacji AirWatch na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-airwatch-test-user"></a>Tworzenie użytkownika testowego aplikacji AirWatch

Aby umożliwić użytkownikom usługi Azure AD logowanie się w aplikacji AirWatch, należy ich aprowizować w aplikacji AirWatch. W przypadku aplikacji AirWatch aprowizowanie jest zadaniem ręcznym.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się w witrynie firmowej **AirWatch** jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Accounts** (Konta), a następnie kliknij pozycję **Users** (Użytkownicy).
  
   ![Użytkownicy](./media/airwatch-tutorial/ic791929.png "Użytkownicy")

3. W menu **Users** (Użytkownicy) kliknij pozycję **List View** (Widok listy), a następnie kliknij pozycję **Add (Dodaj) \> Add User (Dodaj użytkownika)**.
  
   ![Dodawanie użytkownika](./media/airwatch-tutorial/ic791930.png "Dodawanie użytkownika")

4. W oknie dialogowym **Add / Edit User** (Dodawanie/edytowanie użytkownika) wykonaj następujące czynności:

   ![Dodawanie użytkownika](./media/airwatch-tutorial/ic791931.png "Dodawanie użytkownika")

   a. W polach tekstowych **Username** (Nazwa użytkownika), **Password** (Hasło), **Confirm Password** (Potwierdź hasło), **First Name** (Imię), **Last Name** (Nazwisko), **Email Address** (Adres e-mail) wpisz wartości prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.

   b. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Aby aprowizować konta użytkowników usługi AAD, można użyć jakichkolwiek innych narzędzi tworzenia konta użytkownika lub interfejsów API dostarczonych przez firmę AirWatch.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka AirWatch w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji AirWatch, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
