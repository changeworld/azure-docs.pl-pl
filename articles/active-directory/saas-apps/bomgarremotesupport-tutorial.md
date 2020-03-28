---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z pomocą zdalną BeyondTrust | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a pomocą zdalną BeyondTrust.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff21c3ee7721c82232e668ddb9645895080cf79
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74082046"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z pomocą zdalną beyondtrust

W tym samouczku dowiesz się, jak zintegrować pomoc techniczną beyondtrust zdalną z usługą Azure Active Directory (Azure AD). Po zintegrowaniu pomocy technicznej beyondtrust z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do pomocy zdalnej BeyondTrust.
* Włącz użytkownikom automatyczne logowanie do pomocy technicznej beyondtrust za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną obsługą pojedynczą (SSO) beyondtrust.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* BeyondTrust Remote Support obsługuje sso inicjowane przez **usługę SP**
* Pomoc techniczna beyondtrust remote obsługuje **inicjowanie** obsługi administracyjnej użytkowników w czasie

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Dodawanie zdalnej pomocy technicznej BeyondTrust z galerii

Aby skonfigurować integrację pomocy technicznej zdalnej BeyondTrust z usługą Azure AD, należy dodać pomoc techniczną beyondtrust zdalną z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **BeyondTrust Remote Support** w polu wyszukiwania.
1. Wybierz **beyondtrust zdalnej pomocy technicznej** z panelu wyników, a następnie dodać aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla zdalnej pomocy technicznej BeyondTrust

Konfigurowanie i testowanie usługi Azure AD SSO przy użyciu zdalnej pomocy technicznej BeyondTrust przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby funkcja SSO działała, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w pomocy zdalnej BeyondTrust.

