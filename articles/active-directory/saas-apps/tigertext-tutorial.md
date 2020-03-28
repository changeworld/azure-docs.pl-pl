---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem TigerText Secure Messenger | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088675"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Samouczek: Integracja usługi Azure Active Directory z programem TigerText Secure Messenger

W tym samouczku dowiesz się, jak zintegrować program TigerText Secure Messenger z usługą Azure Active Directory (Azure AD).

Integracja programu TigerText Secure Messenger z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do programu TigerText Secure Messenger.
* Można włączyć użytkowników, aby automatycznie zalogować się do TigerText Secure Messenger (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby uzyskać szczegółowe informacje na temat integracji aplikacji z usługą Azure AD (SaaS) z aplikacją, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem TigerText Secure Messenger, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Subskrypcja TigerText Secure Messenger z włączoną rejestracją jednokrotną.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku konfigurujesz i testujesz logowanie jednokrotne usługi Azure AD w środowisku testowym i integrujesz program TigerText Secure Messenger z usługą Azure AD.

TigerText Secure Messenger obsługuje inicjowane przez SP logowanie jednokrotne (Logowanie jednokrotne).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Dodawanie programu TigerText Secure Messenger z portalu Azure Marketplace

Aby skonfigurować integrację programu TigerText Secure Messenger z usługą Azure AD, należy dodać program TigerText Secure Messenger z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com?azure-portal=true).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz **+ Nowa aplikacja** w górnej części okienka.

    ![Opcja Nowa aplikacja](common/add-new-app.png)

1. W polu wyszukiwania wpisz **TigerText Secure Messenger**. W wynikach wyszukiwania wybierz **tigertext secure messenger**, a następnie wybierz pozycję **Dodaj,** aby dodać aplikację.

    ![TigerText Secure Messenger na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z TigerText Secure Messenger na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić łącze między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie TigerText Secure Messenger.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą programu TigerText Secure Messenger, należy wykonać następujące bloki konstrukcyjne:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj logowanie jednokrotne TigerText Secure Messenger,](#configure-tigertext-secure-messenger-single-sign-on)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą Britta Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Utwórz użytkownika testowego TigerText Secure Messenger,](#create-a-tigertext-secure-messenger-test-user)** aby w programie TigerText Secure Messenger był użytkownik o imieniu Britta Simon, który jest połączony z użytkownikiem usługi Azure AD o nazwie Britta Simon.
1. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą programu TigerText Secure Messenger, należy wykonać następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **TigerText Secure Messenger** wybierz pozycję **Logowanie jednokrotne.**

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz pozycję **Edytuj** (ikona ołówka), aby otworzyć podstawowe okienko **konfiguracji SAML.**

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W podstawowym okienku **konfiguracji SAML** należy wykonać następujące czynności:

    ![TigerText Secure Messenger — informacje o logach jednokrotnych](common/sp-identifier.png)

    1. W polu **Zaloguj się pod adresem URL** wprowadź adres URL:

       `https://home.tigertext.com`

    1. W polu **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Wartość **identyfikatora (identyfikatora jednostki)** nie jest rzeczywista. Zaktualizuj tę wartość za pomocą rzeczywistego identyfikatora. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej TigerText Secure Messenger](mailto:prosupport@tigertext.com). Można również odwołać się do wzorców wyświetlanych w podstawowym okienku **konfiguracji SAML** w witrynie Azure portal.

1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** wybierz pozycję **Pobierz,** aby pobrać **kod XML metadanych federacji** z podanych opcji i zapisać go na komputerze.

    ![Opcja pobierania kodu XML metadanych federacji](common/metadataxml.png)

1. W sekcji **Konfigurowanie bezpiecznego messengera TigerText** skopiuj potrzebny adres URL lub adresy URL:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowywania**

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Konfigurowanie logowania jednokrotnego TigerText Secure Messenger

Aby skonfigurować logowanie jednokrotne po stronie tigertext secure messenger, musisz wysłać pobrany kod XML metadanych federacji i odpowiednie skopiowane adresy URL z portalu Azure do [zespołu pomocy technicznej TigerText Secure Messenger](mailto:prosupport@tigertext.com). Zespół TigerText Secure Messenger upewni się, że połączenie SSO SAML jest prawidłowo ustawione po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure portal w lewym okienku wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory**   > **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i "Wszyscy użytkownicy"](common/users.png)

1. U góry ekranu wybierz pozycję **+ Nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    ![Okienko Użytkownik](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **>\@\<BrittaSimon yourcompanydomain.\<>rozszerzenia **. Na **przykład,\@BrittaSimon contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając im dostęp do TigerText Secure Messenger.

1. W portalu Azure wybierz **aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje** > **TigerText Secure Messenger**.

    ![Okienko aplikacji dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **tigertext secure messenger**.

    ![TigerText Secure Messenger na liście aplikacji](common/all-applications.png)

1. W lewym okienku w obszarze **MANAGE**wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **pozycję + Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w okienku Dodawanie **przydziału.**

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście **Użytkownicy,** a następnie wybierz pozycję **Wybierz** u dołu okienka.

1. Jeśli oczekujesz wartości roli w asercji SAML, a następnie w **okienku Wybierz rolę,** wybierz odpowiednią rolę dla użytkownika z listy. U dołu okienka wybierz pozycję **Wybierz**.

1. W okienku **Dodawanie przydziału** wybierz pozycję **Przypisz**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Tworzenie użytkownika testowego TigerText Secure Messenger

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w TigerText Secure Messenger. Współpracuj z [zespołem pomocy technicznej TigerText Secure Messenger,](mailto:prosupport@tigertext.com) aby dodać Brittę Simon jako użytkownika w programie TigerText Secure Messenger. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu portalu Moje aplikacje.

Po wybraniu **tigertext secure messenger** w portalu Moje aplikacje, powinieneś być automatycznie zalogowany do subskrypcji TigerText Secure Messenger, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji o portalu Moje aplikacje, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
