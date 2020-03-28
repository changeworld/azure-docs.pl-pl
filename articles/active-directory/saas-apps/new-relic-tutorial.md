---
title: 'Samouczek: Integracja usługi Azure Active Directory z nową reliktem | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a nową reliktem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: dfa5fbcf5df8ee314aab3e4a8228d81e7e14565b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233515"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Samouczek: Integracja usługi Azure Active Directory z nową reliktem

W tym samouczku dowiesz się, jak zintegrować nową relikt z usługą Azure Active Directory (Azure AD).
Integracja nowej relikwii z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do nowej relikwii.
* Można włączyć użytkowników, aby automatycznie zalogować się do new relikt (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z nową reliktem, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Nowa subskrypcja z obsługą logowania jednokrotnego Relikt

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Nowa relikt obsługuje sso inicjowane przez **SP**

## <a name="adding-new-relic-from-the-gallery"></a>Dodawanie nowej relikwii z galerii

Aby skonfigurować integrację nowej relikwii z usługą Azure AD, należy dodać nową relikt z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać nową relikwię z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Nowy relikt**, wybierz **pozycję Nowa relikt** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Nowa relikwia na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z New Relic na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w nowej relikwii.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą nowej reliktu, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj nowe logowanie jednokrotne reliktów](#configure-new-relic-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz nowego użytkownika testowego reliktów](#create-new-relic-test-user)** — aby mieć odpowiednik Britta Simon w New Relic, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą nowej reliktu, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Nowy relikt** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Nowe informacje o domenie i adresach URL logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Podpisz adres URL** wpisz `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` adres URL, używając następującego wzorca: - Pamiętaj, aby zastąpić swój własny identyfikator konta New Relic.

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `rpm.newrelic.com`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie nowej relikwii** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-new-relic-single-sign-on"></a>Konfigurowanie nowego logowania jednokrotnego reliktów

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy **New Relic** jako administrator.

2. W menu u góry kliknij pozycję **Ustawienia konta**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797036.png "Ustawienia konta")

3. Kliknij kartę **Zabezpieczenia i uwierzytelnianie,** a następnie kliknij kartę **Znak pojedynczy na** karcie.
   
    ![Logowanie jednokrotne](./media/new-relic-tutorial/ic797037.png "Logowanie jednokrotne")

4. Na stronie okna dialogowego SAML wykonaj następujące czynności:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Kliknij **pozycję Wybierz plik,** aby przekazać pobrany certyfikat usługi Azure Active Directory.

    b. W polu **tekstowym Adres URL zdalnego logowania** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.
   
    d. W polu tekstowym **adresu URL docelowego wylogowania** wklej wartość **adresu URL wylogowania,** który został skopiowany z witryny Azure portal.

    d. Kliknij **pozycję Zapisz moje zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **\@brittasimon yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do New Relic.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz pozycję **Nowa relikt**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Nowa relikt**.

    ![Łącze Nowa relikwia na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-new-relic-test-user"></a>Utwórz nowego użytkownika testowego reliktów

Aby umożliwić użytkownikom usługi Azure Active Directory zalogowanie się do nowej relikwii, muszą one zostać zainicjowane w new relic. W przypadku New Relic inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika do nowej relikwii, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy **New Relic** jako administrator.

2. W menu u góry kliknij pozycję **Ustawienia konta**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797040.png "Ustawienia konta")

3. W okienku **Konto** po lewej stronie kliknij pozycję **Podsumowanie**, a następnie kliknij pozycję **Dodaj użytkownika**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797041.png "Ustawienia konta")

4. W oknie dialogowym **Aktywni użytkownicy** wykonaj następujące czynności:
   
    ![Aktywni użytkownicy](./media/new-relic-tutorial/ic797042.png "Aktywni użytkownicy")
   
    a. W polach **tekstowych Poczta e-mail** wpisz adres e-mail prawidłowego użytkownika usługi Azure Active Directory, który chcesz aprowizować.

    b. Jako **rola** wybierz **użytkownika**.

    d. Kliknij **pozycję Dodaj tego użytkownika**.

>[!NOTE]
>Do aprowizowania kont użytkowników usługi Azure AD można użyć innych narzędzi do tworzenia konta użytkownika nowej reliktu lub interfejsów API dostarczonych przez new relikt.
> 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Nowy relikt w Panelu dostępu należy automatycznie zalogować się do nowego reliktu, dla którego skonfigurowano logującą się logującą logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

