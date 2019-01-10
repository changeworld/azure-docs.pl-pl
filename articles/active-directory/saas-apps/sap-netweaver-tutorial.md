---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem SAP NetWeaver | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i oprogramowania SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 607d05818966e62407795640d223f1aed2f59bbb
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156752"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem SAP NetWeaver

W tym samouczku dowiesz się, jak zintegrować oprogramowanie SAP NetWeaver przy użyciu usługi Azure Active Directory (Azure AD).

Integracja oprogramowania SAP NetWeaver z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do oprogramowania SAP NetWeaver.
- Użytkowników, aby automatycznie uzyskać zalogowanych do oprogramowania SAP NetWeaver (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem SAP NetWeaver, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Oprogramowanie SAP NetWeaver logowanie jednokrotne włączone subskrypcji
- Oprogramowanie SAP NetWeaver V7.20 wymagane co najmniej

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie oprogramowania SAP NetWeaver z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-sap-netweaver-from-the-gallery"></a>Dodawanie oprogramowania SAP NetWeaver z galerii

Aby skonfigurować integrację z oprogramowaniem SAP NetWeaver w usłudze Azure AD, należy dodać oprogramowanie SAP NetWeaver z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać oprogramowanie SAP NetWeaver w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **oprogramowanie SAP NetWeaver**, wybierz opcję **oprogramowanie SAP NetWeaver** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Oprogramowanie SAP NetWeaver na liście wyników](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z oprogramowaniem SAP NetWeaver w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w oprogramowanie SAP NetWeaver do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkownika na oprogramowanie SAP NetWeaver musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z oprogramowaniem SAP NetWeaver, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego środowiska SAP NetWeaver](#creating-sapnetweaver-test-user)**  — aby odpowiednikiem Britta Simon w oprogramowanie SAP NetWeaver, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SAP NetWeaver.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z oprogramowaniem SAP NetWeaver, wykonaj następujące czynności:**

1. Otwórz nowe okno przeglądarki sieci web i dziennika do witryny firmy SAP NetWeaver jako administrator

2. Upewnij się, że **http** i **https** usługi są aktywne i odpowiednie porty są przypisane w **SMICM** kod T.

3. Zaloguj się do klientów biznesowych systemem SAP (T01), której wymagana jest usługa rejestracji Jednokrotnej i Aktywuj sesji HTTP zabezpieczeń zarządzania.

    a. Przejdź do kodu transakcji **SICF_SESSIONS**. Wyświetla wszystkie parametry odpowiedniego profilu z bieżących wartości. Wyglądać tak jak poniżej:-
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
    > Dopasowywanie powyżej parametrów, zgodnie z wymaganiami organizacji, powyżej parametry są podane tutaj jako oznaczenie tylko.

    b. W razie potrzeby dostosować parametry w wystąpieniu/domyślny profil systemu SAP i ponowne uruchamianie systemu SAP.

    c. Kliknij dwukrotnie odpowiedniego klienta, aby włączyć sesji zabezpieczeń protokołu HTTP.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktywuj poniżej SICF usług:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Przejdź do kodu transakcji **SAML2** w kliencie business systemu SAP [T01/122]. Interfejs użytkownika zostanie otwarty w przeglądarce. W tym przykładzie firma Microsoft zakłada, że 122 jako SAP business klienta.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Podaj nazwę użytkownika i hasło, wprowadź w interfejsie użytkownika, a następnie kliknij przycisk **Edytuj**.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. Zastąp **Nazwa dostawcy** z T01122 do `http://T01122` i kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Nazwa domyślnego dostawcy uszkodzonym <sid> <client> formatowanie, ale usługa Azure AD oczekuje nazwy w formacie <protocol>://<name>, zalecających, aby zachować nazwę dostawcy, jak https://<sid> <client> umożliwia wielu SAP Aparaty NetWeaver ABAP do skonfigurowania w usłudze Azure AD.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Generowanie metadanych dostawcy usługi**: — gdy to wszystko w konfigurowaniu **lokalnego dostawcy** i **zaufanych dostawców** ustawienia w interfejsie użytkownika SAML 2.0, następnym krokiem powinno być Generowanie pliku metadanych dostawcy usług, (który zawiera wszystkie ustawienia, kontekst uwierzytelniania i inne konfiguracje w systemie SAP). Po wygenerowaniu ten plik należy przekazać go w usłudze Azure AD.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Przejdź do **kartę lokalnego dostawcy**.

    b. Kliknij pozycję **metadanych**.

    c. Zapisz wygenerowany **pliku XML metadanych** na komputerze i przekaż go w **podstawową konfigurację protokołu SAML** sekcji, aby automatycznie polulate **identyfikator** i  **Adres URL odpowiedzi** wartości w witrynie Azure portal.

8. W witrynie Azure portal na **oprogramowanie SAP NetWeaver** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

9. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

10. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

11. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. Kliknij przycisk **przekazywania pliku metadanych** do przekazania **plik metadanych usługodawcy** której uzyskano wcześniej.

    ![Przekaż plik metadanych](common/editmetadataupload.png)

    b. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.

    ![Przekaż plik metadanych](common/uploadmetadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartości Uzyskaj automatycznie wypełnione w **podstawową konfigurację protokołu SAML** sekcji pola tekstowego, jak pokazano poniżej :

    ![Oprogramowanie SAP NetWeaver domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_url.png)

    d. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<your company instance of SAP NetWeaver>`

12. Oprogramowanie SAP NetWeaver aplikacja oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![Sekcja atrybutu](./media/sapnetweaver-tutorial/edit_attribute.png)

13. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    a. Kliknij pozycję **Edytuj** ikonę, aby otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.
    
    ![Sekcja atrybutu](./media/sapnetweaver-tutorial/nameidattribute.png)

    b. Na **Zarządzanie oświadczenia użytkownika** karcie, wykonaj następujące czynności:

    ![Sekcja atrybutu](./media/sapnetweaver-tutorial/nameidattribute1.png)

    * Wybierz **przekształcania**.
  
    * Z **przekształcania** listy wybierz `ExtractMailPrefix()`.
  
    * Z **parametr 1** listy wybierz `user.userprincipalname`.

    * Kliknij pozycję **Zapisz**.

14. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **XML metadanych Federacji** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link do pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_certificate.png)

15. Na **skonfigurować oprogramowanie SAP NetWeaver** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Oprogramowanie SAP NetWeaver konfiguracji](common/configuresection.png)

16. Zaloguj się do systemu SAP i przejdź do kodu transakcji SAML2. Otwiera nowe okno przeglądarki z SAML ekranu konfiguracji.

17. Do konfigurowania punktów końcowych dla zaufanej tożsamości dostawca (Azure AD), przejdź do **zaufanych dostawców** kartę.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

18. Naciśnij klawisz **Dodaj** i wybierz **Przekaż plik metadanych** z menu kontekstowego.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

19. Przekaż plik metadanych, który został pobrany z witryny Azure portal.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

20. Na następnym ekranie, wpisz nazwę aliasu. Na przykład aadsts, a następnie naciśnij klawisz **dalej** aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

21. Upewnij się, że Twoje **algorytm tworzenia skrótu** powinien być **algorytmu SHA-256** i nie wymaga dokonywania żadnych zmian i naciśnij klawisz **dalej**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

22. Na **Endpoints rejestracji jednokrotnej**, użyj **żądania HTTP POST** i kliknij przycisk **dalej** aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

23. Na **pojedynczej punktami końcowymi wylogowania** wybierz **HTTPRedirect** i kliknij przycisk **dalej** aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

24. Na **punktów końcowych artefaktu**, naciśnij klawisz **dalej** aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

25. Na **wymagania dotyczące uwierzytelniania**, kliknij przycisk **Zakończ**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

26. Przejdź do karty **zaufanego dostawcy** > **Federację tożsamości** (od dołu ekranu). Kliknij pozycję **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

27. Kliknij przycisk **Dodaj** w obszarze **Federację tożsamości** kartę (dolnym oknie).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

28. W oknie podręcznym wybierz **nieokreślony** z **NameID obsługiwane formaty** i kliknij przycisk OK.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

29. Należy pamiętać, że **użytkownika identyfikator źródła** i **tryb mapowania identyfikatora użytkownika** wartości określają łącza między SAP użytkowników i oświadczeń w usłudze Azure AD.  

    ####<a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenariusz: Użytkownik SAP do mapowania użytkowników usługi Azure AD.

    a. Zrzut ekranu szczegółów NameID od firmy SAP.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Zrzut ekranu, podając wymagane oświadczenia z usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/claimsaad1.png)

    ####<a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenariusz: Wybierz identyfikator użytkownika SAP oparte na adres e-mail skonfigurowany w SU01. W tym przypadku identyfikator poczty e-mail powinna być skonfigurowana w su01 dla każdego użytkownika, który wymaga logowania jednokrotnego.

    a.  Zrzut ekranu szczegółów NameID od firmy SAP.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Zrzut ekranu, podając wymagane oświadczenia z usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/claimsaad2.png)

30. Kliknij przycisk **Zapisz** a następnie kliknij przycisk **Włącz** umożliwiające dostawcy tożsamości.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/configuration1.png)

31. Kliknij przycisk **OK** po wyświetleniu monitu.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-sap-netweaver-test-user"></a>Tworzenie użytkownika testowego środowiska SAP NetWeaver

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w oprogramowanie SAP NetWeaver. Praca w domu zespół ekspertów SAP lub razem ze swoim partnerem SAP organizacji, aby dodać użytkowników na platformie SAP NetWeaver.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do oprogramowania SAP NetWeaver.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **oprogramowanie SAP NetWeaver**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

1. Gdy dostawca tożsamości usługi Azure AD został aktywowany, spróbuj dostęp pod adres URL do sprawdzenia logowania jednokrotnego (nie będzie żadnego monitu dla nazwy użytkownika i hasło)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    Użyj adresu URL poniżej (lub)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Zastąp sapurl faktyczną nazwą hosta SAP.

2. Powyższy adres URL powinno zająć się poniżej wymienionych ekranu. Jeśli jesteś w stanie nawiązać połączenie maksymalnie poniżej strony ustawień logowania jednokrotnego usługi Azure AD jest wykonywane pomyślnie.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/testingsso.png)

3. Jeśli wystąpi monit o nazwę użytkownika i hasło, można zdiagnozować problem przez włączenie śledzenia, korzystając z poniższych adresów URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
