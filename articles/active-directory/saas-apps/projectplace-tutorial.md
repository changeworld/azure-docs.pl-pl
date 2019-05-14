---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą narzędzia Projectplace | Dokumentacja firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 17fbc6bc4f022a15c34c5ca7b9465be392cdc639
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560629"
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą narzędzia Projectplace

W tym samouczku dowiesz się, jak zintegrować Projectplace w usłudze Azure Active Directory (Azure AD).

Ta integracja zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do narzędzia Projectplace.
* Aby umożliwić użytkownikom automatyczne logowanie do Projectplace (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą narzędzia Projectplace, potrzebne są:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz zarejestrować się w celu [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/) subskrypcji.
* Subskrypcja narzędzia Projectplace, która ma logowanie jednokrotne włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka możesz skonfigurować i testowanie usługi Azure AD rejestracji jednokrotnej w środowisku testowym.

* Projectplace obsługuje logowanie Jednokrotne zainicjowane przez SP.

## <a name="add-projectplace-from-the-gallery"></a>Dodaj Projectplace z galerii

Aby skonfigurować integrację z narzędziem projectplace firmy w usłudze Azure AD, należy dodać Projectplace z galerii z listą zarządzanych aplikacji SaaS.

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**:

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **nową aplikację** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Projectplace**. Wybierz **Projectplace** w wynikach wyszukiwania, a następnie wybierz **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługę Azure AD logowanie jednokrotne za pomocą narzędzia Projectplace przy użyciu użytkownika testu o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiedniego użytkownika w Projectplace.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą narzędzia Projectplace, należy wykonać następujące czynności:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  do włączenia tej funkcji dla użytkowników.
2. **[Konfigurowanie narzędzia Projectplace logowania jednokrotnego](#configure-projectplace-single-sign-on)**  na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowania jednokrotnego.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  włączyć usługi Azure AD logowanie jednokrotne dla użytkownika.
5. **[Tworzenie użytkownika testowego Projectplace](#create-a-projectplace-test-user)**  połączony na reprezentację w postaci usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  można sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz usługę Azure AD logowania jednokrotnego w witrynie Azure portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą narzędzia Projectplace, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Projectplace** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**:

    ![Wybierz opcję logowania jednokrotnego](common/select-sso.png)

2. W **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne:

    ![Wybierz metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okno dialogowe:

    ![Ikona edycji](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** dialogowym **adres URL logowania** wprowadź adres URL, w tym wzorcu:

    `https://<company>.projectplace.com`

   ![Podstawowy plik konfiguracji SAML, okno dialogowe](common/sp-signonurl.png)
    > [!NOTE]
    > Ta wartość jest symbolem zastępczym. Należy użyć rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej Projectplace](https://success.planview.com/Projectplace/Support) można uzyskać wartość. Może również odnosić się do wzorców objętego **podstawową konfigurację protokołu SAML** okno dialogowe, w witrynie Azure portal.

5. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** stronie **certyfikat podpisywania SAML** zaznacz **Pobierz** łącze obok **XML metadanych Federacji** , zgodnie z wymaganiami, a następnie Zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W **Konfigurowanie Projectplace** sekcji, skopiuj odpowiednie adresy URL, w zależności od wymagań.

    ![Skopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Usługa Azure AD identyfikator**.

    1. **Adres URL wylogowania**.

### <a name="configure-projectplace-single-sign-on"></a>Konfigurowanie narzędzia Projectplace logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na **Projectplace** stronie, musisz wysłać pobrany **XML metadanych Federacji** certyfikatu i adresów URL, które zostały skopiowane z portalu Azure w celu [ Zespół pomocy technicznej Projectplace](https://success.planview.com/Projectplace/Support). Ten zespół gwarantuje, że połączenia logowania jednokrotnego SAML jest prawidłowo po obu stronach.

>[!NOTE]
>Jednej konfiguracji logowania jednokrotnego musi być wykonywane przez [zespołem pomocy technicznej Projectplace](https://success.planview.com/Projectplace/Support). Otrzymasz powiadomienie, zaraz po zakończeniu konfiguracji.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testu o nazwie Britta Simon w witrynie Azure portal.

1. W witrynie Azure portal wybierz **usługi Azure Active Directory** w okienku po lewej stronie wybierz **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**:

    ![Wybierz opcję Wszyscy użytkownicy](common/users.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu:

    ![Wybierz nowego użytkownika](common/new-user.png)

3. W **użytkownika** okna dialogowego pole, wykonaj następujące kroki.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **BrittaSimon @\<domenatwojejfirmy >.\< Rozszerzenia >**. (Na przykład BrittaSimon@contoso.com.)

    1. Wybierz **Pokaż hasło**i zanotuj wartość, która znajduje się w **hasło** pole.

    1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Britta Simon korzystać z usługi Azure AD logowania jednokrotnego przez udostępnienie jej Projectplace.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **Projectplace**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Projectplace**.

    ![Lista aplikacji](common/all-applications.png)

3. W okienku po lewej stronie wybierz **użytkowników i grup**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** listy użytkowników, a następnie kliknij **wybierz** znajdujący się u dołu ekranu.

6. Jeśli oczekujesz wartość roli dla asercji SAML **wybierz rolę** okna dialogowego wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-projectplace-test-user"></a>Tworzenie użytkownika testowego Projectplace

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do narzędzia Projectplace, należy dodać je do narzędzia Projectplace. Należy dodać je ręcznie.

Aby utworzyć konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Twojej **Projectplace** witryny firmy jako administrator.

2. Przejdź do **osób**, a następnie wybierz pozycję **członków**:
   
    ![Przejdź do osób, a następnie wybierz elementy członkowskie](./media/projectplace-tutorial/ic790228.png "osoby")

3. Wybierz **Dodawanie elementu członkowskiego**:
   
    ![Wybierz opcję Dodaj element członkowski](./media/projectplace-tutorial/ic790232.png "dodawać członków")

4. W **Dodawanie elementu członkowskiego** sekcji, wykonaj następujące kroki.
   
    ![Dodawanie elementu członkowskiego sekcji](./media/projectplace-tutorial/ic790233.png "nowych elementów członkowskich")
   
    1. W **nowych elementów członkowskich** wprowadź adres e-mail Azure prawidłowe konto usługi AD, który chcesz dodać.
   
    1. Wybierz pozycję **Wyślij**.

   Wiadomość e-mail zawierającą łącze w celu potwierdzenia konta, zanim stanie się aktywny są wysyłane do właściciela konta usługi Azure AD.

>[!NOTE]
>Umożliwia także inne narzędzie do tworzenia konta użytkownika lub interfejsów API dostarczonych przez narzędzia Projectplace, aby dodać konta użytkowników usługi Azure AD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz należy przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego za pomocą panelu dostępu.

Po wybraniu kafelka Projectplace w panelu dostępu, możesz powinny być automatycznie zalogowany do wystąpienia narzędzia Projectplace, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
