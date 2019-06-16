---
title: 'Samouczek: integracja usługi Azure Active Directory z aplikacją LinkedIn Learning | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff3748213d8449704f7b1001469eb8f7a224498
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098069"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Samouczek: integracja usługi Azure Active Directory z aplikacją LinkedIn Learning

Z tego samouczka dowiesz się, jak zintegrować aplikację LinkedIn Learning z usługą Azure Active Directory (Azure AD).
Integracja aplikacji LinkedIn Learning z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji LinkedIn Learning.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji LinkedIn Learning (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją LinkedIn Learning, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji LinkedIn Learning z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja LinkedIn Learning obsługuje logowanie jednokrotne inicjowane za pomocą **SP oraz IDP**
* Aplikacja LinkedIn Learning obsługuje aprowizowanie użytkowników typu **Just In Time**

## <a name="adding-linkedin-learning-from-the-gallery"></a>Dodawanie aplikacji LinkedIn Learning z galerii

Aby skonfigurować integrację aplikacji LinkedIn Learning z usługą Azure AD, należy dodać aplikację LinkedIn Learning z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację LinkedIn Learning z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **LinkedIn Learning**, z listy wyników wybierz opcję **LinkedIn Learning** i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![LinkedIn Learning na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji omówimy konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD w aplikacji LinkedIn Learning w oparciu o użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji LinkedIn Learning.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji LinkedIn Learning, należy ukończyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji LinkedIn Learning](#configure-linkedin-learning-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji LinkedIn Learning](#create-linkedin-learning-test-user)**  — aby utworzyć odpowiednik użytkownika Britta Simon w aplikacji LinkedIn Learning, połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji LinkedIn Learning, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **LinkedIn Learning** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji LinkedIn](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wprowadź **Identyfikator jednostki** skopiowany z portalu LinkedIn. 

    b. W polu tekstowym **Adres URL odpowiedzi** wprowadź **Adres URL usługi Assertion Consumer Service (ACS)** skopiowany z portalu LinkedIn.

    c. Jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez dostawcę tożsamości**, kliknij opcję **Ustaw dodatkowe adresy URL** w sekcji **Podstawowa konfiguracja protokołu SAML**, w której można będzie określić adres URL logowania jednokrotnego. Aby utworzyć adres URL logowania, skopiuj **Adres URL usługi Assertion Consumer Service (ACS)** , po czym zamień ciąg /saml/ na /login/. Adres URL logowanie przybierze wówczas następującą postać:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji LinkedIn](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Wartości te nie są wartościami rzeczywistymi. Te wartości zostaną zastąpione rzeczywistym identyfikatorem i adresem URL odpowiedzi, co zostało wyjaśnione poniżej w sekcji **Konfigurowanie logowania jednokrotnego w aplikacji LinkedIn Learning** samouczka.

5. Aplikacja LinkedIn Learning oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych do konfiguracji atrybutów tokenu języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja LinkedIn Learning oczekuje, że atrybut **nameidentifier** będzie zamapowany na atrybut **user.mail**, dlatego należy zmodyfikować mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutów.

    ![image](common/edit-attribute.png)

6. Oprócz powyższych aplikacja LinkedIn Learning oczekuje jeszcze kilku atrybutów, które powinny zostać przekazane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:
    
    | Name (Nazwa) | Atrybut źródłowy |
    | ---------------| --------------- |
    | email  | user.mail  |
    | department  | user.department  |
    | firstname  | user.givenname  |
    | lastname  | user.surname  |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-linkedin-learning-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji LinkedIn Learning

1. W oddzielnym oknie przeglądarki internetowej zaloguj się do dzierżawy aplikacji LinkedIn Learning jako administrator.

2. W **Centrum kont** kliknij przycisk **Ustawienia globalne** w obszarze **Ustawienia**. Wybierz również opcję **Learning - Default** (Learning — domyślna) z listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Kliknij przycisk **LUB kliknij tutaj, aby załadować i skopiować poszczególne pola formularza**, a także skopiuj **identyfikator jednostki** oraz **adres URL usługi Assertion Consumer Service (ACS)** i wklej go w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Przejdź do sekcji **LinkedIn Admin Settings** (Ustawienia administratora LinkedIn). Przekaż plik XML pobrany z witryny Azure Portal, klikając opcję **Przekaż plik XML**.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Kliknij przycisk **On** (Włącz), aby włączyć funkcję logowania jednokrotnego. Stan funkcji logowania jednokrotnego zmieni się z **Not Connected** (Niepołączona) na **Connected** (Połączona)

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

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

W tej sekcji włączysz możliwość logowania jednokrotnego na platformie Azure dla użytkownika Britta Simon, udzielając mu dostępu do aplikacji LinkedIn Learning.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **LinkedIn Learning**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz **LinkedIn Learning**.

    ![Link do aplikacji LinkedIn Learning na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-linkedin-learning-test-user"></a>Tworzenie użytkownika testowego aplikacji LinkedIn Learning

Aplikacja LinkedIn Learning obsługuje aprowizowanie użytkowników typu Just In Time. Po uwierzytelnieniu użytkownicy są automatycznie tworzeni w aplikacji. Na stronie ustawień administracyjnych w portalu aplikacji LinkedIn Learning portal przerzuć przełącznik **Automatically Assign licenses** (Automatycznie przypisuj licencje), aby aktywować aprowizowanie typu Just In Time. Spowoduje to również przypisanie licencji użytkownikowi.

   ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka LinkedIn Learning w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji LinkedIn Learning, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

