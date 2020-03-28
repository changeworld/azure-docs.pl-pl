---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z SAP Fiori | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a sap fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 917ba9274276fec5d01a40bdf7219e8d4fee1395
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897759"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z SAP Fiori

W tym samouczku dowiesz się, jak zintegrować SAP Fiori z usługą Azure Active Directory (Azure AD). Po zintegrowaniu sap Fiori z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do SAP Fiori.
* Włącz użytkownikom automatyczne logowanie do usługi SAP Fiori za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją rejestracji jednokrotnej SAP Fiori (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* SAP Fiori obsługuje sso inicjowane przez **SP**

> [!NOTE]
> W przypadku uwierzytelniania iFrame zainicjowanego przez SAP Fiori zalecamy użycie parametru **IsPassive** w uwierzytelniania SAML AuthnRequest dla uwierzytelniania dyskretnego. Więcej informacji na temat parametru **IsPassive** można znaleźć w informacjach o [logowaniach jednokrotnych usługi Azure AD SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol)

## <a name="adding-sap-fiori-from-the-gallery"></a>Dodawanie SAP Fiori z galerii

Aby skonfigurować integrację sap Fiori z usługą Azure AD, należy dodać SAP Fiori z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP Fiori** w polu wyszukiwania.
1. Wybierz **SAP Fiori** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla systemu SAP Fiori

Konfigurowanie i testowanie sytua obsługi usługi Azure AD za pomocą usługi SAP Fiori przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik sytuować uszy działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w sap fiori.

Aby skonfigurować i przetestować jednostkę SSO usługi Azure AD za pomocą sap fiori, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj sap Fiori SSO](#configure-sap-fiori-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego SAP Fiori](#create-sap-fiori-test-user)** — aby mieć odpowiednik B.Simon w SAP Fiori, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy SAP Fiori jako administrator.

1. Upewnij się, że usługi **http** i **https** są aktywne i że odpowiednie porty są przypisane do kodu transakcji **SMICM**.

1. Zaloguj się do klienta biznesowego SAP dla systemu SAP **T01,** gdzie wymagane jest logowanie jednokrotne. Następnie aktywuj zarządzanie sesjami zabezpieczeń HTTP.

    1. Przejdź do **SICF_SESSIONS**kodu transakcji . Wyświetlane są wszystkie odpowiednie parametry profilu z bieżącymi wartościami. Wyglądają one jak w poniższym przykładzie:

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
        > Dostosuj parametry na podstawie wymagań organizacji. Powyższe parametry są podane tylko jako przykład.

    1. W razie potrzeby dostosuj parametry w profilu instancji (domyślny) systemu SAP i uruchom ponownie system SAP.

    1. Kliknij dwukrotnie odpowiedniego klienta, aby włączyć sesję zabezpieczeń HTTP.

        ![Bieżące wartości odpowiednich parametrów profilu w SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Aktywuj następujące usługi SICF:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Przejdź do kodu transakcji **SAML2** w kliencie biznesowym dla systemu SAP [**T01/122**]. Interfejs użytkownika konfiguracji zostanie otwarty w nowym oknie przeglądarki. W tym przykładzie używamy klienta biznesowego dla systemu SAP 122.

    ![Strona logowania klienta biznesowego SAP Fiori](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Wprowadź swoją nazwę użytkownika i hasło, a następnie wybierz pozycję **Zaloguj się**.

    ![Konfiguracja SAML 2.0 abap systemu T01/122 strony w SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. W polu **Nazwa dostawcy** zastąp **T01122** **na http:\//T01122**, a następnie wybierz pozycję **Zapisz**.

    > [!NOTE]
    > Domyślnie nazwa dostawcy jest w \<formacie sid \<>> klienta. Usługa Azure AD oczekuje nazwy \<w protokole\<formatu>:// nazwa>. Zaleca się zachowanie nazwy dostawcy jako\://\<https sid \<>klienta>, dzięki czemu można skonfigurować wiele aparatów SAP Fiori ABAP w usłudze Azure AD.

    ![Zaktualizowana nazwa dostawcy na stronie SAML 2.0 Konfiguracja systemu ABAP T01/122 w sap](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Wybierz **kartę** > Dostawca lokalny**Metadane**.

1. W oknie dialogowym **Metadane SAML 2.0** pobierz wygenerowany plik XML metadanych i zapisz go na komputerze.

    ![Łącze Pobierz metadane w oknie dialogowym Metadane SAP SAML 2.0](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **SAP Fiori** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    d. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatora** i **adresu URL odpowiedzi** są automatycznie wypełniane w podstawowym okienku konfiguracji **SAML.** W polu **Podpisz adres URL** wprowadź adres URL `https:\//\<your company instance of SAP Fiori\>`zawierający następujący wzorzec: .

    > [!NOTE]
    > Kilku klientów zgłasza błędy związane z niepoprawnie skonfigurowanymi wartościami **adresu URL odpowiedzi.** Jeśli widzisz ten błąd, możesz użyć następującego skryptu programu PowerShell, aby ustawić poprawny adres URL odpowiedzi dla wystąpienia:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Identyfikator `ServicePrincipal` obiektu można ustawić samodzielnie przed uruchomieniem skryptu lub przekazać go tutaj.

1. Aplikacja SAP Fiori oczekuje, że potwierdzenia SAML będą w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Aby zarządzać tymi wartościami atrybutów, w okienku **Konfigurowanie logowania jednokrotnego z saml** wybierz pozycję **Edytuj**.

    ![Okienko Atrybuty użytkownika](common/edit-attribute.png)

1. W okienku **Atrybuty użytkownika & oświadczenia** skonfiguruj atrybuty tokenu SAML, jak pokazano na poprzednim obrazie. Następnie wykonaj następujące kroki:

    1. Wybierz **pozycję Edytuj,** aby otworzyć okienko **Zarządzanie roszczeniami użytkowników.**

    1. Na liście **Transformacja** wybierz pozycję **ExtractMailPrefix()**.

    1. Na liście **Parametr 1** wybierz **user.userprincipalname**.

    1. Wybierz **pozycję Zapisz**.

       ![Okienko Zarządzanie roszczeniami użytkowników](./media/sapfiori-tutorial/nameidattribute.png)

       ![Sekcja Transformacja w okienku Zarządzanie roszczeniami użytkowników](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie SAP Fiori** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do SAP Fiori.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz SAP **Fiori**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-sap-fiori-sso"></a>Konfigurowanie sytua sap fiori

1. Zaloguj się do systemu SAP i przejdź do kodu transakcji **SAML2**. Zostanie otwarte nowe okno przeglądarki ze stroną konfiguracji SAML.

1. Aby skonfigurować punkty końcowe dla zaufanego dostawcy tożsamości (Azure AD), wybierz kartę **Zaufani dostawcy.**

    ![Karta Zaufani dostawcy w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Wybierz **polecenie Dodaj**, a następnie wybierz polecenie Przekaż plik **metadanych** z menu kontekstowego.

    ![Opcje dodaj i przekaż plik metadanych w sap](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Przekaż plik metadanych pobrany w witrynie Azure portal. Wybierz **pozycję Dalej**.

    ![Wybierz plik metadanych do przesłania w sap](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Na następnej stronie w polu **Alias** wprowadź nazwę aliasu. Na przykład **aadsts**. Wybierz **pozycję Dalej**.

    ![Pole Alias w sap](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Upewnij się, że wartością w polu **Algorytm skrótu** jest **SHA-256**. Wybierz **pozycję Dalej**.

    ![Sprawdź wartość algorytmu szyfrowania w sap](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. W obszarze **Punkty końcowe logowania jednokrotnego**wybierz pozycję **HTTP POST**, a następnie wybierz pozycję **Dalej**.

    ![Opcje punktów końcowych logowania jednokrotnego w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. W obszarze **Pojedyncze punkty końcowe wylogowania**wybierz pozycję **Przekierowanie HTTP**, a następnie wybierz pozycję **Dalej**.

    ![Opcje pojedynczych punktów końcowych wylogowania w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. W obszarze **Punkty końcowe artefaktów**wybierz pozycję **Dalej,** aby kontynuować.

    ![Opcje punktów końcowych artefaktów w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. W **obszarze Wymagania uwierzytelniania**wybierz pozycję **Zakończ**.

    ![Opcje wymagań uwierzytelniania i opcja Zakończ w sap](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Wybierz pozycję**Federacja tożsamości** **zaufanego dostawcy** > (u dołu strony). Wybierz pozycję **Edit** (Edytuj).

    ![Karty Zaufanego dostawcy i federacji tożsamości w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Wybierz pozycję **Dodaj**.

    ![Opcja Dodaj na karcie Federacja tożsamości](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. W oknie dialogowym **Obsługiwane formaty nazw wybierz** pozycję **Nieokreślony**. Kliknij przycisk **OK**.

    ![Okno dialogowe Obsługiwane formaty nameid i opcje w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Wartości **dla źródła identyfikatora użytkownika** i trybu **mapowania identyfikatora użytkownika** określają łącze między użytkownikiem SAP a oświadczeniem usługi Azure AD.  

    **Scenariusz 1:** Mapowanie użytkowników usługi Azure AD na użytkownika usługi SAP

    1. W sap, w obszarze **Szczegóły NameID Format "Nieokreślony"**, należy zwrócić uwagę na szczegóły:

        ![Okno dialogowe Szczegóły formatu NameID "Nieokreślony" w systemie SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. W witrynie Azure portal w obszarze **Atrybuty użytkownika & oświadczenia**należy zwrócić uwagę na wymagane oświadczenia z usługi Azure AD.

        ![Okno dialogowe Atrybuty & oświadczenia użytkownika w witrynie Azure portal](./media/sapfiori-tutorial/claimsaad1.png)

    **Scenariusz 2:** Wybierz identyfikator użytkownika SAP na podstawie skonfigurowany adres e-mail w SU01. W takim przypadku identyfikator wiadomości e-mail powinien być skonfigurowany w su01 dla każdego użytkownika, który wymaga sytego systemu.

    1.  W sap, w obszarze **Szczegóły NameID Format "Nieokreślony"**, należy zwrócić uwagę na szczegóły:

        ![Okno dialogowe Szczegóły formatu NameID "Nieokreślony" w systemie SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. W witrynie Azure portal w obszarze **Atrybuty użytkownika & oświadczenia**należy zwrócić uwagę na wymagane oświadczenia z usługi Azure AD.

       ![Okno dialogowe Atrybuty & oświadczenia użytkownika w witrynie Azure portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Wybierz **pozycję Zapisz**, a następnie wybierz pozycję **Włącz,** aby włączyć dostawcę tożsamości.

    ![Opcje Zapisz i Włącz w sap](./media/sapfiori-tutorial/configuration1.png)

1. Po wyświetleniu monitu wybierz **przycisk OK.**

    ![Opcja OK w oknie dialogowym Konfiguracja SAML 2.0 w systemie SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Tworzenie użytkownika testowego SAP Fiori

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w SAP Fiori. Współpracuj z w swoim zespołem ekspertów SAP lub partnerem SAP w organizacji, aby dodać użytkownika na platformie SAP Fiori.

## <a name="test-sso"></a>Test SSO

1. Po aktywowaniu dostawcy tożsamości usługi Azure AD w sap Fiori spróbuj uzyskać dostęp do jednego z następujących adresów URL, aby przetestować logowanie jednokrotne (nie powinno być monitowane o nazwę użytkownika i hasło):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Zastąp *sapurl* rzeczywistą nazwą hosta SAP.

1. Adres URL testu powinien przejść do następującej strony aplikacji testowej w sap. Jeśli strona zostanie otwarta, azure ad logowania jednokrotnego jest pomyślnie skonfigurowany.

    ![Standardowa strona aplikacji testowej w sap](./media/sapfiori-tutorial/testingsso.png)

1. Jeśli zostanie wyświetlony monit o podanie nazwy użytkownika i hasła, włącz śledzenie, aby pomóc zdiagnozować problem. Użyj następującego adresu URL śledzenia:\//\<https: sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę SAP Fiori z usługą Azure AD](https://aad.portal.azure.com/)
