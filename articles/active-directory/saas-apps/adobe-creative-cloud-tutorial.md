---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą Adobe Creative Cloud | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i rozwiązaniem Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25dd638c15fecbef787e4ceabea9ae7cb4359582
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76120370"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą Adobe Creative Cloud

> [!NOTE]
> W tym artykule opisano niestandardowe ustawienia oparte na saml w usłudze Azure Active Directory (Azure AD) w usłudze Adobe Admin Console. W przypadku zupełnie nowych konfiguracji zaleca się użycie usługi [Azure AD Connector](https://helpx.adobe.com/enterprise/using/sso-setup-azure.html). Usługa Azure AD Connector można skonfigurować w ciągu kilku minut i skraca proces oświadczeń domeny, konfiguracji logowania jednokrotnego i synchronizacji użytkownika.

W tym samouczku dowiesz się, jak zintegrować usługę Adobe Creative Cloud z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Adobe Creative Cloud z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do usługi Adobe Creative Cloud.
* Umożliwia automatyczne logowanie użytkowników do usługi Adobe Creative Cloud za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z funkcją logowania jednokrotnego (SSO) firmy Adobe Creative Cloud.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Rozwiązanie Adobe Creative Cloud obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**





## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Dodawanie rozwiązania Adobe Creative Cloud z galerii

Aby skonfigurować integrację rozwiązania Adobe Creative Cloud z usługą Azure AD, należy dodać rozwiązanie Adobe Creative Cloud z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **adobe creative cloud** w polu wyszukiwania.
1. Wybierz **pozycję Adobe Creative Cloud** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-adobe-creative-cloud"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi Adobe Creative Cloud

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi Adobe Creative Cloud przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Adobe Creative Cloud.

Aby skonfigurować i przetestować sytów usługi Azure AD za pomocą usługi Adobe Creative Cloud, wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne usługi Adobe Creative Cloud](#configure-adobe-creative-cloud-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** — aby mieć odpowiednik B.Simon w usłudze Adobe Creative Cloud, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **Adobe Creative Cloud** znajdź sekcję **Zarządzaj** i wybierz **opcję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://adobe.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego identyfikatora. W celu uzyskania tej wartości skontaktuj się z [zespołem pomocy technicznej klienta rozwiązania Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Adobe Creative Cloud oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja Adobe Creative Cloud oczekuje, że kilka więcej atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Adres e-mail | user.mail |

    > [!NOTE]
    > Aby wartość oświadczenia e-mail była wypełniana w odpowiedzi SAML, użytkownicy muszą mieć ważną licencję usługi Office 365 ExO.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź numer **XML danych federacyjnej,** a następnie wybierz pozycję **Pobierz,** aby pobrać plik metadanych XML i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie usługi Adobe Creative Cloud** skopiuj odpowiednie adresy URL na podstawie wymagań użytkownika.

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

W tej sekcji włączysz funkcję B.Simon, aby korzystać z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi Adobe Creative Cloud.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Adobe Creative Cloud**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-adobe-creative-cloud-sso"></a>Konfigurowanie usługi Adobe Creative Cloud SSO

1. W innym oknie przeglądarki internetowej zaloguj się do [Konsoli administracyjnej Adobe](https://adminconsole.adobe.com) jako administrator systemu.

1. Przejdź do **pozycji Ustawienia** na górnym pasku nawigacyjnym, a następnie wybierz pozycję **Tożsamość**. Zostanie otwarta lista katalogów. Wybierz odpowiedni katalog Federowany.

1. Na stronie **Szczegóły katalogu** wybierz pozycję **Konfiguruj**.

1. Skopiuj identyfikator jednostki i adres URL usługi ACS (adres URL usługi konsumenta potwierdzenia lub adres URL odpowiedzi). Wprowadź adresy URL w odpowiednich polach w witrynie Azure portal.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Użyj wartości identyfikatora jednostki dostarczonej przez firmę Adobe dla **identyfikatora** w oknie dialogowym **Konfigurowanie ustawień aplikacji.**

    b. Użyj wartości adresu URL usługi ACS (adres URL usługi konsumenta potwierdzenia) podanej przez firmę Adobe dla **adresu URL odpowiedzi** w oknie dialogowym **Konfigurowanie ustawień aplikacji.**

1. U dołu strony przekaż plik **XML danych federacyjnej** pobrany z witryny Azure Portal. 

    ![Plik XML danych federacyjnych](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "Kod XML metadanych IdP")

1. Wybierz **pozycję Zapisz**.


### <a name="create-adobe-creative-cloud-test-user"></a>Tworzenie użytkownika testowego rozwiązania Adobe Creative Cloud

Aby umożliwić użytkownikom usługi Azure AD logowanie się do usługi Adobe Creative Cloud, muszą one zostać aprowizowane w usłudze Adobe Creative Cloud. W przypadku rozwiązania Adobe Creative Cloud aprowizowanie jest zadaniem ręcznym.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aby aprowizować konta użytkowników, wykonaj następujące czynności:

1. Zaloguj się w witrynie konsoli administracyjnej [Adobe Admin Console](https://adminconsole.adobe.com) jako administrator.

2. W konsoli firmy Adobe dodaj użytkownika z identyfikatorem Federated ID i przypisz go do profilu produktu. Aby uzyskać szczegółowe informacje na temat dodawania użytkowników, zobacz [Add users in Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers) (Dodawanie użytkowników w konsoli administracyjnej firmy Adobe). 

3. W tym momencie wpisz swój adres e-mail/upn w formularzu logowania Adobe, naciśnij kartę i powinieneś zostać sfederowany z powrotem do usługi Azure AD:
   * Dostęp do\.sieci: www adobe.com > logowanie
   * W narzędziu aplikacji klasycznej > logowanie
   * W aplikacji > pomoc > logowanie

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Adobe Creative Cloud w panelu dostępu powinno nastąpić automatyczne zalogowanie do rozwiązania Adobe Creative Cloud, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Adobe Creative Cloud z usługą Azure AD](https://aad.portal.azure.com/)

- [Konfigurowanie tożsamości (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-identity.html)
  
- [Konfiguracja oprogramowania Microsoft Azure do użytku z funkcją Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

