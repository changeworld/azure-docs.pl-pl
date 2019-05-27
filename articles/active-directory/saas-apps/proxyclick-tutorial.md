---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Proxyclick | Dokumentacja firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: fa146ad5a36cc74a65ec6d3dee98b2ef35bc65ad
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240408"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Proxyclick

W tym samouczku dowiesz się, jak zintegrować Proxyclick w usłudze Azure Active Directory (Azure AD).
Ta integracja zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do Proxyclick.
* Aby umożliwić użytkownikom automatyczne logowanie do Proxyclick (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Proxyclick, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz zarejestrować się w celu [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja Proxyclick, która ma logowanie jednokrotne włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka możesz skonfigurować i testowanie usługi Azure AD rejestracji jednokrotnej w środowisku testowym.

* Proxyclick obsługuje logowanie Jednokrotne zainicjowane przez Dostawcę i inicjowane przez dostawcę tożsamości.

## <a name="add-proxyclick-from-the-gallery"></a>Dodaj Proxyclick z galerii

Aby skonfigurować integrację Proxyclick w usłudze Azure AD, należy dodać Proxyclick z galerii z listą zarządzanych aplikacji SaaS.

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**:

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **nową aplikację** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Proxyclick**. Wybierz **Proxyclick** w wynikach wyszukiwania, a następnie wybierz **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługę Azure AD logowanie jednokrotne za pomocą Proxyclick przy użyciu użytkownika testu o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiedniego użytkownika w Proxyclick.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Proxyclick, należy wykonać następujące czynności:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  do włączenia tej funkcji dla użytkowników.
2. **[Konfigurowanie Proxyclick logowania jednokrotnego](#configure-proxyclick-single-sign-on)**  na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowania jednokrotnego.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  włączyć usługi Azure AD logowanie jednokrotne dla użytkownika.
5. **[Tworzenie użytkownika testowego Proxyclick](#create-a-proxyclick-test-user)**  połączony na reprezentację w postaci usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  można sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz usługę Azure AD logowania jednokrotnego w witrynie Azure portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Proxyclick, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/), na stronie Proxyclick integracji aplikacji wybierz **logowanie jednokrotne**:

    ![Wybierz opcję logowania jednokrotnego](common/select-sso.png)

2. W **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne:

    ![Wybierz metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okno dialogowe:

    ![Ikona edycji](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** okno dialogowe, jeśli chcesz skonfigurować aplikację w trybie inicjowane przez dostawcę tożsamości, wykonaj następujące kroki.

    ![Podstawowy plik konfiguracji SAML, okno dialogowe](common/idp-intiated.png)

    1. W **identyfikator** wprowadź adres URL, w tym wzorcu:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. W **adres URL odpowiedzi** wprowadź adres URL, w tym wzorcu:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Aby skonfigurować aplikację w trybie zainicjowanego przez dostawcę usług, zaznacz **Ustaw dodatkowe adresy URL**. W **adres URL logowania** wprowadź adres URL, w tym wzorcu:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Te wartości symboli zastępczych. Należy użyć rzeczywistego identyfikatora, adres URL odpowiedzi i adres URL logowania. Kroki w celu uzyskania tych wartości są opisane w dalszej części tego samouczka.

6. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** stronie **certyfikat podpisywania SAML** zaznacz **Pobierz** łącze obok **certyfikat (Base64)** , zgodnie z wymaganiami, a następnie Zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W **Konfigurowanie Proxyclick** sekcji, skopiuj odpowiednie adresy URL, w zależności od wymagań:

    ![Skopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Usługa Azure AD identyfikator**.

    1. **Adres URL wylogowania**.

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurowanie Proxyclick logowania jednokrotnego

1. W nowym oknie przeglądarki internetowej Zaloguj się do witryny firmy Proxyclick jako administrator.

2. Wybierz **konta u & stawienia**:

    ![Wybierz konto & Ustawienia](./media/proxyclick-tutorial/configure1.png)

3. Przewiń w dół do **integracje** i wybierz pozycję **SAML**:

    ![Wybieranie języka SAML](./media/proxyclick-tutorial/configure2.png)

4. W **SAML** sekcji, wykonaj następujące kroki.

    ![Sekcja SAML](./media/proxyclick-tutorial/configure3.png)

    1. Kopiuj **adres URL klienta SAML** wartość i wklej go w **adres URL odpowiedzi** pole w **podstawową konfigurację protokołu SAML** okno dialogowe, w witrynie Azure portal.

    1. Kopiuj **adresu URL przekierowania logowania jednokrotnego SAML** wartość i wklej go w **adres URL logowania** i **identyfikator** pola **podstawową konfigurację protokołu SAML** okno dialogowe w witrynie Azure portal.

    1. W **metoda żądania języka SAML** listy wybierz **przekierowania HTTP**.

    1. W **wystawcy** pole, Wklej **usługi Azure AD identyfikator** wartością skopiowaną z witryny Azure portal.

    1. W **URL punktu końcowego usługi SAML 2.0** pole, Wklej **adres URL logowania** wartością skopiowaną z witryny Azure portal.

    1. W programie Notatnik Otwórz plik certyfikatu, który został pobrany z witryny Azure portal. Wklej zawartość tego pliku do **certyfikatu** pole.

    1. Wybierz pozycję **Zapisz zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testu o nazwie Britta Simon w witrynie Azure portal.

1. W witrynie Azure portal wybierz **usługi Azure Active Directory** w okienku po lewej stronie wybierz **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**:

    ![Wybierz opcję Wszyscy użytkownicy](common/users.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu:

    ![Wybierz nowego użytkownika](common/new-user.png)

3. W **użytkownika** okna dialogowego pole, wykonaj następujące kroki.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **BrittaSimon @\<domenatwojejfirmy >.\< Rozszerzenia >** . (Na przykład BrittaSimon@contoso.com.)

    1. Wybierz **Pokaż hasło**i zanotuj wartość, która znajduje się w **hasło** pole.

    1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej Proxyclick.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **Proxyclick**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Proxyclick**.

    ![Lista aplikacji](common/all-applications.png)

3. W okienku po lewej stronie wybierz **użytkowników i grup**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** listy użytkowników, a następnie kliknij **wybierz** znajdujący się u dołu okna.

6. Jeśli oczekujesz wartość roli dla asercji SAML **wybierz rolę** okna dialogowego wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **wybierz** znajdujący się u dołu okna.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-proxyclick-test-user"></a>Tworzenie użytkownika testowego Proxyclick

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Proxyclick, należy dodać je do Proxyclick. Należy je dodać ręcznie.

Aby utworzyć konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy Proxyclick jako administrator.

1. Wybierz **współpracowników** w górnej części okna:

    ![Wybierz współpracowników](./media/proxyclick-tutorial/user1.png)

1. Wybierz **dodawać współpracowników**:

    ![Wybierz opcję Dodaj współpracowników](./media/proxyclick-tutorial/user2.png)

1. W **dodać współpracownika** sekcji, wykonaj następujące kroki.

    ![Dodaj sekcję współpracownika](./media/proxyclick-tutorial/user3.png)

    1. W **E-mail** wprowadź adres e-mail użytkownika. W tym przypadku **brittasimon\@contoso.com**.

    1. W **imię** Wprowadź imię użytkownika. W tym przypadku **Britta**.

    1. W **nazwisko** wprowadź nazwisko użytkownika. W tym przypadku **Simon**.

    1. Wybierz pozycję **Add User** (Dodaj użytkownika).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz należy przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego za pomocą panelu dostępu.

Po wybraniu kafelka Proxyclick w panelu dostępu użytkownik powinien automatyczne logowanie do wystąpienia Proxyclick, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

