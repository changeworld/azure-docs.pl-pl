---
title: 'Samouczek: integracja Azure Active Directory z usługą systemu Zendesk | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30a57258dfe112c1b2d03bc43738c92272a156f9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160846"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Samouczek: integracja Azure Active Directory z usługą systemu Zendesk

Z tego samouczka dowiesz się, jak zintegrować aplikację Zendesk z usługą Azure Active Directory (Azure AD).
Integrowanie aplikacji Zendesk z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Zendesk.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Zendesk (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Zendesk są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Zendesk z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Zendesk obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

* Aplikacja Zendesk obsługuje [**zautomatyzowaną** aprowizację użytkowników](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Dodawanie aplikacji Zendesk z galerii

Aby skonfigurować integrację aplikacji Zendesk z usługą Azure AD, musisz dodać aplikację Zendesk z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Zendesk z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zendesk**, wybierz pozycję **Zendesk** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Zendesk na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Zendesk, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Zendesk.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacji Zendesk, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Zendesk](#configure-zendesk-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Zendesk](#create-zendesk-test-user)** — aby w systemie Zendesk utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacji Zendesk, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Zendesk** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje dotyczące domeny i adresów URL logowania jednokrotnego aplikacji Zendesk](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.zendesk.com`

   b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.zendesk.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja Zendesk oczekuje asercji SAML w konkretnym formacie. Nie istnieją obowiązkowe atrybuty protokołu SAML, ale opcjonalnie możesz zarządzać za pomocą sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Z listy **Atrybut źródłowy** wybierz odpowiednią wartość atrybutu.

    f. Kliknij przycisk **OK**.

    g. Kliknij przycisk **Save** (Zapisz).

    > [!NOTE]
    > Atrybuty rozszerzenia umożliwiają dodawanie atrybutów, które nie są domyślnie dostępne w usłudze Azure AD. Kliknij pozycję [Atrybuty użytkownika, które można ustawić w protokole SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-), aby uzyskać pełną listę atrybutów protokołu SAML, które akceptuje aplikacja **Zendesk**.

7. Na stronie **Ustaw logowanie jednokrotne przy użyciu protokołu SAML** w sekcji **Certyfikat podpisywania protokołu SAML** skopiuj **Odcisk palca** i zapisz go na swoim komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-certificatethumbprint.png)

8. W sekcji **Konfigurowanie aplikacji Zendesk** skopiuj odpowiednie adresy URL zgodnie ze swoimi wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

9. Istnieją dwa sposoby, za pomocą których można skonfigurować aplikację Zendesk — automatycznie i ręcznie.
  
10. Aby zautomatyzować konfigurację w aplikacji Zendesk, musisz zainstalować **rozszerzenie przeglądarki do bezpiecznego logowania Moje aplikacje**, klikając pozycję **Zainstaluj rozszerzenie**.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj aplikację Zendesk**. Spowoduje to bezpośrednie przejście do aplikacji Zendesk. W tym miejscu podaj poświadczenia administratora w celu zalogowania się do aplikacji Zendesk. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje sekcję **Konfigurowanie logowania jednokrotnego aplikacji Zendesk**.

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Zendesk

1. Jeśli chcesz ręcznie skonfigurować aplikację Zendesk, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmowej aplikacji Zendesk jako administrator, a następnie wykonaj następujące kroki:

2. Kliknij przycisk **Administrator**.

3. W okienku nawigacji po lewej stronie kliknij pozycję **Ustawienia**, a następnie kliknij pozycję **Zabezpieczenia**.

4. Na stronie **Zabezpieczenia** wykonaj następujące kroki:

    ![Bezpieczeństwo](././media/zendesk-tutorial/ic773089.png "Zabezpieczenia")

    ![Logowanie jednokrotne](././media/zendesk-tutorial/ic773090.png "Logowanie jednokrotne")

    a. Kliknij kartę **Administratorzy i agenci**.

    b. Wybierz pozycję **Logowanie jednokrotne (SSO) i protokół SAML**, a następnie wybierz pozycję **SAML**.

    d. W polu tekstowym **Adres URL logowania jednokrotnego protokołu SAML** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Adres URL zdalnego wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    e. W polu tekstowym **Odcisk palca certyfikatu** wklej wartość **odcisku palca** certyfikatu skopiowaną z witryny Azure Portal.

    f. Kliknij przycisk **Save** (Zapisz).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon\@yourcompanydomain. Extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Zendesk.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Zendesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz ciąg **Zendesk**.

    ![Link aplikacji Zendesk na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-zendesk-test-user"></a>Tworzenie użytkownika testowego aplikacji Zendesk

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w aplikacji Zendesk. Aplikacja Zendesk obsługuje automatyczną aprowizację użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](Zendesk-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

> [!NOTE]
> Konta **użytkownika końcowego** są automatycznie aprowizowane podczas logowania. Konta **agenta** i **administratora** muszą zostać aprowizowane ręcznie w aplikacji **Zendesk** przed zalogowaniem.

1. Zaloguj się do swojej dzierżawy **Zendesk**.

2. Wybierz kartę **Lista klientów**.

3. Wybierz kartę **Użytkownik**, a następnie kliknij przycisk **Dodaj**.

    ![Dodaj użytkownika](././media/zendesk-tutorial/ic773632.png "Dodawanie użytkownika")
4. Wpisz **nazwę** i **adres e-mail** istniejącego konta usługi Azure AD, które chcesz aprowizować, a następnie kliknij przycisk **Zapisz**.

    ![Nowy użytkownik](././media/zendesk-tutorial/ic773633.png "Nowy użytkownik")

> [!NOTE]
> Aby aprowizować konta użytkowników usługi AAD, możesz użyć dowolnych innych narzędzi do tworzenia kont użytkowników aplikacji Zendesk lub interfejsów API udostępnianych przez aplikację Zendesk.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Zendesk w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Zendesk, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie aprowizacji użytkowników](zendesk-provisioning-tutorial.md)