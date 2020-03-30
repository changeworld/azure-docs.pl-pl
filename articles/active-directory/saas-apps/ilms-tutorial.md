---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą iLMS | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą iLMS.
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
ms.openlocfilehash: 50097aec1c4a003d3494029e8f25bb13b564f207
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944017"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Samouczek: Integracja usługi iLMS z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę iLMS z usługą Azure Active Directory (Azure AD). Podczas integrowania usługi iLMS z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do usługi iLMS.
* Włącz użytkownikom automatyczne logowanie do usługi iLMS za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać miesięczny bezpłatny okres próbny [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) iLMS.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. ILMS obsługuje jednostki SSO inicjowane przez **SP i IDP**

## <a name="adding-ilms-from-the-gallery"></a>Dodawanie iLMS z galerii

Aby skonfigurować integrację usługi iLMS z usługą Azure AD, należy dodać usługi iLMS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **iLMS** w polu wyszukiwania.
1. Wybierz **iLMS** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi iLMS przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usługach iLMS.

Aby skonfigurować i przetestować identyfikatory SSO usługi Azure AD za pomocą usługi iLMS, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne iLMS](#configure-ilms-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego iLMS](#create-ilms-test-user)** — aby mieć odpowiednik Britta Simon w iLMS, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **iLMS** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** **wprowadź** wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wklej wartość **identyfikatora** skopiowaną z sekcji **Usługodawca** w ustawieniach SAML w portalu administracyjnym iLMS.

    b. W polu tekstowym **Odpowiedz adres URL** wklej wartość **punktu końcowego (URL),** którą kopiujesz z sekcji **Usługodawca** w ustawieniach SAML w portalu administracyjnym iLMS o następującym wzorzec`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wklej wartość punktu **końcowego (URL),** którą kopiujesz z sekcji **Usługodawca** w ustawieniach SAML w portalu administracyjnym iLMS jako`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Aby włączyć inicjowanie obsługi administracyjnej JIT, aplikacja iLMS oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    > [!NOTE]
    > Aby zamapować te atrybuty, należy włączyć **opcję Utwórz nieuznane konto użytkownika** w uidlenie. Postępuj zgodnie z instrukcjami [tutaj,](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) aby uzyskać pomysł na konfigurację atrybutów.

1. Oprócz powyższej aplikacji iLMS oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa | Atrybut źródłowy|
    | --------|------------- |
    | Dywizji | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij przycisk **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie usługi iLMS** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-ilms-sso"></a>Konfigurowanie identyfikatora SYC iLMS

1. W innym oknie przeglądarki internetowej zaloguj się do **portalu administracyjnego iLMS** jako administrator.

2. Kliknij **przycisk SSO:SAML** na karcie **Ustawienia,** aby otworzyć ustawienia SAML i wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/1.png)

3. Rozwiń sekcję **Usługodawca** i skopiuj wartość **identyfikator** i **punkt końcowy (URL).**

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/2.png) 

4. W sekcji **Dostawca tożsamości** kliknij pozycję **Importuj metadane**.

5. Wybierz plik **metadanych federacji** pobrany z portalu Azure z sekcji **Certyfikat podpisywania SAML.**

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Jeśli chcesz włączyć inicjowanie obsługi administracyjnej JIT do tworzenia kont iLMS dla użytkowników un-recognize, wykonaj poniższe kroki:

    a. Zaznacz **pozycję Utwórz nieuznane konto użytkownika**.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapuj atrybuty w usłudze Azure AD za pomocą atrybutów w usługach iLMS. W kolumnie atrybutu określ nazwę atrybutów lub wartość domyślną.

    d. Przejdź do karty **Reguły biznesowe** i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/5.png)

    d. Zaznacz **pozycję Utwórz nieuznane regiony, dywizje i działy,** aby utworzyć regiony, działy i działy, które jeszcze nie istnieją w czasie logowania jednokrotnego.

    e. Zaznacz **opcję Aktualizuj profil użytkownika podczas logowania,** aby określić, czy profil użytkownika jest aktualizowany przy każdym logowaniu jednokrotnym.

    f. Jeśli zaznaczona jest opcja **Aktualizuj puste wartości dla pól nieobowiązkowych w profilu użytkownika,** opcjonalne pola profilu, które są puste po zalogowaniu, spowoduje również, że profil iLMS użytkownika będzie zawierał puste wartości dla tych pól.

    g. Sprawdź **wyślij wiadomość e-mail z powiadomieniem o błędzie** i wprowadź wiadomość e-mail użytkownika, w której chcesz otrzymać wiadomość e-mail z powiadomieniem o błędzie.

7. Kliknij przycisk **Zapisz**, aby zapisać ustawienia.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie Britta Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do usługi iLMS.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **iLMS**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-ilms-test-user"></a>Tworzenie użytkownika testowego iLMS

Aplikacja obsługuje just in time inicjowania obsługi administracyjnej użytkowników i po uwierzytelnianiu użytkownicy są tworzone w aplikacji automatycznie. JIT będzie działać, jeśli klikniesz pole wyboru **Utwórz nieuznane konto użytkownika** podczas ustawienia konfiguracji SAML w portalu administracyjnym iLMS.

Jeśli chcesz utworzyć użytkownika ręcznie, wykonaj poniższe czynności:

1. Zaloguj się do witryny firmy iLMS jako administrator.

2. Kliknij **pozycję Zarejestruj użytkownika** na karcie **Użytkownicy,** aby otworzyć stronę **Zarejestruj użytkownika.**

   ![Dodawanie pracownika](./media/ilms-tutorial/3.png)

3. Na stronie **Zarejestruj użytkownika** wykonaj następujące czynności.

    ![Dodawanie pracownika](./media/ilms-tutorial/create_testuser_add.png)

    a. W polu tekstowym **Imię** wpisz imię, takie jak Britta.

    b. W polu **tekstowym Nazwisko** wpisz nazwisko, takie jak Simon.

    d. W polu **tekstowym identyfikator wiadomości e-mail** wpisz BrittaSimon@contoso.comadres e-mail użytkownika w stylu .

    d. W **obszarze regionu** listy rozwijanej wybierz wartość dla regionu.

    e. Z listy rozwijanej **Podział** wybierz wartość podziału.

    f. Z listy rozwijanej **Dział** wybierz wartość działu.

    g. Kliknij przycisk **Zapisz**.

    > [!NOTE]
    > Możesz wysłać wiadomość rejestracyjną do użytkownika, zaznaczając pole wyboru **Wyślij pocztę rejestracyjną.**

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka iLMS w Panelu dostępu należy automatycznie zalogować się do iLMS, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
