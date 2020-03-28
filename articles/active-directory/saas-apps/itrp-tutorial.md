---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą ITRP | Dokumenty firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą ITRP.
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
ms.openlocfilehash: d44391624e29d2bdd182bb07452e0e8def2d1407
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67656691"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Samouczek: Integracja usługi Azure Active Directory z usługą ITRP

W tym samouczku dowiesz się, jak zintegrować ITRP z usługą Azure Active Directory (Azure AD).
Integracja ta zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do usługi ITRP.
* Można włączyć użytkowników, aby automatycznie zalogować się do ITRP (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą ITRP, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja ITRP z włączoną rejestracją jednokrotną.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* ITRP obsługuje sso inicjowane przez sp.

## <a name="add-itrp-from-the-gallery"></a>Dodaj ITRP z galerii

Aby skonfigurować integrację ITRP z usługą Azure AD, należy dodać itrp z galerii do listy zarządzanych aplikacji SaaS.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory:**

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji dla przedsiębiorstw** > **Wszystkie aplikacje:**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wpisz **ITRP**. Wybierz **ITRP** w wynikach wyszukiwania, a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD za pomocą usługi ITRP przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD a odpowiednim użytkownikiem w programie ITRP.

Aby skonfigurować i przetestować logowanie jednookrotne usługi Azure AD za pomocą usługi ITRP, należy wykonać następujące kroki:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby włączyć tę funkcję dla użytkowników.
2. **[Skonfiguruj logowanie jednookrotne ITRP](#configure-itrp-single-sign-on)** po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednookrotne usługi Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć logowanie jednookrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego ITRP,](#create-an-itrp-test-user)** który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą usługi ITRP, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji ITRP wybierz pozycję **Logowanie jednokrotne:**

    ![Wybieranie logowania jednokrotnego](common/select-sso.png)

2. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne:

    ![Wybieranie metody logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę **Edycja,** aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML:**

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML** należy wykonać następujące czynności.

    ![Podstawowe okno dialogowe Konfiguracja SAML](common/sp-identifier.png)

    1. W polu **Zaloguj się na adres URL** wprowadź adres URL w tym wzorzec:
    
       `https://<tenant-name>.itrp.com`

    1. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL w tym wzorcu:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Te wartości są symbolami zastępczymi. Musisz użyć rzeczywistego adresu URL logowania i identyfikatora. Skontaktuj się z [zespołem pomocy technicznej ITRP,](https://www.itrp.com/support) aby uzyskać wartości. Można również odwołać się do wzorców wyświetlanych w oknie dialogowym **Podstawowa konfiguracja SAML** w witrynie Azure portal.

5. W sekcji **Certyfikat podpisywania SAML** wybierz ikonę **Edytuj,** aby otworzyć okno dialogowe **Saml Podpisywanie certyfikatu:**

    ![Ikona Edytuj](common/edit-certificate.png)

6. W oknie dialogowym **Certyfikat podpisywania SAML** skopiuj wartość **Odcisk palca** i zapisz ją:

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Konfigurowanie ITRP** skopiuj odpowiednie adresy URL na podstawie wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowania**.

### <a name="configure-itrp-single-sign-on"></a>Konfigurowanie logowania jednokrotnego ITRP

1. W nowym oknie przeglądarki internetowej zaloguj się do witryny firmy ITRP jako administrator.

1. W górnej części okna wybierz ikonę **Ustawienia:**

    ![Ikona Ustawienia](./media/itrp-tutorial/ic775570.png "Ikona Ustawienia")

1. W lewym okienku wybierz **pozycję Logowanie jednokrotne:**

    ![Wybieranie logowania jednokrotnego](./media/itrp-tutorial/ic775571.png "Wybieranie logowania jednokrotnego")

1. W sekcji konfiguracji **logowania jednokrotnego** należy wykonać następujące kroki.

    ![Sekcja logowania jednokrotnego](./media/itrp-tutorial/ic775572.png "Sekcja logowania jednokrotnego")

    ![Sekcja logowania jednokrotnego](./media/itrp-tutorial/ic775573.png "Sekcja logowania jednokrotnego")

    1. Wybierz pozycję **Włączone**.

    1. W polu **Adres URL zdalnego wylogowywki** wklej wartość **adresu URL wylogowania** skopiowaną z witryny Azure portal.

    1. W polu **adresu URL logowania SAML** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure portal.

    1. W polu **Odcisk palca certyfikatu** wklej wartość **Odcisk palca** certyfikatu, który został skopiowany z witryny Azure portal.

    1. Wybierz **pozycję Zapisz**.

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

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając jej dostęp do ITRP.

1. W portalu Azure wybierz **pozycję Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **ITRP**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **itrp**.

    ![Lista wniosków](common/all-applications.png)

3. W lewym okienku wybierz **pozycję Użytkownicy i grupy:**

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu okna.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **Wybierz** u dołu okna.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-an-itrp-test-user"></a>Tworzenie użytkownika testowego ITRP

Aby umożliwić użytkownikom usługi Azure AD logowanie się do usługi ITRP, należy dodać je do usługi ITRP. Należy dodać je ręcznie.

Aby utworzyć konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do dzierżawy ITRP.

1. W górnej części okna wybierz ikonę **Rekordy:**

    ![Ikona Rekordów](./media/itrp-tutorial/ic775575.png "Ikona Rekordów")

1. W menu wybierz pozycję **Kontakty**:

    ![Wybierz osoby](./media/itrp-tutorial/ic775587.png "Wybierz osoby")

1. Wybierz znak plus**+**( ), aby dodać nową osobę:

    ![Wybierz znak plus](./media/itrp-tutorial/ic775576.png "Wybierz znak plus")

1. W oknie dialogowym **Dodawanie nowej osoby** należy wykonać następujące czynności.

    ![Okno dialogowe Dodawanie nowej osoby](./media/itrp-tutorial/ic775577.png "Okno dialogowe Dodawanie nowej osoby")

    1. Wprowadź nazwę i adres e-mail prawidłowego konta usługi Azure AD, które chcesz dodać.

    1. Wybierz **pozycję Zapisz**.

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure AD można użyć dowolnego narzędzia do tworzenia konta użytkownika lub interfejsu API dostarczonego przez usługę ITRP.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka ITRP w Panelu dostępu należy automatycznie zalogować się do wystąpienia ITRP, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji o Panelu dostępu, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
