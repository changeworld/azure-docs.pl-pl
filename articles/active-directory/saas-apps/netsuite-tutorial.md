---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z programem NetSuite | Dokumenty firmy Microsoft'
description: Dowiedz się, jak można skonfigurować funkcję logowania jednokrotnego między usługą Azure Active Directory i aplikacją NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a920e58f1ffd4c3e3e9769bf6346100a8677b90
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760060"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure AD (SSO) z programem NetSuite

W tym samouczku dowiesz się, jak zintegrować netsuite z usługą Azure Active Directory (Azure AD). Po zintegrowaniu NetSuite z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do NetSuite.
* Włącz użytkownikom automatyczne logowanie się do netsuite za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego netsuite.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. 

NetSuite obsługuje:

* Jednostki SSO inicjowane przez IDP.
* JIT (just-in-time) inicjowania obsługi administracyjnej użytkownika.
* [Automatyczne inicjowanie obsługi administracyjnej przez użytkowników](NetSuite-provisioning-tutorial.md).
* Po skonfigurowaniu netsuite można wymusić formanty sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Formanty sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Ponieważ identyfikator tej aplikacji jest stałą wartością ciągu, tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="add-netsuite-from-the-gallery"></a>Dodawanie netsuite z galerii

Aby skonfigurować integrację programu NetSuite z usługą Azure AD, dodaj program NetSuite z galerii do listy zarządzanych aplikacji SaaS, wykonując następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta służbowego lub szkolnego lub osobistego konta Microsoft.
1. W lewym okienku wybierz usługę **Azure Active Directory.**
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **polecenie NetSuite** w polu wyszukiwania.
1. W okienku wyników wybierz pozycję **NetSuite**, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu NetSuite

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą programu NetSuite przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji NetSuite.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą programu NetSuite, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso) aby umożliwić użytkownikom korzystanie z tej funkcji.
    * [Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user) aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem B.Simon.  
    * [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić użytkownikowi B.Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. [Skonfiguruj logowanie jednokrotne netsuite,](#configure-netsuite-sso) aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * [Utwórz użytkownika testowego NetSuite,](#create-the-netsuite-test-user) aby mieć odpowiednik użytkownika B.Simon w NetSuite, który jest połączony z reprezentacją usługi Azure AD użytkownika.
1. [Przetestuj sytą próbę sycącą,](#test-sso) aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Aby włączyć usługę Azure AD SSO w witrynie Azure portal, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **NetSuite** poszukaj sekcji **Zarządzaj,** a następnie wybierz pozycję **Logowanie jednokrotne**.
1. W okienku **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę **Edytuj** ("ołówek") obok **pozycji Podstawowa konfiguracja SAML**.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** w polu tekstowym **Odpowiedz adres URL** wpisz adres URL w jednym z następujących formatów:

    ||
    |-|
    | `https://<Account ID>.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.sandbox.NetSuite.com/saml2/acs`|

    * Otrzymasz ** < `Account ID` ** wartość w sekcji konfiguracji Netsuite, która jest wyjaśniona w dalszej części samouczka w kroku 8 w ramach konfiguracji Netsuite. Dokładną domenę znajdziesz (na przykład system.na0.netsuite.com w tym przypadku).

        ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Wartości w poprzednich adresach URL nie są prawdziwe. Zaktualizuj je za pomocą rzeczywistego adresu URL odpowiedzi. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej klienta NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). Można również odwołać się do formatów wyświetlanych w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

1. Aplikacja NetSuite oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji NetSuite oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > Wartość atrybutu konta nie jest rzeczywista. Zaktualizuj tę wartość, jak wyjaśniono w dalszej części tego samouczka.

1. Na stronie Konfigurowanie logowania jednokrotnego za pomocą saml w sekcji Certyfikat podpisywania SAML znajdź kod XML metadanych federacji i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie NetSuite** skopiuj odpowiedni adres URL lub adresy URL, w zależności od wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. W lewym okienku witryny Azure portal wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.

1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

1. W okienku Właściwości **użytkownika** wykonaj następujące kroki:

   a. W polu **Nazwa** wprowadź **b.simon**.  
   b. W polu **Nazwa użytkownika** username@companydomain.extension wprowadź (na B.Simon@contoso.comprzykład ).  
   d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.  
   d. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika B.Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając dostęp do NetSuite.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **NetSuite**.
1. W okienku przeglądu poszukaj sekcji **Zarządzaj,** a następnie wybierz łącze **Użytkownicy i grupy.**

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika,** a następnie w okienku **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Przycisk "Dodaj użytkownika"](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** z listy rozwijanej **Użytkownicy** wybierz pozycję **B.Simon**, a następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, wykonaj następujące czynności:

   a. W okienku **Wybierz rolę** na liście rozwijanej wybierz odpowiednią rolę dla użytkownika.  
   b. U dołu ekranu wybierz przycisk **Wybierz.**
1. W okienku **Dodawanie przydziału** wybierz przycisk **Przypisz.**

## <a name="configure-netsuite-sso"></a>Konfigurowanie sytcha syd w programie NetSuite

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny firmy NetSuite jako administrator.

2. Na górnym pasku nawigacyjnym wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Włącz funkcje firmy** > **Enable Features**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na pasku narzędzi na środku strony **wybierz**suite.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-suitecloud.png)

4. W obszarze **Zarządzanie uwierzytelnianiem**zaznacz pole wyboru **Saml Logowanie jednokrotne,** aby włączyć opcję logowania jednokrotnego SAML w programie NetSuite.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na górnym pasku nawigacyjnym wybierz pozycję **Instalator**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

6. Na liście **Zadania instalacji** wybierz pozycję **Integracja**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-integration.png)

