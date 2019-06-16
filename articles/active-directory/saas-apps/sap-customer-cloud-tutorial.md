---
title: 'Samouczek: Integracja usługi Azure Active Directory z platformą SAP Cloud for Customer | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 669dfaa40cfe1bc65618d8706910e19d72c233ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092050"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Samouczek: Integracja usługi Azure Active Directory z platformą SAP Cloud for Customer

Z tego samouczka dowiesz się, jak zintegrować platformę SAP Cloud for Customer z usługą Azure Active Directory (Azure AD).
Zintegrowanie platformy SAP Cloud for Customer z usługą Azure AD daje następujące korzyści:

* Możliwość kontrolowania dostępu do platformy SAP Cloud for Customer za pomocą usługi Azure AD.
* Umożliwianie użytkownikom automatycznego logowania się do platformy SAP Cloud for Customer (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z platformą SAP Cloud for Customer, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja platformy SAP Cloud for Customer z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Platforma SAP Cloud for Customer obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Dodawanie platformy SAP Cloud for Customer z galerii

Aby skonfigurować integrację platformy SAP Cloud for Customer z usługą Azure AD, należy dodać tę platformę z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać platformę SAP Cloud for Customer z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SAP Cloud for Customer**, wybierz pozycję **SAP Cloud for Customer** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Platforma SAP Cloud for Customer na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z platformą SAP Cloud for Customer, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem platformy SAP Cloud for Customer.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z platformą SAP Cloud for Customer, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego platformy SAP Cloud for Customer](#configure-sap-cloud-for-customer-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego platformy SAP Cloud for Customer](#create-sap-cloud-for-customer-test-user)** — aby udostępnić na platformie SAP Cloud for Customer odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD na platformie SAP Cloud for Customer, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **SAP Cloud for Customer** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego platformy SAP Cloud for Customer](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<server name>.crm.ondemand.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta platformy SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja SAP Cloud for Customer oczekuje asercji SAML w konkretnym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

6. W sekcji **Atrybuty użytkownika** okna dialogowego **Atrybuty i oświadczenia użytkownika** wykonaj następujące kroki:

    a. Kliknij **ikonę Edytuj**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Wybierz pozycję **Przekształcanie** dla pola **Źródło**.

    c. Z listy **Przekształcenie** wybierz pozycję **ExtractMailPrefix()** .

    d. Z listy **Parametr 1** wybierz atrybut użytkownika, którego chcesz użyć na potrzeby implementacji.
    Na przykład jeśli chcesz użyć identyfikatora EmployeeID jako unikatowego identyfikatora użytkownika, a wartość atrybutu jest przechowywana w elemencie ExtensionAttribute2, wybierz pozycję user.extensionattribute2.

    e. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

8. W sekcji **Konfigurowanie platformy SAP Cloud for Customer** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-sap-cloud-for-customer-single-sign-on"></a>Konfigurowanie logowania jednokrotnego na platformie SAP Cloud for Customer
   
1. Zaloguj się do portalu platformy SAP Cloud for Customer z uprawnieniami administratora.
   
2. Przejdź do karty **Application and User Management Common Task** (Typowe zadanie aplikacji i zarządzania użytkownikami), a następnie kliknij kartę **Identity Provider** (Dostawca tożsamości).
   
3. Kliknij pozycję **New Identity Provider** (Nowy dostawca tożsamości), a następnie wybierz plik XML z metadanymi pobrany z witryny Azure Portal. Przez zaimportowanie tych metadanych system automatycznie przekaże wymagany certyfikat podpisywania i certyfikat szyfrowania.
   
    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
4. Usługa Azure Active Directory wymaga elementu adresu URL usługi konsumenta asercji w żądaniu SAML, dlatego zaznacz pole wyboru **Include Assertion Consumer Service URL** (Dołącz adres URL usługi konsumenta asercji)
   
5. Kliknij przycisk **Activate Single Sign-On** (Aktywuj logowanie jednokrotne).
   
6. Zapisz zmiany.
   
7. Kliknij kartę **My System** (Mój system).
   
    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
8. W polu tekstowym **Azure AD Sign On URL** (Adres URL logowania usługi Azure AD) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
   
    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
9. Określ, czy pracownik może ręcznie dokonać wyboru między logowaniem się przy użyciu identyfikatora użytkownika i hasła a logowaniem jednokrotnym, zaznaczając pole wyboru **Manual Identity Provider Selection** (Ręczny wybór dostawcy tożsamości).
   
10. W sekcji **SSO URL** (Adres URL logowania jednokrotnego sekcji) określ adres URL, który powinien być używany przez pracowników w celu logowania się do systemu. 
    Na liście **URL Sent to Employee** (Adres URL wysyłany do pracownika) możesz wybrać jedną z następujących opcji:
   
    **Non-SSO URL** (Adres URL w przypadku braku obsługi logowania jednokrotnego)
   
    System wysyła do pracownika tylko normalny adres URL systemu. Pracownik nie może zalogować się za pomocą logowania jednokrotnego i zamiast tego musi użyć hasła lub certyfikatu.
   
    **SSO URL** (Adres URL logowania jednokrotnego) 
   
    System wysyła do pracownika tylko adres URL logowania jednokrotnego. Pracownik może zalogować się za pomocą logowania jednokrotnego. Żądanie uwierzytelnienia zostanie przekierowane za pośrednictwem dostawcy tożsamości.
   
    **Automatic Selection** (Wybór automatyczny)
   
    Jeśli logowanie jednokrotne nie jest aktywne, system wysyła do pracownika normalny adres URL systemu. Jeśli logowanie jednokrotne jest aktywne, system sprawdza, czy pracownik ma hasło. Jeśli hasło jest dostępne, do pracownika wysyłany jest zarówno adres URL logowania jednokrotnego, jak i adres URL w przypadku braku obsługi logowania jednokrotnego. Jeśli jednak pracownik nie ma hasła, do pracownika wysyłany jest tylko adres URL logowania jednokrotnego.
   
11. Zapisz zmiany.

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

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do platformy SAP Cloud for Customer.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw** i **Wszystkie aplikacje**, a następnie **SAP Cloud for Customer**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz ciąg **SAP Cloud for Customer** i wybierz odpowiednią pozycję.

    ![Link platformy SAP Cloud for Customer na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Tworzenie użytkownika testowego klienta platformy SAP Cloud for Customer

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na platformie SAP Cloud for Customer. Postępuj zgodnie ze wskazówkami udzielanymi przez  [zespół pomocy technicznej platformy SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html), aby dodać użytkowników na platformie SAP Cloud for Customer. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

> [!NOTE]
> Upewnij się, że wartość NameID jest zgodna z polem nazwy użytkownika na platformie SAP Cloud for Customer.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP Cloud for Customer na panelu dostępu powinno nastąpić automatyczne zalogowanie do platformy SAP Cloud for Customer, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

