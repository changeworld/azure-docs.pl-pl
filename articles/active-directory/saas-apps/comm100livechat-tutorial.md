---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z czatem na żywo comm100 | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e41161a2e1bfd5544410bba0cb470bacbf152fd2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561256"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z czatem na żywo comm100

W tym samouczku dowiesz się, jak zintegrować czat na żywo Comm100 z usługą Azure Active Directory (Azure AD). Po zintegrowaniu czatu na żywo Comm100 z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do czatu na żywo Comm100.
* Włącz użytkownikom automatyczne logowanie do czatu na żywo Comm100 za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Comm100 Live Chat jednokrotnie logowanie (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Aplikacja Comm100 Live Chat obsługuje jednokrotne logowanie inicjowane przez **dostawcę usługi**

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Dodawanie aplikacji Comm100 Live Chat z galerii

Aby skonfigurować integrację aplikacji Comm100 Live Chat z usługą Azure AD, musisz dodać aplikację Comm100 Live Chat z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania wpisaj **czat na żywo Comm100.**
1. Wybierz **comm100 Czat na żywo** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla czatu na żywo comm100

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą czatu na żywo Comm100 przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w czacie na żywo comm100.

Aby skonfigurować i przetestować samouszeńców usługi Azure AD za pomocą czatu na żywo Comm100, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie logowania do czatu na żywo Comm100](#configure-comm100-live-chat-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego czatu na żywo Comm100](#create-comm100-live-chat-test-user)** — aby mieć odpowiednik B.Simon w czacie na żywo Comm100, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Czatu na żywo Comm100** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > Wartość adresu URL logowania nie jest prawdziwa. Ta wartość adresu URL logowania zostanie zaktualizowana przy użyciu rzeczywistego adresu URL logowania, co objaśniono w dalszej części tego samouczka.

1. Comm100 Live Chat aplikacja oczekuje potwierdzeń SAML w określonym formacie, który wymaga, aby dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/edit-attribute.png)

1. Oprócz powyższej aplikacji Czat na żywo Comm100 oczekuje kilku więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ---------------| --------------- |
    |   email    | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie czatu na żywo Comm100** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Comm100 Live Chat.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Comm100 Live Chat**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-comm100-live-chat-sso"></a>Konfigurowanie comm100 Live Chat SSO

1. W innym oknie przeglądarki internetowej zaloguj się do czatu na żywo Comm100 jako administrator zabezpieczeń.

1. W prawej górnej części strony kliknij pozycję **Moje konto**.

   ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. W lewej części menu kliknij pozycję **Zabezpieczenia**, a następnie kliknij pozycję **Agent logowania jednokrotnego**.

   ![Zabezpieczenia aplikacji Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Na stronie **Agent logowania jednokrotnego** wykonaj następujące czynności:

   ![Zabezpieczenia aplikacji Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Skopiuj pierwsze wyróżnione łącze i wklej go w skrzynce **tekstowej adresu URL logowania** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

   b. W polu tekstowym **Adres URL logowania jednokrotnego protokołu SAML** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

   d. W polu **tekstowym adres URL zdalnego wylogowania** wklej wartość **adresu URL wylogowania,** który został skopiowany z witryny Azure portal.

   d. Kliknij pozycję **Wybierz plik**, aby przekazać certyfikat zakodowany w formacie base-64 pobrany wcześniej z witryny Azure Portal do pozycji **Certyfikat**.

   e. Kliknij **pozycję Zapisz zmiany**.

### <a name="create-comm100-live-chat-test-user"></a>Tworzenie użytkownika testowego aplikacji Comm100 Live Chat

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do czatu na żywo Comm100, muszą one zostać aprowizowane do czatu na żywo Comm100. W aplikacji Comm100 Live Chat aprowizacja to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do czatu na żywo Comm100 jako administrator zabezpieczeń.

2. W prawej górnej części strony kliknij pozycję **Moje konto**.

    ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. W lewej części menu, kliknij pozycję **Agenci**, a następnie kliknij pozycję **Nowy agent**.

    ![Agent aplikacji Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Na stronie **Nowy agent** wykonaj następujące czynności:

    ![Nowy agent aplikacji Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. W polu tekstowym **E-mail** wpisz adres e-mail użytkownika, takiego jak **\@B.simon contoso.com**.

    b. W polu **tekstowym Imię** wprowadź imię użytkownika, takiego jak **B**.

    d. W polu tekstowym **Last Name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    d. W polu **tekstowym Nazwa wyświetlana** wprowadź nazwę wyświetlaną użytkownika, taką jak **B.simon**

    e. W polu tekstowym **Hasło** wpisz swoje hasło.

    f. Kliknij przycisk **Zapisz**.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka aplikacji Comm100 Live Chat w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Comm100 Live Chat, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj czat na żywo comm100 za pomocą usługi Azure AD](https://aad.portal.azure.com/)

