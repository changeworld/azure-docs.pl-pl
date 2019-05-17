---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą logowania jednokrotnego SAML dla programu Jira przez GmbH rozpoznawania | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 020e0395c7678f6856a293c8386a44d7a959bc06
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786529"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Samouczek: integracja usługi Azure Active Directory z usługą logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH

W tym samouczku dowiesz się, jak skonfigurować logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH w usłudze Azure Active Directory (Azure AD).
Zintegrowanie usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, który zalogować się do programu Jira za pomocą wtyczki SAML logowania jednokrotnego przy rozdzielczości GmbH.
* Aby umożliwić użytkownikom można automatycznie zalogowany do programu Jira za pomocą kont usługi Azure AD przy użyciu logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH (logowanie jednokrotne).
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integracji z usługą Azure AD i logowania jednokrotnego SAML dla programu Jira przy rozdzielczości GmbH, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowania jednokrotnego SAML dla programu Jira przez rozwiązania obsługuje GmbH **SP** i **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Dodawanie aplikacji dla przedsiębiorstw dla logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne w usłudze Azure AD, należy dodać nową aplikację przedsiębiorstwa. W galerii, jest wstępnie skonfigurowanych aplikacji, ustawienie wstępne w tym celu **logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH**.

**Aby dodać usługę logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie kliknij przycisk **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH**, wybierz opcję **logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH** z panelu wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikacja. Można również zmienić nazwę aplikacji przedsiębiorstwa.

     ![Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Konfiguracja i testowanie logowania jednokrotnego przy użyciu wtyczkę logowania jednokrotnego SAML i usługa Azure AD

W tej sekcji możesz przetestować i konfigurowanie logowania jednokrotnego do programu Jira dla użytkownika usługi Azure AD. Ta czynność zostanie wykonana dla użytkownika testu o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH.

Aby skonfigurować i przetestować logowanie jednokrotne, należy wykonać następujące czynności:

1. **[Konfigurowanie usługi Azure AD aplikacji enterprise dla logowania jednokrotnego](#configure-the-azure-ad-enterprise-application-for-single-sign-on)**  — Konfigurowanie usługi Azure AD aplikacji enterprise dla rejestracji jednokrotnej
2. **[Skonfiguruj wtyczkę logowania jednokrotnego SAML wystąpienia swojej Jira](#configure-the-saml-sso-plugin-of-your-jira-instance)**  — Konfigurowanie ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — Tworzenie użytkownika testowego w usłudze Azure AD.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Włączanie użytkownika testowego użyć pojedynczego logowania jednokrotnego stronie platformy Azure.
1. **[Tworzenie użytkownika testowego w usłudze Jira](#create-the-test-user-also-in-jira)**  — Tworzenie użytkownika testowego odpowiednika w usłudze Jira dla użytkownika testowego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Sprawdź, czy konfiguracja działa.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Konfigurowanie usługi Azure AD aplikacji enterprise dla logowania jednokrotnego

W tej sekcji możesz skonfigurować logowanie jednokrotne w witrynie Azure portal.

Aby skonfigurować logowanie jednokrotne za pomocą logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/), w właśnie utworzyłeś **logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH** aplikacja dla przedsiębiorstw, wybierz opcję **logowanie jednokrotne** w panelu po lewej stronie.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. Dla **wybierz jedną metodę logowania jednokrotnego**, wybierz opcję **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Później, kliknij przycisk **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, a następnie wykonaj następujące czynności:

    ![Informacje na temat domeny i adresów URL usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Informacje na temat domeny i adresów URL usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Identyfikator, adres URL odpowiedzi i adres URL logowania, należy zastąpić  **\<serwera podstawowego adresu url >** przy użyciu podstawowego adresu URL wystąpienia programu Jira. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal. Jeśli masz problem, skontaktuj się z nami pod adresem [zespołu pomocy technicznej logowania jednokrotnego SAML dla programu Jira przez rozwiązania klienta GmbH](https://www.resolution.de/go/support).

4. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** stronie **certyfikat podpisywania SAML** sekcji, Pobierz **XML metadanych Federacji** i zapisz go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Skonfiguruj wtyczkę logowania jednokrotnego SAML wystąpienia swojej Jira 

1. W oknie przeglądarki internetowej innej należy zalogować się jako administrator z wystąpieniem programu Jira.

2. Umieść kursor nad koło zębate po prawej stronie, a następnie kliknij przycisk **Zarządzanie aplikacjami**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon1.png)

3. Jeśli nastąpi przekierowanie do strony dostępu administratora, wprowadź **hasło** i kliknij przycisk **Potwierdź** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon2.png)

4. Jira zwykle przekieruje Cię do witryny marketplace Atlassian. Jeśli nie, kliknij polecenie **Znajdź nowe aplikacje** w panelu po lewej stronie. Wyszukaj **SAML logowanie jednokrotne (SSO) dla programu JIRA** i kliknij przycisk **zainstalować** przycisk, aby zainstalować wtyczkę SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/store.png)

5. Rozpocznie się instalacja wtyczki. Gdy wszystko będzie gotowe, kliknij przycisk **Zamknij** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/store-2.png)

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/store-3.png)

