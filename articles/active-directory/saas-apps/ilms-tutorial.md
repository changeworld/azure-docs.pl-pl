---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą iLMS | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee12bfc8a79b6abcfdf2978d4e640c34f801e346
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989705"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Samouczek: Integracja iLMS z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować iLMS w usłudze Azure Active Directory (Azure AD). W ramach iLMS integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do iLMS.
* Umożliwianie użytkownikom można automatycznie zalogowany do iLMS za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* iLMS logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. obsługuje iLMS **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-ilms-from-the-gallery"></a>Dodawanie iLMS z galerii

Aby skonfigurować integrację iLMS w usłudze Azure AD, należy dodać iLMS z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **iLMS** w polu wyszukiwania.
1. Wybierz **iLMS** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą iLMS za pomocą użytkownika testu o nazwie **Britta Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w iLMS.

Aby skonfigurować i testowanie logowania jednokrotnego usługi Azure AD za pomocą iLMS, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego iLMS](#configure-ilms-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego iLMS](#create-ilms-test-user)**  — aby mają odpowiednika w pozycji Britta simon w iLMS połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **iLMS** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** strony, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, wprowadź wartości dla następujących pól:

    a. W **identyfikator** pole tekstowe, Wklej **identyfikator** wartości w przypadku kopiowania z **usługodawcy** sekcji Ustawienia języka SAML w portalu administracyjnym iLMS.

    b. W **adres URL odpowiedzi** pole tekstowe, Wklej **(adres URL punktu końcowego)** wartości w przypadku kopiowania z **usługodawcy** sekcji Ustawienia języka SAML w portalu administracyjnym iLMS o następującym wzorcem `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W **adres URL logowania** pole tekstowe, Wklej **(adres URL punktu końcowego)** wartości w przypadku kopiowania z **usługodawcy** sekcji Ustawienia języka SAML w portalu administracyjnym iLMS jako `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Aby włączyć JIT inicjowania obsługi administracyjnej, aplikacja iLMS oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe Atrybuty użytkownika.

    > [!NOTE]
    > Należy włączyć **Utwórz konto użytkownika Un-recognized** w iLMS do mapowania tych atrybutów. Postępuj zgodnie z instrukcjami [tutaj](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) i Uzyskaj konfiguracji atrybutów.

1. Ponadto powyżej iLMS aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Name (Nazwa) | Atrybut źródłowy|
    | --------|------------- |
    | Dzielenie | user.department |
    | region | User.state |
    | Dział | user.jobtitle |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

1. Na **Konfigurowanie iLMS** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-ilms-sso"></a>Konfigurowanie iLMS logowania jednokrotnego

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **portalu administracyjnego iLMS** jako administrator.

2. Kliknij przycisk **SSO:SAML** w obszarze **ustawienia** kartę, aby otworzyć ustawienia języka SAML i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/1.png)

3. Rozwiń **usługodawcy** sekcji a następnie skopiuj **identyfikator** i **(adres URL punktu końcowego)** wartość.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/2.png) 

4. W obszarze **dostawcy tożsamości** kliknij **Importowanie metadanych**.

5. Wybierz **metadanych Federacji** plik pobrany z witryny Azure portal z **certyfikat podpisywania SAML** sekcji.

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

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do iLMS.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **iLMS**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

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

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka iLMS w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze iLMS, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
