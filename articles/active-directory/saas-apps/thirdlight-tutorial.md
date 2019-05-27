---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą ThirdLight | Dokumentacja firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ThirdLight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 67c8dcfffd78d4c0114a96622235d6548627fa92
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236971"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą ThirdLight

W tym samouczku dowiesz się, jak zintegrować ThirdLight w usłudze Azure Active Directory (Azure AD). Ta integracja zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do ThirdLight.
* Aby umożliwić użytkownikom automatyczne logowanie do ThirdLight (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Jeśli chcesz dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą ThirdLight, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja ThirdLight, która ma logowanie jednokrotne włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka możesz skonfigurować i testowanie usługi Azure AD rejestracji jednokrotnej w środowisku testowym.

* ThirdLight obsługuje logowanie Jednokrotne zainicjowane przez SP.

## <a name="add-thirdlight-from-the-gallery"></a>Dodaj ThirdLight z galerii

Aby skonfigurować integrację ThirdLight w usłudze Azure AD, należy dodać ThirdLight z galerii z listą zarządzanych aplikacji SaaS.

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**:

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **nową aplikację** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **ThirdLight**. Wybierz **ThirdLight** w wynikach wyszukiwania, a następnie wybierz **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługę Azure AD logowanie jednokrotne za pomocą ThirdLight przy użyciu użytkownika testu o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiedniego użytkownika w ThirdLight.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą ThirdLight, należy wykonać następujące czynności:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  do włączenia tej funkcji dla użytkowników.
2. **[Konfigurowanie ThirdLight logowania jednokrotnego](#configure-thirdlight-single-sign-on)**  na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowania jednokrotnego.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  włączyć usługi Azure AD logowanie jednokrotne dla użytkownika.
5. **[Tworzenie użytkownika testowego ThirdLight](#create-a-thirdlight-test-user)**  połączony na reprezentację w postaci usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  można sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz usługę Azure AD logowania jednokrotnego w witrynie Azure portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z ThirdLight, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/), na stronie ThirdLight integracji aplikacji wybierz **logowanie jednokrotne**:

    ![Wybierz opcję logowania jednokrotnego](common/select-sso.png)

2. W **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne:

    ![Wybierz metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okno dialogowe:

    ![Ikona edycji](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** okna dialogowego pole, wykonaj następujące czynności.

    ![Podstawowy plik konfiguracji SAML, okno dialogowe](common/sp-identifier.png)

    1. W **adres URL logowania** wprowadź adres URL, w tym wzorcu:
    
          `https://<subdomain>.thirdlight.com/`

    1. W **identyfikator jednostki** wprowadź adres URL, w tym wzorcu:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Te wartości symboli zastępczych. Należy użyć rzeczywisty adres URL logowania i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej ThirdLight](https://www.thirdlight.com/support) można pobrać wartości. Może również odnosić się do wzorców objętego **podstawową konfigurację protokołu SAML** okno dialogowe, w witrynie Azure portal.

5. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** stronie **certyfikat podpisywania SAML** zaznacz **Pobierz** łącze obok **XML metadanych Federacji** , zgodnie z wymaganiami i Zapisz plik na komputerze:

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W **Konfigurowanie ThirdLight** sekcji, skopiuj odpowiednie adresy URL, w zależności od wymagań:

    ![Skopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Usługa Azure AD identyfikator**.

    1. **Adres URL wylogowania**.

### <a name="configure-thirdlight-single-sign-on"></a>Konfigurowanie ThirdLight logowania jednokrotnego

1. W nowym oknie przeglądarki internetowej Zaloguj się do witryny firmy ThirdLight jako administrator.

1. Przejdź do **konfiguracji** > **administrowania systemem** > **SAML2**:

    ![Administrowanie systemem](./media/thirdlight-tutorial/ic805843.png "Administracja systemu")

1. W sekcji konfiguracji SAML2 wykonaj następujące kroki.
  
    ![Sekcja konfiguracji SAML2](./media/thirdlight-tutorial/ic805844.png "SAML2 sekcji konfiguracji")

    1. Wybierz **Włącz SAML2 logowania jednokrotnego**.

    1. W obszarze **źródło metadanych tożsamości**, wybierz opcję **Load Metadata dostawcy tożsamości z pliku XML**.

    1. Otwórz plik metadanych, który został pobrany z witryny Azure portal w poprzedniej sekcji. Skopiuj zawartość pliku i wklej go do **XML metadanych dostawcy tożsamości** pole.

    1. Wybierz **ustawień zapisywanie SAML2**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testu o nazwie Britta Simon w witrynie Azure portal.

1. W witrynie Azure portal wybierz **usługi Azure Active Directory** w okienku po lewej stronie wybierz **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**:

    ![Wybierz opcję Wszyscy użytkownicy](common/users.png)

2. Wybierz **nowego użytkownika** w górnej części okna:

    ![Wybierz nowego użytkownika](common/new-user.png)

3. W **użytkownika** okna dialogowego pole, wykonaj następujące kroki.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **BrittaSimon @\<domenatwojejfirmy >.\< Rozszerzenia >** . (Na przykład BrittaSimon@contoso.com.)

    1. Wybierz **Pokaż hasło**i zanotuj wartość, która znajduje się w **hasło** pole.

    1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej ThirdLight.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **ThirdLight**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **ThirdLight**.

    ![Lista aplikacji](common/all-applications.png)

3. W okienku po lewej stronie wybierz **użytkowników i grup**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** listy użytkowników, a następnie kliknij **wybierz** znajdujący się u dołu okna.

6. Jeśli oczekujesz wartość roli dla asercji SAML **wybierz rolę** okna dialogowego wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **wybierz** znajdujący się u dołu okna.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-thirdlight-test-user"></a>Tworzenie użytkownika testowego ThirdLight

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do ThirdLight, należy dodać je do ThirdLight. Należy je dodać ręcznie.

Aby utworzyć konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy ThirdLight jako administrator.

1. Przejdź do **użytkowników** kartę.

1. Wybierz **użytkowników i grup**.

1. Wybierz **Dodaj nowego użytkownika**.

1. Wprowadź nazwę użytkownika, nazwę i opis i adres e-mail prawidłowy, usługa Azure AD konta chcesz aprowizować. Wybierz ustawienie wstępne lub grupy nowych elementów członkowskich.

1. Wybierz pozycję **Utwórz**.

> [!NOTE]
> Można użyć dowolnego narzędzia tworzenia konta użytkownika lub interfejsów API dostarczonych przez ThirdLight można uaktywniać ich konta usługi Azure AD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz należy przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego za pomocą panelu dostępu.

Po wybraniu kafelka ThirdLight w panelu dostępu użytkownik powinien automatyczne logowanie do wystąpienia ThirdLight, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
