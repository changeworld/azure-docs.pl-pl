---
title: 'Samouczek: Integracja usługi Azure Active Directory z platformą Zscaler | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i rozwiązania Zscaler
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 631f65ff042c2f1d241626d5f480eae9a8b216a9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705037"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Samouczek: Integracja usługi Azure Active Directory z platformą Zscaler

W tym samouczku dowiesz się, jak zintegrować rozwiązania Zscaler w usłudze Azure Active Directory (Azure AD).
Integracja rozwiązania Zscaler z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do rozwiązania Zscaler.
* Aby umożliwić użytkownikom można automatycznie zalogowany do rozwiązania Zscaler (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z platformą Zscaler, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Rozwiązania Zscaler logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje rozwiązania Zscaler **SP** jednokrotne logowanie inicjowane przez

* Obsługuje rozwiązania Zscaler **Just In Time** aprowizacji użytkowników

## <a name="adding-zscaler-from-the-gallery"></a>Dodawanie rozwiązania Zscaler z galerii

Aby skonfigurować integrację rozwiązania Zscaler w usłudze Azure AD, należy dodać rozwiązania Zscaler z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać rozwiązania Zscaler z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **rozwiązania Zscaler**, wybierz opcję **rozwiązania Zscaler** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Rozwiązania Zscaler na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowania i testowania usługi Azure AD logowanie jednokrotne z platformą Zscaler w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w rozwiązania Zscaler musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z platformą Zscaler, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie rozwiązania Zscaler logowania jednokrotnego](#configure-zscaler-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego rozwiązania Zscaler](#create-zscaler-test-user)**  — aby odpowiednikiem Britta Simon w rozwiązania Zscaler połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z platformą Zscaler, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **rozwiązania Zscaler** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Rozwiązania Zscaler domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.zscaler.net`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta rozwiązania Zscaler](https://www.zscaler.com/company/contact) można uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja rozwiązania Zscaler oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe  **Atrybuty użytkownika** .

    ![image](common/edit-attribute.png)

6. Ponadto powyżej rozwiązania Zscaler aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:
    
    | Name (Nazwa) | Atrybut źródłowy |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.
    
    f. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Kliknij [tutaj](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management), aby dowiedzieć się, jak skonfigurować rolę w usłudze Azure AD

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. Na **konfigurowanie rozwiązania Zscaler** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-zscaler-single-sign-on"></a>Konfigurowanie rozwiązania Zscaler logowanie jednokrotne

1. Aby zautomatyzować konfigurację w ramach rozwiązania Zscaler, musisz zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **instalacji rozwiązania Zscaler** nastąpi bezpośrednie przekierowanie do aplikacji rozwiązania Zscaler. W tym miejscu należy podać poświadczenia administratora do logowania się do rozwiązania Zscaler. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzować kroki od 3 do 6.

    ![Konfigurowanie logowania jednokrotnego](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować rozwiązania Zscaler, Otwórz nowe okno przeglądarki sieci web i logowania do witryny rozwiązania Zscaler firmy jako administrator i wykonaj następujące czynności:

4. Przejdź do obszaru **Administracja > Uwierzytelnianie > Ustawienia uwierzytelniania** i wykonaj następujące kroki:
   
    ![Administracja](./media/zscaler-tutorial/ic800206.png "Administracja")

    a. W obszarze Typ uwierzytelniania wybierz pozycję **SAML**.

    b. Kliknij pozycję **Skonfiguruj język SAML**.

5. W oknie **Edytowanie języka SAML** wykonaj następujące kroki i kliknij pozycję Zapisz.  
            
    ![Zarządzanie użytkownikami i uwierzytelnianiem](./media/zscaler-tutorial/ic800208.png "Zarządzanie użytkownikami i uwierzytelnianiem")
    
    a. W polu tekstowym **Adres URL portalu języka SAML** wklej **adres URL logowania** skopiowany z witryny Azure Portal.

    b. W polu tekstowym **Atrybut nazwy logowania** wprowadź identyfikator **NameID**.

    c. Kliknij pozycję **Przekaż**, aby przekazać certyfikat podpisywania języka SAML na platformie Azure, który został pobrany z witryny Azure Portal w obrębie **publicznego certyfikatu SSL**.

    d. Przełącz element **Włącz automatyczne aprowizowanie języka SAML**.

    e. W polu tekstowym **Atrybut nazwy wyświetlanej użytkownika** wprowadź ciąg **displayName**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu displayName.

    f. W polu tekstowym **Atrybut nazwy grupy** wprowadź ciąg **memberOf**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu memberOf.

    g. W polu **Atrybut nazwy działu** wprowadź ciąg **department**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu department.

    h. Kliknij pozycję **Zapisz**.

6. Na stronie okna dialogowanie **Konfigurowanie uwierzytelniania użytkownika** wykonaj następujące kroki:

    ![Administracja](./media/zscaler-tutorial/ic800207.png)

    a. Umieść kursor nad menu **Aktywacja** w lewym dolnym rogu.

    b. Kliknij pozycję **Aktywuj**.

## <a name="configuring-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurowanie ustawień serwera proxy w programie Internet Explorer

1. Uruchom program **Internet Explorer**.

2. Wybierz pozycję **Opcje internetowe** z menu **Narzędzia**, aby otworzyć okno dialogowe **Opcje internetowe**.   
    
     ![Opcje internetowe](./media/zscaler-tutorial/ic769492.png "Opcje internetowe")

3. Kliknij kartę **Połączenia**.   
  
     ![Połączenia](./media/zscaler-tutorial/ic769493.png "Połączenia")

4. Kliknij przycisk **Ustawienia sieci LAN**, aby otworzyć okno dialogowe **Ustawienia sieci lokalnej (LAN)**.

5. W sekcji Serwer proxy wykonaj następujące kroki:   
   
    ![Serwer proxy](./media/zscaler-tutorial/ic769494.png "Serwer proxy")

    a. Zaznacz pole wyboru **Użyj serwera proxy dla sieci LAN**.

    b. W polu tekstowym adresu wpisz **gateway.zscaler.net**.

    c. W polu tekstowym Port wpisz **80**.

    d. Zaznacz pole wyboru **Nie używaj serwera proxy dla adresów lokalnych**.

    e. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Ustawienia sieci lokalnej (LAN)**.

6. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Opcje internetowe**.

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do rozwiązania Zscaler.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **rozwiązania Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **rozwiązania Zscaler**.

    ![Łącze rozwiązania Zscaler na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_users.png)

6. W oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę użytkownika na liście, a następnie kliknij przycisk **Wybierz** znajdujący się u dołu ekranu.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_roles.png)

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_assign.png)

### <a name="create-zscaler-test-user"></a>Tworzenie użytkownika testowego rozwiązania Zscaler

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w rozwiązania Zscaler. Rozwiązania Zscaler obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w rozwiązania Zscaler, nowy katalog jest tworzony po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka rozwiązania Zscaler w panelu dostępu, użytkownik powinien być automatycznie zalogowany do rozwiązania Zscaler, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

