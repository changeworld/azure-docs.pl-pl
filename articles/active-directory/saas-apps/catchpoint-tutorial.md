---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z punktem catchpoint'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a punktem catchpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b19e286d299811a950df05f93d221bd710676ea
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743495"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Samouczek: Integracja z logiem jednokrotnym usługi Azure Active Directory z punktem catchpoint

W tym samouczku dowiesz się, jak zintegrować program Catchpoint z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu Catchpoint z usługą Azure AD można:

* Kontroluj dostęp użytkowników do punktu catchpoint z usługi Azure AD.
* Włącz automatyczne logowanie catchpoint dla użytkowników korzystających z kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja Catchpoint z włączoną rejestracją jednokrotną.A Catchpoint subscription with single sign-on (SSO) enabled.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Catchpoint obsługuje jednostkę SSO inicjowane przez sp i protokół IDP.
* Catchpoint obsługuje just-in-time (JIT) inicjowania obsługi administracyjnej użytkowników.
* Po skonfigurowaniu Catchpoint, można wymusić kontrolę sesji. Ten środek ostrożności chroni przed eksfiltracją i infiltracją poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji jest rozszerzeniem dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-catchpoint-from-the-gallery"></a>Dodawanie punktu catchpoint z galerii

Aby skonfigurować integrację catchpoint do usługi Azure AD, dodaj Catchpoint do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą służbowego, szkolnego lub osobistego konta Microsoft.
1. W lewym okienku wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji dla przedsiębiorstw,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Catchpoint** w polu wyszukiwania.
1. Wybierz **punkt catchpoint** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla punktu catchpoint

Aby użytkownik ujedniaka bez instrukcji obsługi działał, należy połączyć użytkownika usługi Azure AD z użytkownikiem w programie Catchpoint. W tym samouczku skonfigurujemy użytkownika testowego o nazwie **B.Simon**. 

Wykonaj następujące sekcje:

