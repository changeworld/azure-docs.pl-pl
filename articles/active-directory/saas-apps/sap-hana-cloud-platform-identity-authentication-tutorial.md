---
title: 'Samouczek: Integracja usługi Azure Active Directory z uwierzytelnianiem tożsamości platformy SAP Cloud Platform | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługami Azure Active Directory i SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95be73bd125c124409585a478fa9707e7b6a2ac2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76289071"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z uwierzytelnianiem tożsamości platformy SAP cloud

W tym samouczku dowiesz się, jak zintegrować uwierzytelnianie tożsamości platformy SAP Cloud Platform z usługą Azure Active Directory (Azure AD). Po zintegrowaniu uwierzytelniania tożsamości platformy SAP Cloud Platform z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do uwierzytelniania tożsamości platformy SAP Cloud Platform.
* Włącz użytkownikom automatyczne logowanie do uwierzytelniania tożsamości platformy SAP Cloud Platform za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją rejestracji jednokrotnej (SSO) usługi SAP Cloud Platform Identity Authentication.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa SAP Cloud Platform Identity Authentication obsługuje logowanie jednokrotne zainicjowane przez **dostawcę usługi** oraz **dostawcę tożsamości**
* Po skonfigurowaniu uwierzytelniania tożsamości platformy SAP Cloud Platform można wymusić kontrole sesji, które chronią eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Formanty sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

Zanim zagłębisz się w szczegóły techniczne, konieczne jest zrozumienie koncepcji, które zostaną omówione. Usługi SAP Cloud Platform Identity Authentication oraz Active Directory Federation Services umożliwiają wdrożenie logowania jednokrotnego we wszystkich aplikacjach i usługach chronionych przez usługę Azure AD (jako dostawcę tożsamości) z aplikacjami i usługami SAP chronionymi przez usługę SAP Cloud Platform Identity Authentication.

Obecnie usługa SAP Cloud Platform Identity Authentication pełni funkcję dodatkowego dostawcy tożsamości w aplikacjach SAP. Usługa Azure Active Directory z kolei pełni funkcję głównego dostawcy tożsamości w tej konfiguracji. 

Na poniższym diagramie przedstawiono tę relację:

