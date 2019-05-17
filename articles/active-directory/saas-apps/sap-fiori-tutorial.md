---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą rozwiązaniu SAP Fiori | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i rozwiązaniu SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 9e7993ee1cb439ebeaa9f64bee55429aa54f9cee
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65776171"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą rozwiązaniu SAP Fiori

W tym samouczku dowiesz się, jak zintegrować rozwiązaniu SAP Fiori, za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie rozwiązaniu SAP Fiori z usługą Azure AD zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do rozwiązaniu SAP Fiori.
* Użytkownicy mogą automatycznie zalogować się na rozwiązaniu SAP Fiori przy użyciu konta usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą rozwiązaniu SAP Fiori, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcję usługi Azure AD, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed przystąpieniem do wykonywania.
* Subskrypcję rozwiązaniu SAP Fiori, za pomocą logowania jednokrotnego włączone.
* Rozwiązaniu SAP Fiori 7.20 lub nowszy jest wymagany.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka konfigurowania i testowania usługi Azure AD logowanie jednokrotne w środowisku testowym, a integracja rozwiązaniu SAP Fiori z usługą Azure AD.

Rozwiązaniu SAP Fiori obsługuje następujące funkcje:

* **Zainicjowane przez Dostawcę logowania jednokrotnego**

## <a name="add-sap-fiori-in-the-azure-portal"></a>Dodaj rozwiązaniu SAP Fiori w witrynie Azure portal

