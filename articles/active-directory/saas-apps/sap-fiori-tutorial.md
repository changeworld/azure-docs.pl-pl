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
ms.openlocfilehash: e94fe3156677a507eab91eee339ed29bf7b4ad2e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257641"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą rozwiązaniu SAP Fiori

W tym samouczku dowiesz się, jak zintegrować rozwiązaniu SAP Fiori, za pomocą usługi Azure Active Directory (Azure AD).
Integrowanie rozwiązaniu SAP Fiori z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do rozwiązaniu SAP Fiori.
* Użytkownikom można automatycznie zalogowany na rozwiązaniu SAP Fiori (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą rozwiązaniu SAP Fiori, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* SAP Fiori logowanie jednokrotne włączone subskrypcji
* SAP Fiori V7.20 wymagane co najmniej

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje rozwiązaniu SAP Fiori **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-sap-fiori-from-the-gallery"></a>Dodawanie rozwiązaniu SAP Fiori z galerii

Aby skonfigurować integrację usługi Azure AD rozwiązaniu SAP Fiori, należy dodać rozwiązaniu SAP Fiori z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać rozwiązaniu SAP Fiori z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **rozwiązaniu SAP Fiori**, wybierz opcję **rozwiązaniu SAP Fiori** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Rozwiązaniu SAP Fiori, na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą rozwiązaniu SAP Fiori w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkownika w rozwiązaniu SAP Fiori musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego dzięki rozwiązaniu SAP Fiori, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie SAP Fiori logowania jednokrotnego](#configure-sap-fiori-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego rozwiązaniu SAP Fiori](#create-sap-fiori-test-user)**  — aby odpowiednikiem Britta Simon w rozwiązaniu SAP Fiori, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowania jednokrotnego, dzięki rozwiązaniu SAP Fiori, wykonaj następujące czynności:

1. Otwórz nowe okno przeglądarki sieci web i zaloguj się do witryny firmy SAP Fiori jako administrator

2. Upewnij się, że usługi **http** i **https** są aktywne i że w kodzie transakcji **SMICM** są przypisane odpowiednie porty.

3. Zaloguj się do klientów biznesowych systemem SAP (T01), której wymagana jest usługa rejestracji Jednokrotnej i Aktywuj sesji HTTP zabezpieczeń zarządzania.

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

    ![Link do pobierania certyfikatu](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    d. Aktywuj poniższe usługi SICF:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Przejdź do kodu transakcji **SAML2** w kliencie biznesowym systemu SAP [T01/122]. Spowoduje to otwarcie interfejsu użytkownika w przeglądarce. W tym przykładzie dla klienta biznesowego systemu SAP przyjmujemy wartość 122.

    ![Link do pobierania certyfikatu](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

5. Podaj nazwę użytkownika i hasło, które należy wprowadzić w interfejsie użytkownika, a następnie kliknij pozycję **Edit** (Edytuj).

    ![Link do pobierania certyfikatu](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

6. W polu **Provider Name** (Nazwa dostawcy) zastąp ciąg T01122 ciągiem `http://T01122` i kliknij pozycję **Save** (Zapisz).

    > [!NOTE]
    > Nazwa domyślnego dostawcy uszkodzonym <sid><client> formatowanie, ale usługa Azure AD oczekuje nazwy w formacie <protocol>://<name>, zalecających, aby zachować nazwę dostawcy, jak https://<sid><client> umożliwia wielu aparatów SAP Fiori ABAP do skonfigurowania w usłudze Azure AD .

    ![Link do pobierania certyfikatu](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

7. **Generowanie metadanych dostawcy usługi** — po skonfigurowaniu ustawień **dostawcy lokalnego** i **zaufanych dostawców** w interfejsie użytkownika SAML 2.0 następnym krokiem będzie wygenerowanie pliku metadanych dostawcy usług (który zawiera wszystkie ustawienia, kontekst uwierzytelniania i inne konfiguracje w systemie SAP). Gdy plik zostanie wygenerowany, należy przekazać go w usłudze Azure AD.

    ![Link do pobierania certyfikatu](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

    a. Przejdź do **karty dostawcy lokalnego**.

    b. Kliknij pozycję **Metadata** (Metadane).

    c. Zapisz wygenerowany **pliku XML metadanych** na komputerze i przekaż go w **podstawową konfigurację protokołu SAML** sekcji, aby automatycznie wypełnić **identyfikator** i  **Adres URL odpowiedzi** wartości w witrynie Azure portal.

8. W [witryny Azure portal](https://portal.azure.com/)na **rozwiązaniu SAP Fiori** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

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

    ![SAP Fiori domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier-reply.png)

    d. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca:
    `https://<your company instance of SAP Fiori>`

    > [!NOTE]
    > Zaobserwowaliśmy, że niektórzy klienci zgłaszają błąd polegający na tym, że adres URL odpowiedzi skonfigurowany dla ich wystąpienia jest nieprawidłowy. Jeśli wystąpi taki błąd, możesz użyć następującego skryptu programu PowerShell jako obejścia w celu ustawienia prawidłowego adresu URL odpowiedzi dla wystąpienia:
    ```
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    ``` 
    > Identyfikator obiektu ServicePrincipal musisz najpierw ustawić samodzielnie lub możesz również przekazać go w tym miejscu.

12. SAP Fiori aplikacja oczekuje twierdzenia SAML, w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

13. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    a. Kliknij **ikonę Edytuj**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](./media/sapfiori-tutorial/nameidattribute.png)

    ![image](./media/sapfiori-tutorial/nameidattribute1.png)

    b. Z listy **Przekształcenie** wybierz pozycję **ExtractMailPrefix()**.

    c. Z listy **Parametr 1** wybierz pozycję **user.userprinicipalname**.

    d. Kliknij pozycję **Zapisz**.

14. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

15. Na **Konfigurowanie rozwiązaniu SAP Fiori** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-sap-fiori-single-sign-on"></a>Konfigurowanie SAP Fiori logowanie jednokrotne

1. Zaloguj się do systemu SAP i przejdź do kodu transakcji SAML2. Spowoduje to otwarcie nowego okna przeglądarki z ekranem konfiguracji protokołu SAML.

2. Aby skonfigurować punkty końcowe dla zaufanego dostawcy tożsamości (Azure AD), przejdź na kartę **Trusted Providers** (Zaufani dostawcy).

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

3. Naciśnij pozycję **Add** (Dodaj) i wybierz polecenie **Upload Metadata File** (Przekaż plik metadanych) z menu kontekstowego.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

4. Przekaż plik metadanych, który został pobrany z witryny Azure Portal.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

5. Na następnym ekranie wpisz nazwę aliasu. Może to być na przykład „aadsts”. Następnie naciśnij pozycję **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

6. Upewnij się, że w polu **Digest Algorithm** (Algorytm porządkowania ) znajduje się wartość **SHA-256** i że nie są wymagane żadne zmiany, a następnie naciśnij pozycję **Next** (Dalej).

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

7. Na stronie **Single Sign-On Endpoints** (Punkty końcowe logowania jednokrotnego) użyj opcji **HTTP POST** i kliknij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

8. Na stronie **Single Logout Endpoints** (Punkty końcowe wylogowania jednokrotnego) wybierz opcję **HTTPRedirect** i kliknij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

9. Na stronie **Artifact Endpoints** (Punkty końcowe artefaktu) naciśnij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

10. Na stronie **Authentication Requirements** (Wymagania dotyczące uwierzytelniania) kliknij przycisk **Finish** (Zakończ).

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

11. Przejdź do karty **Trusted Provider (Zaufany dostawca)** > **Identity Federation (Federacja tożsamości)** (na dole ekranu). Kliknij pozycję **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

12. Kliknij przycisk **Add** (Dodaj) na karcie **Identity Federation** (Federacja tożsamości) (dolne okno).

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

13. W oknie podręcznym wybierz opcję **Unspecified** (Nieokreślone) w polu **Supported NameID formats** (Obsługiwane formaty identyfikatora nazwy) i kliknij przycisk OK.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

14. Należy pamiętać, że **użytkownika identyfikator źródła** i **tryb mapowania Identyfikatora użytkownika** wartości określają łącza między SAP użytkowników i oświadczeń w usłudze Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenariusz: mapowanie użytkownika systemu SAP na użytkownika usługi Azure AD.

    a. Zrzut ekranu przedstawiający szczegóły identyfikatora nazwy w systemie SAP.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/nameiddetails.png)

    b. Zrzut ekranu przedstawiający wymagane oświadczenia z usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenariusz: Wybierz identyfikator użytkownika SAP oparte na adres e-mail skonfigurowany w SU01. W tym przypadku identyfikator poczty e-mail powinna być skonfigurowana w su01 dla każdego użytkownika, który wymaga logowania jednokrotnego.

    a.  Zrzut ekranu przedstawiający szczegóły identyfikatora nazwy w systemie SAP.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    b. Zrzut ekranu przedstawiający wymagane oświadczenia z usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/claimsaad2.png)

15. Kliknij przycisk **Zapisz**, a następnie kliknij przycisk **Włącz**, aby włączyć dostawcę tożsamości.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/configuration1.png)

16. Po wyświetleniu monitu kliknij przycisk **OK**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład BrittaSimon@contoso.com.

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do rozwiązaniu SAP Fiori.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **rozwiązaniu SAP Fiori**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **rozwiązaniu SAP Fiori**.

    ![Link rozwiązaniu SAP Fiori, na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sap-fiori-test-user"></a>Tworzenie użytkownika testowego rozwiązaniu SAP Fiori

W tej sekcji utworzysz użytkownika w rozwiązaniu SAP Fiori o nazwie Britta Simon. Praca w domu zespół ekspertów SAP lub razem ze swoim partnerem SAP organizacji, aby dodać użytkowników na platformie SAP Fiori.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

1. Po aktywowaniu usługi Azure AD jako dostawcy tożsamości spróbuj uzyskać dostęp do poniższego adresu URL, aby sprawdzić działanie logowania jednokrotnego (nie zostanie wyświetlony monit o nazwę użytkownika i hasło)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (lub) użyj poniższego adresu URL

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Zastąp ciąg „sapurl” faktyczną nazwą hosta systemu SAP.

2. Powyższy adres URL powinien przenieść Cię na pokazany poniżej ekran. Jeśli poniższa strona zostanie wyświetlona, oznacza to, że konfiguracja logowania jednokrotnego usługi Azure AD została przeprowadzona pomyślnie.

    ![Konfigurowanie logowania jednokrotnego](./media/sapfiori-tutorial/testingsso.png)

3. Jeśli zostanie wyświetlony monit o nazwę użytkownika i hasło, zdiagnozuj problem, włączając śledzenie przy użyciu poniższego adresu URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)