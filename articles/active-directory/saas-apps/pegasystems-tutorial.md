---
title: 'Samouczek: Integracja usługi Azure Active Directory z systemami Pega | Dokumenty firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a pega systems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: bd54466e054055ff84cd5bb2b28c5cc074ac0017
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026806"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Samouczek: Integracja usługi Azure Active Directory z systemami Pega

W tym samouczku dowiesz się, jak zintegrować systemy Pega z usługą Azure Active Directory (Azure AD).

Integracja ta zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do Pega Systems.
* Można włączyć użytkowników do automatycznego logowania do Pega Systems (logowanie jednokrotne) za pomocą ich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pega systems, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz zarejestrować się w celu uzyskania [miesięcznej wersji próbnej.](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja Pega Systems z włączoną funkcją logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pega Systems obsługuje identyfikatory SSO inicjowane przez SP i inicjowane przez IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Dodaj Pega Systems z galerii

Aby skonfigurować integrację pega systems z usługą Azure AD, należy dodać Pega Systems z galerii do listy zarządzanych aplikacji SaaS.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory:**

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji** > Enterprise**Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Pega Systems**. W wynikach wyszukiwania wybierz **pozycję Pega Systems,** a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD za pomocą pega systems przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD a odpowiednim użytkownikiem w systemach Pega.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą systemów Pega, należy wykonać następujące kroki:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby włączyć tę funkcję dla użytkowników.
2. **[Skonfiguruj logowanie jednokrotne pega systems](#configure-pega-systems-single-sign-on)** po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednookrotne usługi Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć logowanie jednookrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego Pega Systems,](#create-a-pega-systems-test-user)** który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą systemów Pega, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Pega Systems** wybierz pozycję **Logowanie jednokrotne:**

    ![Wybierz opcję Logowanie jednokrotne](common/select-sso.png)

2. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne:

    ![Wybieranie metody logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę **Edycja,** aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML:**

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Konfiguracja podstawowego SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez IdP, wykonaj następujące kroki.

    ![Podstawowe okno dialogowe Konfiguracja SAML](common/idp-intiated.png)

    1. W polu **Identyfikator** wprowadź adres URL w tym wzorcu:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. W polu **Odpowiedz na adres URL** wprowadź adres URL w tym wzorzec:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez sp, wybierz pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki.

    ![Informacje o domenie i adresach URL Pega Systems](common/both-advanced-urls.png)

    1. W polu **Podpisz na adresie URL** wprowadź wartość adresu URL znaku.

    1. W polu **Stan przekazywania** wprowadź adres URL w tym wzorzec:`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Podane tutaj wartości są symbolami zastępczymi. Musisz użyć rzeczywistego identyfikatora, adresu URL odpowiedzi, podpisuj adres URL i adresu URL stanu przekazywania. Możesz uzyskać wartości adresu URL identyfikatora i odpowiedzi z aplikacji Pega, jak wyjaśniono w dalszej części tego samouczka. Aby uzyskać wartość stanu przekaźnika, skontaktuj się z [zespołem pomocy technicznej Pega Systems](https://www.pega.com/contact-us). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Aplikacja Pega Systems wymaga potwierdzenia SAML być w określonym formacie. Aby uzyskać je w poprawnym formacie, należy dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia atrybuty domyślne. Wybierz ikonę **Edytuj,** aby otworzyć okno dialogowe **Atrybuty użytkownika:**

    ![Atrybuty użytkownika](common/edit-attribute.png)

7. Oprócz atrybutów pokazanych na poprzednim zrzucie ekranu, aplikacja Pega Systems wymaga kilku innych atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać te atrybuty tokenu SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Te wartości są specyficzne dla organizacji. Podaj odpowiednie wartości.

    1. Wybierz **pozycję Dodaj nowe oświadczenie,** aby otworzyć okno dialogowe Zarządzanie **roszczeniami użytkowników:**

    ![Wybierz pozycję Dodaj nowe oświadczenie](common/new-save-attribute.png)

    ![Okno dialogowe Zarządzanie oświadczeniami użytkownika](common/new-attribute-details.png)

    1. W polu tekstowym **Nazwa** podaj nazwę atrybutu pokazanego dla tego wiersza.

    1. Pozostaw pole **Obszaru nazw** puste.

    1. W obszarze **Źródło**wybierz pozycję **Atrybut**.

    1. Na liście **Atrybut źródła** wybierz wartość atrybutu wyświetlaną dla tego wiersza.

    1. Wybierz **ok**.

    1. Wybierz **pozycję Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** wybierz łącze **Pobierz** obok **pozycji XML metadanych federacji**, zgodnie z wymaganiami i zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie systemów Pega** skopiuj odpowiednie adresy URL na podstawie wymagań użytkownika.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowania**.

### <a name="configure-pega-systems-single-sign-on"></a>Konfigurowanie logowania jednokrotnego pega systems

1. Aby skonfigurować logowanie jednokrotne po stronie **Pega Systems,** zaloguj się do portalu Pega za pomocą konta administratora w innym oknie przeglądarki.

2. Wybierz **pozycję Utwórz** > **usługę uwierzytelniania****SysAdmin:** > 

    ![Wybierz usługę uwierzytelniania](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Wykonaj następujące kroki na ekranie **Utwórz usługę uwierzytelniania.**

    ![Ekran Utwórz usługę uwierzytelniania](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Na liście **Typ** wybierz **SAML 2.0**.

    1. W polu **Nazwa** wprowadź dowolną nazwę (na przykład **przysłów Yso usługi Azure AD**).

    1. W polu **Krótki opis** wprowadź opis.  

    1. Wybierz **pozycję Utwórz i otwórz**.
    
4. W sekcji **informacji dostawcy tożsamości (IdP)** wybierz pozycję **Importuj metadane dostawcy tożsamości** i przejdź do pliku metadanych pobranego z witryny Azure portal. Kliknij **przycisk Prześlij,** aby załadować metadane:

    ![Sekcja informacji o dostawcy tożsamości (IdP)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Import wypełni dane IdP, jak pokazano poniżej:

    ![Zaimportowane dane IdP](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Wykonaj następujące kroki w sekcji **Ustawienia usługodawcy (SP).**

    ![Ustawienia usługodawcy](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Skopiuj wartość **identyfikacji jednostki** i wklej ją do pola **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    1. Skopiuj wartość **lokalizacji usługi konsumenta oświadczeń (ACS)** i wklej ją do pola **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    1. Wybierz **pozycję Wyłącz podpisywanie żądań**.

7. Wybierz **pozycję Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure portal.

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory** w lewym okienku, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy:**

    ![Wybierz pozycję Wszyscy użytkownicy](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu:

    ![Wybierz pozycję Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **Użytkownik** wykonaj następujące czynności.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wprowadź brittasimon@ **\<> swojej>.\<>rozszerzenia **. (Na przykład BrittaSimon@contoso.com.)

    d. Wybierz **pozycję Pokaż hasło**, a następnie zapisz wartość w polu **Hasło.**

    d. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając jej dostęp do Pega Systems.

1. W portalu Azure wybierz **pozycję Aplikacje dla przedsiębiorstw**, wybierz pozycję Wszystkie **aplikacje**, a następnie wybierz **pozycję Pega Systems**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Pega Systems**.

    ![Lista wniosków](common/all-applications.png)

3. W lewym okienku wybierz **pozycję Użytkownicy i grupy:**

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-pega-systems-test-user"></a>Tworzenie użytkownika testowego Pega Systems

Następnie musisz utworzyć użytkownika o imieniu Britta Simon w Pega Systems. Współpracuj z [zespołem pomocy technicznej Pega Systems,](https://www.pega.com/contact-us) aby tworzyć użytkowników.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Pega Systems w Panelu dostępu należy automatycznie zalogować się do wystąpienia Pega Systems, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)