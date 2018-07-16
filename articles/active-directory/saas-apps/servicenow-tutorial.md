---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu usługi ServiceNow | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 8aebe6bef536840722d9b07c846687eaf6d195db
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051073"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu usługi ServiceNow

W tym samouczku dowiesz się, jak zintegrować usługi ServiceNow z usługą Azure Active Directory (Azure AD).

Integrowanie usługi ServiceNow z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi ServiceNow.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do usługi ServiceNow (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu usługi ServiceNow, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Dla usługi ServiceNow, wystąpienie lub dzierżawy usługi ServiceNow, Calgary, wersji lub nowszej
- Express usługi ServiceNow, wystąpienie usługi ServiceNow, Express, Helsinki, wersji lub nowszej
- Dzierżawca usługi ServiceNow musi mieć [wielu pojedynczy znak na dodatek dostawcy](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) włączone. Można to zrobić [przesyłania żądania obsługi](https://hi.service-now.com).
- W przypadku automatycznej konfiguracji należy włączyć dodatek wielu dostawców dla usługi ServiceNow.

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi ServiceNow za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-servicenow-from-the-gallery"></a>Dodawanie usługi ServiceNow za pomocą galerii
Aby skonfigurować integrację z usługą ServiceNow w usłudze Azure AD, należy dodać usługi ServiceNow z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi ServiceNow z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **usługi ServiceNow**, wybierz opcję **ServiceNow** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Usługi ServiceNow na liście wyników](./media/servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowania jednokrotnego przy użyciu usługi ServiceNow, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać odpowiednika użytkownika usługi ServiceNow dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkownika usługi ServiceNow musi zostać ustanowione.

W usługi ServiceNow, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu usługi ServiceNow, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowanie jednokrotne dla usługi ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie usługi Azure AD logowanie jednokrotne dla usługi ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Tworzenie użytkownika testowego usługi ServiceNow](#create-a-servicenow-test-user)**  — aby odpowiednikiem Britta Simon w usługi ServiceNow, połączonego z usługi Azure AD reprezentacja użytkownika.
5. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Konfigurowanie usługi Azure AD logowanie jednokrotne dla usługi ServiceNow

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi ServiceNow.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu usługi ServiceNow, wykonaj następujące czynności:**

1. W witrynie Azure portal na **ServiceNow** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Na **usługi ServiceNow, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena usługi ServiceNow i adresy URL pojedynczego logowania jednokrotnego informacji](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<instance-name>.service-now.com/navpage.do`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<instance-name>.service-now.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Należy zaktualizować te wartości z rzeczywistych adres URL logowania i identyfikator, który zostało wyjaśnione w dalszej części tego samouczka.

4. Na **certyfikat podpisywania SAML** sekcji, wykonaj następujące czynności: 

    ![Link pobierania certyfikatu](./media/servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. Kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika, ponieważ ten adres Url metadanych Federacji aplikacji, które zostaną użyte w dalszej części tego samouczka.

    b. Kliknij przycisk **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/servicenow-tutorial/tutorial_general_400.png)

6. Zaloguj się jako administrator aplikacji ServiceNow.

7. Aktywuj **integracja — wiele pojedynczego logowania Instalatora dostawcy** wtyczkę, wykonując następne kroki:

    a. W okienku nawigacji po lewej stronie wyszukiwania **definicji systemu** sekcji z paska wyszukiwania, a następnie kliknij przycisk **wtyczek**.

    ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_servicenow_03.png "Aktywuj dodatek typu plug-in")

     b. Wyszukaj **integracja — wiele pojedynczego logowania Instalatora dostawcy**.

     ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_servicenow_04.png "Aktywuj dodatek typu plug-in")

    c. Wybierz wtyczkę. Kliknij prawym przyciskiem myszy i wybierz **Aktywuj/uaktualnić**.

    d. Kliknij przycisk **Aktywuj** przycisku.

8. Istnieją dwa sposoby, w którym **ServiceNow** mogą być skonfigurowane automatyczne i ręczne.

9. Do konfigurowania **ServiceNow** automatycznie wykonaj poniższe kroki

    a. Wróć do **ServiceNow** Signle logowania na stronie w witrynie Azure portal.

    b. Jednym kliknięciem Konfigurowanie usługi zostanie podana dla usługi ServiceNow oznacza to, aby platforma Azure AD automatycznie skonfigurować usługi ServiceNow dla uwierzytelniania opartego na SAML. Aby włączyć tę usługę, przejdź do **konfiguracji usługi ServiceNow** kliknij **skonfigurować usługi ServiceNow** otworzyć Konfigurowanie logowania jednokrotnego okno.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    c. Wprowadź nazwę wystąpienia usługi ServiceNow, nazwa użytkownika administratora i hasło administratora w **Konfigurowanie logowania jednokrotnego** formularza, a następnie kliknij przycisk **Konfiguruj teraz**. Należy zauważyć, że podana nazwa użytkownika administratora musi mieć **security_admin** rola przypisana w usługi ServiceNow, w tym do pracy. W przeciwnym razie ręcznie skonfigurować usługi ServiceNow, używać usługi Azure AD jako dostawcy tożsamości SAML, kliknij pozycję **ręcznie skonfigurować logowanie jednokrotne** i skopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z sekcji krótki.

    ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/configure.png "skonfigurować adres URL aplikacji")

    d. Zaloguj się jako administrator aplikacji ServiceNow.

    e. W konfiguracji automatycznej wszystkie niezbędne ustawienia zostaną skonfigurowane na **ServiceNow** po stronie ale **certyfikat X.509** nie jest domyślnie włączona. Masz Dokonaj mapowania go ręcznie do dostawcy tożsamości w usługi ServiceNow. Wykonaj poniższe kroki, aby uzyskać takie same:
    
    * W okienku nawigacji po lewej stronie kliknij pozycję **dostawców tożsamości** w obszarze **wielu dostawców logowania jednokrotnego**.

      ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_07.png "skonfigurować logowanie jednokrotne")

    * Kliknij dostawcę tożsamości automatycznie generowanych

      ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_08.png "skonfigurować logowanie jednokrotne")

    * Przewiń w dół do **certyfikat X.509** sekcji. Wybierz pozycję **Edit** (Edytuj).

      ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_09.png "skonfigurować logowanie jednokrotne")
    
    * Wybierz certyfikat, a następnie kliknij ikonę strzałki w prawo, aby dodać certyfikat

      ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_11.png "skonfigurować logowanie jednokrotne")

    * Kliknij pozycję **Zapisz**.

    * Kliknij pozycję **Aktywuj** w prawym górnym rogu strony.

10. Do konfigurowania **ServiceNow** ręcznie wykonaj poniższe kroki

11. Zaloguj się jako administrator aplikacji ServiceNow.

12. W okienku nawigacji po lewej stronie wyszukiwania **wielu dostawców logowania jednokrotnego** sekcji z paska wyszukiwania, a następnie kliknij przycisk **właściwości**.

    ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/tutorial_servicenow_06.png "skonfigurować adres URL aplikacji")

13. Na **wiele właściwości logowania jednokrotnego dostawcy** okno dialogowe, należy wykonać następujące czynności:

    ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/ic7694981.png "skonfigurować adres URL aplikacji")

    a. Jako **umożliwiają wielu dostawcy logowania jednokrotnego**, wybierz opcję **tak**.

    b. Jako **włączyć automatyczne importowanie użytkowników z wszystkich dostawców tożsamości do tabeli użytkownika**, wybierz opcję **tak**.

    c. Jako **debugowania Włącz rejestrowanie dla wielu dostawcy integracja logowania jednokrotnego**, wybierz opcję **tak**.

    d. W **tabeli pola na użytkownika...**  polu tekstowym wpisz **nazwa_użytkownika**.

    e. Kliknij pozycję **Zapisz**.

14. W okienku nawigacji po lewej stronie wyszukiwania **wielu dostawców logowania jednokrotnego** sekcji z paska wyszukiwania, a następnie kliknij przycisk **certyfikaty x509**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_05.png "skonfigurować logowanie jednokrotne")

15. Na **certyfikaty X.509** okno dialogowe, kliknij przycisk **New**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694974.png "skonfigurować logowanie jednokrotne")

16. Na **certyfikaty X.509** okno dialogowe, należy wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694975.png "skonfigurować logowanie jednokrotne")

    a. W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (na przykład: **TestSAML2.0**).

    b. Wybierz **Active**.

    c. Jako **Format**, wybierz opcję **PEM**.

    d. Jako **typu**, wybierz opcję **zaufania Cert Store**.

    e. Otwórz swój certyfikat zakodowany w formacie Base64, pobrany z platformy Azure w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu PEM** pola tekstowego.

     f. Kliknij przycisk **przesłać**.

17. W okienku nawigacji po lewej stronie kliknij pozycję **dostawców tożsamości**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_07.png "skonfigurować logowanie jednokrotne")

18. Na **dostawców tożsamości** okno dialogowe, kliknij przycisk **New**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694977.png "skonfigurować logowanie jednokrotne")

19. Na **dostawców tożsamości** okno dialogowe, kliknij przycisk **SAML2 Update1?**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694978.png "skonfigurować logowanie jednokrotne")

20. W oknie dialogowym właściwości Update1 SAML2 wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/idp.png "skonfigurować logowanie jednokrotne")

    a. Wybierz **adresu URL** opcji **Importuj metadane dostawcy tożsamości** okno dialogowe.

    b. Wprowadź **adres Url metadanych Federacji aplikacji** skopiowanej w witrynie Azure portal.

    c. Kliknij przycisk **Importuj**.

21. Odczytuje adres URL metadanych dostawcy tożsamości i wypełni wszystkie informacje dotyczące pól.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694982.png "skonfigurować logowanie jednokrotne")

    a. W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (na przykład **SAML 2.0**).
    
    b. Kopiuj **strony głównej usługi ServiceNow** wartość, wklej ją w **adres URL logowania** polu tekstowym w **usługi ServiceNow, domena i adresy URL** sekcji w witrynie Azure portal.

    > [!NOTE]
    > Strona główna wystąpienia usługi ServiceNow to składa się z Twojego **adres URL dzierżawy ServieNow** i **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

    c. Kopiuj **identyfikator jednostki / wystawcy** wartość, wklej ją w **identyfikator** polu tekstowym w **usługi ServiceNow, domena i adresy URL** sekcji w witrynie Azure portal.

    d. Kliknij przycisk **zaawansowane**. W **pole użytkownika** polu tekstowym wpisz **e-mail** lub **nazwa_użytkownika**, w zależności od pole, które jest używany do jednoznacznego identyfikowania użytkowników w danym wdrożeniu usługi ServiceNow.

    > [!NOTE]
    > Można skonfigurować usługi Azure AD, aby emitować identyfikator użytkownika usługi Azure AD (główna nazwa użytkownika) lub adres e-mail jako unikatowy identyfikator w tokenie SAML, przechodząc do **ServiceNow > atrybuty > logowanie jednokrotne** części witryny Azure portal żądane pola mapowania i **nameidentifier** atrybutu. Wartość przechowywana wybranego atrybutu w usłudze Azure AD (na przykład główna nazwa użytkownika) musi być zgodna wartość przechowywaną w ServiceNow wprowadzony pola (na przykład nazwa_użytkownika)

    e. W obszarze **x509 certyfikatu**, wyświetla certyfikatu utworzonego w poprzednim kroku.

    > [!NOTE]
    > Usługi ServiceNow nie zezwolenia na aktywację dostawcy tożsamości nie klikając przycisk Testuj połączenie, aby zastąpić taki sam, wykonaj poniższe kroki.

22. Kliknij ikonę menu z nowego dostawcy tożsamości, który został utworzony jako część konfiguracji i wybierz listę **skopiuj sys_id**

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694992.png "skonfigurować logowanie jednokrotne")

23. W polu wyszukiwania lewy górny Wyszukaj **sys_properties.list** i naciśnij klawisz enter.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694993.png "skonfigurować logowanie jednokrotne")

24. Kliknij przycisk **Nowy**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694994.png "skonfigurować logowanie jednokrotne")

25. W **właściwość systemu** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694995.png "skonfigurować logowanie jednokrotne")

    a. Wprowadź `glide.authenticate.sso.redirect.idp` wartość w polu tekstowym Nazwa.

    b. W **wartość** pole tekstowe, Wklej sys_id kopiowania wartości skopiowanej w poprzednich krokach.

    c. Wybierz **prywatnej**.

    d. Kliknij przycisk **przesłać**.

26. Kliknij przycisk **Nowy**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694994.png "skonfigurować logowanie jednokrotne")

27. W **właściwość systemu** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694996.png "skonfigurować logowanie jednokrotne")

    a. Wprowadź `glide.authenticate.multisso.test.connection.mandatory` wartość w polu tekstowym Nazwa.

    b. W **wartość** polu tekstowym wprowadź **false**.

    c. Kliknij przycisk **przesłać**.

28. Po to powyżej kroku, teraz będzie można aktywować nowy dostawca tożsamości i usługi logowania jednokrotnego powinna działać

> [!NOTE]
> Ponadto należy pamiętać, że masz testowanie nowej konfiguracji dostawcy tożsamości w nowe okno incognito

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurowanie usługi Azure AD logowanie jednokrotne dla usługi ServiceNow Express

1. W witrynie Azure portal na **ServiceNow** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Na **usługi ServiceNow, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. W **adres URL logowania** polu tekstowym wpisz wartość, przy użyciu następującego wzorca: `https://<instance-name>.service-now.com/navpage.do`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywisty adres URL logowania i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta usługi ServiceNow](https://www.servicenow.com/support/contact-support.html) do uzyskania tych wartości.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_certificates.png)

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_general_400.png)

6. Jednym kliknięciem Konfigurowanie usługi zostanie podana dla usługi ServiceNow oznacza to, aby platforma Azure AD automatycznie skonfigurować usługi ServiceNow dla uwierzytelniania opartego na SAML. Aby włączyć tę usługę, przejdź do **konfiguracji usługi ServiceNow** kliknij **skonfigurować usługi ServiceNow** otworzyć Konfigurowanie logowania jednokrotnego okno.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Wprowadź nazwę wystąpienia usługi ServiceNow, nazwa użytkownika administratora i hasło administratora w **Konfigurowanie logowania jednokrotnego** formularza, a następnie kliknij przycisk **Konfiguruj teraz**. Należy zauważyć, że podana nazwa użytkownika administratora musi mieć **security_admin** rola przypisana w usługi ServiceNow, w tym do pracy. W przeciwnym razie ręcznie skonfigurować usługi ServiceNow, używać usługi Azure AD jako dostawcy tożsamości SAML, kliknij pozycję **ręcznie skonfigurować logowanie jednokrotne** i skopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z sekcji krótki.

    ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/configure.png "skonfigurować adres URL aplikacji")

