---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą przesączyć | Dokumentacja firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i przesączyć.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094594"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą przesączyć

W tym samouczku dowiesz się, jak zintegrować przesączyć w usłudze Azure Active Directory (Azure AD).

Ta integracja zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do przesączyć.
* Aby umożliwić użytkownikom automatyczne logowanie do przesączyć (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą przesączyć, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja przesączyć ma logowanie jednokrotne włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka możesz skonfigurować i testowanie usługi Azure AD rejestracji jednokrotnej w środowisku testowym.

* Przesączyć obsługuje logowanie Jednokrotne zainicjowane przez Dostawcę i inicjowane przez dostawcę tożsamości.

## <a name="add-percolate-from-the-gallery"></a>Dodaj przesączyć z galerii

Aby skonfigurować integrację z przesączyć w usłudze Azure AD, należy dodać przesączyć z galerii z listą zarządzanych aplikacji SaaS.

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**:

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **nową aplikację** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **przesączyć**. Wybierz **przesączyć** w wynikach wyszukiwania, a następnie wybierz **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługę Azure AD logowanie jednokrotne za pomocą przesączyć przy użyciu użytkownika testu o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiedniego użytkownika w przesączyć.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą przesączyć, należy wykonać następujące czynności:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  do włączenia tej funkcji dla użytkowników.
2. **[Konfigurowanie przesączyć logowania jednokrotnego](#configure-percolate-single-sign-on)**  na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowania jednokrotnego.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  włączyć usługi Azure AD logowanie jednokrotne dla użytkownika.
5. **[Tworzenie użytkownika testowego przesączyć](#create-a-percolate-test-user)**  połączony na reprezentację w postaci usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  można sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz usługę Azure AD logowania jednokrotnego w witrynie Azure portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą przesączyć, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **przesączyć** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**:

    ![Wybierz opcję logowania jednokrotnego](common/select-sso.png)

2. W **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne:

    ![Wybierz jedną metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okno dialogowe:

    ![Ikona edycji](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** okno dialogowe, nie trzeba podejmować żadnych działań, aby skonfigurować aplikację w trybie inicjowane przez dostawcę tożsamości. Aplikacja jest już zintegrowana z platformą Azure.

    ![Przesączyć domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/preintegrated.png)

5. Aby skonfigurować aplikację w trybie zainicjowanego przez dostawcę usług, zaznacz **Ustaw dodatkowe adresy URL** i w **adres URL logowania** wprowadź **https://percolate.com/app/login** :

   ![Przesączyć domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/metadata-upload-additional-signon.png)
6. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** stronie **certyfikat podpisywania SAML** zaznacz **kopiowania** ikonę, aby skopiować **adres Url metadanych Federacji aplikacji** . Zapisz ten adres URL.

    ![Skopiuj adres URL metadanych Federacji aplikacji](common/copy-metadataurl.png)

7. W **Konfigurowanie przesączyć** sekcji, skopiuj odpowiednie adresy URL, w zależności od wymagań.

    ![Skopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Usługa Azure AD identyfikator**.

    1. **Adres URL wylogowania**.

### <a name="configure-percolate-single-sign-on"></a>Konfigurowanie przesączyć logowania jednokrotnego

1. W nowym oknie przeglądarki internetowej Zaloguj się do przesączyć jako administrator.

2. W lewej części strony głównej wybierz **ustawienia**:
    
    ![Wybierz ustawienia](./media/percolate-tutorial/configure01.png)

3. W okienku po lewej stronie wybierz **logowania jednokrotnego** w obszarze **organizacji**:

    ![Wybierz opcję logowania jednokrotnego w ramach organizacji](./media/percolate-tutorial/configure02.png)

    1. W **adres URL logowania** pole, Wklej **adres URL logowania** wartością skopiowaną z witryny Azure portal.

    1. W **identyfikator jednostki** pole, Wklej **usługi Azure AD identyfikator** wartością skopiowaną z witryny Azure portal.

    1. Otwórz w Notatniku certyfikat zakodowany base-64, pobrany z witryny Azure portal. Skopiuj jego zawartość i wklej go do **x509 certyfikaty** pole.

    1. W **atrybut E-mail** wprowadź **emailaddress**.

    1. **Adres URL metadanych dostawcy tożsamości** pole pole jest opcjonalne. Jeśli został skopiowany **adres Url metadanych Federacji aplikacji** w witrynie Azure portal, wkleić go w tym polu.

    1. W **AuthNRequests powinny być podpisane?** listy wybierz **nie**.

    1. W **włączyć logowanie Jednokrotne automatycznej aprowizacji** listy wybierz **nie**.

    1. Wybierz pozycję **Zapisz**.

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

W tej sekcji można udostępnić Britta Simon do jej udostępnienie Przesączyć za pomocą usługi Azure AD logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **przesączyć**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **przesączyć**.

    ![Lista aplikacji](common/all-applications.png)

3. W okienku po lewej stronie wybierz **użytkowników i grup**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Użytkownicy i grupy](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** listy użytkowników, a następnie kliknij **wybierz** znajdujący się u dołu ekranu.

6. Jeśli oczekujesz wartość roli dla asercji SAML **wybierz rolę** okna dialogowego wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-percolate-test-user"></a>Tworzenie użytkownika testowego przesączyć

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do przesączyć, należy dodać je do przesączyć. Należy je dodać ręcznie.

Aby utworzyć konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do przesączyć jako administrator.

2. W okienku po lewej stronie wybierz **użytkowników** w obszarze **organizacji**. Wybierz **nowi użytkownicy**:

    ![Wybierz nowego użytkownika](./media/percolate-tutorial/configure03.png)

3. Na **tworzenia użytkowników** strony, wykonaj następujące kroki.

    ![Utwórz stronę użytkownicy](./media/percolate-tutorial/configure04.png)

    1. W **E-mail** wprowadź adres e-mail użytkownika. Na przykład brittasimon@contoso.com.

    1. W **imię i nazwisko** wprowadź nazwę użytkownika. Na przykład **Brittasimon**.

    1. Wybierz **tworzenia użytkowników**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz należy przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego za pomocą panelu dostępu.

Po wybraniu kafelka przesączyć w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze przesączyć wystąpienia, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)