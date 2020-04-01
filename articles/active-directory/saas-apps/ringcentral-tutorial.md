---
title: 'Samouczek: Integracja usługi Azure Active Directory z RingCentral | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: de7cf57d177902efdbb44524703481e8c65c75c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72991470"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Samouczek: Integracja ringcentral z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować RingCentral z usługą Azure Active Directory (Azure AD). Po zintegrowaniu RingCentral z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do RingCentral.
* Włącz użytkownikom automatyczne logowanie do RingCentral za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego RingCentral (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* RingCentral obsługuje zainicjowane przez **IDP** SSO

## <a name="adding-ringcentral-from-the-gallery"></a>Dodawanie RingCentral z galerii

Aby skonfigurować integrację RingCentral z usługą Azure AD, należy dodać RingCentral z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **RingCentral** w polu wyszukiwania.
1. Wybierz **pozycję RingCentral** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą funkcji RingCentral przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby użytkownik sytojęzyczny działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w RingCentral.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą ringcentral, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne RingCentral](#configure-ringcentral-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego RingCentral](#create-ringcentral-test-user)** — aby mieć odpowiednik B.Simon w RingCentral, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **RingCentral** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    1. Kliknij pozycję **Przekaż plik metadanych**.
    1. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.
    1. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatora** i **adresu URL odpowiedzi** są automatycznie wypełniane w sekcji **Podstawowa konfiguracja SAML.**

    > [!Note]
    > Otrzymasz **plik metadanych dostawcy usług** na ringcentral konfiguracji SSO strony, która jest wyjaśniona w dalszej części samouczka.

1. Jeśli nie masz **pliku metadanych usługodawcy,** wprowadź wartości dla następujących pól:

    a. W polach tekstowych **Identyfikator** wpisz adres URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie Britta Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do RingCentral.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **pozycję RingCentral**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-ringcentral-sso"></a>Konfigurowanie sytów sytowych ringcentral

1. Aby zautomatyzować konfigurację w ringcentral, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij **Przycisk Ustaw RingCentral** przekieruje Cię do aplikacji RingCentral. W tym miejscu podaj poświadczenia administratora, aby zalogować się do RingCentral. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja instalacji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować RingCentral, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy RingCentral jako administrator i wykonaj następujące czynności:

1. Na górze kliknij **narzędzia**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Przejdź do **logowania jednokrotnego**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Na stronie **Logowanie jednokrotne w** sekcji **Konfiguracja logowania jednokrotnego** z **kroku 1** kliknij pozycję **Edytuj** i wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego** wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Kliknij **przycisk Przeglądaj,** aby przekazać plik metadanych pobrany z witryny Azure Portal.

    b. Po przesłaniu metadanych wartości są automatycznie wypełniane w sekcji **Informacje ogólne logowania.**

    d. W sekcji **Mapowanie atrybutów** wybierz pozycję **Mapuj atrybut e-mail jako**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Kliknij przycisk **Zapisz**.

    e. Z **kroku 2** kliknij **przycisk Pobierz,** aby pobrać plik **metadanych dostawcy usług** i przekazać go w sekcji **Podstawowa konfiguracja SAML,** aby automatycznie wypełniać wartości **identyfikatora** i **odpowiedzi adresu URL** w witrynie Azure portal.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na tej samej stronie przejdź do sekcji **Włącz jednocześnie** i wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Wybierz **włącz usługę SSO**.

    * Wybierz **pozycję Zezwalaj użytkownikom na logowanie się za pomocą poświadczeń logowania jednośliowego lub ringcentral**.

    * Kliknij przycisk **Zapisz**.

### <a name="create-ringcentral-test-user"></a>Utwórz użytkownika testowego RingCentral

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w RingCentral. Praca z [zespołem pomocy technicznej klienta RingCentral,](https://success.ringcentral.com/RCContactSupp) aby dodać użytkowników na platformie RingCentral. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka RingCentral w Panelu dostępu należy automatycznie zalogować się do RingCentral, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj narzędzie RingCentral za pomocą usługi Azure AD](https://aad.portal.azure.com/)