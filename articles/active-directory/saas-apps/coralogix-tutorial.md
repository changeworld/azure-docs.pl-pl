---
title: 'Samouczek: integracja Azure Active Directory z usługą Coralogix | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8589c366c029ab51c7cd740a1b63cff7c0481a51
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158464"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Samouczek: integracja Azure Active Directory z usługą Coralogix

Z tego samouczka dowiesz się, jak zintegrować platformę Coralogix z usługą Azure Active Directory (Azure AD).
Integracja platformy Coralogix z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do platformy Coralogix.
* Możesz umożliwić użytkownikom automatyczne logowanie się do usługi Coralogix (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z platformą Coralogix, potrzebne są następujące elementy:

- Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [miesięczną wersję próbną](https://azure.microsoft.com/pricing/free-trial/).
- Subskrypcja z włączonym logowaniem jednokrotnym (Coralogix). 

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Coralogix obsługuje logowanie jednokrotne zainicjowane przez usługę SP.

## <a name="add-coralogix-from-the-gallery"></a>Dodaj Coralogix z galerii

Aby skonfigurować integrację programu Coralogix z usługą Azure AD, najpierw Dodaj Coralogix z galerii do listy zarządzanych aplikacji SaaS.

Aby dodać Coralogix z galerii, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz ikonę **Azure Active Directory** .

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Coralogix**. W okienku wyników wybierz pozycję **Coralogix** , a następnie wybierz przycisk **Dodaj** , aby dodać aplikację.

     ![Platforma Coralogix na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Coralogix na podstawie użytkownika testowego o nazwie Britta Simon.
Aby logowanie jednokrotne działało, należy nawiązać połączenie między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Coralogix.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Coralogix, najpierw wykonaj następujące bloki konstrukcyjne:

1. [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Skonfiguruj logowanie](#configure-coralogix-single-sign-on) jednokrotne w usłudze Coralogix, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. [Utwórz użytkownika testowego Coralogix](#create-a-coralogix-test-user) , aby uzyskać odpowiednik Britta Simon w Coralogix, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. [Przetestuj logowanie jednokrotne](#test-single-sign-on), aby sprawdzić działanie konfiguracji.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Coralogix, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Coralogix** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą języka SAML** wybierz ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Domena i adresy URL platformy Coralogix — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W polu **adres URL logowania** wprowadź adres URL z następującym wzorcem: `https://<SUBDOMAIN>.coralogix.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wprowadź adres URL, na przykład:
    
    `https://api.coralogix.com/saml/metadata.xml`

    lub

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zaktualizuj wartość przy użyciu rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta Coralogix](mailto:info@coralogix.com) , aby uzyskać wartość. Można również odwołać się do wzorców w sekcji **podstawowe informacje o konfiguracji SAML** w Azure Portal.

5. Aplikacja Coralogix oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz przycisk **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika** .

    ![image](common/edit-attribute.png)

6. W sekcji **oświadczenia użytkownika** w oknie dialogowym **atrybuty użytkownika** Edytuj oświadczenia przy użyciu ikony **Edytuj** . Możesz również dodać oświadczenia za pomocą polecenia **Dodaj nowe oświadczenie** , aby skonfigurować ATRYBUT tokenu SAML, jak pokazano na poprzedniej ilustracji. Następnie wykonaj następujące kroki:
    
    a. Wybierz **ikonę Edytuj** , aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkowników** .

    obraz ![obrazu](./media/coralogix-tutorial/tutorial_usermail.png) ![](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Z listy **Format identyfikatora** wybierz pozycję **Adres e-mail**.

    d. Z listy **Atrybut źródłowy** wybierz pozycję **user.mail**.

    d. Wybierz pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **plik XML metadanych Federacji** z określonych opcji zgodnie z wymaganiami. Następnie zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

8. W sekcji **Konfiguracja Coralogix** skopiuj odpowiednie adresy URL.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-coralogix-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Coralogix

Aby skonfigurować Logowanie jednokrotne po stronie **Coralogix** , Wyślij pobrane **XML metadanych Federacji** i skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej Coralogix](mailto:info@coralogix.com). Upewnij się, że połączenie SSO protokołu SAML zostało prawidłowo ustawione na obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. W górnej części ekranu wybierz pozycję **nowy użytkownik**.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **użytkownik** wykonaj następujące czynności.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wprowadź "brittasimon@yourcompanydomain.extension". Na przykład w tym przypadku możesz wprowadzić "brittasimon@contoso.com".

    d. Zaznacz pole wyboru **Pokaż hasło** , a następnie zanotuj wartość wyświetlaną w polu **hasło** .

    d. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do platformy Coralogix.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Coralogix**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Coralogix**.

    ![Link platformy Coralogix na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Wybierz przycisk **Dodaj użytkownika** . Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy. Następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę dla użytkownika z listy. Następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz przycisk **Przypisz** .

### <a name="create-a-coralogix-test-user"></a>Tworzenie użytkownika testowego Coralogix

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na platformie Coralogix. Współpracuj z [zespołem pomocy technicznej Coralogix](mailto:info@coralogix.com) , aby dodać użytkowników z platformy Coralogix. Przed użyciem logowania jednokrotnego należy utworzyć i aktywować użytkowników.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu aplikacji.

Po wybraniu kafelka Coralogix w portalu aplikacji należy automatycznie zalogować się do Coralogix. Aby uzyskać więcej informacji na temat portalu aplikacji, zobacz [co to jest portal usługi webapps?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

