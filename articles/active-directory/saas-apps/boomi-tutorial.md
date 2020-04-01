---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z boomi | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22a36d5e6c36008c3a574cbcf9be8ec4f52b82b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77086458"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z boomi

W tym samouczku dowiesz się, jak zintegrować Boomi z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Boomi z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Boomi.
* Włącz użytkownikom automatyczne logowanie do boomi za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z funkcją rejestracji jednokrotnej Boomi (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Platforma Boomi obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**
* Po skonfigurowaniu Boomi można wymusić kontrole sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Formanty sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-boomi-from-the-gallery"></a>Dodawanie platformy Boomi z galerii

Aby skonfigurować integrację platformy Boomi z usługą Azure AD, musisz dodać platformę Boomi z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Boomi** w polu wyszukiwania.
1. Wybierz **Boomi** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla boomi

Konfigurowanie i testowanie usługi Azure AD SSO z Boomi przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Boomi.

Aby skonfigurować i przetestować syg jako jeden na dział usługi Azure AD za pomocą boomi, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Logowanie SSO Boomi](#configure-boomi-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Boomi](#create-boomi-test-user)** — aby mieć odpowiednik B.Simon w Boomi, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Boomi** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego.**
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli w sekcji **Podstawowa konfiguracja SAML** masz **plik metadanych dostawcy usług** i chcesz skonfigurować go w trybie inicjowanym przez **dostawcę** tożsamości, wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    d. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatora** i **adresu URL odpowiedzi** są automatycznie wypełniane w sekcji Podstawowa konfiguracja SAML.

    ![image](common/idp-intiated.png)

    > [!Note]
    > **Otrzymasz plik metadanych usługodawcy** z sekcji **Konfigurowanie SSO Boomi,** co wyjaśniono w dalszej części samouczka. Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

1. Aplikacja Boomi oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej, aplikacja Boomi oczekuje, że kilka więcej atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Boomi** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Boomi.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Boomi**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-boomi-sso"></a>Konfigurowanie syfonu SSO boomi

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Boomi jako administrator.

1. Przejdź do pozycji **Nazwa firmy** i przejdź do pozycji **Set up** (Konfigurowanie).

1. Kliknij kartę **SSO Options** (Opcje logowania jednokrotnego), a następnie wykonaj poniższe kroki.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Zaznacz pole wyboru **Enable SAML Single Sign-On** (Włącz logowanie jednokrotne SAML).

    b. Kliknij przycisk **Import** (Importuj), aby przekazać pobrany certyfikat z usługi Azure AD do pola **Identity Provider Certificate** (Certyfikat dostawcy tożsamości).

    d. W polu tekstowym **Identity Provider Login URL** (Adres URL logowania dostawcy tożsamości) wstaw wartość**adresu URL logowania** z okna konfiguracji aplikacji usługi Azure AD.

    d. W przypadku **lokalizacji identyfikatora federacji**wybierz **identyfikator federacji w FEDERATION_ID** przycisk opcji elementu atrybutu.

    e. Skopiuj **adres URL MetaData AtomSphere**, przejdź do **adresu URL MetaData** za pośrednictwem wybranej przeglądarki i zapisz dane wyjściowe w pliku. Przekaż **adres URL metadata** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    f. Kliknij przycisk **Save** (Zapisz).

### <a name="create-boomi-test-user"></a>Tworzenie użytkownika testowego platformy Boomi

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Boomi, muszą one być aprowizowane do Boomi. W przypadku platformy Boomi aprowizowanie jest zadaniem ręcznym.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy Boomi jako administrator.

1. Po zalogowaniu się przejdź do pozycji **User Management** (Zarządzanie użytkownikami) i wybierz pozycję **Users** (Użytkownicy).

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_001.png "Użytkownicy")

1. Kliknij **+** ikonę i zostanie otwarte okno dialogowe **Dodaj/Zachowaj role użytkownika.**

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_002.png "Użytkownicy")

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_003.png "Użytkownicy")

    a. W polu tekstowym **User e-mail address** (Adres e-mail użytkownika) wpisz adres e-mail użytkownika, taki jak B.Simon@contoso.com.

    b. W polu tekstowym **Imię** wpisz imię użytkownika, takiego jak B.

    d. W polu **tekstowym Nazwisko** wpisz nazwisko użytkownika, takiego jak Simon.

    d. Wprowadź **identyfikator federacyjny** użytkownika. Każdy użytkownik musi mieć identyfikator federacyjny, który w sposób unikatowy identyfikuje użytkownika w ramach konta.

    e. Przypisz do użytkownika rolę **Standard User** (Użytkownik standardowy). Nie należy przypisywać roli Administrator, ponieważ dałoby to im normalny dostęp do atmosfery, a także dostęp do logowania jednokrotnego.

    f. Kliknij przycisk **OK**.

    > [!NOTE]
    > Użytkownik nie otrzyma powitalnej wiadomości e-mail z powiadomieniem zawierającym hasło, które może być użyte do zalogowania się do konta AtomSphere, ponieważ ich hasło jest zarządzane za pośrednictwem dostawcy tożsamości. Do aprowizowania kont użytkowników usługi Azure Active Directory możesz użyć dowolnych innych interfejsów API lub narzędzi do tworzenia kont użytkowników platformy Boomi oferowanych przez tę platformę.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Boomi w panelu dostępu powinno nastąpić automatyczne zalogowanie do platformy Boomi, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Wypróbuj Boomi z usługą Azure AD](https://aad.portal.azure.com/)
