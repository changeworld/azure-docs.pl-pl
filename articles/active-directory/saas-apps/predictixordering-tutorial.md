---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem Predictix Ordering | Dokumenty firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem Predictix Ordering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2fe2f976-e97f-4368-9695-3e1624409e8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 457ab3a0d5e816becbd2b32d858d5172951f27ad
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094121"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Samouczek: Integracja usługi Azure Active Directory z programem Predictix Ordering

W tym samouczku dowiesz się, jak zintegrować program Predictix Ordering z usługą Azure Active Directory (Azure AD).
Integracja ta zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do predictix zamawiania.
* Można włączyć użytkowników do automatycznego logowania się do Predictix Ordering (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem Predictix Ordering, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja Predictix Ordering, która ma włączoną funkcję logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Predictix Ordering obsługuje sytę inicjowane przez sp.

## <a name="add-predictix-ordering-from-the-gallery"></a>Dodawanie predictix zamawiania z galerii

Aby skonfigurować integrację predictix zamawiania do usługi Azure AD, należy dodać Predictix Ordering z galerii do listy zarządzanych aplikacji SaaS.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory:**

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji dla przedsiębiorstw** > **Wszystkie aplikacje:**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Predictix Ordering**. W wynikach wyszukiwania wybierz **pozycję Predictix Ordering,** a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD za pomocą predictix ordering przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD a odpowiednim użytkownikiem w programie Predictix Ordering.

Aby skonfigurować i przetestować logowanie jednooznaczne usługi Azure AD za pomocą predictix ordering, należy wykonać następujące kroki:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby włączyć tę funkcję dla użytkowników.
2. **[Konfigurowanie predictix zamawiania logowania jednokrotnego](#configure-predictix-ordering-single-sign-on)** po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednookrotne usługi Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć logowanie jednookrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego Predictix Ordering,](#create-a-predictix-ordering-test-user)** który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure portal.

Aby skonfigurować logowanie jednooznaczne usługi Azure AD za pomocą predictix ordering, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Predictix Ordering** wybierz pozycję **Logowanie jednokrotne:**

    ![Wybierz opcję Logowanie jednokrotne](common/select-sso.png)

2. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne:

    ![Wybieranie metody logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę **Edycja,** aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML:**

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML** wykonaj następujące kroki.

    ![Podstawowe okno dialogowe Konfiguracja SAML](common/sp-identifier.png)

    1. W polu **Zaloguj się na adres URL** wprowadź adres URL w tym wzorzec:

       `https://<companyname-pricing>.ordering.predictix.com/sso/request`

    1. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL w tym wzorcu:

        | |
        |--|
        | `https://<companyname-pricing>.dev.ordering.predictix.com` |
        | `https://<companyname-pricing>.ordering.predictix.com` |
        | |

    > [!NOTE]
    > Te wartości są symbolami zastępczymi. Musisz użyć rzeczywistego adresu URL logowania i identyfikatora. Skontaktuj się z [zespołem pomocy technicznej Predictix Ordering,](https://www.predix.io/support/) aby uzyskać wartości. Można również odwołać się do wzorców wyświetlanych w oknie dialogowym **Podstawowa konfiguracja SAML** w witrynie Azure portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Signing Certificate (Certyfikat podpisywania SAML)** wybierz łącze **Pobierz** obok **pozycji Certyfikat (Base64)**— zgodnie z wymaganiami i zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie kolejności Predictix** skopiuj odpowiednie adresy URL na podstawie wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    2. **Identyfikator usługi Azure AD**.

    3. **Adres URL wylogowania**.

### <a name="configure-predictix-ordering-single-sign-on"></a>Konfigurowanie rejestracji jednokrotnej zamawiania predictix

Aby skonfigurować logowanie jednokrotne po stronie Predictix Ordering, należy wysłać pobrany certyfikat i adresy URL skopiowane z witryny Azure portal do [zespołu pomocy technicznej Predictix Ordering](https://www.predix.io/support/). Ten zespół zapewnia, że połączenie SSO SAML jest prawidłowo ustawione po obu stronach.

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

W tej sekcji włączysz Britta Simon do korzystania z usługi Azure AD logowania jednokrotnego, przyznając jej dostęp do Predictix Zamawianie.

1. W portalu Azure wybierz **aplikacje enterprise**, wybierz **wszystkie aplikacje**, a następnie wybierz **pozycję Predictix Ordering**:

    ![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **predictix ordering**.

    ![Lista wniosków](common/all-applications.png)

3. W lewym okienku wybierz **pozycję Użytkownicy i grupy:**

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-predictix-ordering-test-user"></a>Tworzenie użytkownika testowego predictix ordering

Następnie należy utworzyć użytkownika o nazwie Britta Simon w Predictix Zamawiania. Pracuj z [zespołem pomocy technicznej Predictix Ordering,](https://www.predix.io/support/) aby dodawać użytkowników. Użytkownicy muszą być tworzone i aktywowane przed użyciem logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Predictix Ordering w Panelu dostępu należy automatycznie zalogować się do wystąpienia Predictix Ordering, dla którego skonfigurowano logowanie logujące. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
