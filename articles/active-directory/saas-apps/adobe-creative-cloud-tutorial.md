---
title: 'Samouczek: integracja usługi Azure Active Directory z rozwiązaniem Adobe Creative Cloud | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i rozwiązaniem Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35bd52904ab081e41cb43a346288234c18a7f43b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285109"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Samouczek: integracja usługi Azure Active Directory z rozwiązaniem Adobe Creative Cloud

Z tego samouczka dowiesz się, jak zintegrować rozwiązanie Adobe Creative Cloud z usługą Azure Active Directory (Azure AD).
Integrowanie rozwiązania Adobe Creative Cloud z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do rozwiązania Adobe Creative Cloud.
* Twoi użytkownicy mogą być automatycznie logowani do rozwiązania Adobe Creative Cloud (logowanie jednokrotne) przy użyciu swoich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z rozwiązaniem Adobe Creative Cloud potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja rozwiązania Adobe Creative Cloud z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie Adobe Creative Cloud obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Dodawanie rozwiązania Adobe Creative Cloud z galerii

Aby skonfigurować integrację rozwiązania Adobe Creative Cloud z usługą Azure AD, należy dodać rozwiązanie Adobe Creative Cloud z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać rozwiązanie Adobe Creative Cloud z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Adobe Creative Cloud**, wybierz pozycję **Adobe Creative Cloud** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Rozwiązanie Adobe Creative Cloud na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z rozwiązaniem Adobe Creative Cloud, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem rozwiązania Adobe Creative Cloud.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z rozwiązaniem Adobe Creative Cloud, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w rozwiązaniu Adobe Creative Cloud](#configure-adobe-creative-cloud-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego rozwiązania Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** — aby udostępnić w rozwiązaniu Adobe Creative Cloud odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w rozwiązaniu Adobe Creative Cloud, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Adobe Creative Cloud** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego rozwiązania Adobe Creative Cloud](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz wartość: `https://adobe.com`.

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego identyfikatora. W celu uzyskania tej wartości skontaktuj się z [zespołem pomocy technicznej klienta rozwiązania Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja Adobe Creative Cloud oczekuje asercji SAML w konkretnym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa | Atrybut źródłowy|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Aby wartość oświadczenia e-mail była wypełniana w odpowiedzi SAML, użytkownicy muszą mieć ważną licencję usługi Office 365 ExO.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. W sekcji **Skonfiguruj aplikację Adobe Creative Cloud** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Konfigurowanie logowania jednokrotnego rozwiązania Adobe Creative Cloud

1. W innym oknie przeglądarki zaloguj się jako administrator do konsoli administracyjnej [Adobe Admin Console](https://adminconsole.adobe.com).

2. Wybierz pozycję **Ustawienia** na górnym pasku nawigacyjnym, a następnie wybierz pozycję **Tożsamość**. Zostanie otwarta lista domen. Kliknij link **Konfiguruj** dla Twojej domeny. Następnie wykonaj następujące czynności w sekcji **Single Sign On Configuration Required** (Wymagana konfiguracja logowania jednokrotnego). Aby uzyskać więcej informacji, zobacz [Konfigurowanie domeny](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Ustawienia](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Ustawienia")

    a. Kliknij przycisk **Browse** (Przeglądaj), aby przekazać certyfikat pobrany z usługi Azure AD w polu **IDP Certificate** (Certyfikat IDP).

    b. W polu tekstowym **IDP issuer** (Wystawca IDP) wprowadź wartość **identyfikatora tożsamości SAML** skopiowaną z sekcji **Konfigurowanie logowania jednokrotnego** w witrynie Azure Portal.

    c. W polu tekstowym **IDP Login URL** (Adres URL logowania IDP) wprowadź wartość **adresu URL usługi logowania jednokrotnego SAML** skopiowaną z sekcji **Konfigurowanie logowania jednokrotnego** w witrynie Azure Portal.

    d. Wybierz wartość **HTTP — Redirect** w polu **IDP Binding** (Powiązanie IDP).

    e. Wybierz wartość **Email Address** (Adres e-mail) w polu **User Login Setting** (Ustawienie logowania użytkownika).

    f. Kliknij przycisk **Save** (Zapisz).

3. Na pulpicie nawigacyjnym będzie teraz widoczny plik XML **„Pobierz metadane”**. Zawiera on adresy URL EntityDescriptor i AssertionConsumerService firmy Adobe. Otwórz ten plik i skonfiguruj te adresy w aplikacji usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Użyj wartości EntityDescriptor dostarczonej przez firmę Adobe w polu **Identyfikator** okna dialogowego **Konfigurowanie ustawień aplikacji**.

    b. Użyj wartości AssertionConsumerService dostarczonej przez firmę Adobe w polu **Adres URL odpowiedzi** okna dialogowego **Konfigurowanie ustawień aplikacji**.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do rozwiązania Adobe Creative Cloud.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Adobe Creative Cloud**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Adobe Creative Cloud**.

    ![Link Adobe Creative Cloud na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-adobe-creative-cloud-test-user"></a>Tworzenie użytkownika testowego rozwiązania Adobe Creative Cloud

Aby użytkownicy usługi Azure AD mogli logować się do rozwiązania Adobe Creative Cloud, muszą być aprowizowani w rozwiązaniu Adobe Creative Cloud. W przypadku rozwiązania Adobe Creative Cloud aprowizowanie jest zadaniem ręcznym.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aby aprowizować konta użytkowników, wykonaj następujące czynności:

1. Zaloguj się w witrynie konsoli administracyjnej [Adobe Admin Console](https://adminconsole.adobe.com) jako administrator.

2. W konsoli firmy Adobe dodaj użytkownika z identyfikatorem Federated ID i przypisz go do profilu produktu. Aby uzyskać szczegółowe informacje na temat dodawania użytkowników, zobacz [Add users in Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers) (Dodawanie użytkowników w konsoli administracyjnej firmy Adobe). 

3. Na tym etapie wpisz swój adres e-mail/nazwę upn w formularzu logowania Adobe i naciśnij klawisz Tab. Powinien nastąpić sfederowany powrót do usługi Azure AD:
   * Program Web access: www\.adobe.com > Zaloguj się
   * W narzędziu aplikacji klasycznej > logowanie
   * W aplikacji > pomoc > logowanie

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Adobe Creative Cloud w panelu dostępu powinno nastąpić automatyczne zalogowanie do rozwiązania Adobe Creative Cloud, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Konfigurowanie domeny (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Konfiguracja oprogramowania Microsoft Azure do użytku z funkcją Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)