1. [Skonfiguruj sytuasz usługi Azure AD ,](#configure-azure-ad-sso)aby włączyć tę funkcję dla użytkowników.
    * [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user), aby przetestować logowanie jednokrotne usługi Azure AD za pomocą b.simon.
    * [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. [Skonfiguruj logowanie jednokrotne punktu catchpoint](#configure-catchpoint-sso), aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * [Utwórz użytkownika testowego catchpoint](#create-a-catchpoint-test-user), aby umożliwić łączenie konta testowego B.Simon Azure AD do podobnego konta użytkownika w catchpoint.
1. [Przetestuj opcję SSO](#test-sso), aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki w witrynie Azure portal, aby włączyć usługę Azure AD SSO:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Na stronie integracji aplikacji **Catchpoint** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą narzędzia SAML** wybierz ikonę pióra, aby edytować podstawowe ustawienia **konfiguracji SAML.**

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Skonfiguruj tryb inicjowany dla punktu catchpoint:
   - W przypadku trybu inicjowanego przez **IDP**wprowadź wartości dla następujących pól:
     - Dla **identyfikatora:**`https://portal.catchpoint.com/SAML2`
     - Dla **odpowiedzi adres URL:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - W przypadku trybu inicjowanego w dodatku **SP**wybierz pozycję **Ustaw dodatkowe adresy URL** i wprowadź następującą wartość:
     - Aby **uzyskać adres URL logowania:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Catchpoint aplikacja oczekuje potwierdzeń SAML w określonym formacie. Dodawanie mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższa tabela zawiera listę atrybutów domyślnych:

    | Nazwa | Atrybut źródłowy|
    | ------------ | --------- |
    | Givenname | user.givenneame |
    | Nazwisko | user.surname |
    | Emailaddress | user.mail |
    | Nazwa | user.userprincipalname |
    | Unikatowy identyfikator użytkownika | user.userprincipalname |

    ![Zrzut ekranu przedstawiający listę oświadczeń & atrybutów użytkownika](common/default-attributes.png)

1. Ponadto catchpoint aplikacji oczekuje innego atrybutu mają być przekazywane w odpowiedzi SAML. Zobacz poniższą tabelę. Ten atrybut jest również wstępnie wypełniony, ale można go przejrzeć i zaktualizować, aby dopasować go do wymagań.

    | Nazwa | Atrybut źródłowy|
    | ------------ | --------- |
    | namespace | user.assignedrole |

    > [!NOTE]
    > Oświadczenie `namespace` musi być mapowane z nazwą konta. Ta nazwa konta powinna być skonfigurowana z rolą w usłudze Azure AD, która ma zostać przekazana z powrotem w odpowiedzi SAML. Aby uzyskać więcej informacji na temat ról w usłudze Azure AD, zobacz [Konfigurowanie oświadczenia roli wystawionego w tokenie SAML dla aplikacji korporacyjnych.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)

1. Przejdź do strony **Konfigurowanie logowania jednokrotnego za pomocą saml.** W sekcji **Certyfikat podpisywania SAML** znajdź **certyfikat (Base64)**. Wybierz **pozycję Pobierz,** aby zapisać certyfikat na komputerze.

    ![Łącze do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie punktu catchpoint** skopiuj potrzebne adresy URL w późniejszym kroku.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji można użyć witryny Azure portal, aby utworzyć użytkownika testowego usługi Azure AD o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład wprowadź wartość `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło.** Zanotuj wyświetlaną wartość hasła.
   1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do catchpoint.

1. W portalu Azure wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **punkt catchpoint**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Łącze "Dodaj użytkownika"](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy użytkowników. Kliknij **pozycję Wybierz** u dołu ekranu.
1. Jeśli oczekujesz wartości roli w asercji SAML, poszukaj w oknie dialogowym **Wybierz rolę** i wybierz rolę użytkownika z listy. Kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-catchpoint-sso"></a>Konfigurowanie sytcha w punkcie catchpoint

1. W innym oknie przeglądarki sieci Web zaloguj się do aplikacji Catchpoint jako administrator.

1. Wybierz ikonę **Ustawienia,** a następnie **dostawcę tożsamości logowania przesuwu .**

    ![Zrzut ekranu z ustawieniami punktu catchpoint z wybraną wybraną usługą Dostawca tożsamości logowania do systemu sypkiego](./media/catchpoint-tutorial/configuration1.png)

1. Na stronie **Jednokrotne znakowanie wejdź** w następujące pola:

   ![Zrzut ekranu strony logowania jednokrotnego catchpoint](./media/catchpoint-tutorial/configuration2.png)

   Pole | Wartość
   ----- | ----- 
   **Namespace** | Prawidłowa wartość obszaru nazw.
   **Wystawca dostawcy tożsamości** | Wartość `Azure AD Identifier` z witryny Azure portal.
   **Adres URL logowania jednokrotnego** | Wartość `Login URL` z witryny Azure portal.
   **Certyfikat** | Zawartość pobranego pliku `Certificate (Base64)` z witryny Azure portal. Użyj Notatnika, aby wyświetlić i skopiować.

   Możesz również przekazać **kod XML metadanych federacji,** wybierając opcję **Przekaż metadane.**

1. Wybierz **pozycję Zapisz**.

### <a name="create-a-catchpoint-test-user"></a>Tworzenie użytkownika testowego punktu catchpoint

Catchpoint obsługuje just-in-time inicjowania obsługi administracyjnej użytkowników, który jest domyślnie włączony. W tej sekcji nie ma żadnych elementów akcji. Jeśli B.Simon nie istnieje jeszcze jako użytkownik w Catchpoint, jest tworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu portalu Moje aplikacje.

Po wybraniu kafelka Catchpoint w portalu Moje aplikacje, powinieneś być automatycznie zalogowany do aplikacji Catchpoint z skonfigurowanym logiem logującym się. Aby uzyskać więcej informacji o portalu Moje aplikacje, zobacz [Logowanie się i uruchamianie aplikacji z portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

> [!NOTE]
> Po zalogowaniu się do aplikacji Catchpoint za pośrednictwem strony logowania, po podaniu **poświadczeń Catchpoint**wprowadź prawidłową wartość **obszaru nazw** w polu **Poświadczenia firmy (logowanie logowania)** i wybierz pozycję **Zaloguj**.
> 
> ![Konfiguracja punktu catchpoint](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj punkt catchpoint za pomocą usługi Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
