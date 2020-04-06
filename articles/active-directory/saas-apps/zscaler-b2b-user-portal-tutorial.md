---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z portalem użytkownika Zscaler B2B | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a portalem użytkownika Zscaler B2B.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 75b90449-095d-4e47-a1fd-8b5cbbfe7e3d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28af94c92fa56d5217aee91053521d2c12b0aef
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668930"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z portalem użytkownika Zscaler B2B

W tym samouczku dowiesz się, jak zintegrować portal użytkownika Zscaler B2B z usługą Azure Active Directory (Azure AD). Po zintegrowaniu portalu użytkownika Zscaler B2B z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do portalu użytkownika Zscaler B2B.
* Włącz użytkownikom automatyczne logowanie do portalu użytkownika Zscaler B2B za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z pomocą jednego logowania (SSO) zscaler B2B User Portal.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Portal użytkownika Zscaler B2B obsługuje zainicjowane przez protokół SSO protokołu **IDP**

* Portal użytkownika Zscaler B2B obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

* Po skonfigurowaniu portalu użytkownika Zscaler B2B można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-zscaler-b2b-user-portal-from-the-gallery"></a>Dodawanie portalu użytkownika Zscaler B2B z galerii

Aby skonfigurować integrację portalu użytkownika Zscaler B2B z usługą Azure AD, należy dodać portal użytkownika Zscaler B2B z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Zscaler B2B User Portal** w polu wyszukiwania.
1. Wybierz **Zscaler B2B User Portal** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-b2b-user-portal"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla portalu użytkownika Zscaler B2B

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą portalu użytkownika Zscaler B2B przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w portalu użytkownika Zscaler B2B.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą portalu użytkownika Zscaler B2B, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie logowania do użytkownika Zscaler B2B](#configure-zscaler-b2b-user-portal-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego portalu użytkownika Zscaler B2B](#create-zscaler-b2b-user-portal-test-user)** — aby mieć odpowiednik B.Simon w portalu użytkownika Zscaler B2B, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Zscaler B2B User Portal** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta portalu użytkownika Zscaler B2B,](https://help.zscaler.com/) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie portalu użytkownika Zscaler B2B** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do portalu użytkownika Zscaler B2B.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Zscaler B2B User Portal**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-zscaler-b2b-user-portal-sso"></a>Konfigurowanie identyfikatora SSO portalu użytkownika Zscaler B2B

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Zscaler B2B User Portal jako administrator i wykonaj następujące czynności:

1. Po lewej stronie menu kliknij pozycję **Administracja** i przejdź do sekcji **UWIERZYTELNIANIE,** kliknij pozycję **Konfiguracja idP**.

    ![Administracja administratora dostępu prywatnego zscaler](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. W prawym górnym rogu kliknij pozycję **Dodaj konfigurację idp**. 

    ![Zscaler Administrator dostępu prywatnego idp](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. Na stronie **Dodaj konfigurację IdP** wykonaj następujące czynności:
 
    ![Zscaler Administrator dostępu prywatnego wybierz](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. Kliknij **pozycję Wybierz plik,** aby przekazać pobrany plik metadanych z usługi Azure AD w polu **Przekazywanie pliku metadanych IdP.**

    b. Odczytuje **metadane IdP** z usługi Azure AD i wypełnia wszystkie informacje o polach, jak pokazano poniżej.

    ![Zscaler Private Access Administrator config](./media/zscaler-b2b-user-tutorial/config.png)

    d. Wybierz domenę z pola **Domeny.**
    
    d. Kliknij przycisk **Zapisz**.

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Utwórz użytkownika testowego portalu użytkownika Zscaler B2B

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Zscaler B2B User Portal. Zscaler B2B User Portal obsługuje just-in-time użytkownika inicjowania obsługi administracyjnej, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w portalu użytkownika Zscaler B2B, nowy jest tworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Portalu użytkownika Zscaler B2B w Panelu dostępu należy automatycznie zalogować się do portalu użytkownika Zscaler B2B, dla którego skonfigurowano logowanie jednośladowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj portal użytkownika Zscaler B2B w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)