6. Następnie kliknij przycisk **Zarządzaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/store-4.png)
    
8. Później, kliknij przycisk **Konfiguruj** do skonfigurowania tylko zainstalowanych wtyczek.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/store-5.png)

9. W **plik konfiguracji wtyczki SAML SingleSignOn** kreatora, kliknij przycisk **Dodaj nowy dostawca tożsamości** Konfigurowanie usługi Azure AD jako nowy dostawca tożsamości.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon4.png) 

10. Na **wybierz dostawcy tożsamości SAML** strony, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5a.png)
 
    a. W polu IdP type (Typ dostawcy tożsamości) wybierz pozycję **Azure AD**.
    
    b. Dodaj **nazwa** dostawcy tożsamości (np. usługi Azure AD).
    
    c. Dodaj (opcjonalnie) **opis** dostawcy tożsamości (np. usługi Azure AD).
    
    d. Kliknij przycisk **Dalej**.
    
11. Na **konfiguracji dostawcy tożsamości** kliknij **dalej**.
 
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5b.png)

12. Na stronie **Import SAML IdP Metadata** (Importuj metadane dostawcy tożsamości protokołu SAML) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5c.png)

    a. Kliknij przycisk **wybierz plik XML metadanych** przycisk, a następnie wybierz **XML metadanych Federacji** plik został pobrany przed.

    b. Kliknij przycisk **importu** przycisku.
     
    c. Poczekaj chwilę import zakończy się pomyślnie.  
     
    d. Kliknij przycisk **Dalej**.
    
13. Na **atrybutu nazwy użytkownika i transformacji** kliknij **dalej** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5d.png)
    
14. Na **użytkownika tworzenia i aktualizowania** kliknij **z & apisz dalej** Aby zapisać ustawienia.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6a.png)
    
15. Na **testowanie ustawień usługi** kliknij **pominąć test & ręcznie skonfigurować** Aby pominąć test użytkownika teraz. Odbędzie się w następnej sekcji i wymaga niektóre ustawienia w witrynie Azure portal.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6b.png)
    
