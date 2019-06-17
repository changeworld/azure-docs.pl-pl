---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Work.com | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7a6dc16eef1bb36a5bd6cbf0502a83481230bc0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087094"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Work.com

W tym samouczku dowiesz się, jak zintegrować Work.com w usłudze Azure Active Directory (Azure AD).
Integrowanie Work.com z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Work.com.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Work.com (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Work.com, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Work.com logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Work.com **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-workcom-from-the-gallery"></a>Dodawanie Work.com z galerii

Aby skonfigurować integrację Work.com w usłudze Azure AD, należy dodać Work.com z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Work.com z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Work.com**, wybierz opcję **Work.com** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Work.com na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Work.com w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Work.com musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Work.com, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Work.com logowania jednokrotnego](#configure-workcom-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Work.com](#create-workcom-test-user)**  — aby odpowiednikiem Britta Simon w Work.com połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

>[!NOTE]
>Aby skonfigurować logowanie jednokrotne, musisz skonfigurować niestandardową nazwę domeny Work.com jeszcze. Należy zdefiniować co najmniej nazwę domeny, test nazwa domeny i wdróż je w całej organizacji.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Work.com, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Work.com** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Work.com domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Work.com](https://help.salesforce.com/articleView?id=000159855&type=3) można uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie Work.com** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-workcom-single-sign-on"></a>Konfigurowanie Work.com logowanie jednokrotne

1. Zaloguj się do dzierżawy Work.com jako administrator.

2. Przejdź do **Instalatora**.
   
    ![Konfigurowanie](./media/work-com-tutorial/ic794108.png "Konfigurowanie")

3. W okienku nawigacji po lewej stronie w **administrowanie** kliknij **Zarządzanie domenami** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moja domena** otworzyć **Moja domena** strony. 
   
    ![My Domain](./media/work-com-tutorial/ic767825.png "My Domain")

4. Aby sprawdzić, czy domenę zostało skonfigurowane prawidłowo, upewnij się, że jest on "**kroku 4 wdrożone dla użytkowników**" i zapoznaj się z "**Moje ustawienia domeny**".
   
    ![Nie wdrożono użytkownika domeny](./media/work-com-tutorial/ic784377.png "domeny wdrożenia użytkownika")

5. Zaloguj się do dzierżawy Work.com.

6. Przejdź do **Instalatora**.
    
    ![Konfigurowanie](./media/work-com-tutorial/ic794108.png "Konfigurowanie")

7. Rozwiń **środki kontroli bezpieczeństwa** menu, a następnie kliknij przycisk **ustawienia rejestracji jednokrotnej**.
    
    ![Ustawienia logowania jednokrotnego](./media/work-com-tutorial/ic794113.png "Ustawienia logowania jednokrotnego")

8. Na **ustawienia rejestracji jednokrotnej** okna dialogowego strony, wykonaj następujące czynności:
    
    ![SAML włączone](./media/work-com-tutorial/ic781026.png "SAML włączone")
    
    a. Wybierz pozycję **SAML Enabled** (SAML włączone).
    
    b. Kliknij przycisk **Nowy**.

9. W **SAML pojedynczego ustawień logowania jednokrotnego** sekcji, wykonaj następujące czynności:
    
    ![SAML pojedynczego logowania jednokrotnego ustawienia](./media/work-com-tutorial/ic794114.png "SAML pojedynczego logowania jednokrotnego ustawienia")
    
    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę konfiguracji.  
       
    > [!NOTE]
    > Podanie wartości dla **nazwa** automatycznie wypełnić **Nazwa interfejsu API** pola tekstowego.
    
    b. W **wystawcy** pola tekstowego, Wklej wartość **usługi Azure AD identyfikator** skopiowanej w witrynie Azure portal.
    
    c. Aby przekazać certyfikat pobrany z witryny Azure portal, kliknij przycisk **Przeglądaj**.
    
    d. W **identyfikator jednostki** polu tekstowym wpisz `https://salesforce-work.com`.
    
    e. Jako **typu tożsamości SAML**, wybierz opcję **potwierdzenie zawiera identyfikator federacji z obiektu użytkownika**.
    
    f. Jako **lokalizacji tożsamości SAML**, wybierz opcję **tożsamość jest w elemencie NameIdentfier instrukcji podmiotu**.
    
    g. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.

    h. W **adres URL wylogowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.
    
    i. Jako **dostawcy inicjowane żądania powiązania usługi**, wybierz opcję **żądania HTTP Post**.
    
    j. Kliknij pozycję **Zapisz**.

10. W portalu klasycznym Work.com, w okienku nawigacji po lewej stronie kliknij pozycję **Zarządzanie domenami** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moja domena** otworzyć **Moja domena** Strona. 
    
    ![My Domain](./media/work-com-tutorial/ic794115.png "My Domain")

11. Na **Moja domena** stronie **znakowanie strony logowania** kliknij **Edytuj**.
    
    ![Strona logowania znakowania](./media/work-com-tutorial/ic767826.png "strony logowania, znakowania")

12. Na **znakowanie strony logowania** strony w **usługi uwierzytelniania** sekcji Nazwa Twojej **ustawień logowania jednokrotnego SAML** jest wyświetlana. Zaznacz go, a następnie kliknij przycisk **Zapisz**.
    
    ![Strona logowania znakowania](./media/work-com-tutorial/ic784366.png "strony logowania, znakowania")

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Work.com.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Work.com**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Work.com**.

    ![Link Work.com na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-workcom-test-user"></a>Tworzenie użytkownika testowego Work.com

Dla użytkowników usługi Azure Active Directory można było zarejestrować się w musi być obsługiwana na Work.com. W przypadku Work.com Inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:

1. Zaloguj się do witryny firmy Work.com jako administrator.

2. Przejdź do **Instalatora**.
   
    ![Konfigurowanie](./media/work-com-tutorial/IC794108.png "Konfigurowanie")

3. Przejdź do **Zarządzanie użytkownikami \> użytkowników**.
   
    ![Zarządzanie użytkownikami](./media/work-com-tutorial/IC784369.png "Zarządzanie użytkownikami")

4. Kliknij pozycję **New User** (Nowy użytkownik).
   
    ![Wszyscy użytkownicy](./media/work-com-tutorial/IC794117.png "wszystkich użytkowników")

5. W sekcji użytkownikowi edytować, wykonaj następujące czynności, w atrybutach prawidłowe Azure konto usługi AD do aprowizowania w powiązanych pól tekstowych:
   
    ![Edycja użytkownika](./media/work-com-tutorial/ic794118.png "Edycja użytkownika")
   
    a. W **imię** polu tekstowym wpisz **imię** użytkownika **Britta**.
    
    b. W **nazwisko** polu tekstowym wpisz **nazwisko** użytkownika **Simon**.
    
    c. W **Alias** polu tekstowym wpisz **nazwa** użytkownika **BrittaS**.
    
    d. W **E-mail** polu tekstowym wpisz **adres e-mail** użytkownika Brittasimon@contoso.com.
    
    e. W **nazwa_użytkownika** pole tekstowe, wpisz nazwę użytkownika, takie jak Brittasimon@contoso.com.
    
    f. W **pseudonim** polu tekstowym wpisz **pseudonim** użytkownika **Simon**.
    
    g. Wybierz **roli**, **licencji użytkownika**, i **profilu**.
    
    h. Kliknij pozycję **Zapisz**.  
      
    > [!NOTE]
    > Właściciel konta usługi Azure AD otrzyma wiadomość e-mail, w tym link do potwierdzenia konta, zanim stanie się aktywny.
    > 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Work.com w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Work.com, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

