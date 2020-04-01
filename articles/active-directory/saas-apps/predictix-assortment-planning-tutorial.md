---
title: 'Samouczek: Integracja usługi Azure Active Directory z planowaniem asortymentu Predictix | Dokumenty firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a planowaniem asortymentu predictix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 37e686ff-f8e5-40b1-9d7e-f64b076917b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: bc3ea2f6fddc233a69d96c0c885ab310ed1e77c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094166"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Samouczek: Integracja usługi Azure Active Directory z planowaniem asortymentu predictix

W tym samouczku dowiesz się, jak zintegrować planowanie asortymentu Predictix z usługą Azure Active Directory (Azure AD).
Integracja ta zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do planowania asortymentu Predictix.
* Można włączyć użytkowników do automatycznego logowania się do predictix planowania asortymentu (logowanie jednokrotne) z ich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z planowaniem asortymentu predictix, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja planowania asortymentu Predictix z włączoną funkcją logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Predictix Assortment Planning obsługuje samoso inicjowane przez SP.

## <a name="add-predictix-assortment-planning-from-the-gallery"></a>Dodaj planowanie asortymentu Predictix z galerii

Aby skonfigurować integrację planowania asortymentu Predictix z usługą Azure AD, należy dodać planowanie asortymentu Predictix z galerii do listy zarządzanych aplikacji SaaS.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory:**

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji dla przedsiębiorstw** > **Wszystkie aplikacje:**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Predictix Assortment Planning**. W wynikach wyszukiwania **wybierz pozycję Planowanie asortymentu predictix,** a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD za pomocą planowania asortymentu Predictix przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD a odpowiednim użytkownikiem w planowaniu asortymentu predictix.

Aby skonfigurować i przetestować logowanie jednookrotne usługi Azure AD za pomocą planowania asortymentu Predictix, należy wykonać następujące kroki:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby włączyć tę funkcję dla użytkowników.
2. **[Skonfiguruj logowanie jednookrotne predictix planowanie](#configure-predictix-assortment-planning-single-sign-on)** asortymentu po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednookrotne usługi Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć logowanie jednookrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego planowania asortymentu Predictix,](#create-a-predictix-assortment-planning-test-user)** który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą planowania asortymentu Predictix, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **planowania asortymentu Predictix** wybierz pozycję **Logowanie jednokrotne:**

    ![Wybierz opcję Logowanie jednokrotne](common/select-sso.png)

2. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne:

    ![Wybieranie metody logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę **Edycja,** aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML:**

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML** wykonaj następujące kroki.

    ![Podstawowe okno dialogowe Konfiguracja SAML](common/sp-identifier.png)

    1. W polu **Zaloguj się na adres URL** wprowadź adres URL w tym wzorzec:

       | |
        |--|
        | `https://<sub-domain>.ap.predictix.com/sso/request`|
        | `https://<sub-domain>.dev.ap.predictix.com/`|
        | |

    1. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL w tym wzorcu:

        | |
        |--|
        | `https://<sub-domain>.ap.predictix.com`|
        | `https://<sub-domain>.dev.ap.predictix.com`|
        | |

    > [!NOTE]
    > Te wartości są symbolami zastępczymi. Musisz użyć rzeczywistego adresu URL logowania i identyfikatora. Skontaktuj się z [zespołem pomocy technicznej planowania asortymentu Predictix,](https://www.infor.com/support) aby uzyskać wartości. Można również odwołać się do wzorców wyświetlanych w oknie dialogowym **Podstawowa konfiguracja SAML** w witrynie Azure portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Signing Certificate (Certyfikat podpisywania SAML)** wybierz łącze **Pobierz** obok **pozycji Certyfikat (Base64)**— zgodnie z wymaganiami i zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie planowania asortymentu Predictix** skopiuj odpowiednie adresy URL na podstawie wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowania**.

### <a name="configure-predictix-assortment-planning-single-sign-on"></a>Konfigurowanie logowania jednokrotnego planowania asortymentu Predictix

Aby skonfigurować logowanie jednokrotne po stronie Planowania asortymentu Predictix, należy wysłać pobrany certyfikat i skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej predictix.](https://www.infor.com/support) Ten zespół zapewnia, że połączenie SSO SAML jest prawidłowo ustawione po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure portal.

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory** w lewym okienku, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy:**

    ![Wybierz pozycję Wszyscy użytkownicy](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu:

    ![Wybierz pozycję Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **Użytkownik** należy wykonać następujące czynności.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź BrittaSimon@ **\<> swojej>.\<>rozszerzenia **. (Na przykład BrittaSimon@contoso.com.)

    1. Wybierz **pozycję Pokaż hasło**, a następnie zapisz wartość w polu **Hasło.**

    1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz Britta Simon do korzystania z usługi Azure AD logowania jednokrotnego, przyznając jej dostęp do planowania asortymentu Predictix.

1. W portalu Azure wybierz **pozycję Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Planowanie asortymentu Predictix**.

    ![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Predictix Assortment Planning**.

    ![Lista wniosków](common/all-applications.png)

3. W lewym okienku wybierz **pozycję Użytkownicy i grupy:**

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-predictix-assortment-planning-test-user"></a>Tworzenie użytkownika testowego planowania asortymentu Predictix

Następnie należy utworzyć użytkownika o nazwie Britta Simon w Predictix Planowania asortymentu. Aby dodać użytkowników, pracuj z [zespołem pomocy technicznej predictix assortment planning.](https://www.infor.com/support) Użytkownicy muszą być tworzone i aktywowane przed użyciem logowania jednokrotnego.

> [!NOTE]
> Posiadacz konta usługi Azure AD otrzymuje wiadomość e-mail i wybiera łącze, aby potwierdzić konto, zanim stanie się aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Planowania asortymentu Predictix w Panelu dostępu należy automatycznie zalogować się do wystąpienia planowania asortymentu Predictix, dla którego skonfigurowano logowanie logujące się. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)