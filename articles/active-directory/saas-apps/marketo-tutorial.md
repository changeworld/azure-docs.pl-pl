---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą programu Marketo | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i programu Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 09f452a0971e2a0e74e51edd2db44eecda39c204
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265767"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą programu Marketo

W tym samouczku dowiesz się, jak zintegrować usługi Marketo za pomocą usługi Azure Active Directory (Azure AD).
Integrowanie usługi Marketo za pomocą usługi Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi Marketo.
* Aby umożliwić użytkownikom można automatycznie zalogowany Marketo (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą programu Marketo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Marketo logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje usługi Marketo **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-marketo-from-the-gallery"></a>Dodawanie usługi Marketo za pomocą galerii

Aby skonfigurować integrację usługi Marketo w usłudze Azure AD, należy dodać Marketo za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi Marketo za pomocą galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Marketo**, wybierz opcję **Marketo** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Marketo, na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą programu Marketo, w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Marketo musi zostać nawiązane.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą programu Marketo, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie usługi Marketo logowania jednokrotnego](#configure-marketo-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Marketo](#create-marketo-test-user)**  — aby odpowiednikiem Britta Simon w Marketo, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą programu Marketo, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Marketo** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Domena usługi Marketo i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://saml.marketo.com/sp`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Marketo](http://investors.marketo.com/contactus.cfm) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie Marketo** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-marketo-single-sign-on"></a>Konfigurowanie usługi Marketo logowanie jednokrotne

1. Aby uzyskać identyfikator Munchkin aplikacji, zaloguj się przy użyciu poświadczeń administratora usługi Marketo i wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji programu Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** przycisku w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu integracji, a następnie kliknij przycisk **łącze Munchkin**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Skopiuj identyfikator Munchkin wyświetlone na ekranie, a następnie wykonaj adres URL odpowiedzi, w Kreatorze konfiguracji usługi Azure AD.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Aby skonfigurować logowanie Jednokrotne w aplikacji, wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji programu Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** przycisku w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu integracji, a następnie kliknij przycisk **Single Sign On**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Aby włączyć ustawienia protokołu SAML, kliknij przycisk **Edytuj** przycisku.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Włączone** ustawień logowania jednokrotnego.
   
    f. Wklej **usługi Azure AD identyfikator**w **identyfikator wystawcy** pola tekstowego.
   
    g. W **identyfikator jednostki** polu tekstowym wprowadź adres URL w postaci `http://saml.marketo.com/sp`.
   
    h. Wybierz lokalizację identyfikator użytkownika jako **identyfikator nazwy elementu**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Jeśli Twojego identyfikatora użytkownika nie ma wartości nazwy UPN, a następnie zmień wartość na karcie atrybutu.
   
    i. Przekaż certyfikat, który został pobrany z Kreatora konfiguracji usługi Azure AD. **Zapisz** ustawienia.
   
    j. Edytowanie ustawień stron przekierowania.
   
    k. Wklej **adres URL logowania** w **adres URL logowania** pola tekstowego.
   
    l. Wklej **adres URL wylogowania** w **adres URL wylogowania** pola tekstowego.
   
    m. W **błędny adres URL**, kopiowania usługi **adres URL wystąpienia usługi Marketo** i kliknij przycisk **Zapisz** przycisk, aby zapisać ustawienia.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Aby włączyć logowanie Jednokrotne dla użytkowników, wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji programu Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** przycisku w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do **zabezpieczeń** menu i kliknij przycisk **ustawienia logowania**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Sprawdź **wymagają logowania jednokrotnego** opcji i **Zapisz** ustawienia.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_14.png)

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi Marketo.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Marketo**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Marketo**.

    ![Łącze programu Marketo, na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-marketo-test-user"></a>Tworzenie użytkownika testowego programu Marketo

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Marketo. wykonaj następujące kroki, aby utworzyć użytkownika na platformie programu Marketo.

1. Zaloguj się do aplikacji programu Marketo przy użyciu poświadczeń administratora.

2. Kliknij przycisk **administratora** przycisku w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Przejdź do **zabezpieczeń** menu i kliknij przycisk **użytkowników i ról**
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Kliknij przycisk **Zaproś nowego użytkownika** link na karcie Użytkownicy
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. W Kreatorze Zaproś nowego użytkownika, wprowadź następujące informacje
   
    a. Wprowadź nazwę użytkownika **E-mail** adres w polu tekstowym
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Wprowadź **imię** w polu tekstowym
   
    c. Wprowadź **nazwisko** w polu tekstowym
   
    d. Kliknij przycisk **Dalej**

6. W **uprawnienia** zaznacz **userRoles** i kliknij przycisk **dalej**
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kliknij przycisk **wysyłania** przycisk, aby wysłać zaproszenia do użytkownika
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Użytkownik otrzymuje powiadomienie e-mail i kliknij łącze i zmienić hasło, aby aktywować konto. 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Marketo w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze programu Marketo, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

