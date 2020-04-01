---
title: 'Samouczek: Integracja usługi Azure Active Directory z Platformą Obywatelską | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a platformą civic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d790454-143e-40ac-b3cb-5a256977b4db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ccf124c5a4160715df4e685e405dcd591c49ae7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68496826"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Samouczek: Integracja platformy obywatelskiej z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować platformę obywatelską z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Platformy Obywatelskiej z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do Platformy Obywatelskiej.
* Włącz automatyczne logowanie użytkowników do platformy obywatelskiej za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać miesięczny bezpłatny okres próbny [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Platforma Obywatelska z obsługą rejestracji jednokrotnej (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Platforma Obywatelska wspiera **SP** zainicjowane SSO





## <a name="adding-civic-platform-from-the-gallery"></a>Dodawanie Platformy Obywatelskiej z galerii

Aby skonfigurować integrację Platformy Obywatelskiej z usługą Azure AD, musisz dodać Platformę Obywatelską z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Platformę Obywatelską** w polu wyszukiwania.
1. Wybierz **Platformę Obywatelską** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą platformy obywatelskiej przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w platformie Civic.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą platformy civic, wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne Platformy Obywatelskiej](#configure-civic-platform-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Platformy Obywatelskiej](#create-civic-platform-test-user)** - aby mieć odpowiednik B.Simon w Platformie Obywatelskiej, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **Platformy Obywatelskiej** znajdź sekcję **Zarządzaj** i wybierz **opcję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.accela.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `civicplatform.accela.com`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem wsparcia Klienta Platformy Obywatelskiej,](mailto:skale@accela.com) aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

1. Przejdź do**rejestracji aplikacji** usługi Azure **Active Directory** > w usłudze Azure AD, wybierz aplikację.

1. Skopiuj **identyfikator katalogu (dzierżawy)** i zapisz go w Notatniku.

    ![Skopiuj katalog (identyfikator dzierżawy) i zapisz go w kodzie aplikacji](media/civic-platform-tutorial/directoryid.png)

1. Skopiuj **identyfikator aplikacji** i zapisz go w Notatniku.

   ![Kopiowanie identyfikatora aplikacji (klienta)](media/civic-platform-tutorial/applicationid.png)

1. Przejdź do**rejestracji aplikacji** usługi Azure **Active Directory** > w usłudze Azure AD, wybierz aplikację. Wybierz **pozycję Certyfikaty & wpisy tajne**.

1. Wybierz **pozycję Wpisy tajne klienta -> Nowy klucz tajny klienta**.

1. Podaj opis klucza tajnego i czas trwania. Po zakończeniu wybierz pozycję **Dodaj**.

   > [!NOTE]
   > Po zapisaniu klucza tajnego klienta wyświetlana jest wartość klucza tajnego klienta. Skopiuj tę wartość, ponieważ nie można pobrać klucza później.

   ![Skopiuj wartość tajną, ponieważ nie można pobrać tej wartości później](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Konfigurowanie SSO Platformy Obywatelskiej

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Atlassian Cloud jako administrator.

1. Kliknij **standardowe opcje**.

    ![Link do pobierania certyfikatu](media/civic-platform-tutorial/standard-choices.png)

1. Utwórz standardowy wybór **ssoconfig**.

1. Wyszukaj **ssoconfig** i prześlij.

    ![Link do pobierania certyfikatu](media/civic-platform-tutorial/sso-config.png)

1. Rozwiń SSOCONFIG klikając na czerwoną kropkę.

    ![Link do pobierania certyfikatu](media/civic-platform-tutorial/sso-config01.png)

1. Podaj informacje konfiguracyjne związane z logowaniami do logowania w następującym kroku:

    ![Link do pobierania certyfikatu](media/civic-platform-tutorial/sso-config02.png)

    1. W polu **applicationid** wprowadź wartość **identyfikatora aplikacji,** która została skopiowana z witryny Azure portal.

    1. W polu **clientSecret** wprowadź wartość **Secret,** która została skopiowana z witryny Azure portal.

    1. W polu **directoryId** wprowadź wartość **identyfikatora katalogu (dzierżawy),** która została skopiowana z portalu Azure.

    1. Wprowadź idpName. Przykład: `Azure`.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Platformy Obywatelskiej.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście wniosków wybierz **Platformę Obywatelską**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-civic-platform-test-user"></a>Tworzenie użytkownika testowego Platformy Obywatelskiej

W tej sekcji tworzysz użytkownika o nazwie B.Simon w Platformie Obywatelskiej. Współpracuj z zespołem wsparcia Platformy Obywatelskiej, aby dodać użytkowników do [zespołu wsparcia Klienta Platformy Obywatelskiej](mailto:skale@accela.com). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Platformy Obywatelskiej w panelu dostępu należy automatycznie zalogować się do Platformy Obywatelskiej, dla której masz skonfigurowany SSO. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

