---
title: 'Samouczek: integracja usługi Azure Active Directory z oprogramowaniem SAP NetWeaver | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i oprogramowaniem SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b648b8458c7f91cae6edb079fbd2ac78553dd969
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523787"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Samouczek: integracja usługi Azure Active Directory z oprogramowaniem SAP NetWeaver

Z tego samouczka dowiesz się, jak zintegrować oprogramowanie SAP NetWeaver z usługą Azure Active Directory (Azure AD).
Zintegrowanie oprogramowania SAP NetWeaver z usługą Azure AD daje następujące korzyści:

* Możliwość kontrolowania dostępu do oprogramowania SAP NetWeaver za pomocą usługi Azure AD.
* Możliwość skonfigurowania użytkowników pod kątem automatycznego logowania do oprogramowania SAP NetWeaver przy użyciu kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem SAP NetWeaver, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja oprogramowania SAP NetWeaver z obsługą logowania jednokrotnego
* Oprogramowanie SAP NetWeaver w wersji co najmniej 7.20

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie SAP NetWeaver obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-sap-netweaver-from-the-gallery"></a>Dodawanie oprogramowania SAP NetWeaver z galerii

Aby skonfigurować integrację oprogramowania SAP NetWeaver z usługą Azure AD, należy dodać oprogramowanie SAP NetWeaver z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać oprogramowanie SAP NetWeaver z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **SAP NetWeaver**, wybierz pozycję **SAP NetWeaver** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Oprogramowanie SAP NetWeaver na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w oprogramowaniu SAP NetWeaver, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem oprogramowania SAP NetWeaver.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w oprogramowaniu SAP NetWeaver, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego oprogramowania SAP NetWeaver](#configure-sap-netweaver-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego oprogramowania SAP NetWeaver](#create-sap-netweaver-test-user)** — aby udostępnić w oprogramowaniu SAP NetWeaver odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w oprogramowaniu SAP NetWeaver, wykonaj następujące kroki:

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do firmowej witryny oprogramowania SAP NetWeaver jako administrator.

2. Upewnij się, że usługi **http** i **https** są aktywne i że w kodzie transakcji **SMICM** są przypisane odpowiednie porty.

3. Zaloguj się do klienta biznesowego systemu SAP (T01), w którym jest wymagane logowanie jednokrotne, i aktywuj zarządzanie sesją zabezpieczeń protokołu HTTP.

    a. Przejdź do sekcji **SICF_SESSIONS** kodu transakcji. Pokazuje ona wszystkie odpowiednie parametry profilu z bieżącymi wartościami. Wyglądają one podobnie do poniższych:-
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
    > Dostosuj te parametry zgodnie z wymaganiami organizacji — powyższe parametry są tu podane jedynie jako przykład.

    b. W razie potrzeby dostosuj parametry w profilu wystąpienia/profilu domyślnym systemu SAP i uruchom ponownie systemu SAP.

    c. Kliknij dwukrotnie odpowiedniego klienta, aby włączyć sesję zabezpieczeń protokołu HTTP.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktywuj poniższe usługi SICF:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Przejdź do kodu transakcji **SAML2** w kliencie biznesowym systemu SAP [T01/122]. Spowoduje to otwarcie interfejsu użytkownika w przeglądarce. W tym przykładzie dla klienta biznesowego systemu SAP przyjmujemy wartość 122.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Podaj nazwę użytkownika i hasło, które należy wprowadzić w interfejsie użytkownika, a następnie kliknij pozycję **Edit** (Edytuj).

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. W polu **Provider Name** (Nazwa dostawcy) zastąp ciąg T01122 ciągiem `http://T01122` i kliknij pozycję **Save** (Zapisz).

    > [!NOTE]
    > Nazwa domyślnego dostawcy uszkodzonym `<sid><client>` formatowanie, ale usługa Azure AD oczekuje nazwy w formacie `<protocol>://<name>`, zalecających, aby zachować nazwę dostawcy, jak `https://<sid><client>` umożliwia wielu aparatów SAP NetWeaver ABAP do skonfigurowania w usłudze Azure AD.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Generowanie metadanych dostawcy usługi** — po skonfigurowaniu ustawień **dostawcy lokalnego** i **zaufanych dostawców** w interfejsie użytkownika SAML 2.0 następnym krokiem będzie wygenerowanie pliku metadanych dostawcy usług (który zawiera wszystkie ustawienia, kontekst uwierzytelniania i inne konfiguracje w systemie SAP). Gdy plik zostanie wygenerowany, należy przekazać go w usłudze Azure AD.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Przejdź do **karty dostawcy lokalnego**.

    b. Kliknij pozycję **Metadata** (Metadane).

    c. Zapisz wygenerowany **pliku XML metadanych** na komputerze i przekaż go w **podstawową konfigurację protokołu SAML** sekcji, aby automatycznie wypełniać **identyfikator** i  **Adres URL odpowiedzi** wartości w witrynie Azure portal.

8. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji oprogramowania **SAP NetWeaver** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

9. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

10. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

11. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**, aby przekazać uzyskany wcześniej **plik metadanych dostawcy usług**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartości **Identyfikator** i **Adres URL odpowiedzi** zostaną automatycznie wypełnione w polach tekstowych sekcji **Podstawowa konfiguracja protokołu SAML**, jak pokazano poniżej:

    ![Informacje o domenie i adresach URL logowania jednokrotnego oprogramowania SAP NetWeaver](common/sp-identifier-reply.png)

    d. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Zaobserwowaliśmy, że niektórzy klienci zgłaszają błąd polegający na tym, że adres URL odpowiedzi skonfigurowany dla ich wystąpienia jest nieprawidłowy. Jeśli wystąpi taki błąd, możesz użyć następującego skryptu programu PowerShell jako obejścia w celu ustawienia prawidłowego adresu URL odpowiedzi dla wystąpienia:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > Identyfikator obiektu ServicePrincipal musisz najpierw ustawić samodzielnie lub możesz również przekazać go w tym miejscu.

12. Aplikacja SAP NetWeaver oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

13. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    a. Kliknij **ikonę Edytuj**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Z listy **Przekształcenie** wybierz pozycję **ExtractMailPrefix()**.

    c. Z listy **Parametr 1** wybierz pozycję **user.userprinicipalname**.

    d. Kliknij pozycję **Zapisz**.

14. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

15. W sekcji **Konfigurowanie aplikacji SAP NetWeaver** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-sap-netweaver-single-sign-on"></a>Konfigurowanie logowania jednokrotnego oprogramowania SAP NetWeaver

1. Zaloguj się do systemu SAP i przejdź do protokołu SAML2 kodu transakcji. Spowoduje to otwarcie nowego okna przeglądarki z ekranem konfiguracji protokołu SAML.

2. Aby skonfigurować punkty końcowe dla zaufanego dostawcy tożsamości (Azure AD), przejdź na kartę **Trusted Providers** (Zaufani dostawcy).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Naciśnij pozycję **Add** (Dodaj) i wybierz polecenie **Upload Metadata File** (Przekaż plik metadanych) z menu kontekstowego.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Przekaż plik metadanych, który został pobrany z witryny Azure Portal.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Na następnym ekranie wpisz nazwę aliasu. Może to być na przykład „aadsts”. Następnie naciśnij pozycję **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Upewnij się, że w polu **Digest Algorithm** (Algorytm porządkowania ) znajduje się wartość **SHA-256** i że nie są wymagane żadne zmiany, a następnie naciśnij pozycję **Next** (Dalej).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Na stronie **Single Sign-On Endpoints** (Punkty końcowe logowania jednokrotnego) użyj opcji **HTTP POST** i kliknij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Na stronie **Single Logout Endpoints** (Punkty końcowe wylogowania jednokrotnego) wybierz opcję **HTTPRedirect** i kliknij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Na stronie **Artifact Endpoints** (Punkty końcowe artefaktu) naciśnij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Na stronie **Authentication Requirements** (Wymagania dotyczące uwierzytelniania) kliknij przycisk **Finish** (Zakończ).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Przejdź do karty **Trusted Provider (Zaufany dostawca)** > **Identity Federation (Federacja tożsamości)** (na dole ekranu). Kliknij pozycję **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Kliknij przycisk **Add** (Dodaj) na karcie **Identity Federation** (Federacja tożsamości) (dolne okno).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. W oknie podręcznym wybierz opcję **Unspecified** (Nieokreślone) w polu **Supported NameID formats** (Obsługiwane formaty identyfikatora nazwy) i kliknij przycisk OK.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Zauważ, że wartości **źródła identyfikatora użytkownika** i **trybu mapowania identyfikatora użytkownika** określają połączenie między użytkownikiem systemu SAP i oświadczeniem usługi Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenariusz: mapowanie użytkownika systemu SAP na użytkownika usługi Azure AD.

    a. Zrzut ekranu przedstawiający szczegóły identyfikatora nazwy w systemie SAP.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Zrzut ekranu przedstawiający wymagane oświadczenia z usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenariusz: wybieranie identyfikatora użytkownika systemu SAP na podstawie skonfigurowanego adresu e-mail w rozwiązaniu SU01. W tym przypadku identyfikator poczty e-mail należy skonfigurować w rozwiązaniu su01 dla każdego użytkownika, który wymaga logowania jednokrotnego.

    a.  Zrzut ekranu przedstawiający szczegóły identyfikatora nazwy w systemie SAP.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Zrzut ekranu przedstawiający wymagane oświadczenia z usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Kliknij przycisk **Zapisz**, a następnie kliknij przycisk **Włącz**, aby włączyć dostawcę tożsamości.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/configuration1.png)