16. Kliknij przycisk **OK** Aby pominąć to ostrzeżenie.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon. Z użytkownikiem przetestujesz logowania jednokrotnego.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](common/users.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W **właściwości użytkownika**, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W **nazwa** wprowadź **Britta Simon**.
  
    b. W **nazwa_użytkownika** wprowadź <b> BrittaSimon@contoso.com </b>.

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji dodasz Britta Simon do aplikacji przedsiębiorstwa, który umożliwia jej używać logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**. 

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji Wyszukaj utworzony na początku tego samouczka aplikacja dla przedsiębiorstw. Jeśli wykonujesz kroki tego samouczka jest nazywany **logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH**. Jeśli został on podany inną nazwę, wyszukaj dla tej nazwy.

    ![Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH na liście aplikacji](common/all-applications.png)

3. W okienku po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

4. Wybierz **Dodaj użytkownika**, a następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** z listy użytkowników, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML, następnie w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu .

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-the-test-user-also-in-jira"></a>Tworzenie użytkownika testowego również w usłudze Jira

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH, ich musi być obsługiwana do logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH. W przypadku tego samouczka trzeba ręcznie aprowizacji. Jednak istnieją również inne modele inicjowania obsługi administracyjnej wtyczka logowania jednokrotnego SAML według rozdzielczości, na przykład **Just In Time** inicjowania obsługi administracyjnej. Zajrzyj do dokumentacji, ich w [logowania jednokrotnego SAML, rozpoznawanie GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Jeśli masz pytania, na jego temat, skontaktuj się z działem pomocy technicznej związanej z [obsługę rozwiązywania](https://www.resolution.de/go/support).

**Aby ręcznie zainicjować obsługę konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do wystąpienia programu Jira jako administrator.

2. Umieść kursor koła zębatego i wybierz **Zarządzanie użytkownikami**.

   ![Dodawanie pracownika](./media/samlssojira-tutorial/user1.png)

3. Jeśli nastąpi przekierowanie do strony dostępu administratora, wprowadź **hasło** i kliknij przycisk **Potwierdź** przycisku.

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user2.png) 

4. W obszarze **Zarządzanie użytkownikami** kliknij sekcję pozycję **Utwórz użytkownika**.

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user3-new.png) 

5. Na **"Utwórz nowego użytkownika"** okna dialogowego strony, wykonaj następujące kroki. Należy dokładnie utworzyć użytkownika w usłudze Azure AD, takie jak:

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user4-new.png) 

    a. W **adres E-mail** polu tekstowym wpisz adres e-mail użytkownika: <b> BrittaSimon@contoso.com </b>.

    b. W **imię i nazwisko** pole tekstowe, wpisz pełną nazwę użytkownika: **Britta Simon**.

    c. W **Username** polu tekstowym wpisz adres e-mail użytkownika: <b> BrittaSimon@contoso.com </b>. 

    d. W **hasło** polu tekstowym Wprowadź hasło użytkownika.

    e. Kliknij przycisk **Utwórz użytkownika** na zakończenie tworzenia użytkownika.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH w panelu dostępu powinno nastąpić automatyczne zalogowanie się do usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

Możesz również przetestować logowanie jednokrotne, jeśli przejdziesz do [https://\<serwera podstawowego adresu url >/wtyczek/serwletu/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Zastąp  **\<serwera podstawowego adresu url >** przy użyciu podstawowego adresu URL wystąpienia programu Jira.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Włącz przekierowanie pojedynczego logowania dla programu Jira

Jak wspomniano w sekcji przed, istnieją obecnie dwie metody wyzwalanie rejestracji jednokrotnej. Za pomocą **witryny Azure portal** lub za pomocą **specjalne łącze do Twojego wystąpienia programu Jira**. Wtyczka logowania jednokrotnego SAML, rozpoznawanie GmbH umożliwia także wyzwolić logowanie jednokrotne, po prostu **uzyskiwania dostępu do dowolnego adresu URL, wskazując wystąpienia swojej Jira**.

W zasadzie wszystkim użytkownikom uzyskiwanie dostępu do programu Jira nastąpi przekierowanie do logowania jednokrotnego po aktywowaniu opcję we wtyczce.

Aby aktywować przekierowania logowania jednokrotnego, wykonaj następujące czynności **wystąpienia swojej Jira**:

1. Dostęp do strony konfiguracji wtyczki SAML logowania jednokrotnego w usłudze Jira.
1. Kliknij pozycję **przekierowania** w panelu po lewej stronie.
![](./media/samlssojira-tutorial/ssore1.png)

1. Znaczników **włączyć logowanie Jednokrotne przekierowania**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Naciśnij klawisz **Zapisz ustawienia** przycisk w prawym górnym rogu.

Po aktywowaniu opcji, nadal możesz połączyć if monitu nazwy użytkownika/hasła **Włącz nosso** zaznaczono opcję, przechodząc do [https://\<serwera podstawowego adresu url > /login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Jak zawsze zastępuje  **\<serwera podstawowego adresu url >** z Twojego podstawowego adresu URL.


## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

