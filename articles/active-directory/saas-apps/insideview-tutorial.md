---
title: 'Samouczek: Integracja usługi Azure Active Directory z insideview | Dokumenty firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 2149b8410104b39652b176895a31b42e094265f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100086"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Samouczek: Integracja usługi Azure Active Directory z insideview

W tym samouczku dowiesz się, jak zintegrować InsideView z usługą Azure Active Directory (Azure AD).
Integracja ta zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do InsideView.
* Można włączyć użytkowników, aby automatycznie zalogować się do InsideView (logowanie jednokrotne) za pomocą ich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z insideview, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja InsideView, która ma włączoną funkcję logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* InsideView obsługuje identyfikator sytowy inicjowany przez protokół IdP.

## <a name="add-insideview-from-the-gallery"></a>Dodaj InsideView z galerii

Aby skonfigurować integrację InsideView z usługą Azure AD, należy dodać InsideView z galerii do listy zarządzanych aplikacji SaaS.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory:**

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji dla przedsiębiorstw** > **Wszystkie aplikacje:**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wpisz **InsideView**. Wybierz **insideview** w wynikach wyszukiwania, a następnie wybierz pozycję **Dodaj**.

    ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD za pomocą funkcji InsideView przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD a odpowiednim użytkownikiem w insideview.

Aby skonfigurować i przetestować logowanie jednookrotne usługi Azure AD za pomocą insideview, należy wykonać następujące kroki:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby włączyć tę funkcję dla użytkowników.
2. **[Konfigurowanie logowania jednokrotnego InsideView](#configure-insideview-single-sign-on)** po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednookrotne usługi Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć logowanie jednookrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego InsideView,](#create-an-insideview-test-user)** który jest połączony z reprezentacją usługi Azure AD użytkownika.
6. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą insideview, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji InsideView wybierz pozycję **Logowanie jednokrotne:**

    ![Wybieranie logowania jednokrotnego](common/select-sso.png)

2. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne:

    ![Wybieranie metody logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę **Edycja,** aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML:**

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML** należy wykonać następujące czynności.

    ![Podstawowe okno dialogowe Konfiguracja SAML](common/idp-reply.png)

    W polu **Odpowiedz na adres URL** wprowadź adres URL w tym wzorzec:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Ta wartość jest symbolem zastępczym. Musisz użyć rzeczywistego adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej InsideView,](mailto:support@insideview.com) aby uzyskać wartość. Można również odwołać się do wzorców wyświetlanych w oknie dialogowym **Podstawowa konfiguracja SAML** w witrynie Azure portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Signing Certificate (Certyfikat podpisywania SAML)** wybierz łącze **Pobierz** obok **pozycji Certyfikat (Raw)**— zgodnie z wymaganiami i zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/certificateraw.png)

6. W sekcji **Konfigurowanie insideview** skopiuj odpowiednie adresy URL na podstawie wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowania**.

### <a name="configure-insideview-single-sign-on"></a>Konfigurowanie logowania jednokrotnego InsideView

1. W nowym oknie przeglądarki internetowej zaloguj się do witryny firmy InsideView jako administrator.

1. W górnej części okna wybierz pozycję **Administrator**, **Ustawienia SingleSignOn**, a następnie **dodaj SAML**.
   
   ![Ustawienia logowania jednokrotnego SAML](./media/insideview-tutorial/ic794135.png "Ustawienia logowania jednokrotnego SAML")

1. W sekcji **Dodaj nowy SAML,** należy wykonać następujące kroki.

    ![Dodawanie nowej sekcji SAML](./media/insideview-tutorial/ic794136.png "Dodawanie nowej sekcji SAML")

    1. W polu **Nazwa STS** wprowadź nazwę konfiguracji.

    1. W polu **SamlP/WS-Fed Niezamawiany punkt końcowy** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure portal.

    1. Otwórz certyfikat raw pobrany z witryny Azure portal. Skopiuj zawartość certyfikatu do schowka, a następnie wklej zawartość do pola **Certyfikat STS.**

    1. W polu **Mapowanie identyfikatora użytkownika programu Crm** wprowadź . **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

    1. W polu **Mapowanie wiadomości e-mail programu Crm** wprowadź plik **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    1. W polu **Mapowanie imienia crm** wprowadź . **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**

    1. W polu **Mapowanie ostatnioname crm** wprowadź . **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**  

    1. Wybierz **pozycję Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure portal.

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory** w lewym okienku, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy:**

    ![Wybierz pozycję Wszyscy użytkownicy](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** w górnej części okna:

    ![Wybierz pozycję Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **Użytkownik** należy wykonać następujące czynności.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź BrittaSimon@ **\<> swojej>.\<>rozszerzenia **. (Na przykład BrittaSimon@contoso.com.)

    1. Wybierz **pozycję Pokaż hasło**, a następnie zapisz wartość w polu **Hasło.**

    1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając jej dostęp do InsideView.

1. W portalu Azure wybierz **pozycję Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **InsideView**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **insideview**.

    ![Lista wniosków](common/all-applications.png)

3. W lewym okienku wybierz **pozycję Użytkownicy i grupy:**

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu okna.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **Wybierz** u dołu okna.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-an-insideview-test-user"></a>Tworzenie użytkownika testowego InsideView

Aby umożliwić użytkownikom usługi Azure AD zalogować się do InsideView, należy dodać je do InsideView. Należy dodać je ręcznie.

Aby utworzyć użytkowników lub kontakty w InsideView, skontaktuj się z [zespołem pomocy technicznej InsideView](mailto:support@insideview.com).

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure AD można użyć dowolnego narzędzia do tworzenia konta użytkownika lub interfejsu API dostarczonego przez insideview.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka InsideView w Panelu dostępu należy automatycznie zalogować się do wystąpienia InsideView, dla którego skonfigurowano logującą się logującą. Aby uzyskać więcej informacji o Panelu dostępu, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
