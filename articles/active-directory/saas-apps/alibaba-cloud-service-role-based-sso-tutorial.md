---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d4b154b8ce55c381f1398c696bc439067dccfab
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785761"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Samouczek: Integracja usługi Azure Active Directory z usługą w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach)

W tym samouczku dowiesz się, jak zintegrować firmę Alibaba usługi w chmurze (Role-based SSO) z usługą Azure Active Directory (Azure AD).
Integrowanie firmę Alibaba usługi w chmurze (Role-based SSO) z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach).
* Umożliwia użytkownikom można automatycznie zalogowany do usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach), potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Usługa w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) obsługuje **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Dodawanie firmę Alibaba usługi w chmurze (Role-based SSO) z galerii

Aby skonfigurować integrację firmę Alibaba usługi w chmurze (Role-based SSO) w usłudze Azure AD, należy dodać firmę Alibaba usługi w chmurze (Role-based SSO) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać firmę Alibaba usługi w chmurze (Role-based SSO) z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **firmę Alibaba usługi w chmurze (Role-based SSO)**, wybierz opcję **firmę Alibaba usługi w chmurze (Role-based SSO)** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![Usługa w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) na liście wyników](common/search-new-app.png)

5. Na **firmę Alibaba usługi w chmurze (Role-based SSO)** kliknij **właściwości** w okienku nawigacji po lewej stronie, a następnie skopiuj **obiekt o identyfikatorze** i zapisz go na komputerze późniejszego użytku.

    ![Właściwości konfiguracji](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne z usługą w chmurze firmę Alibaba (oparta na rolach SSO) w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika firmę Alibaba w usłudze w chmurze (Role-based SSO) musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie opartej na rolach logowanie jednokrotne w usłudze w chmurze firmę Alibaba](#configure-role-based-single-sign-on-in-alibaba-cloud-service)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) logowanie jednokrotne](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego firmę Alibaba usługi w chmurze (Role-based SSO)](#create-alibaba-cloud-service-role-based-sso-test-user)**  — aby odpowiednikiem Britta Simon firmę Alibaba w usłudze w chmurze (Role-based SSO) połączoną usługę Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z usługą w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach), wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **firmę Alibaba usługi w chmurze (Role-based SSO)** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    >[!NOTE]
    >Otrzymasz metadanych dostawcy usług z tym [adresu URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![image](common/upload-metadata.png)

    b. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.

    ![image](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartości Uzyskaj automatycznie wypełnione w sekcji firmę Alibaba usługi w chmurze (Role-based SSO) w polu tekstowym:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Jeśli **identyfikator** i **adres URL odpowiedzi** wartości nie uzyskać wypełniona automatycznie, a następnie wprowadź wartości ręcznie zgodnie z wymaganiami.

5. Aplikacja usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach) oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij przycisk **Edytuj** ikonę, aby otworzyć **atrybutów użytkownika** okna dialogowego.

    ![image](common/edit-attribute.png)

6. Ponadto powyżej firmę Alibaba usługi w chmurze (Role-based SSO) aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Name (Nazwa) | Przestrzeń nazw | Atrybut źródłowy|
    | ---------------| ------------| --------------- |
    | Rola | https:\//www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https:\//www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

    > [!NOTE]
    > Kliknij [tutaj](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management), aby dowiedzieć się, jak skonfigurować **rolę** w usłudze Azure AD

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

8. Na **Konfigurowanie usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach)** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Konfigurowanie opartej na rolach logowanie jednokrotne w usłudze w chmurze firmę Alibaba

1. Zaloguj się w chmurze firmę Alibaba [konsoli pamięci RAM](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) przy użyciu Account1.

2. W okienku nawigacji po lewej stronie wybierz **logowania jednokrotnego**.

3. Na **rejestracji Jednokrotnej opartej na rolach** kliknij pozycję **utworzyć dostawcy tożsamości**.

4. Na wyświetlonej stronie wprowadź `AAD` w polu Nazwa dostawcy tożsamości, podaj opis w **Uwaga** kliknij **przekazywanie** Przekaż plik metadanych Federacji, które są pobierane przed, a następnie kliknij przycisk  **OK**.

5. Po pomyślnym utworzeniu przez dostawcę tożsamości, kliknij przycisk **Utwórz rolę RAM**.

6. W **nazwy roli RAM** pole, wprowadź `AADrole`, wybierz opcję `AAD` z **wybierz dostawcę tożsamości** listy rozwijanej listy i kliknij przycisk OK.

    >[!NOTE]
    >Można udzielić uprawnienia do roli, zgodnie z potrzebami. Po utworzeniu dostawcy tożsamości i odpowiednich ról, zaleca się zapisywania ARNs tożsamości i roli do późniejszego użytku. Możesz uzyskać ARNs na stronie informacje o tożsamości, a strona informacji o roli.

7. Skojarz roli firmę Alibaba chmury w pamięci RAM (AADrole) za pomocą użytkownika usługi Azure AD (u2): Aby skojarzyć roli pamięci RAM, za pomocą użytkownika usługi Azure AD, musi utworzyć rolę w usłudze Azure AD, wykonaj następujące czynności:

    a. Zaloguj się na [funkcji Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9).

    b. Kliknij przycisk **modyfikowania uprawnień** uzyskać wymaganych uprawnień do tworzenia roli.

    ![Konfiguracja wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Wybierz następujące uprawnienia z listy, a następnie kliknij przycisk **zmodyfikować uprawnienia**, jak pokazano na poniższej ilustracji.

    ![Konfiguracja wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Po uprawnienia są przyznawane, zaloguj się do programu Graph Explorer ponownie.

    d. Na stronie Graph Explorer wybierz **UZYSKAĆ** z pierwszej listy rozwijanej i **beta** z drugiej listy rozwijanej. Następnie wprowadź `https://graph.microsoft.com/beta/servicePrincipals` w polu obok listy rozwijanej, a następnie kliknij przycisk **Uruchom kwerendę**.

    ![Konfiguracja wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Jeśli używasz wielu katalogów, możesz wprowadzić `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` w polu zapytania.

    e. W **odpowiedzi w wersji zapoznawczej** sekcji, wyodrębnić właściwość appRoles z "jednostka usługi" do późniejszego użytku.

    ![Konfiguracja wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Możesz znaleźć właściwość appRoles, wprowadzając `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` w polu zapytania. Należy pamiętać, że `objectID` jest identyfikator obiektu, zostały skopiowane z usługi Azure AD **właściwości** strony.

    f. Wróć do Eksploratora programu Graph, zmień metodę z **UZYSKAĆ** do **PATCH**, wklej następującą zawartość do **treść żądania** sekcji, a następnie kliknij przycisk **Uruchom kwerendę** :
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value` Jest ARNs tożsamości i roli utworzonej w konsoli pamięci RAM. W tym miejscu możesz dodać wiele ról, zgodnie z potrzebami. Usługa Azure AD będzie wysyłać wartości z tych ról jako wartość oświadczenia w odpowiedzi SAML. Jednak możesz tylko dodawać nowe role po `msiam_access` część na potrzeby operacji patch. Do wygładzania proces tworzenia, zaleca się użyć generator identyfikator, takich jak GUID Generator do generowania identyfikatorów w czasie rzeczywistym.

    g. Po jednostki usługi są zainstalowane odpowiednie poprawki z rolą wymagane, Przypisz rolę za pomocą użytkownika usługi Azure AD (u2), wykonując kroki **przypisać użytkownika testowego usługi Azure AD** części samouczka.

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Konfigurowanie chmury firmę Alibaba (oparta na rolach SSO) usługi logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na **firmę Alibaba usługi w chmurze (Role-based SSO)** stronie, musisz wysłać pobrany **XML metadanych Federacji** i skopiować adresy URL z witryny Azure portal do [ Zespołu pomocy technicznej usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach)](https://www.aliyun.com/service/). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach).

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **firmę Alibaba usługi w chmurze (Role-based SSO)**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **firmę Alibaba usługi w chmurze (Role-based SSO)**.

    ![Link firmę Alibaba usługi w chmurze (Role-based SSO) na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. Na **użytkowników i grup** kartę, zaznacz u2 z listy użytkowników, a następnie kliknij przycisk **wybierz**. Następnie kliknij przycisk **przypisać**.

    ![Konfiguracja testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. Wyświetl przypisaną rolę i przetestować firmę Alibaba usługi w chmurze (Role-based SSO).

    ![Konfiguracja testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Po przypisaniu użytkowników (u2) utworzona rola jest automatycznie dołączany do użytkownika. Jeśli utworzono wiele ról, należy dołączyć odpowiednią rolę dla użytkownika, zgodnie z potrzebami. Jeśli chcesz zaimplementować opartej na rolach logowania jednokrotnego z usługi Azure AD do wielu kont w chmurze firmę Alibaba, powtórz te czynności.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Tworzenie użytkownika testowego firmę Alibaba usługi w chmurze (Role-based logowanie Jednokrotne)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon firmę Alibaba w usłudze w chmurze (Role-based SSO). Praca z [zespołu pomocy technicznej usługi w chmurze firmę Alibaba (logowanie Jednokrotne oparte na rolach)](https://www.aliyun.com/service/) Aby dodać użytkowników na platformie firmę Alibaba usługi w chmurze (Role-based SSO). Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

Po ukończeniu poprzedniego konfiguracje testu firmę Alibaba usługi w chmurze (Role-based SSO), wykonując następujące czynności:

1. W witrynie Azure portal przejdź do **firmę Alibaba usługi w chmurze (Role-based SSO)** wybierz opcję **logowanie jednokrotne**i kliknij przycisk **testu**.

    ![Konfiguracja testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Kliknij pozycję **Zaloguj się jako bieżący użytkownik**.

    ![Konfiguracja testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Na stronie wyboru konta wybierz u2.

    ![Konfiguracja testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Zostanie wyświetlona następująca strona, wskazujący, że usługa rejestracji Jednokrotnej w opartej na rolach jest pomyślne.

    ![Konfiguracja testu](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

