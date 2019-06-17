---
title: 'Samouczek: integracja usługi Azure Active Directory z platformą Coralogix | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 721e0c40ec2e02dabee0681e01fea4182b906183
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67104641"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Samouczek: integracja usługi Azure Active Directory z platformą Coralogix

Z tego samouczka dowiesz się, jak zintegrować platformę Coralogix z usługą Azure Active Directory (Azure AD).
Integracja platformy Coralogix z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do platformy Coralogix.
* Aby umożliwić użytkownikom automatyczne logowanie do Coralogix (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z platformą Coralogix, potrzebne są następujące elementy:

- Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
- Coralogix logowanie jednokrotne włączone subskrypcji. 

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Coralogix obsługuje logowanie Jednokrotne zainicjowane przez SP.

## <a name="add-coralogix-from-the-gallery"></a>Dodaj Coralogix z galerii

Aby skonfigurować integrację Coralogix w usłudze Azure AD, najpierw dodać Coralogix z galerii z listą zarządzanych aplikacji SaaS.

Aby dodać Coralogix z galerii, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Coralogix**. Wybierz **Coralogix** w okienku wyników, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

     ![Platforma Coralogix na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Coralogix w oparciu o użytkownika testu o nazwie Britta Simon.
Dla logowania jednokrotnego do pracy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w Coralogix.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Coralogix, najpierw wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Konfigurowanie Coralogix logowania jednokrotnego](#configure-coralogix-single-sign-on) do konfigurowania pojedynczego ustawień logowania jednokrotnego na stronie aplikacji.
3. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. [Tworzenie użytkownika testowego Coralogix](#create-a-coralogix-test-user) mieć odpowiednikiem Britta Simon Coralogix połączonego z usługi Azure AD reprezentacja użytkownika.
6. [Przetestuj logowanie jednokrotne](#test-single-sign-on), aby sprawdzić działanie konfiguracji.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Coralogix, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Coralogix** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą języka SAML** wybierz ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** okna dialogowego pole, wykonaj następujące czynności:

    ![Domena i adresy URL platformy Coralogix — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W **adres URL logowania** wprowadź adres URL z następującym wzorcem: `https://<SUBDOMAIN>.coralogix.com`

    b. W **identyfikator jednostki** tekstu wprowadź adres URL, takich jak:
    
    `https://api.coralogix.com/saml/metadata.xml`

    lub

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwe. Zaktualizuj wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Coralogix](mailto:info@coralogix.com) można uzyskać wartość. Może również odnosić się do wzorców w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

5. Aplikacja Coralogix oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** przycisk, aby otworzyć **atrybutów użytkownika** okno dialogowe.

    ![image](common/edit-attribute.png)

6. W **oświadczenia użytkownika** sekcji **atrybutów użytkownika** okno dialogowe Edytuj oświadczenia za pomocą **Edytuj** ikony. Można również dodać oświadczenia, za pomocą **Dodaj nowe oświadczenie** skonfigurować atrybut tokenu SAML, jak pokazano na poprzedniej ilustracji. Następnie wykonaj następujące kroki:
    
    a. Wybierz **ikonę edycji** otworzyć **Zarządzanie oświadczenia użytkownika** okno dialogowe.

    ![obraz](./media/coralogix-tutorial/tutorial_usermail.png) ![obrazu](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Z listy **Format identyfikatora** wybierz pozycję **Adres e-mail**.

    c. Z listy **Atrybut źródłowy** wybierz pozycję **user.mail**.

    d. Wybierz pozycję **Zapisz**.

7. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** zaznacz **Pobierz** można pobrać **XML metadanych Federacji**  z danymi opcjami zgodnie z wymaganiami. Następnie zapisz go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

8. W **Konfigurowanie Coralogix** sekcji, skopiuj odpowiednie adresy URL.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-coralogix-single-sign-on"></a>Konfigurowanie Coralogix logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na **Coralogix** po stronie, Wyślij pobrany **XML metadanych Federacji** i skopiować adresy URL w witrynie Azure portal do [Coralogix zespołem pomocy technicznej](mailto:info@coralogix.com). Pozwalają zagwarantować, że połączenia logowania jednokrotnego SAML jest prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](common/users.png)

2. W górnej części ekranu wybierz **nowego użytkownika**.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W **użytkownika** okna dialogowego pole, wykonaj następujące kroki.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wprowadź "brittasimon@yourcompanydomain.extension." Na przykład, w tym przypadku możesz wprowadzić "brittasimon@contoso.com."

    c. Wybierz **hasło Show** pole wyboru, a następnie zanotuj tę wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do platformy Coralogix.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **Coralogix**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Coralogix**.

    ![Link platformy Coralogix na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

4. Wybierz **Dodaj użytkownika** przycisku. Następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okno dialogowe.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy. Następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę dla użytkownika z listy. Następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.

### <a name="create-a-coralogix-test-user"></a>Tworzenie użytkownika testowego Coralogix

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na platformie Coralogix. Praca z [zespołem pomocy technicznej Coralogix](mailto:info@coralogix.com) Aby dodać użytkowników na platformie Coralogix. Należy utworzyć i aktywować użytkowników przed użyciem logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji możesz przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu portalu MyApps.

Po wybraniu kafelka Coralogix w portalu MyApps powinny być automatycznie zarejestrowaniu w usłudze Coralogix. Aby uzyskać więcej informacji na temat portalu MyApps, zobacz [co to jest MyApps portal?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