Aby skonfigurować i przetestować usługę Azure AD SSO przy pomocy zdalnej pomocy technicznej BeyondTrust, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj identyfikator sygny obsługi zdalnej BeyondTrust](#configure-beyondtrust-remote-support-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego BeyondTrust Remote Support](#create-beyondtrust-remote-support-test-user)** — aby mieć odpowiednik B.Simon w BeyondTrust remote support, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **BeyondTrust Remote Support** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<HOSTNAME>.bomgar.com/saml`

    b. W polu **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<HOSTNAME>.bomgar.com`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Otrzymasz te wartości wyjaśnione w dalszej części samouczka.

1. Aplikacja BeyondTrust Remote Support oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji BeyondTrust Remote Support oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ---------------| ----------|
    | Givenname | user.givenname |
    | Emailaddress | user.mail |
    | Nazwa | user.userprincipalname |
    | Nazwa użytkownika | user.userprincipalname |
    | Grupy | user.groups |
    | Unikatowy identyfikator użytkownika | user.userprincipalname |

    > [!NOTE]
    > Podczas przypisywania grup usługi Azure AD dla aplikacji BeyondTrust Remote Support opcja "Grupy zwrócone w żądaniu" musi zostać zmodyfikowana z None to SecurityGroup. Grupy zostaną zaimportowane do aplikacji jako ich identyfikatory obiektów. Identyfikator obiektu grupy usługi Azure AD można znaleźć, sprawdzając właściwości w interfejsie usługi Azure Active Directory. Będzie to wymagane do odwoływania się i przypisywania grup usługi Azure AD do odpowiednich zasad grupy.

1. Podczas ustawiania unikatowego identyfikatora użytkownika wartość ta musi być ustawiona na NameID-Format: **Persistent**. Wymagamy, aby był to identyfikator trwały, aby poprawnie zidentyfikować i skojarzyć użytkownika z poprawnymi zasadami grupy dla uprawnień. Kliknij ikonę edycji, aby otworzyć okno dialogowe **Atrybuty & Oświadczenia użytkownika,** aby edytować wartość unikatowego identyfikatora użytkownika.

1. W sekcji **Zarządzanie zgłoś** kliknij **format wybierz identyfikator nazwy** i ustaw wartość **Trwałe** i kliknij przycisk **Zapisz**.

    ![Atrybuty i oświadczenia użytkownika](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie zdalnej pomocy technicznej BeyondTrust** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do pomocy technicznej zdalnej BeyondTrust.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **BeyondTrust Remote Support**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-beyondtrust-remote-support-sso"></a>Konfigurowanie usługi SSO zdalnej obsługi BeyondTrust

1. W innym oknie przeglądarki sieci Web zaloguj się do pomocy technicznej zdalnej BeyondTrust jako administrator.

1. Kliknij menu **STATUS** i skopiuj **identyfikator**, **odpowiedz adres URL** i Zaloguj się na **adres URL** i użyj tych wartości w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    ![Konfigurowanie zdalnej obsługi BeyondTrust](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Przejdź do interfejsu BeyondTrust Remote `https://support.example.com/login` Support /login, w którym **support.example.com** jest główną nazwa hosta urządzenia i uwierzytelnij się przy użyciu poświadczeń administracyjnych.

1. Przejdź do **pozycji Użytkownicy &** > **dostawcy zabezpieczeń**.

1. W menu rozwijanym wybierz **SAML** i kliknij przycisk **Utwórz dostawcę.**

1. W sekcji Ustawienia dostawcy tożsamości istnieje opcja przekazywania metadanych dostawcy tożsamości. Znajdź plik XML metadanych pobrany z witryny Azure portal i kliknij przycisk **Przekaż.** **Identyfikator jednostki,** **adres URL usługi logowania jednokrotnego** i certyfikat zostaną automatycznie przesłane, a **powiązanie protokołu** będzie musiało zostać zmienione na HTTP **POST**. Zobacz zrzut ekranu poniżej:

    ![Konfigurowanie zdalnej obsługi BeyondTrust](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>Utwórz użytkownika testowego pomocy zdalnej BeyondTrust

Tutaj skonfigurujemy Ustawienia aprowisk użytkownika. Wartości używane w tej sekcji będą odwoływać się z **atrybutów użytkownika & oświadczenia** sekcji w witrynie Azure portal. Skonfigurowaliśmy to jako wartości domyślne, które są już importowane w momencie tworzenia, jednak wartość można dostosować w razie potrzeby.

![Tworzenie użytkownika](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> Grupy i atrybut e-mail nie są niezbędne dla tej implementacji. Jeśli korzystanie z grup usługi Azure AD i przypisywanie ich do beyondtrust zdalnych zasad grupy pomocy technicznej dla uprawnień, identyfikator obiektu grupy należy odwoływać się za pośrednictwem jego właściwości w witrynie Azure portal i umieszczone w sekcji "Dostępne grupy". Po zakończeniu tej pracy grupa Identyfikator obiektu/AD będzie teraz dostępna do przypisania do zasad grupy uprawnień.

![Tworzenie użytkownika](./media/bomgarremotesupport-tutorial/config-user2.png)

![Tworzenie użytkownika](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> Alternatywnie można ustawić domyślną zasadę grupy w dostawcy zabezpieczeń SAML2. Definiując tę opcję, spowoduje to przypisanie wszystkim użytkownikom, którzy uwierzytelniają za pośrednictwem saml uprawnień określonych w zasadach grupy. Zasady Ogólne elementy członkowskie są zawarte w beyondtrust zdalnej pomocy technicznej/uprzywilejowanego dostępu zdalnego z ograniczonymi uprawnieniami, które mogą służyć do testowania uwierzytelniania i przypisywania użytkowników do poprawnych zasad. Użytkownicy nie będą wypełniać się na liście użytkowników SAML2 za pośrednictwem /login > Użytkownicy & Security do czasu pierwszej pomyślnej próby uwierzytelnienia. Dodatkowe informacje na temat zasad grupy można znaleźć pod następującym linkiem:`https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka BeyondTrust Remote Support w Panelu dostępu należy automatycznie zalogować się do zdalnej pomocy technicznej BeyondTrust, dla której skonfigurowano logującą się logującą log. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj zdalną pomoc techniczną BeyondTrust za pomocą usługi Azure AD](https://aad.portal.azure.com/)
