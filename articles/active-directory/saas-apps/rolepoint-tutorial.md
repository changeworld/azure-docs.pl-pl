---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem RolePoint | Dokumenty firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem RolePoint.
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
ms.openlocfilehash: 0b6fd17d2f8577532778733866260f43e9ac7685
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092731"
---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>Samouczek: Integracja usługi Azure Active Directory z programem RolePoint

W tym samouczku dowiesz się, jak zintegrować program RolePoint z usługą Azure Active Directory (Azure AD).
Integracja ta zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do programu RolePoint.
* Można włączyć użytkowników, aby automatycznie zalogować się do programu RolePoint (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem RolePoint, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja programu RolePoint z włączoną funkcją logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program RolePoint obsługuje funkcję SSO inicjowane przez dodatek SP.

## <a name="add-rolepoint-from-the-gallery"></a>Dodawanie programu RolePoint z galerii

Aby skonfigurować integrację programu RolePoint z usługą Azure AD, należy dodać program RolePoint z galerii do listy zarządzanych aplikacji SaaS.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory:**

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji dla przedsiębiorstw** > **Wszystkie aplikacje:**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wpisz **program RolePoint**. Wybierz **pozycję RolePoint** w wynikach wyszukiwania, a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD za pomocą programu RolePoint przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD a odpowiednim użytkownikiem w programie RolePoint.

Aby skonfigurować i przetestować logowanie jednookrotne usługi Azure AD za pomocą programu RolePoint, należy wykonać następujące kroki:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby włączyć tę funkcję dla użytkowników.
2. **[Konfigurowanie logowania jednokrotnego programu RolePoint](#configure-rolepoint-single-sign-on)** po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednookrotne usługi Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć logowanie jednookrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego programu RolePoint,](#create-a-rolepoint-test-user)** który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą programu RolePoint, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji programu RolePoint wybierz pozycję **Logowanie jednokrotne:**

    ![Wybieranie logowania jednokrotnego](common/select-sso.png)

2. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne:

    ![Wybieranie metody logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę **Edycja,** aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML:**

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML** należy wykonać następujące czynności.

    ![Podstawowe okno dialogowe Konfiguracja SAML](common/sp-identifier.png)

    1. W polu **Zaloguj się na adres URL** wprowadź adres URL w tym wzorzec:

       `https://<subdomain>.rolepoint.com/login`

    1. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL w tym wzorcu:

       `https://app.rolepoint.com/<instancename>`

    > [!NOTE]
    > Te wartości są symbolami zastępczymi. Musisz użyć rzeczywistego adresu URL logowania i identyfikatora. Sugerujemy użycie unikatowej wartości ciągu w identyfikatorze. Skontaktuj się z [zespołem pomocy technicznej programu RolePoint,](mailto:info@rolepoint.com) aby uzyskać te wartości. Można również odwołać się do wzorców wyświetlanych w oknie dialogowym **Podstawowa konfiguracja SAML** w witrynie Azure portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** wybierz łącze **Pobierz** obok **pozycji XML metadanych federacji**, zgodnie z wymaganiami, i zapisz plik na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie programu RolePoint** skopiuj odpowiednie adresy URL na podstawie wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowania**.


### <a name="configure-rolepoint-single-sign-on"></a>Konfigurowanie logowania jednokrotnego programu RolePoint

Aby skonfigurować logowanie jednokrotne po stronie programu RolePoint, należy pracować z [zespołem pomocy technicznej programu RolePoint](mailto:info@rolepoint.com). Wyślij ten zespół plik XML metadanych federacji i adresy URL, które zostały wysłane z witryny Azure portal. Skonfigurują program RolePoint, aby upewnić się, że połączenie SSO SAML jest poprawnie ustawione po obu stronach.

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

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając jej dostęp do programu RolePoint.

1. W portalu Azure wybierz **pozycję Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **RolePoint**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **RolePoint**.

    ![Lista wniosków](common/all-applications.png)

3. W lewym okienku wybierz **pozycję Użytkownicy i grupy:**

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu okna.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **Wybierz** u dołu okna.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-rolepoint-test-user"></a>Tworzenie użytkownika testowego programu RolePoint

Następnie należy utworzyć użytkownika o nazwie Britta Simon w programie RolePoint. Praca z [zespołem pomocy technicznej programu RolePoint w](mailto:info@rolepoint.com) celu dodania użytkowników do programu RolePoint. Użytkownicy muszą być tworzone i aktywowane, zanim będzie można korzystać z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka programu RolePoint w Panelu dostępu należy automatycznie zalogować się do wystąpienia programu RolePoint, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji o Panelu dostępu, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
