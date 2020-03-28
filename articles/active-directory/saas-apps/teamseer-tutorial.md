---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem TeamSeer | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3e505e4823db8c9f42bbff216f30326081110dcf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088980"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Samouczek: Integracja usługi Azure Active Directory z programem TeamSeer

W tym samouczku dowiesz się, jak zintegrować program TeamSeer z usługą Azure Active Directory (Azure AD).
Integracja usługi TeamSeer z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do TeamSeer.
* Można włączyć użytkowników, aby automatycznie zalogować się do TeamSeer (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem TeamSeer, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego teamseer

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* TeamSeer obsługuje sso inicjowane przez **SP**

## <a name="adding-teamseer-from-the-gallery"></a>Dodawanie teamseer z galerii

Aby skonfigurować integrację programu TeamSeer z usługą Azure AD, należy dodać program TeamSeer z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać teamseer z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **TeamSeer**, wybierz **teamseer** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![TeamSeer na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z TeamSeer na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie TeamSeer.

Aby skonfigurować i przetestować logowanie jednookrotne usługi Azure AD za pomocą usługi TeamSeer, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne teamseer](#configure-teamseer-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego TeamSeer](#create-teamseer-test-user)** — aby mieć odpowiednik Britta Simon w TeamSeer, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą usługi TeamSeer, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **TeamSeer** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o logowanie do domeny teamseer i adresów URL](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta TeamSeer,](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie teamseer** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-teamseer-single-sign-on"></a>Konfigurowanie logowania jednokrotnego programu TeamSeer

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy TeamSeer jako administrator.

1. Przejdź do **administratora HR**.

    ![Administrator HR](./media/teamseer-tutorial/ic789634.png "Administrator HR")

1. Kliknij **pozycję Ustawienia**.

    ![Instalacja](./media/teamseer-tutorial/ic789635.png "Konfiguracja")

1. Kliknij **pozycję Konfigurowanie szczegółów dostawcy SAML**.

    ![Ustawienia SAML](./media/teamseer-tutorial/ic789636.png "Ustawienia SAML")

1. W sekcji szczegóły dostawcy SAML wykonaj następujące kroki:

    ![Ustawienia SAML](./media/teamseer-tutorial/ic789637.png "Ustawienia SAML")

    a. W polu tekstowym **adresu URL** wklej wartość adresu **URL logowania,** która została skopiowana z witryny Azure portal.

    b. Otwórz certyfikat zakodowany w wersji podstawowej 64 w notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola tekstowego **certyfikatu publicznego IdP.**

1. Aby ukończyć konfigurację dostawcy SAML, wykonaj następujące czynności:

    ![Ustawienia SAML](./media/teamseer-tutorial/ic789638.png "Ustawienia SAML")

    a. W **testowych adresach e-mail**wpisz adres e-mail użytkownika testowego.
  
    b. W polach tekstowych **Wystawcy** wpisz adres URL wystawcy usługodawcy.
  
    d. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do TeamSeer.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **TeamSeer**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **TeamSeer**.

    ![Łącze TeamSeer na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-teamseer-test-user"></a>Tworzenie użytkownika testowego TeamSeer

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do usługi TeamSeer, muszą one być aprowizowane w ShiftPlanning. W przypadku TeamSeer inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy **TeamSeer** jako administrator.

1. Przejdź do **strony \> Administrator działu kadr,** a następnie kliknij pozycję **Uruchom kreatora Nowego Użytkownika**.

    ![Administrator HR](./media/teamseer-tutorial/ic789640.png "Administrator HR")

1. W sekcji **Szczegóły użytkownika** wykonaj następujące czynności:

    ![Dane użytkownika](./media/teamseer-tutorial/ic789641.png "Dane użytkownika")

    a. Wpisz **imię,** **nazwisko**, **nazwę użytkownika (adres e-mail)** prawidłowego konta usługi Azure AD, które chcesz udostępnić w powiązanych polach tekstowych.
  
    b. Kliknij przycisk **alej**.

1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby dodać nowego użytkownika i kliknij przycisk **Zakończ**.

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure AD można użyć innych narzędzi do tworzenia konta użytkownika TeamSeer lub interfejsów API dostarczonych przez program TeamSeer.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka TeamSeer w Panelu dostępu należy automatycznie zalogować się do programu TeamSeer, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
