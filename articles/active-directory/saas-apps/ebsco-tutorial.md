---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z EBSCO | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a ebsco.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f37085744b9a0e7785ef3a411d53e4df5d15e494
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72595005"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z EBSCO

W tym samouczku dowiesz się, jak zintegrować EBSCO z usługą Azure Active Directory (Azure AD). Po zintegrowaniu EBSCO z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do EBSCO.
* Włącz użytkownikom automatyczne logowanie do ebsco za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z funkcją logowania jednokrotnego (SSO) EBSCO.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* EBSCO obsługuje **sp i idp** zainicjowane SSO
* EBSCO obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-ebsco-from-the-gallery"></a>Dodawanie EBSCO z galerii

Aby skonfigurować integrację EBSCO z usługą Azure AD, należy dodać EBSCO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **EBSCO** w polu wyszukiwania.
1. Wybierz **EBSCO** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi EBSCO

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą ebsco przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w EBSCO.

Aby skonfigurować i przetestować usługę Azure AD SSO przy za pomocą usługi EBSCO, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie SSO EBSCO](#configure-ebsco-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego EBSCO](#create-ebsco-test-user)** — aby mieć odpowiednik B.Simon w EBSCO, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **EBSCO** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    W polu tekstowym **Identyfikator** wpisz adres URL: `pingsso.ebscohost.com`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta EBSCO,](mailto:support@ebsco.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    o **Unikalne elementy:**  

    o **Custid** = Wprowadź unikatowy identyfikator klienta EBSCO 

    o **Profil** = Klienci mogą dostosować link do bezpośredniego użytkownika do określonego profilu (w zależności od tego, co kupują od EBSCO). Mogą wprowadzić określony identyfikator profilu. Główne identyfikatory to eds (EBSCO Discovery Service) i ehost (bazy danych EBSOCOhost). Instrukcje dla tego samego są podane [tutaj](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. Aplikacja EBSCO oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

    > [!Note]
    > Atrybut **name** jest obowiązkowy i jest mapowany z **wartością identyfikatora nazwy** w aplikacji EBSCO. Jest to domyślnie dodawane, więc nie trzeba dodawać tego ręcznie.

1. Oprócz powyższej aplikacji EBSCO oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Adres e-mail   | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie EBSCO** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do EBSCO.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **EBSCO**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-ebsco-sso"></a>Konfigurowanie sytcha w ebsco

Aby skonfigurować logowanie jednokrotne po stronie **EBSCO,** należy wysłać pobrany **kod XML metadanych federacji** i odpowiednie skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej EBSCO.](mailto:support@ebsco.com) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-ebsco-test-user"></a>Tworzenie użytkownika testowego EBSCO

W przypadku EBSCO inicjowanie obsługi administracyjnej użytkownika jest automatyczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

Usługa Azure AD przekazuje wymagane dane do aplikacji EBSCO. Inicjowanie obsługi administracyjnej EBSCO może być automatyczne lub wymagać jednorazowego formularza. To zależy od tego, czy klient ma wiele wcześniej istniejących kont EBSCOhost z ustawieniami osobistymi zapisanymi. To samo można omówić z [zespołem wsparcia EBSCO](mailto:support@ebsco.com) podczas wdrażania. Tak czy inaczej, klient nie musi tworzyć żadnych kont EBSCOhost przed testowaniem.

   > [!Note]
   > Można zautomatyzować inicjowanie obsługi administracyjnej/personalizację użytkowników EBSCOhost. Skontaktuj się z [zespołem pomocy technicznej EBSCO](mailto:support@ebsco.com) na temat inicjowania obsługi administracyjnej użytkowników just-in-time.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

1. Po kliknięciu kafelka EBSCO w Panelu dostępu należy automatycznie zalogować się do aplikacji EBSCO.
Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

1. Po zalogowaniu się do aplikacji kliknij przycisk **logowania w** prawym górnym rogu.

    ![Logowanie EBSCO na liście Aplikacje](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. Otrzymasz jednorazowy monit o powiązanie logowania instytucjonalnego / SAML z **linkiem do istniejącego konta MyEBSCOhost z kontem instytucji teraz** lub **Utwórz nowe konto MyEBSCOhost i połącz je z kontem instytucji.** Konto służy do personalizacji w aplikacji EBSCOhost. Wybierz opcję **Utwórz nowe konto,** a zobaczysz, że formularz personalizacji jest wstępnie wypełniony z wartościami z odpowiedzi saml, jak pokazano na poniższym zrzucie ekranu. Kliknij **przycisk "Kontynuuj",** aby zapisać ten wybór.
    
     ![Użytkownik EBSCO na liście Aplikacje](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Po zakończeniu powyższej konfiguracji wyczyść pliki cookie/pamięć podręczną i zaloguj się ponownie. Nie musisz ponownie logować się ręcznie, a ustawienia personalizacji są zapamiętywane.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj EBSCO z usługą Azure AD](https://aad.portal.azure.com/)