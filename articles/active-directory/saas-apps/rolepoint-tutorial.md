---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą RolePoint | Dokumentacja firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i RolePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68d37f40-15da-45f5-a9e1-d53f78e786d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: d2f854c869ead8016a5437a4e40339d9455c9ad7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226414"
---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą RolePoint

W tym samouczku dowiesz się, jak zintegrować RolePoint w usłudze Azure Active Directory (Azure AD).
Ta integracja zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do RolePoint.
* Aby umożliwić użytkownikom automatyczne logowanie do RolePoint (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą RolePoint, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja RolePoint przy użyciu logowania jednokrotnego włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka możesz skonfigurować i testowanie usługi Azure AD rejestracji jednokrotnej w środowisku testowym.

* RolePoint obsługuje logowanie Jednokrotne zainicjowane przez SP.

## <a name="add-rolepoint-from-the-gallery"></a>Dodaj RolePoint z galerii

Aby skonfigurować integrację RolePoint w usłudze Azure AD, należy dodać RolePoint z galerii z listą zarządzanych aplikacji SaaS.

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**:

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **nową aplikację** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **RolePoint**. Wybierz **RolePoint** w wynikach wyszukiwania, a następnie wybierz **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługę Azure AD logowanie jednokrotne za pomocą RolePoint przy użyciu użytkownika testu o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiedniego użytkownika w RolePoint.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą RolePoint, należy wykonać następujące czynności:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  do włączenia tej funkcji dla użytkowników.
2. **[Konfigurowanie RolePoint logowania jednokrotnego](#configure-rolepoint-single-sign-on)**  na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowania jednokrotnego.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  włączyć usługi Azure AD logowanie jednokrotne dla użytkownika.
5. **[Tworzenie użytkownika testowego RolePoint](#create-a-rolepoint-test-user)**  połączony na reprezentację w postaci usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  można sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz usługę Azure AD logowania jednokrotnego w witrynie Azure portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z RolePoint, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/), na stronie RolePoint integracji aplikacji wybierz **logowanie jednokrotne**:

    ![Wybierz opcję logowania jednokrotnego](common/select-sso.png)

2. W **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne:

    ![Wybierz metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okno dialogowe:

    ![Ikona edycji](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** okna dialogowego pole, wykonaj następujące kroki.

    ![Podstawowy plik konfiguracji SAML, okno dialogowe](common/sp-identifier.png)

    1. W **adres URL logowania** wprowadź adres URL, w tym wzorcu:

       `https://<subdomain>.rolepoint.com/login`

    1. W **identyfikator jednostki** wprowadź adres URL, w tym wzorcu:

       `https://app.rolepoint.com/<instancename>`

    > [!NOTE]
    > Te wartości symboli zastępczych. Należy użyć rzeczywisty adres URL logowania i identyfikator. Zaleca się, że używasz unikatowej wartości ciągu w identyfikatorze. Skontaktuj się z pomocą [zespołem pomocy technicznej RolePoint](mailto:info@rolepoint.com) do uzyskania tych wartości. Może również odnosić się do wzorców objętego **podstawową konfigurację protokołu SAML** okno dialogowe, w witrynie Azure portal.

5. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** stronie **certyfikat podpisywania SAML** zaznacz **Pobierz** łącze obok **XML metadanych Federacji** , zgodnie z wymaganiami i Zapisz plik na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W **Konfigurowanie RolePoint** sekcji, skopiuj odpowiednie adresy URL, w zależności od wymagań:

    ![Skopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Usługa Azure AD identyfikator**.

    1. **Adres URL wylogowania**.


### <a name="configure-rolepoint-single-sign-on"></a>Konfigurowanie RolePoint logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne stronie RolePoint, potrzebne do pracy z [zespołem pomocy technicznej RolePoint](mailto:info@rolepoint.com). Wyślij ten zespół pliku XML metadanych Federacji oraz w adresach URL, które masz w witrynie Azure portal. Mogą skonfigurować RolePoint, aby upewnić się, że połączenia logowania jednokrotnego SAML jest prawidłowo po obu stronach.

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

W tej sekcji można udostępnić Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej RolePoint.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **RolePoint**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **RolePoint**.

    ![Lista aplikacji](common/all-applications.png)

3. W okienku po lewej stronie wybierz **użytkowników i grup**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** listy użytkowników, a następnie kliknij **wybierz** znajdujący się u dołu okna.

6. Jeśli oczekujesz wartość roli dla asercji SAML **wybierz rolę** okna dialogowego wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **wybierz** znajdujący się u dołu okna.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-rolepoint-test-user"></a>Tworzenie użytkownika testowego RolePoint

Następnie należy utworzyć użytkownika o nazwie Britta Simon w RolePoint. Praca z [zespołem pomocy technicznej RolePoint](mailto:info@rolepoint.com) dodać użytkowników do RolePoint. Użytkownicy muszą zostać utworzona i uaktywniona przed użyciem logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz należy przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego za pomocą panelu dostępu.

Po wybraniu kafelka RolePoint w panelu dostępu użytkownik powinien automatyczne logowanie do wystąpienia RolePoint, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
