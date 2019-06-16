---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą ITRP | Dokumentacja firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: c3be09c998399cdca886f207bf5fc621bf51d8dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099597"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą ITRP

W tym samouczku dowiesz się, jak zintegrować ITRP w usłudze Azure Active Directory (Azure AD).
Ta integracja zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do ITRP.
* Aby umożliwić użytkownikom automatyczne logowanie do ITRP (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą ITRP, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja ITRP, która ma logowanie jednokrotne włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka możesz skonfigurować i testowanie usługi Azure AD rejestracji jednokrotnej w środowisku testowym.

* ITRP obsługuje logowanie Jednokrotne zainicjowane przez SP.

## <a name="add-itrp-from-the-gallery"></a>Dodaj ITRP z galerii

Aby skonfigurować integrację ITRP w usłudze Azure AD, należy dodać ITRP z galerii z listą zarządzanych aplikacji SaaS.

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**:

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **nową aplikację** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **ITRP**. Wybierz **ITRP** w wynikach wyszukiwania, a następnie wybierz **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługę Azure AD logowanie jednokrotne za pomocą ITRP przy użyciu użytkownika testu o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiedniego użytkownika w ITRP.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą ITRP, należy wykonać następujące czynności:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  do włączenia tej funkcji dla użytkowników.
2. **[Konfigurowanie ITRP logowania jednokrotnego](#configure-itrp-single-sign-on)**  na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowania jednokrotnego.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  włączyć usługi Azure AD logowanie jednokrotne dla użytkownika.
5. **[Tworzenie użytkownika testowego ITRP](#create-an-itrp-test-user)**  połączony na reprezentację w postaci usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  można sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz usługę Azure AD logowania jednokrotnego w witrynie Azure portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z ITRP, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/), na stronie ITRP integracji aplikacji wybierz **logowanie jednokrotne**:

    ![Wybierz opcję logowania jednokrotnego](common/select-sso.png)

2. W **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne:

    ![Wybierz jedną metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okno dialogowe:

    ![Ikona edycji](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** okna dialogowego pole, wykonaj następujące kroki.

    ![Podstawowy plik konfiguracji SAML, okno dialogowe](common/sp-identifier.png)

    1. W **adres URL logowania** wprowadź adres URL, w tym wzorcu:
    
       `https://<tenant-name>.itrp.com`

    1. W **identyfikator jednostki** wprowadź adres URL, w tym wzorcu:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Te wartości symboli zastępczych. Należy użyć rzeczywisty adres URL logowania i identyfikator. Skontaktuj się z pomocą [zespołu pomocy technicznej ITRP](https://www.itrp.com/support) można pobrać wartości. Może również odnosić się do wzorców objętego **podstawową konfigurację protokołu SAML** okno dialogowe, w witrynie Azure portal.

5. W **certyfikat podpisywania SAML** zaznacz **Edytuj** ikonę, aby otworzyć **certyfikat podpisywania SAML** okno dialogowe:

    ![Ikona edycji](common/edit-certificate.png)

6. W **certyfikat podpisywania SAML** okno dialogowe, kopia **odcisk palca** wartość i zapisz go:

    ![Skopiuj wartość odcisku palca](common/copy-thumbprint.png)

7. W **Konfigurowanie ITRP** sekcji, skopiuj odpowiednie adresy URL, w zależności od wymagań:

    ![Skopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Usługa Azure AD identyfikator**.

    1. **Adres URL wylogowania**.

### <a name="configure-itrp-single-sign-on"></a>Konfigurowanie ITRP logowania jednokrotnego

1. W nowym oknie przeglądarki internetowej Zaloguj się do witryny firmy ITRP jako administrator.

1. W górnej części okna wybierz **ustawienia** ikony:

    ![Ikona ustawienia](./media/itrp-tutorial/ic775570.png "ikonę ustawienia")

1. W okienku po lewej stronie wybierz **logowania jednokrotnego**:

    ![Wybierz opcję logowania jednokrotnego](./media/itrp-tutorial/ic775571.png "wybierz logowania jednokrotnego")

1. W **logowania jednokrotnego** konfiguracji sekcji, wykonaj następujące kroki.

    ![Single Sign-On sekcję](./media/itrp-tutorial/ic775572.png "sekcji logowania jednokrotnego")

    ![Single Sign-On sekcję](./media/itrp-tutorial/ic775573.png "sekcji logowania jednokrotnego")

    1. Wybierz **włączone**.

    1. W **adres URL wylogowania zdalnego** pole, Wklej **adres URL wylogowania** wartością skopiowaną z witryny Azure portal.

    1. W **adres URL logowania jednokrotnego SAML** pole, Wklej **adres URL logowania** wartością skopiowaną z witryny Azure portal.

    1. W **odcisk palca certyfikatu** pole, Wklej **odcisk palca** wartość certyfikatu, który został skopiowany z witryny Azure portal.

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

W tej sekcji można udostępnić Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej ITRP.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **ITRP**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **ITRP**.

    ![Lista aplikacji](common/all-applications.png)

3. W okienku po lewej stronie wybierz **użytkowników i grup**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** listy użytkowników, a następnie kliknij **wybierz** znajdujący się u dołu okna.

6. Jeśli oczekujesz wartość roli dla asercji SAML **wybierz rolę** okna dialogowego wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **wybierz** znajdujący się u dołu okna.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-an-itrp-test-user"></a>Tworzenie użytkownika testowego ITRP

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do ITRP, należy dodać je do ITRP. Należy je dodać ręcznie.

Aby utworzyć konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do dzierżawy ITRP.

1. W górnej części okna wybierz **rekordów** ikony:

    ![Ikona rekordów](./media/itrp-tutorial/ic775575.png "ikonę rekordów")

1. Wybierz z menu **osób**:

    ![Wybierz osoby](./media/itrp-tutorial/ic775587.png "wybierz osoby")

1. Wybierz znak plus ( **+** ) aby dodać nową osobę:

    ![Wybierz znak plus](./media/itrp-tutorial/ic775576.png "wybierz znak plus")

1. W **Dodawanie nowej osoby** okna dialogowego pole, wykonaj następujące kroki.

    ![Okno dialogowe Dodawanie nowej osoby](./media/itrp-tutorial/ic775577.png "okno dialogowe Dodawanie nowej osoby")

    1. Wprowadź nazwę i adres e-mail adres prawidłowy Azure konto usługi AD, który chcesz dodać.

    1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Można użyć dowolnego narzędzia tworzenia konta użytkownika lub interfejsów API dostarczonych przez ITRP można uaktywniać ich konta usługi Azure AD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz należy przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego za pomocą panelu dostępu.

Po wybraniu kafelka ITRP w panelu dostępu użytkownik powinien automatyczne logowanie do wystąpienia ITRP, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