8. Zaloguj się jako administrator aplikacji Express usługi ServiceNow.

9. W okienku nawigacji po lewej stronie kliknij pozycję **logowania jednokrotnego**.

    ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/ic7694980ex.png "skonfigurować adres URL aplikacji")

10. Na **logowania jednokrotnego** okno dialogowe, kliknij ikonę konfiguracji w prawym górnym rogu, a następnie ustaw następujące właściwości:

    ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/ic7694981ex.png "skonfigurować adres URL aplikacji")

    a. Przełącz **umożliwiają wielu dostawcy logowania jednokrotnego** po prawej stronie.
    
    b. Przełącz **debugowania Włącz rejestrowanie dla wielu dostawcy integracja logowania jednokrotnego** po prawej stronie.
    
    c. W **tabeli pola na użytkownika...**  polu tekstowym wpisz **nazwa_użytkownika**.

11. Na **logowania jednokrotnego** okno dialogowe, kliknij przycisk **Dodaj nowy certyfikat**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694973ex.png "skonfigurować logowanie jednokrotne")

12. Na **certyfikaty X.509** okno dialogowe, należy wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694975.png "skonfigurować logowanie jednokrotne")

    a. W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (na przykład: **TestSAML2.0**).

    b. Wybierz **Active**.

    c. Jako **Format**, wybierz opcję **PEM**.

    d. Jako **typu**, wybierz opcję **zaufania Cert Store**.

    e. Otwórz swój certyfikat zakodowany w formacie Base64, pobrany z witryny Azure portal w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu PEM** pola tekstowego.

    f. Kliknij przycisk **aktualizacji**