16. Po wyświetleniu monitu kliknij przycisk **OK**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do oprogramowania SAP NetWeaver.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw** i **Wszystkie aplikacje**, a następnie pozycję**SAP NetWeaver**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **SAP NetWeaver**.

    ![Link oprogramowania SAP NetWeaver na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sap-netweaver-test-user"></a>Tworzenie użytkownika testowego oprogramowania SAP NetWeaver

W tej sekcji utworzysz użytkownika Britta Simon w oprogramowaniu SAP NetWeaver. Aby dodać użytkownika na platformie SAP NetWeaver, skontaktuj się z zespołem ekspertów ds. systemu SAP w swojej organizacji lub z odpowiednim partnerem firmy SAP.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

1. Po aktywowaniu usługi Azure AD jako dostawcy tożsamości spróbuj uzyskać dostęp do poniższego adresu URL, aby sprawdzić działanie logowania jednokrotnego (nie zostanie wyświetlony monit o nazwę użytkownika i hasło)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (lub) użyj poniższego adresu URL

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Zastąp ciąg „sapurl” faktyczną nazwą hosta systemu SAP.

2. Powyższy adres URL powinien przenieść Cię na pokazany poniżej ekran. Jeśli poniższa strona zostanie wyświetlona, oznacza to, że konfiguracja logowania jednokrotnego usługi Azure AD została przeprowadzona pomyślnie.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/testingsso.png)

3. Jeśli zostanie wyświetlony monit o nazwę użytkownika i hasło, zdiagnozuj problem, włączając śledzenie przy użyciu poniższego adresu URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