7. W obszarze **Zarządzanie uwierzytelnianiem**wybierz pozycję **Saml Logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-saml.png)

8. W okienku **Instalacji SAML** w obszarze **Konfiguracja NetSuite**wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Zaznacz pole wyboru **Metoda uwierzytelniania podstawowego.**

    b. W obszarze **Metadane dostawcy tożsamości SAMLV2**wybierz pozycję **Przekaż plik metadanych IDP,** a następnie wybierz pozycję **Przeglądaj,** aby przekazać plik metadanych pobrany z witryny Azure portal.

    d. Wybierz pozycję **Prześlij**.

9. Na górnym pasku nawigacyjnym NetSuite wybierz pozycję **Ustawienia**, a następnie wybierz pozycję**Informacje o** **firmie** > .

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-account-id.png)

    b. W okienku **Informacje o firmie** w prawej kolumnie skopiuj wartość **identyfikatora konta.**

    d. Wklej **identyfikator konta** skopiowany z konta NetSuite do pola **Wartość atrybutu** w usłudze Azure AD.

10. Zanim użytkownicy będą mogli wykonywać logowanie jednokrotne do aplikacji NetSuite, należy im wcześniej przypisać odpowiednie uprawnienia w tej aplikacji. Aby przypisać te uprawnienia, wykonaj następujące czynności:

    a. Na górnym pasku nawigacyjnym wybierz pozycję **Instalator**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

    b. W lewym okienku wybierz **pozycję Użytkownicy/Role**, a następnie wybierz pozycję **Zarządzaj rolami**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-manage-roles.png)

    d. Wybierz **pozycję Nowa rola**.

    d. Wprowadź **nazwę** nowej roli.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-new-role.png)

    e. Wybierz **pozycję Zapisz**.

    f. Na górnym pasku nawigacyjnym wybierz pozycję **Uprawnienia**. Następnie wybierz **pozycję Instalator**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-sso.png)

    g. Wybierz **saml logowanie jednokrotne**, a następnie wybierz pozycję **Dodaj**.

    h. Wybierz **pozycję Zapisz**.

    i. Na górnym pasku nawigacyjnym wybierz pozycję **Instalator**, a następnie wybierz pozycję **Menedżer instalacji**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

    j. W lewym okienku wybierz **pozycję Użytkownicy/Role**, a następnie wybierz pozycję **Zarządzaj użytkownikami**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Wybierz użytkownika testowego, wybierz pozycję **Edytuj**, a następnie wybierz kartę **Dostęp.**

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-edit-user.png)

    l. W okienku **Role** przypisz utworzoną odpowiednią rolę.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-add-role.png)

    m. Wybierz **pozycję Zapisz**.

### <a name="create-the-netsuite-test-user"></a>Tworzenie użytkownika testowego NetSuite

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w NetSuite. Aplikacja NetSuite obsługuje aprowizację użytkowników just in time, która jest domyślnie włączona. W tej sekcji nie ma dla Ciebie żadnego elementu akcji. Jeśli użytkownik jeszcze nie istnieje w aplikacji NetSuite, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka NetSuite w Panelu dostępu należy automatycznie zalogować się do netsuite, dla którego skonfigurowano logującą się logującą logującą log. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Wypróbuj program NetSuite z usługą Azure AD](https://aad.portal.azure.com/)
- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić NetSuite dzięki zaawansowanej widoczności i sterowaniu](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)