13. Na **logowania jednokrotnego** okno dialogowe, kliknij przycisk **Dodaj nowy dostawca tożsamości**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694976ex.png "skonfigurować logowanie jednokrotne")

14. Na **Dodaj nowego dostawcę tożsamości** okna dialogowego, w obszarze **Konfigurowanie dostawcy tożsamości**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694982ex.png "skonfigurować logowanie jednokrotne")

    a. W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (na przykład: **SAML 2.0**).

    b. W **adres URL dostawcy tożsamości** pole, Wklej wartość **identyfikator dostawcy tożsamości**, które zostały skopiowane z witryny Azure portal.
    
    c. W **AuthnRequest dostawcy tożsamości** pole, Wklej wartość **adresu URL żądania uwierzytelniania**, które zostały skopiowane z witryny Azure portal.

    d. W **SingleLogoutRequest dostawcy tożsamości** pole, Wklej wartość **adresu URL usługi wylogowania jednokrotnego**, które zostały skopiowane z witryny Azure portal

    e. Jako **certyfikatu dostawcy tożsamości**, wybranie certyfikatu utworzonego w poprzednim kroku.

15. Kliknij przycisk **Zaawansowane ustawienia**, a następnie w obszarze **dodatkowe właściwości dostawcy tożsamości**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694983ex.png "skonfigurować logowanie jednokrotne")

    a. W **Protokół powiązania dla dostawcy tożsamości SingleLogoutRequest** polu tekstowym wpisz **urn: oasis: nazwy: tc: SAML:2.0:bindings:HTTP-przekierowania**.

    b. W **zasad NameID** polu tekstowym wpisz **urn: oasis: nazwy: tc: SAML:1.1:nameid — format: nieokreślony**.

    c. W **metoda AuthnContextClassRef**, typ `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Usuń zaznaczenie opcji **tworzenie AuthnContextClass**.

16. W obszarze **dodatkowe właściwości dostawcy usług**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694984ex.png "skonfigurować logowanie jednokrotne")

    a. W **strony głównej usługi ServiceNow** pole tekstowe, wpisz adres URL strony głównej wystąpienia usługi ServiceNow.

    > [!NOTE]
    > Strona główna wystąpienia usługi ServiceNow to składa się z Twojego **adres URL dzierżawy ServieNow** i **/navpage.do** (na przykład: `https://fabrikam.service-now.com/navpage.do`).

    b. W **identyfikator jednostki / wystawca** pole tekstowe, wpisz adres URL dzierżawy usługi ServiceNow.

    c. W **identyfikator URI odbiorców** pole tekstowe, wpisz adres URL dzierżawy usługi ServiceNow.

    d. W **pochylanie zegara** polu tekstowym wpisz **60**.

    e. W **pole użytkownika** polu tekstowym wpisz **e-mail** lub **nazwa_użytkownika**, w zależności od pole, które jest używany do jednoznacznego identyfikowania użytkowników w danym wdrożeniu usługi ServiceNow.

    > [!NOTE]
    > Można skonfigurować usługi Azure AD, aby emitować identyfikator użytkownika usługi Azure AD (główna nazwa użytkownika) lub adres e-mail jako unikatowy identyfikator w tokenie SAML, przechodząc do **ServiceNow > atrybuty > logowanie jednokrotne** części witryny Azure portal żądane pola mapowania i **nameidentifier** atrybutu. Wartość przechowywana wybranego atrybutu w usłudze Azure AD (na przykład główna nazwa użytkownika) musi być zgodna wartość przechowywaną w ServiceNow wprowadzony pola (na przykład nazwa_użytkownika)

    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/servicenow-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/servicenow-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/servicenow-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/servicenow-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-servicenow-test-user"></a>Tworzenie użytkownika testowego usługi ServiceNow

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w usługi ServiceNow. Usługi ServiceNow obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów można znaleźć [tutaj](servicenow-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

> [!NOTE]
> Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej klienta usługi ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do usługi ServiceNow.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon usługi ServiceNow, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **ServiceNow**.

    ![Link usługi ServiceNow na liście aplikacji](./media/servicenow-tutorial/tutorial_servicenow_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi ServiceNow w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji usługi ServiceNow.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](servicenow-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/servicenow-tutorial/tutorial_general_01.png
[2]: ./media/servicenow-tutorial/tutorial_general_02.png
[3]: ./media/servicenow-tutorial/tutorial_general_03.png
[4]: ./media/servicenow-tutorial/tutorial_general_04.png

[100]: ./media/servicenow-tutorial/tutorial_general_100.png

[200]: ./media/servicenow-tutorial/tutorial_general_200.png
[201]: ./media/servicenow-tutorial/tutorial_general_201.png
[202]: ./media/servicenow-tutorial/tutorial_general_202.png
[203]: ./media/servicenow-tutorial/tutorial_general_203.png
