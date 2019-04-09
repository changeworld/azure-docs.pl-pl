---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą iLMS | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9bcb465f76e09675333e6e608249cba11f722e3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274659"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą iLMS

W tym samouczku dowiesz się, jak zintegrować iLMS w usłudze Azure Active Directory (Azure AD).
Integrowanie iLMS z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do iLMS.
* Aby umożliwić użytkownikom można automatycznie zalogowany do iLMS (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą iLMS, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* iLMS pojedynczego logowania jednokrotnego włączonych subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* obsługuje iLMS **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-ilms-from-the-gallery"></a>Dodawanie iLMS z galerii

Aby skonfigurować integrację iLMS w usłudze Azure AD, należy dodać iLMS z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać iLMS z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **iLMS**, wybierz opcję **iLMS** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![iLMS na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą iLMS w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w iLMS musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą iLMS, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie iLMS logowania jednokrotnego](#configure-ilms-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego iLMS](#create-ilms-test-user)**  — aby mają odpowiednika w pozycji Britta simon w iLMS połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z iLMS, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **iLMS** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![iLMS domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W **identyfikator** pole tekstowe, Wklej **identyfikator** wartości w przypadku kopiowania z **usługodawcy** sekcji Ustawienia języka SAML w portalu administracyjnym iLMS.

    b. W **adres URL odpowiedzi** pole tekstowe, Wklej **(adres URL punktu końcowego)** wartości w przypadku kopiowania z **usługodawcy** sekcji Ustawienia języka SAML w portalu administracyjnym iLMS o następującym wzorcem `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![iLMS domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/metadata-upload-additional-signon.png)

    W **adres URL logowania** pole tekstowe, Wklej **(adres URL punktu końcowego)** wartości w przypadku kopiowania z **usługodawcy** sekcji Ustawienia języka SAML w portalu administracyjnym iLMS jako `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

6. Aby włączyć JIT inicjowania obsługi administracyjnej, aplikacja iLMS oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Należy włączyć **Utwórz konto użytkownika Un-recognized** w iLMS do mapowania tych atrybutów. Postępuj zgodnie z instrukcjami [tutaj](http://support.inspiredelearning.com/customer/portal/articles/2204526) i Uzyskaj konfiguracji atrybutów.

7. Ponadto powyżej iLMS aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Name (Nazwa) | Atrybut źródłowy|
    | --------|------------- |
    | Dzielenie | user.department |
    | region | User.state |
    | department | user.jobtitle |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. Na **Konfigurowanie iLMS** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-ilms-single-sign-on"></a>Konfigurowanie iLMS logowania jednokrotnego

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **portalu administracyjnego iLMS** jako administrator.

2. Kliknij przycisk **SSO:SAML** w obszarze **ustawienia** kartę, aby otworzyć ustawienia języka SAML i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/1.png)

3. Rozwiń **usługodawcy** sekcji a następnie skopiuj **identyfikator** i **(adres URL punktu końcowego)** wartość.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/2.png) 

4. W obszarze **dostawcy tożsamości** kliknij **Importowanie metadanych**.

5. Wybierz **metadanych Federacji** plik pobrany z witryny Azure Portal z **certyfikat podpisywania SAML** sekcji.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Jeśli chcesz włączyć JIT inicjowania obsługi administracyjnej do tworzenia iLMS kont un-rozpoznaje użytkowników, wykonaj poniższe kroki:

    a. Sprawdź **utworzyć konto użytkownika nie jest rozpoznawanym**.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapowanie atrybutów w usłudze Azure AD za pomocą atrybutów w iLMS. W kolumnie atrybutów określenie nazwy atrybutów lub wartość domyślną.

    c. Przejdź do **reguły biznesowe** kartę i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/5.png)

    d. Sprawdź **Tworzenie regionów Un-recognized, działów i działów** utworzyć regionów, działów i działów, które jeszcze nie istnieją w czasie rejestracji jednokrotnej.

    e. Sprawdź **aktualizacji użytkownika profilu podczas logowania** do określenia, czy profil użytkownika jest aktualizowany za pomocą każdego logowania jednokrotnego.

    f. Jeśli **wartości puste aktualizacji dla innych pól obowiązkowych w profilu użytkownika** opcja jest zaznaczona, pola opcjonalne profilu, które są puste po zalogowaniu będą również powodować profilu iLMS użytkownika zawiera wartości puste dla tych pól.

    g. Sprawdź **Wyślij błąd wiadomość E-mail z powiadomieniem** i wprowadź adres e-mail użytkownika, w których chcesz otrzymywać wiadomość e-mail z powiadomieniem błędu.

7. Kliknij przycisk **Zapisz**, aby zapisać ustawienia.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/save.png)

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do iLMS.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **iLMS**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **iLMS**.

    ![Link iLMS na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-ilms-test-user"></a>Tworzenie użytkownika testowego iLMS

Aplikacja obsługuje tylko w czasie Inicjowanie obsługi użytkowników i uwierzytelnianie użytkowników są tworzone automatycznie w aplikacji. JIT będzie działać po kliknięciu **Utwórz konto użytkownika Un-recognized** wyboru podczas ustawienia konfiguracji protokołu SAML w portalu administracyjnym iLMS.

Jeśli musisz utworzyć ręcznie przez użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy iLMS jako administrator.

2. Kliknij przycisk **zarejestrowania użytkownika** w obszarze **użytkowników** kartę, aby otworzyć **zarejestrowania użytkownika** strony.

   ![Dodawanie pracownika](./media/ilms-tutorial/3.png)

3. Na **zarejestrowania użytkownika** strony, wykonaj następujące kroki.

    ![Dodawanie pracownika](./media/ilms-tutorial/create_testuser_add.png)

    a. W **imię** polu tekstowym Nazwa typu pierwszy, takich jak Britta.

    b. W **nazwisko** polu tekstowym wpisz nazwisko, takich jak Simon.

    c. W **identyfikator poczty E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak BrittaSimon@contoso.com.

    d. W **Region** listy rozwijanej wybierz wartość dla regionu.

    e. W **dzielenia** listy rozwijanej wybierz wartość dla działu.

    f. W **działu** listy rozwijanej wybierz wartość dla działu.

    g. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Możesz wysłać wiadomość e-mail dotycząca rejestracji użytkownika, wybierając **Wyślij wiadomość e-mail dotycząca rejestracji** pola wyboru.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka iLMS w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze iLMS, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)