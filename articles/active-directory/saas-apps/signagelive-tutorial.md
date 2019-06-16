---
title: 'Samouczek: Integracja usługi Azure Active Directory z platformą Signagelive | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą Signagelive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c31f201179f9f0eb7077ed275aae927a2a4fe376
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090721"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Samouczek: integracja usługi Azure Active Directory z platformą Signagelive

Z tego samouczka dowiesz się, jak zintegrować platformę Signagelive z usługą Azure Active Directory (Azure AD).
Integrowanie platformy Signagelive z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do platformy Signagelive.
* Aby umożliwić użytkownikom automatyczne logowanie do Signagelive (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z platformą Signagelive, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja pojedynczego logowania na — z włączonym Signagelive.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Signagelive obsługuje logowanie Jednokrotne zainicjowane przez SP.

## <a name="add-signagelive-from-the-gallery"></a>Dodaj Signagelive z galerii

Aby skonfigurować integrację Signagelive w usłudze Azure AD, najpierw dodać Signagelive z galerii z listą zarządzanych aplikacji SaaS.

Aby dodać Signagelive z galerii, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje** opcji.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Signagelive**. 

     ![Signagelive na liście wyników](common/search-new-app.png)

5. Wybierz **Signagelive** w okienku wyników, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z platformą Signagelive, korzystając z danych użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w Signagelive.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Signagelive, najpierw wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Konfigurowanie Signagelive logowania jednokrotnego](#configure-signagelive-single-sign-on) do konfigurowania pojedynczego ustawień logowania jednokrotnego na stronie aplikacji.
3. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. [Tworzenie użytkownika testowego Signagelive](#create-a-signagelive-test-user) mieć odpowiednikiem Britta Simon Signagelive połączonego z usługi Azure AD reprezentacja użytkownika.
6. [Przetestuj logowanie jednokrotne](#test-single-sign-on), aby sprawdzić działanie konfiguracji.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Signagelive, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Signagelive** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** wybierz opcję **Edytuj** otworzyć **podstawową konfigurację protokołu SAML** okno dialogowe.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    ![Domena i adresy URL platformy Signagelive — informacje dotyczące logowania jednokrotnego](common/sp-signonurl.png)

    W **adres URL logowania** wprowadź adres URL, który korzysta z następującego wzorca:  `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj wartość za pomocą adresu URL logowania rzeczywistych. Aby uzyskać wartość, skontaktuj się z pomocą [zespołem pomocy technicznej klienta Signagelive](mailto:support@signagelive.com) . Może również odnosić się do wzorców, które są wyświetlane w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

5. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** zaznacz **Pobierz** można pobrać **certyfikatu (Raw)** z podanych opcji dla Twojego zapotrzebowania. Następnie zapisz go na komputerze.

    ![Link pobierania certyfikatu](common/certificateraw.png)

6. W **Konfigurowanie Signagelive** sekcji, skopiuj adresy URL, które są potrzebne.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-signagelive-single-sign-on"></a>Konfigurowanie Signagelive logowanie jednokrotne

Aby skonfigurować logowanie jednokrotne na stronie Signagelive, Wyślij pobrany **certyfikatu (Raw)** i skopiować adresy URL w witrynie Azure portal do [zespołem pomocy technicznej Signagelive](mailto:support@signagelive.com). Pozwalają zagwarantować, że połączenia logowania jednokrotnego SAML jest prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W **użytkownika** okna dialogowego pole, wykonaj następujące kroki.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wprowadź "brittasimon@yourcompanydomain.extension". Na przykład, w tym przypadku możesz wprowadzić "BrittaSimon@contoso.com".

    c. Wybierz **hasło Show** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do platformy Signagelive.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **Signagelive**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Signagelive**.

    ![Link Signagelive na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

4. Wybierz **Dodaj użytkownika** przycisku. Następnie w **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W **użytkowników i grup** dialogowym **użytkowników** listy wybierz **Britta Simon**. Następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. Jeśli oczekiwane wartości roli w potwierdzenie SAML, a następnie, w **wybierz rolę** okna dialogowego wybierz odpowiednią rolę dla użytkownika z listy. Następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.

### <a name="create-a-signagelive-test-user"></a>Tworzenie użytkownika testowego Signagelive

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na platformie Signagelive. Praca z [zespołem pomocy technicznej Signagelive](mailto:support@signagelive.com) Aby dodać użytkowników na platformie Signagelive. Należy utworzyć i aktywować użytkowników przed użyciem logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji możesz przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu portalu MyApps.

Po wybraniu **Signagelive** kafelka w portalu MyApps możesz powinna istnieć możliwość automatycznej rejestracji. Aby uzyskać więcej informacji na temat portalu MyApps, zobacz [co to jest MyApps portal?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

