---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem RunMyProcess | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c31a209e8521b24e7f604dbe630f689fca484e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68880412"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Samouczek: Integracja programu RunMyProcess z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować RunMyProcess z usługą Azure Active Directory (Azure AD). Po zintegrowaniu RunMyProcess z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do RunMyProcess.
* Włącz użytkownikom automatyczne logowanie do RunMyProcess za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) RunMyProcess.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* RunMyProcess obsługuje sso inicjowane przez **SP**

## <a name="adding-runmyprocess-from-the-gallery"></a>Dodawanie RunMyProcess z galerii

Aby skonfigurować integrację RunMyProcess do usługi Azure AD, należy dodać RunMyProcess z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **RunMyProcess** w polu wyszukiwania.
1. Wybierz **runmyprocess** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi RunMyProcess przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w RunMyProcess.

Aby skonfigurować i przetestować usługę Azure AD SSO przy za pomocą usługi RunMyProcess, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne RunMyProcess](#configure-runmyprocess-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego RunMyProcess](#create-runmyprocess-test-user)** — aby mieć odpowiednik B.Simon w RunMyProcess, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **RunMyProcess** znajdź sekcję **Zarządzaj** i wybierz opcję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta RunMyProcess,](mailto:support@runmyprocess.com) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie runmyprocess** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Konfigurowanie syda zamieszczanych przez program RunMyProcess

1. W innym oknie przeglądarki sieci Web zaloguj się do dzierżawy RunMyProcess jako administrator.

1. W lewym panelu nawigacyjnym kliknij pozycję **Konto** i wybierz pozycję **Konfiguracja**.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Przejdź do sekcji **Metoda uwierzytelniania** i wykonaj poniższe czynności:

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Jako **metoda**, wybierz **opcję SSO z Samlv2**.

    b. W polu tekstowym **przekierowania logowania** logowania wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    d. W polu tekstowym **przekierowania wyloguj** wklej wartość **adresu URL wylogowania,** który został skopiowany z witryny Azure Portal.

    d. W polu **tekstowym Format nazwy** wpisz wartość **Format identyfikatora nazwy** jako **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    e. Otwórz pobrany plik certyfikatu z witryny Azure portal w notatniku, skopiuj zawartość pliku certyfikatu, a następnie wklej go do pola tekstowego **certyfikatu.**

    f. Kliknij ikonę **Zapisz.**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do RunMyProcess.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **RunMyProcess**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-runmyprocess-test-user"></a>Utwórz użytkownika testowego RunMyProcess

Aby umożliwić użytkownikom usługi Azure AD zalogować się do RunMyProcess, muszą one być aprowizowane do RunMyProcess. W przypadku RunMyProcess inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy RunMyProcess jako administrator.

1. Kliknij **pozycję Konto** i wybierz pozycję **Użytkownicy** w lewym panelu nawigacyjnym, a następnie kliknij pozycję **Nowy użytkownik**.

    ![Nowy użytkownik](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nowy użytkownik")

1. W sekcji **Ustawienia użytkownika** wykonaj następujące czynności:

    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil")
  
    a. Wpisz **nazwę** i **adres e-mail** prawidłowego konta usługi Azure AD, które chcesz udostępnić w powiązanych polach tekstowych.

    b. Wybierz **język IDE,** **Język**i **Profil**.

    d. Wybierz **pozycję Wyślij do mnie wiadomość e-mail do tworzenia konta**.

    d. Kliknij przycisk **Zapisz**.

    > [!NOTE]
    > Do aprowizowania kont użytkowników usługi Azure Active Directory można użyć innych narzędzi do tworzenia kont użytkowników RunMyProcess dostarczonych przez program RunMyProcess.

### <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka RunMyProcess w Panelu dostępu należy automatycznie zalogować się do RunMyProcess, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)