Aby zintegrować rozwiązaniu SAP Fiori z usługą Azure AD, należy dodać rozwiązaniu SAP Fiori, z listą zarządzanych aplikacji SaaS.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Wybierz pozycję **Aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać aplikację, wybierz pozycję **nową aplikację**.

    ![Nowa opcja aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **rozwiązaniu SAP Fiori**. W wynikach wyszukiwania wybierz **rozwiązaniu SAP Fiori**, a następnie wybierz pozycję **Dodaj**.

    ![Rozwiązaniu SAP Fiori, na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą rozwiązaniu SAP Fiori, w oparciu o nazwie użytkownika testowego **Britta Simon**. Dla logowania jednokrotnego do pracy należy ustanowić połączone relację między użytkownikiem usługi Azure AD i powiązanych użytkowników w rozwiązaniu SAP Fiori.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego dzięki rozwiązaniu SAP Fiori, należy wykonać poniższe bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystać z tej funkcji. |
| **[Konfigurowanie rozwiązaniu SAP Fiori logowania jednokrotnego](#configure-sap-fiori-single-sign-on)** | Konfiguruje pojedynczy ustawień logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testy usługi Azure AD logowanie jednokrotne dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia Britta Simon korzystać z usługi Azure AD logowania jednokrotnego. |
| **[Tworzenie użytkownika testowego rozwiązaniu SAP Fiori](#create-an-sap-fiori-test-user)** | Tworzy odpowiednikiem Britta Simon w rozwiązaniu SAP Fiori, połączonego z usługi Azure AD reprezentacja użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz usługi Azure AD logowania jednokrotnego dzięki rozwiązaniu SAP Fiori w witrynie Azure portal.

1. Otwórz nowe okno przeglądarki sieci web i zaloguj się do witryny firmy SAP Fiori jako administrator.

1. Upewnij się, że **http** i **https** usługi są aktywne i odpowiednie porty są przypisane do kodu transakcji **SMICM**.

1. Zaloguj się do SAP Business klienta dla systemu SAP **T01**, gdzie rejestracji jednokrotnej jest wymagana. A następnie Aktywuj zarządzanie sesjami zabezpieczeń protokołu HTTP.

    1. Przejdź do kodu transakcji **SICF_SESSIONS**. Wyświetlane są wszystkie parametry odpowiedniego profilu z bieżących wartości. Wyglądają jak w poniższym przykładzie:

        ```
        login/create_sso2_ticket = 2
        login/accept_sso2_ticket = 1
        login/ticketcache_entries_max = 1000
        login/ticketcache_off = 0  login/ticket_only_by_https = 0 
        icf/set_HTTPonly_flag_on_cookies = 3
        icf/user_recheck = 0  http/security_session_timeout = 1800
        http/security_context_cache_size = 2500
        rdisp/plugin_auto_logout = 1800
        rdisp/autothtime = 60
        ```

        >[!NOTE]
        > Dostosuj parametry zgodnie z wymaganiami organizacji. Poprzedni parametry są podane tylko jako przykład.

    1. W razie potrzeby dostosować parametry w profilu wystąpienie (domyślne) w systemie SAP i ponownym uruchomieniu systemu SAP.

    1. Kliknij dwukrotnie odpowiednie kliencie, aby umożliwić sesję zabezpieczeń protokołu HTTP.

        ![Strona bieżących wartości z odpowiedniego profilu parametrów w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Aktywuj następujących usług SICF:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Przejdź do kodu transakcji **SAML2** w kliencie SAP system Business [**T01/122**]. Konfiguracja interfejsu użytkownika zostanie otwarty w nowym oknie przeglądarki. W tym przykładzie używamy klientów biznesowych dla systemu SAP 122.

    ![Klient programu SAP Fiori Business strony logowania](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Wprowadź nazwę użytkownika i hasło, a następnie wybierz pozycję **Zaloguj się na**.

    ![Na stronie SAML 2.0 konfiguracji z ABAP System T01/122 w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. W **Nazwa dostawcy** Zastąp **T01122** z **http:\//T01122**, a następnie wybierz pozycję **Zapisz**.

    > [!NOTE]
    > Domyślnie, nazwa dostawcy jest w formacie \<sid >\<klienta >. Usługa Azure AD oczekuje nazwy w formacie \<protokołu > ://\<name >. Firma Microsoft zaleca, aby zachować taką nazwę dostawcy, jak https\://\<sid >\<klienta > tak wielu aparatów SAP Fiori ABAP można skonfigurować w usłudze Azure AD.

    ![Nazwa dostawcy zaktualizowane na stronie SAML 2.0 konfiguracji programu ABAP System T01/122 w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Wybierz **kartę lokalnego dostawcy** > **metadanych**.

1. W **SAML 2.0 metadanych** okna dialogowego pole, Pobierz plik XML metadanych wygenerowanych i zapisz go na komputerze.

    ![Link pobierania metadanych w oknie dialogowym SAP SAML 2.0 metadanych](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. W [witryny Azure portal](https://portal.azure.com/)w **rozwiązaniu SAP Fiori** okienko integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![-Opcja logowania jednokrotnego](common/select-sso.png)

1. W **wybierz jedną metodę logowania jednokrotnego** okienku wybierz **SAML** lub **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** okienku wybierz **Edytuj** (ikonę ołówka) aby otworzyć **podstawową konfigurację protokołu SAML** okienka.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    1. Wybierz **przekazywania pliku metadanych**.

        ![Opcja przekazywania pliku metadanych](common/upload-metadata.png)

   1. Aby wybrać plik metadanych, wybierz ikonę folderu, a następnie wybierz **przekazywanie**.

       ![Wybierz plik metadanych, a następnie wybierz przycisk Przekaż](common/browse-upload-metadata.png)

1. Po pomyślnym przekazaniu pliku metadanych, **identyfikator** i **adres URL odpowiedzi** wartości są wypełniane automatycznie w **podstawową konfigurację protokołu SAML** okienka. W **adres URL logowania** wprowadź adres URL z następującym wzorcem: https:\//\<wystąpienie firmy SAP Fiori\>.

    ![SAP Fiori domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/sp-identifier-reply.png)

    > [!NOTE]
    > Kilka klientów zgłaszaj błędy związane z niepoprawnie skonfigurowany **adres URL odpowiedzi** wartości. Jeśli zostanie wyświetlony ten błąd, służy poniższy skrypt programu PowerShell można ustawić poprawny adres URL odpowiedzi dla swojego wystąpienia:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Możesz ustawić `ServicePrincipal` identyfikator obiektu użytkownika przed uruchomieniem skryptu, lub można przekazać je tutaj.

1. Aplikacja SAP Fiori oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Zarządzanie wartości tych atrybutów w **Ustaw się logowanie jednokrotne z SAML** okienku wybierz **Edytuj**.

    ![Okienko atrybuty użytkownika](common/edit-attribute.png)

1. W **atrybutów użytkowników i oświadczeń** okienko, skonfiguruj atrybuty tokenu języka SAML, jak pokazano na poprzedniej ilustracji. Następnie wykonaj następujące kroki:

    1. Wybierz **Edytuj** otworzyć **Zarządzanie oświadczenia użytkownika** okienka.

    1. W **przekształcania** listy wybierz **ExtractMailPrefix()**.

    1. W **parametr 1** listy wybierz **user.userprinicipalname**.

    1. Wybierz pozycję **Zapisz**.

       ![W okienku Zarządzanie użytkownika oświadczeń](./media/sapfiori-tutorial/nameidattribute.png)

       ![W sekcji transformacji w okienku Zarządzanie oświadczenia użytkownika](./media/sapfiori-tutorial/nameidattribute1.png)


1. W **Ustaw się logowanie jednokrotne z SAML** okienko w **certyfikat podpisywania SAML** zaznacz **Pobierz** obok **XML metadanych Federacji**. Wybierz opcję pobierania, w zależności od wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikatu](common/metadataxml.png)

1. W **Konfigurowanie rozwiązaniu SAP Fiori** sekcji, skopiuj następujące adresy URL, zgodnie z wymaganiami:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>Konfigurowanie rozwiązaniu SAP Fiori logowania jednokrotnego

1. Zaloguj się do systemu SAP i przejdź do kodu transakcji **SAML2**. Otwiera nowe okno przeglądarki ze stroną konfiguracji protokołu SAML.

1. Aby skonfigurować punkty końcowe dla zaufanego dostawcy tożsamości (Azure AD), wybierz **zaufanych dostawców** kartę.

    ![Na karcie zaufanych dostawców w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Wybierz **Dodaj**, a następnie wybierz pozycję **Przekaż plik metadanych** z menu kontekstowego.

    ![Opcje dodawania i przekaż plik metadanych w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Przekaż plik metadanych, który został pobrany w witrynie Azure portal. Wybierz opcję **Dalej**.

    ![Wybierz plik metadanych do przekazania w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Na następnej stronie w **Alias** wprowadź nazwę aliasu. Na przykład **aadsts**. Wybierz opcję **Dalej**.

    ![Pole aliasu w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Upewnij się, że wartość w **algorytm tworzenia skrótu** pole jest **algorytmu SHA-256**. Wybierz opcję **Dalej**.

    ![Sprawdź wartość algorytm tworzenia skrótu w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. W obszarze **Endpoints rejestracji jednokrotnej**, wybierz opcję **żądania HTTP POST**, a następnie wybierz pozycję **dalej**.

    ![Opcje punktów końcowych logowania jednokrotnego w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. W obszarze **pojedynczej punktami końcowymi wylogowania**, wybierz opcję **przekierowania HTTP**, a następnie wybierz pozycję **dalej**.

    ![Opcje punktów końcowych wylogowania jednokrotnego w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. W obszarze **punktów końcowych artefaktu**, wybierz opcję **dalej** aby kontynuować.

    ![Opcje punktów końcowych artefaktów w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. W obszarze **wymagania dotyczące uwierzytelniania**, wybierz opcję **Zakończ**.

    ![Opcje wymagania dotyczące uwierzytelniania i opcję Zakończ w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Wybierz **zaufanego dostawcy** > **Federację tożsamości** (w dolnej części strony). Wybierz pozycję **Edit** (Edytuj).

    ![Na kartach zaufanego dostawcy i federacji tożsamości w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Wybierz pozycję **Dodaj**.

    ![Opcja Dodaj na karcie federacji tożsamości](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. W **obsługiwane formaty NameID** okno dialogowe, wybierz opcję **nieokreślony**. Kliknij przycisk **OK**.

    ![Okno dialogowe obsługiwane formaty NameID i opcje w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Wartości **źródło identyfikator użytkownika** i **tryb mapowania Identyfikatora użytkownika** określić link między użytkownikiem SAP i oświadczenia usługi Azure AD.  

    **Scenariusz 1**: Użytkownik SAP do mapowania użytkowników usługi Azure AD

    1. W systemie SAP w obszarze **szczegóły Format identyfikatora NameID "Nieokreślone"**, zanotuj szczegóły:

        ![Okno dialogowe "Nieokreślone" Szczegóły Format identyfikatora NameID, w systemie SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. W witrynie Azure portal w obszarze **atrybutów użytkowników i oświadczeń**, należy pamiętać, wymagane oświadczenia z usługi Azure AD.

        ![Okno dialogowe atrybutów użytkowników i oświadczeń w witrynie Azure portal](./media/sapfiori-tutorial/claimsaad1.png)

    **Scenariusz 2**: Wybierz identyfikator użytkownika SAP na podstawie adresu e-mail skonfigurowany w SU01. W tym przypadku identyfikator poczty e-mail należy skonfigurować w SU01 dla każdego użytkownika, który wymaga logowania jednokrotnego.

    1.  W systemie SAP w obszarze **szczegóły Format identyfikatora NameID "Nieokreślone"**, zanotuj szczegóły:

        ![Okno dialogowe "Nieokreślone" Szczegóły Format identyfikatora NameID, w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. W witrynie Azure portal w obszarze **atrybutów użytkowników i oświadczeń**, należy pamiętać, wymagane oświadczenia z usługi Azure AD.

       ![Okno dialogowe atrybutów użytkowników i oświadczeń w witrynie Azure portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Wybierz **Zapisz**, a następnie wybierz pozycję **Włącz** umożliwiające dostawcy tożsamości.

    ![Zapisz i Włącz opcje w systemie SAP](./media/sapfiori-tutorial/configuration1.png)

1. Wybierz **OK** po wyświetleniu monitu.

    ![Opcja OK w oknie dialogowym plik konfiguracji SAML 2.0 w systemie SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure Portal wybierz kolejno pozycje **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Użytkownicy i wszystkie opcje użytkowników](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **brittasimon\@\<Twojej domeny firmy >.\< Rozszerzenia >**. Na przykład **brittasimon\@contoso.com**.

    1. Wybierz **hasło Show** pole wyboru. Zanotuj wartość, która jest wyświetlana w **hasło** pole.

    1. Wybierz pozycję **Utwórz**.

    ![W okienku użytkownika](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz udzielić dostępu Britta Simon rozwiązaniu SAP Fiori, dzięki czemu korzystaniem Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **rozwiązaniu SAP Fiori**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **rozwiązaniu SAP Fiori**.

    ![Rozwiązaniu SAP Fiori, na liście aplikacji](common/all-applications.png)

1. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja użytkowników i grup](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W **użytkowników i grup** okienku wybierz **Britta Simon** na liście Użytkownicy. Wybierz pozycję **Wybierz**.

1. Jeśli są oczekiwane wartości roli dla asercji SAML w **wybierz rolę** okienku zaznacz odpowiednie rolę dla użytkownika z listy. Wybierz pozycję **Wybierz**.

1. W **Dodaj przydziału** okienku wybierz **przypisać**.

### <a name="create-an-sap-fiori-test-user"></a>Tworzenie użytkownika testowego rozwiązaniu SAP Fiori

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w rozwiązaniu SAP Fiori. Praca z wewnętrznych zespół ekspertów lub partnerem SAP organizacji, aby dodać użytkownika na platformie SAP Fiori SAP.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

1. Po aktywowaniu dostawcy tożsamości usługi Azure AD w rozwiązaniu SAP Fiori, spróbuj uzyskać dostęp do jednego z następujących adresów URL do przetestowania logowania jednokrotnego (nie powinny być monit o nazwę użytkownika i hasło):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Zastąp *sapurl* z rzeczywistą nazwę hosta SAP.

1. Testuj adres URL powinien zostać wyświetlony następujący strony aplikacji testowej w systemie SAP. Jeśli zostanie otwarta strona usługi Azure AD logowanie jednokrotne jest pomyślnie skonfigurowana.

    ![Standardowa testowanie strony aplikacji w systemie SAP](./media/sapfiori-tutorial/testingsso.png)

1. Po wyświetleniu monitu o nazwę użytkownika i hasło, należy włączyć śledzenie pomóc w zdiagnozowaniu problemu. Użyj następującego adresu URL śledzenia: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool? klienta sap = 122 & języka sap = EN #.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej, przejrzyj następujące artykuły:

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
