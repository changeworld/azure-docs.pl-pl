---
title: 'Samouczek: Integracja usługi Azure Active Directory z topdesk - publiczne | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą TOPdesk — publiczne.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: e5575a2e8f776e87fcd4e6f4a7a9244752ebfd9a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71950424"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Samouczek: Integracja usługi Azure Active Directory z topdesk - public

W tym samouczku dowiesz się, jak zintegrować TOPdesk — publiczne z usługą Azure Active Directory (Azure AD).
Integracja TOPdesk — publiczne z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do TOPdesk — public.
* Można włączyć użytkowników do automatycznego logowania się do TOPdesk — publiczne (logowanie jednokrotne) za pomocą ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z topdesk — public, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk — publiczna subskrypcja z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* TOPdesk - Publiczne **obsługuje** sp zainicjowane SSO

## <a name="adding-topdesk---public-from-the-gallery"></a>Dodawanie TOPdesk - Publiczne z galerii

Aby skonfigurować integrację TOPdesk — Public do usługi Azure AD, należy dodać TOPdesk — publiczne z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać TOPdesk — publiczne z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **TOPdesk - Public**, select **TOPdesk - Public** from result panel, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![TOPdesk - Publiczne na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą TOPdesk — publiczne na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w topdesku — należy ustanowić public.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi TOPdesk — public, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj topdesk — publiczne logowanie jednokrotne](#configure-topdesk---public-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz TOPdesk — publiczny użytkownik testowy](#create-topdesk---public-test-user)** — aby mieć odpowiednik Britta Simon w TOPdesk — publiczne, które jest połączone z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą usługi TOPdesk — public, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie INTEGRACJA aplikacji **TOPdesk — Public** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4.  W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    >[!NOTE]
    >**Plik metadanych dostawcy usług** otrzymasz z sekcji **Konfigurowanie topdesk — publiczne logowanie jednokrotne,** która została wyjaśniona w dalszej części samouczka.

    a. Kliknij pozycję **Przekaż plik metadanych**.
    
    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    d. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatora** i **adresu URL odpowiedzi** są automatycznie wypełniane w sekcji Podstawowa konfiguracja SAML.

    ![TOPdesk — informacje o logach do pojedynczego logowania o domenie publicznej i adresach URL](common/sp-identifier-reply.png)

    d. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<companyname>.topdesk.net`

    e. W polach tekstowych **Identyfikator URL** wpisz adres URL metadanych TOPdesk, który można pobrać z konfiguracji TOPdesk. Należy użyć następującego wzorca:`https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Jeśli wartości **adresu URL** **identyfikatora** i odpowiedzi nie są wypełniane automatycznie, należy wprowadzić je ręcznie. W przypadku identyfikatora postępuj zgodnie ze wzorcem, jak wspomniano powyżej, a otrzymasz wartość adresu URL odpowiedzi z sekcji **Konfigurowanie TOPdesk — publiczne logowanie jednokrotne,** która została wyjaśniona w dalszej części samouczka. Wartość **adresu URL logowania** nie jest rzeczywista, więc musisz zaktualizować wartość za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z [TOPdesk — zespół pomocy technicznej klienta publicznego,](https://help.topdesk.com/saas/enterprise/user/) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie topdesk - public** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-topdesk---public-single-sign-on"></a>Konfigurowanie topdesk - publiczne logowanie jednokrotne

1. Zaloguj się do witryny **TOPdesk — firma publiczna** jako administrator.

2. W menu **TOPdesk** kliknij pozycję **Settings** (Ustawienia).
   
    ![Ustawienia](./media/topdesk-public-tutorial/ic790598.png "Ustawienia")

3. Kliknij pozycję **Login Settings** (Ustawienia logowania).
   
    ![Ustawienia logowania](./media/topdesk-public-tutorial/ic790599.png "Ustawienia logowania")

4. Rozwiń menu **Login Settings** (Ustawienia logowania), a następnie kliknij pozycję **General** (Ogólne).
   
    ![Ogólne](./media/topdesk-public-tutorial/ic790600.png "Ogólne")

5. W sekcji **Public** sekcji konfiguracji **logowania SAML** wykonaj następujące kroki:
   
    ![Ustawienia techniczne](./media/topdesk-public-tutorial/ic790601.png "Ustawienia techniczne")
   
    a. Kliknij pozycję **Download** (Pobierz), aby pobrać publiczny plik metadanych, a następnie zapisz go lokalnie na komputerze.
   
    b. Otwórz pobrany plik metadanych, a następnie zlokalizuj węzeł **AssertionConsumerService.**

    ![TwierdzenieConsumerService](./media/topdesk-public-tutorial/ic790619.png "TwierdzenieConsumerService")
   
    d. Skopiuj wartość **AssertionConsumerService,** wklej tę wartość w polu tekstowym **Odpowiedz adres URL** w sekcji **Podstawowa konfiguracja SAML.**      
   
6. Aby utworzyć plik certyfikatu, wykonaj następujące kroki:
    
    ![Certyfikat](./media/topdesk-public-tutorial/ic790606.png "Certyfikat")
    
    a. Otwórz plik metadanych pobrany w witrynie Azure Portal.
    
    b. Rozwiń węzeł **RoleDescriptor**, w którym element **xsi:type** ma wartość **fed:ApplicationServiceType**.
    
    d. Skopiuj wartość węzła**X509Certificate**.
    
    d. Zapisz skopiowaną wartość **X509Certificate** lokalnie na komputerze w pliku.

7. W sekcji **Public** (Publiczne) kliknij przycisk **Add** (Dodaj).
    
    ![Logowanie SAML](./media/topdesk-public-tutorial/ic790625.png "Logowanie SAML")

8. W oknie dialogowym **SAML configuration assistant** (Asystent konfiguracji SAML) wykonaj następujące kroki:
    
    ![Asystent konfiguracji SAML](./media/topdesk-public-tutorial/ic790608.png "Asystent konfiguracji SAML")
    
    a. Aby przekazać plik metadanych pobrany w witrynie Azure Portal, w obszarze **Federation Metadata** (Metadane federacji) kliknij przycisk **Browse** (Przeglądaj).

    b. Aby przekazać plik certyfikatu, w obszarze **Certificate (RSA)** (Certyfikat — RSA) kliknij przycisk **Browse** (Przeglądaj).

    d. Aby przekazać plik logo uzyskany od zespołu pomocy technicznej TOPdesk, w obszarze **Logo icon** (Ikona logo) kliknij przycisk **Browse** (Przeglądaj).

    d. W polu tekstowym **User name attribute** (Atrybut nazwy użytkownika) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. W polu tekstowym **Display name** (Nazwa wyświetlana) wpisz nazwę konfiguracji.

    f. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** brittasimon@yourcompanydomain.extensionużytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do TOPdesk — public.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **TOPdesk — Publiczne**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **TOPdesk — Publiczne**.

    ![ToPdesk — łącze publiczne na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-topdesk---public-test-user"></a>Tworzenie TOPdesk — publiczny użytkownik testowy

Aby umożliwić użytkownikom usługi Azure AD zalogować się do topdesk — publiczne, muszą być aprowizowane do TOPdesk — Public. W przypadku TOPdesk — publiczne inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:

1. Zaloguj się do witryny **TOPdesk — firma publiczna** jako administrator.

2. W menu u góry kliknij pozycję **TOPdesk \> New \> Support Files \> Person**.
   
    ![Person (Osoba)](./media/topdesk-public-tutorial/ic790628.png "Person (Osoba)")

3. W oknie dialogowym Nowa osoba wykonaj następujące czynności:
   
    ![Nowa osoba](./media/topdesk-public-tutorial/ic790629.png "Nowa osoba")
   
    a. Kliknij kartę General (Ogólne).

    b. W polu **tekstowym Nazwisko** wpisz nazwisko użytkownika takiego jak Simon
 
    d. Wybierz **witrynę** dla konta.
 
    d. Kliknij przycisk **Zapisz**.

> [!NOTE]
> Można użyć dowolnego innego topdesk — narzędzia do tworzenia konta użytkowników publicznych lub interfejsy API dostarczone przez TOPdesk — publiczne do aprowizowania kont użytkowników usługi Azure AD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka TOPdesk — Public w Panelu dostępu należy automatycznie zalogować się do topdesku — public, dla którego skonfigurowano logującą się jednokrotność. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
