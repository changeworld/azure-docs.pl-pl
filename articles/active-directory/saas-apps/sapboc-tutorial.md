---
title: 'Samouczek: integracja usługi Azure Active Directory z rozwiązaniem SAP Business Object Cloud | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a rozwiązaniem SAP Business Object Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c64fb2dc4c494252f0078b8d23e05b15f07ebe00
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65903747"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Samouczek: Integracja usługi Azure Active Directory z rozwiązaniem SAP Business Object Cloud

Z tego samouczka dowiesz się, jak zintegrować rozwiązanie SAP Business Object Cloud z usługą Azure Active Directory (Azure AD).
Zintegrowanie rozwiązania SAP Business Object Cloud z usługą Azure AD zapewnia następujące korzyści:

* Możliwość kontrolowania dostępu do rozwiązania SAP Business Object Cloud za pomocą usługi Azure AD.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do rozwiązania SAP Business Object Cloud (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z rozwiązaniem SAP Business Object Cloud potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja rozwiązania SAP Business Object Cloud z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie SAP Business Object Cloud obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Dodawanie rozwiązania SAP Business Object Cloud z galerii

Aby skonfigurować integrację rozwiązania SAP Business Object Cloud z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

**Aby dodać rozwiązanie SAP Business Object Cloud z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SAP Business Object Cloud**, wybierz pozycję **SAP Business Object Cloud** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Pozycja SAP Business Object Cloud na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z rozwiązaniem SAP Business Object Cloud, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem rozwiązania SAP Business Object Cloud.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z rozwiązaniem SAP Business Object Cloud, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w rozwiązaniu SAP Business Object Cloud](#configure-sap-business-object-cloud-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego rozwiązania SAP Business Object Cloud](#create-sap-business-object-cloud-test-user)** — aby w rozwiązaniu SAP Business Object Cloud istniał odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z rozwiązaniem SAP Business Object Cloud, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **SAP Business Object Cloud** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL rozwiązania SAP Business Object Cloud na potrzeby logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca:
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Wartości tych adresów URL są tylko na potrzeby demonstracyjne. Zaktualizuj wartości rzeczywistym adresem URL logowania i adresem URL identyfikatora. Aby uzyskać adres URL logowania, skontaktuj się z [zespołem pomocy technicznej klienta rozwiązania SAP Business Object Cloud](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Adres URL identyfikatora można uzyskać, pobierając metadane rozwiązania SAP Business Object Cloud z poziomu konsoli administracyjnej. Jest to wyjaśnione w dalszej części tego samouczka.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w rozwiązaniu SAP Business Object Cloud

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmowej rozwiązania SAP Business Object Cloud jako administrator.

2. Wybierz pozycję **Menu** (Menu) > **System** (System) > **Administration** (Administracja).
    
    ![Wybierz pozycję Menu (Menu), wybierz pozycję System (System), a następnie wybierz pozycję Administration (Administracja)](./media/sapboc-tutorial/config1.png)

3. Na karcie **Security** (Zabezpieczenia) wybierz ikonę **edycji** (ikona ołówka).
    
    ![Na karcie Security (Zabezpieczenia) wybierz ikonę edycji (ikona ołówka)](./media/sapboc-tutorial/config2.png)  

4. W obszarze **Authentication Method** (Metoda uwierzytelniania) wybierz pozycję **SAML Single Sign-On (SSO)** (Logowanie jednokrotne protokołu SAML).

    ![Wybierz pozycję SAML Single Sign-On (SSO) (Logowanie jednokrotne protokołu SAML) jako metodę uwierzytelniania](./media/sapboc-tutorial/config3.png)  

5. Aby pobrać metadane dostawcy usług (Krok 1.), wybierz pozycję **Download** (Pobierz). W pliku metadanych znajdź i skopiuj wartość elementu **entityID**. W witrynie Azure Portal w oknie dialogowym **Podstawowa konfiguracja protokołu SAML** wklej wartość w polu **Identyfikator**.

    ![Skopiuj i wklej wartość elementu entityID](./media/sapboc-tutorial/config4.png)  

6. Aby przekazać metadane dostawcy usług (Krok 2.) do pliku, który został pobrany z witryny Azure Portal, w obszarze **Upload Identity Provider metadata** (Przekazywanie metadanych dostawcy tożsamości) wybierz pozycję **Upload** (Przekaż).  

    ![W obszarze Upload Identity Provider metadata (Przekazywanie metadanych dostawcy tożsamości) wybierz pozycję Upload (Przekaż)](./media/sapboc-tutorial/config5.png)

7. Z listy **User Attribute** (Atrybut użytkownika) wybierz atrybut użytkownika (Krok 3.), którego chcesz użyć na potrzeby implementacji. Ten atrybut użytkownika jest mapowany na dostawcę tożsamości. Aby wprowadzić atrybut niestandardowy na stronie użytkownika, użyj opcji **Custom SAML Mapping** (Niestandardowe mapowanie protokołu SAML). Możesz również wybrać wartość **Email** (Adres e-mail) lub **USER ID** (Identyfikator użytkownika) jako atrybut użytkownika. W tym przykładzie wybraliśmy wartość **Email** (Adres e-mail), ponieważ oświadczenie identyfikatora użytkownika zamapowano z atrybutem **userprincipalname** w sekcji **Atrybuty i oświadczenia użytkownika** w witrynie Azure Portal. Dzięki temu uzyskaliśmy unikatowy adres e-mail użytkownika, który jest wysyłany do aplikacji SAP Business Object Cloud w każdej pomyślnej odpowiedzi protokołu SAML.

    ![Wybierz atrybut użytkownika](./media/sapboc-tutorial/config6.png)

8. Aby zweryfikować konto za pomocą dostawcy tożsamości (Krok 4.) w polu **Login Credential (Email)** (Poświadczenia danych logowania — adres e-mail) wprowadź adres e-mail użytkownika. Następnie wybierz pozycję **Verify Account** (Sprawdź konto). System dodaje poświadczenia logowania do konta użytkownika.

    ![Wprowadź adres e-mail, a następnie wybierz pozycję Verify Account (Sprawdź konto)](./media/sapboc-tutorial/config7.png)

9. Wybierz ikonę **zapisywania**.

    ![Ikona zapisywania](./media/sapboc-tutorial/save.png)

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do rozwiązania SAP Business Object Cloud.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **SAP Business Object Cloud**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **SAP Business Object Cloud**.

    ![Link do rozwiązania SAP Business Object Cloud na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sap-business-object-cloud-test-user"></a>Tworzenie użytkownika testowego rozwiązania SAP Business Object Cloud

Aby użytkownicy usługi Azure AD mogli logować się do rozwiązania SAP Business Object Cloud, muszą oni zostać zaaprowizowani w tym rozwiązaniu. W rozwiązaniu SAP Business Object Cloud aprowizacja to zadanie ręczne.

Aby aprowizować konto użytkownika:

1. Zaloguj się do witryny firmowej rozwiązania SAP Business Object Cloud jako administrator.

2. Wybierz pozycję **Menu** (Menu) > **Security** (Zabezpieczenia) > **Users** (Użytkownicy).

    ![Dodawanie pracownika](./media/sapboc-tutorial/user1.png)

3. Na stronie **Users** (Użytkownicy) wybierz pozycję **+**, aby dodać szczegółowe informacje dotyczące nowego użytkownika. 

    ![Strona dodawania użytkowników](./media/sapboc-tutorial/user4.png)

    Następnie wykonaj następujące kroki:

    a. W polu **USER ID** (Identyfikator użytkownika) wprowadź identyfikator użytkownika, np. **Britta**.

    b. W polu **FIRST NAME** (Imię) wprowadź imię użytkownika, np. **Britta**.

    c. W polu **LAST NAME** (Nazwisko) wprowadź nazwisko użytkownika, np. **Simon**.

    d. W polu **DISPLAY NAME** (Nazwa wyświetlana) wprowadź imię i nazwisko użytkownika, np. **Britta Simon**.

    e. W **E-MAIL** wprowadź adres e-mail użytkownika, takie jak **brittasimon\@contoso.com**.

    f. Na stronie **Select Roles** (Wybieranie ról) wybierz odpowiednią rolę użytkownika, a następnie wybierz pozycję **OK**.

      ![Wybieranie roli](./media/sapboc-tutorial/user3.png)

    g. Wybierz ikonę **zapisywania**.    

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP Business Object Cloud w panelu dostępu powinno nastąpić automatyczne zalogowanie do rozwiązania SAP Business Object Cloud, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