![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

W tej konfiguracji dzierżawa usługi SAP Cloud Platform Identity Authentication jest konfigurowana jako zaufana aplikacja w usłudze Azure Active Directory.

Wszystkie usługi i aplikacje SAP, które chcesz zabezpieczyć w ten sposób, są następnie konfigurowane w konsoli zarządzania usługą SAP Cloud Platform Identity Authentication.

Autoryzacja dostępu do aplikacji i usług SAP musi mieć miejsce w usłudze SAP Cloud Platform Identity Authentication, a nie w usłudze Azure Active Directory.

Dzięki skonfigurowaniu usługi SAP Cloud Platform Identity Authentication jako aplikacji w witrynie Azure Active Directory Marketplace nie musisz konfigurować pojedynczych oświadczeń lub asercji SAML.

> [!NOTE]
> Obecnie tylko logowanie jednokrotne przez Internet zostało przetestowane przez obie strony. Przepływy, które są niezbędne do nawiązania komunikacji pomiędzy aplikacją a interfejsem API lub pomiędzy interfejsami API, powinny działać, ale nie zostały jeszcze przetestowane. Zostaną przetestowane później.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Dodawanie usługi SAP Cloud Platform Identity Authentication z galerii

Aby skonfigurować integrację usługi SAP Cloud Platform Identity Authentication z usługą Azure AD, należy dodać aplikację SAP Cloud Platform Identity Authentication z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania **uwierzytelnianie tożsamości platformy SAP Cloud.**
1. Wybierz **sap cloud platform uwierzytelniania tożsamości** z panelu wyników, a następnie dodać aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla uwierzytelniania tożsamości platformy SAP Cloud

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą uwierzytelniania tożsamości platformy SAP Cloud platform przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby funkcja SSO działała, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w uwierzytelnianiu tożsamości platformy SAP Cloud Platform.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą uwierzytelniania tożsamości platformy SAP Cloud, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne uwierzytelnianie tożsamości platformy SAP Cloud Platform](#configure-sap-cloud-platform-identity-authentication-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego uwierzytelniania tożsamości platformy SAP Cloud Platform](#create-sap-cloud-platform-identity-authentication-test-user)** — aby mieć odpowiednik B.Simon w uwierzytelnianiu tożsamości platformy sap cloud, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **uwierzytelniania tożsamości platformy SAP Cloud,** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    ![Domena i adresy URL usługi SAP Cloud Platform Identity Authentication — informacje dotyczące logowania jednokrotnego](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`<IAS-tenant-id>.accounts.ondemand.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta usługi SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html), aby uzyskać te wartości. Jeśli nie rozumiesz wartości identyfikatora, zapoznaj się z tematem [Tenant SAML 2.0 configuration (Konfiguracja formatu SAML 2.0 dzierżawy)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) w dokumentacji usługi SAP Cloud Platform Identity Authentication.

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez sp: **SP**

    ![Domena i adresy URL usługi SAP Cloud Platform Identity Authentication — informacje dotyczące logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Użyj konkretnego adresu URL logowania aplikacji biznesowej. Skontaktuj się z [zespołem obsługi klienta usługi SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html), jeśli masz jakiekolwiek wątpliwości.

1. Aplikacja SAP Cloud Platform Identity Authentication oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji SAP Cloud Platform Identity Authentication oczekuje kilku więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie usługi SAP Cloud Platform Identity Authentication** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do uwierzytelniania tożsamości platformy sap cloud.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **SAP Cloud Platform Identity Authentication**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Konfigurowanie sytuowanego uwierzytelnienia tożsamości platformy SAP cloud

1. Aby umożliwić aplikacji skonfigurowanie logowania jednokrotnego, przejdź do konsoli administracyjnej usługi SAP Cloud Platform Identity Authentication. Adres URL odpowiada następującemu wzorcowi: `https://<tenant-id>.accounts.ondemand.com/admin`. Następnie zapoznaj się z tematem [Integration with Microsoft Azure AD (Integracja z usługą Microsoft Azure AD)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html) w dokumentacji usługi SAP Cloud Platform Identity Authentication.

2. W witrynie Azure Portal wybierz przycisk **Zapisz**.

3. Wykonuj dalsze czynności tylko, jeśli chcesz dodać i włączyć logowanie jednokrotne dla kolejnej aplikacji SAP. Powtórz kroki opisane w sekcji **Dodawanie usługi SAP Cloud Platform Identity Authentication z galerii**.

4. W witrynie Azure Portal na stronie integracji aplikacji **SAP Cloud Platform Identity Authentication**, wybierz pozycję **Połączone logowanie**.

    ![Konfigurowanie funkcji Połączone logowanie](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Zapisz konfigurację.

> [!NOTE]
> Nowa aplikacja wykorzystuje konfigurację pojedynczego logowania poprzedniej aplikacji SAP. Upewnij się, że korzystasz z tych samych firmowych dostawców tożsamości w konsoli administracyjnej usługi SAP Cloud Platform Identity Authentication.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Tworzenie użytkownika testowego usługi SAP Cloud Platform Identity Authentication

Nie musisz tworzyć użytkownika w usłudze SAP Cloud Platform Identity Authentication. Użytkownicy, którzy są w magazynie użytkowników usługi Azure AD, mogą korzystać z funkcji logowania jednokrotnego.

Usługa SAP Cloud Platform Identity Authentication obsługuje opcję federacji tożsamości. Ta opcja umożliwia aplikacji sprawdzenie, czy użytkownicy, którzy są uwierzytelniani przez firmowego dostawcę tożsamości, istnieją w magazynie użytkowników usługi SAP Cloud Platform Identity Authentication.

Opcja federacji tożsamości jest domyślnie wyłączona. Jeśli opcja federacji tożsamości jest włączona, tylko użytkownicy, którzy są zaimportowani w usłudze SAP Cloud Platform Identity Authentication, mogą uzyskać dostęp do aplikacji.

Abu uzyskać więcej informacji na temat włączania lub wyłączania funkcji federacji tożsamości w usłudze SAP Cloud Platform Identity Authentication, zobacz „Enable Identity Federation with SAP Cloud Platform Identity Authentication” („Włączanie funkcji federacji tożsamości w usłudze SAP Cloud Platform Identity Authentication”) w temacie [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication (Konfigurowanie funkcji federacji tożsamości z magazynem użytkowników w usłudze SAP Cloud Platform Identity Authentication)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP Cloud Platform Identity Authentication w panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi SAP Cloud Platform Identity Authentication, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj uwierzytelnianie tożsamości platformy SAP cloud w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić uwierzytelnianie tożsamości platformy SAP Cloud Platform za pomocą zaawansowanej widoczności i kontroli](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
