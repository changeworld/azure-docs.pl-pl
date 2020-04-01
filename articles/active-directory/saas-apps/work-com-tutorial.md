---
title: 'Samouczek: Integracja usługi Azure Active Directory z Work.com | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a Work.com.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087094"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Samouczek: Integracja usługi Azure Active Directory z Work.com

W tym samouczku dowiesz się, jak zintegrować Work.com z usługą Azure Active Directory (Azure AD).
Integracja Work.com z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do Work.com.
* Można włączyć użytkowników, aby automatycznie zalogować się do Work.com (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Work.com, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Work.com subskrypcję z włączoną funkcją logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Work.com obsługuje sso inicjowane przez **SP**

## <a name="adding-workcom-from-the-gallery"></a>Dodawanie Work.com z galerii

Aby skonfigurować integrację Work.com z usługą Azure AD, należy dodać Work.com z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Work.com z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Work.com**, wybierz **Work.com** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Work.com na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Work.com na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Work.com.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą Work.com, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Work.com logowanie jednokrotne](#configure-workcom-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz Work.com użytkownika testowego](#create-workcom-test-user)** — aby mieć odpowiednik Britta Simon w Work.com, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

>[!NOTE]
>Aby skonfigurować logowanie jednokrotne, należy jeszcze skonfigurować niestandardową nazwę domeny Work.com. Należy zdefiniować co najmniej nazwę domeny, przetestować nazwę domeny i wdrożyć ją w całej organizacji.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą Work.com, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Work.com** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Work.com informacje o logowanie do domeny i adresów URL](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Work.com,](https://help.salesforce.com/articleView?id=000159855&type=3) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie Work.com** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-workcom-single-sign-on"></a>Konfigurowanie Work.com logowanie jednokrotne

1. Zaloguj się do dzierżawy Work.com jako administrator.

2. Przejdź do **strony Instalator**.
   
    ![Instalacja](./media/work-com-tutorial/ic794108.png "Konfiguracja")

3. W lewym okienku nawigacji w sekcji **Administrowanie** kliknij pozycję **Zarządzanie domenami,** aby rozwinąć powiązaną sekcję, a następnie kliknij pozycję **Moja domena,** aby otworzyć stronę **Moja domena.** 
   
    ![Moja domena](./media/work-com-tutorial/ic767825.png "Moja domena")

4. Aby sprawdzić, czy twoja domena została poprawnie skonfigurowana, upewnij się, że znajduje się w "**Kroku 4 Wdrożone dla użytkowników**" i przejrzyj swoje " Moje ustawienia**domeny**".
   
    ![Domena wdrożona dla użytkownika](./media/work-com-tutorial/ic784377.png "Domena wdrożona dla użytkownika")

5. Zaloguj się do dzierżawy Work.com.

6. Przejdź do **strony Instalator**.
    
    ![Instalacja](./media/work-com-tutorial/ic794108.png "Konfiguracja")

7. Rozwiń menu **Kontrole zabezpieczeń,** a następnie kliknij polecenie **Ustawienia logowania jednokrotnego**.
    
    ![Ustawienia logowania jednokrotnego](./media/work-com-tutorial/ic794113.png "Ustawienia logowania jednokrotnego")

8. Na stronie okna dialogowego **Ustawienia logowania jednokrotnego** wykonaj następujące czynności:
    
    ![Saml włączone](./media/work-com-tutorial/ic781026.png "Saml włączone")
    
    a. Wybierz pozycję **SAML Enabled** (SAML włączone).
    
    b. Kliknij **pozycję Nowy**.

9. W sekcji **Ustawienia logowania jednokrotnego SAML** wykonaj następujące czynności:
    
    ![Ustawienie logowania jednokrotnego SAML](./media/work-com-tutorial/ic794114.png "Ustawienie logowania jednokrotnego SAML")
    
    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę konfiguracji.  
       
    > [!NOTE]
    > Podanie wartości **name** automatycznie wypełnia pole tekstowe **Nazwa interfejsu API.**
    
    b. W polu tekstowym **wystawcy** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure portal.
    
    d. Aby przekazać pobrany certyfikat z witryny Azure portal, kliknij przycisk **Przeglądaj**.
    
    d. W polach tekstowych Identyfikator `https://salesforce-work.com` **jednostki** wpisz .
    
    e. Jako **typ tożsamości SAML**wybierz **asercja zawiera identyfikator federacji z obiektu User**.
    
    f. Jako **lokalizacja tożsamości SAML,** wybierz **tożsamość jest w NameIdentfier element instrukcji Podmiot**.
    
    g. W polu tekstowym **adresu URL logowania dostawcy tożsamości** wklej wartość adresu URL **logowania** skopiowanego z witryny Azure portal.

    h. W polu tekstowym **adresu URL wylogowania dostawcy tożsamości** wklej wartość adresu URL **wylogowania** skopiowanego z witryny Azure portal.
    
    i. Jako **powiązanie żądania inicjowanego dostawcy usług**wybierz pozycję HTTP **Post**.
    
    j. Kliknij przycisk **Zapisz**.

10. W klasycznym portalu Work.com po lewej stronie kliknij pozycję **Zarządzanie domenami,** aby rozwinąć powiązaną sekcję, a następnie kliknij pozycję **Moja domena,** aby otworzyć stronę **Moja domena.** 
    
    ![Moja domena](./media/work-com-tutorial/ic794115.png "Moja domena")

11. Na stronie **Moja domena** w sekcji **Znakowanie strony logowania** kliknij pozycję **Edytuj**.
    
    ![Znakowanie strony logowania](./media/work-com-tutorial/ic767826.png "Znakowanie strony logowania")

12. Na stronie **Znakowanie strony logowania** w sekcji **Usługa uwierzytelniania** wyświetlana jest nazwa ustawień logowania **SAML.** Zaznacz go, a następnie kliknij przycisk **Zapisz**.
    
    ![Znakowanie strony logowania](./media/work-com-tutorial/ic784366.png "Znakowanie strony logowania")

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

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do Work.com.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz **Work.com**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Work.com**.

    ![Łącze Work.com na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-workcom-test-user"></a>Utwórz Work.com użytkownika testowego

Aby użytkownicy usługi Azure Active Directory mogli się zalogować, muszą być aprowizowani w celu Work.com. W przypadku Work.com inicjowania obsługi administracyjnej jest zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:

1. Zaloguj się do witryny firmy Work.com jako administrator.

2. Przejdź do **strony Instalator**.
   
    ![Instalacja](./media/work-com-tutorial/IC794108.png "Konfiguracja")

3. Przejdź do **zarządzania użytkownikami \> **.
   
    ![Zarządzanie użytkownikami](./media/work-com-tutorial/IC784369.png "Zarządzanie użytkownikami")

4. Kliknij pozycję **New User** (Nowy użytkownik).
   
    ![Wszyscy użytkownicy](./media/work-com-tutorial/IC794117.png "Wszyscy użytkownicy")

5. W sekcji Edycja użytkownika wykonaj następujące kroki w atrybutach prawidłowego konta usługi Azure AD, które chcesz udostępnić powiązanym skrzynkom tekstowym:
   
    ![Edycja użytkownika](./media/work-com-tutorial/ic794118.png "Edycja użytkownika")
   
    a. W polach tekstowych **Imię** wpisz **imię** użytkownika **Britta**.
    
    b. W polach tekstowych **Nazwisko** wpisz **nazwisko** użytkownika **Simon**.
    
    d. W polach **tekstowych Alias** wpisz **nazwę** użytkownika **BrittaS**.
    
    d. W **polach tekstowych Poczta e-mail** wpisz **adres e-mail** użytkownika Brittasimon@contoso.com.
    
    e. W polu tekstowym **Nazwa użytkownika** wpisz nazwę Brittasimon@contoso.comużytkownika, taką jak .
    
    f. W polach tekstowych **Nazwa nicku** wpisz **nazwę nicku** użytkownika **Simon**.
    
    g. Wybierz **pozycję Rola,** **Licencja użytkownika**i **Profil**.
    
    h. Kliknij przycisk **Zapisz**.  
      
    > [!NOTE]
    > Posiadacz konta usługi Azure AD otrzyma wiadomość e-mail z łączem potwierdzającym konto, zanim stanie się ono aktywne.
    > 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Work.com w Panelu dostępu należy automatycznie zalogować się do Work.com dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

