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
ms.date: 10/04/2018
ms.author: jeedes
ms.openlocfilehash: 5b7baffea8e718810a91ea9687a007d36c806aab
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850010"
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

- Subskrypcji usługi Azure AD
- Dla usługi ServiceNow, wystąpienie lub dzierżawy usługi ServiceNow, Calgary, wersji lub nowszej
- Express usługi ServiceNow, wystąpienie usługi ServiceNow, Express, Helsinki, wersji lub nowszej
- Dzierżawca usługi ServiceNow musi mieć [wielu pojedynczy znak na dodatek dostawcy](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) włączone. Można to zrobić [przesyłania żądania obsługi](https://hi.service-now.com).
- W przypadku automatycznej konfiguracji należy włączyć dodatek wielu dostawców dla usługi ServiceNow.

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi ServiceNow za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-servicenow-from-the-gallery"></a>Dodawanie usługi ServiceNow za pomocą galerii

Aby skonfigurować integrację z usługą ServiceNow w usłudze Azure AD, należy dodać usługi ServiceNow z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi ServiceNow z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

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

2. Kliknij przycisk **pojedynczej zmiany trybu logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_general_300.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_general_301.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_general_302.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    ![Domena usługi ServiceNow i adresy URL pojedynczego logowania jednokrotnego informacji](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<instance-name>.service-now.com/navpage.do`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Te wartości są prawdziwe. Należy zaktualizować te wartości z rzeczywistych adres URL logowania i identyfikator, który zostało wyjaśnione w dalszej części tego samouczka.

6. Na **certyfikat podpisywania SAML** sekcji, wykonaj następujące czynności:

    ![Link pobierania certyfikatu](./media/servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. Kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika, ponieważ ten adres Url metadanych Federacji aplikacji, które zostaną użyte w dalszej części tego samouczka.

    b. Kliknij pozycję **Pobierz** można pobrać **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

7. Zaloguj się jako administrator aplikacji ServiceNow.

8. Aktywuj **integracja — wiele pojedynczego logowania Instalatora dostawcy** wtyczkę, wykonując następne kroki:

    a. W okienku nawigacji po lewej stronie wyszukiwania **definicji systemu** sekcji z paska wyszukiwania, a następnie kliknij przycisk **wtyczek**.

    ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_servicenow_03.png "Aktywuj dodatek typu plug-in")

     b. Wyszukaj **integracja — wiele pojedynczego logowania Instalatora dostawcy**.

     ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_servicenow_04.png "Aktywuj dodatek typu plug-in")

    c. Wybierz wtyczkę. Kliknij prawym przyciskiem myszy i wybierz **Aktywuj/uaktualnić**.

     ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_activate.png "Aktywuj dodatek typu plug-in")

    d. Kliknij przycisk **Aktywuj** przycisku.

     ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_activate1.png "Aktywuj dodatek typu plug-in")

9. W okienku nawigacji po lewej stronie wyszukiwania **wielu dostawców logowania jednokrotnego** sekcji z paska wyszukiwania, a następnie kliknij przycisk **właściwości**.

    ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/tutorial_servicenow_06.png "skonfigurować adres URL aplikacji")

10. Na **wiele właściwości logowania jednokrotnego dostawcy** okno dialogowe, należy wykonać następujące czynności:

    ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/ic7694981.png "skonfigurować adres URL aplikacji")

    * Jako **umożliwiają wielu dostawcy logowania jednokrotnego**, wybierz opcję **tak**.
  
    * Jako **włączyć automatyczne importowanie użytkowników z wszystkich dostawców tożsamości do tabeli użytkownika**, wybierz opcję **tak**.

    * Jako **debugowania Włącz rejestrowanie dla wielu dostawcy integracja logowania jednokrotnego**, wybierz opcję **tak**.

    * W **tabeli pola na użytkownika...**  polu tekstowym wpisz **nazwa_użytkownika**.
  
    * Kliknij pozycję **Zapisz**.

11. Istnieją dwa sposoby, w którym **ServiceNow** można skonfigurować — automatyczne i ręczne.

12. Do konfigurowania **ServiceNow** automatycznie, wykonaj następujące czynności:

    * Wróć do **ServiceNow** logowania jednokrotnego na stronie w witrynie Azure portal.

    * Jednym kliknięciem Konfigurowanie usługi zostanie podana dla usługi ServiceNow oznacza to, aby platforma Azure AD automatycznie skonfigurować usługi ServiceNow dla uwierzytelniania opartego na SAML. Aby włączyć tę usługę, przejdź do **konfiguracji usługi ServiceNow** kliknij **skonfigurować usługi ServiceNow** otworzyć Konfigurowanie logowania jednokrotnego okno.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Wprowadź nazwę wystąpienia usługi ServiceNow, nazwa użytkownika administratora i hasło administratora w **Konfigurowanie logowania jednokrotnego** formularza, a następnie kliknij przycisk **Konfiguruj teraz**. Należy zauważyć, że podana nazwa użytkownika administratora musi mieć **security_admin** rola przypisana w usługi ServiceNow, w tym do pracy. W przeciwnym razie ręcznie skonfigurować usługi ServiceNow, używać usługi Azure AD jako dostawcy tożsamości SAML, kliknij pozycję **ręcznie skonfigurować logowanie jednokrotne** i skopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z sekcji krótki.

        ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/configure.png "skonfigurować adres URL aplikacji")

    * Zaloguj się jako administrator aplikacji ServiceNow.

    * W konfiguracji automatycznej wszystkie niezbędne ustawienia zostaną skonfigurowane na **ServiceNow** po stronie ale **certyfikat X.509** nie jest domyślnie włączona. Masz Dokonaj mapowania go ręcznie do dostawcy tożsamości w usługi ServiceNow. Wykonaj poniższe kroki, aby uzyskać takie same:

    * W okienku nawigacji po lewej stronie wyszukiwania **wielu dostawców logowania jednokrotnego** sekcji z paska wyszukiwania, a następnie kliknij przycisk **dostawców tożsamości**.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_07.png "skonfigurować logowanie jednokrotne")

    * Kliknij na automatycznie generowany dostawcy tożsamości

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_08.png "skonfigurować logowanie jednokrotne")

    *  Na **dostawcy tożsamości** sekcji, wykonaj następujące czynności:

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/automatic_config.png "skonfigurować logowanie jednokrotne")

        * W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (na przykład **Microsoft Azure federacyjnego logowania jednokrotnego**).

        * Usuń wypełniane **SingleLogoutRequest dostawcy tożsamości** wartość z pola tekstowego.

        * Kopiuj **strony głównej usługi ServiceNow** wartość, wklej ją w **adres URL logowania** polu tekstowym w **usługi ServiceNow, domena i adresy URL** sekcji w witrynie Azure portal.

            > [!NOTE]
            > Strona główna wystąpienia usługi ServiceNow to składa się z Twojego **adres URL dzierżawy ServieNow** i **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

        * Kopiuj **identyfikator jednostki / wystawcy** wartość, wklej ją w **identyfikator** polu tekstowym w **usługi ServiceNow, domena i adresy URL** sekcji w witrynie Azure portal.

        * Upewnij się, że **zasad NameID** ustawiono `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` wartość. 

    * Przewiń w dół do **certyfikat X.509** zaznacz **Edytuj**.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_09.png "skonfigurować logowanie jednokrotne")

    * Wybierz certyfikat, a następnie kliknij ikonę strzałki w prawo, aby dodać certyfikat

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_11.png "skonfigurować logowanie jednokrotne")

    * Kliknij pozycję **Zapisz**.

    * Kliknij pozycję **Testuj połączenie** w prawym górnym rogu strony.

        ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_activate2.png "Aktywuj dodatek typu plug-in")

    * Po kliknięciu **Testuj połączenie**, otrzymasz wyskakujące, w którym należy wprowadzić poświadczenia i jest wyświetlany poniżej strony z wynikami. **Wyniki testów wylogowania logowania jednokrotnego** oczekuje się, błąd można zignorować błąd i kliknij przycisk **Aktywuj** przycisku.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/servicenowactivate.png "skonfigurować logowanie jednokrotne")
  
13. Do konfigurowania **ServiceNow** ręcznie, wykonaj następujące czynności:

    * Zaloguj się jako administrator aplikacji ServiceNow.

    * W okienku nawigacji po lewej stronie kliknij pozycję **dostawców tożsamości**.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_07.png "skonfigurować logowanie jednokrotne")

    * Na **dostawców tożsamości** okno dialogowe, kliknij przycisk **New**.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694977.png "skonfigurować logowanie jednokrotne")

    * Na **dostawców tożsamości** okno dialogowe, kliknij przycisk **SAML**.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694978.png "skonfigurować logowanie jednokrotne")

    * Na **Importuj metadane dostawcy tożsamości** okna podręcznego, wykonaj następujące czynności:

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/idp.png "skonfigurować logowanie jednokrotne")

        * Wprowadź **adres Url metadanych Federacji aplikacji** skopiowanej w witrynie Azure portal.

        * Kliknij przycisk **Importuj**.

    * Odczytuje adres URL metadanych dostawcy tożsamości i wypełni wszystkie informacje dotyczące pól.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694982.png "skonfigurować logowanie jednokrotne")

        * W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (na przykład **Microsoft Azure federacyjnego logowania jednokrotnego**).

        * Usuń wypełniane **SingleLogoutRequest dostawcy tożsamości** wartość z pola tekstowego.

        * Kopiuj **strony głównej usługi ServiceNow** wartość, wklej ją w **adres URL logowania** polu tekstowym w **usługi ServiceNow, domena i adresy URL** sekcji w witrynie Azure portal.

            > [!NOTE]
            > Strona główna wystąpienia usługi ServiceNow to składa się z Twojego **adres URL dzierżawy usługi ServiceNow** i **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

        * Kopiuj **identyfikator jednostki / wystawcy** wartość, wklej ją w **identyfikator** polu tekstowym w **usługi ServiceNow, domena i adresy URL** sekcji w witrynie Azure portal.

        * Upewnij się, że **zasad NameID** ustawiono `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` wartość.

        * Kliknij przycisk **zaawansowane**. W **pole użytkownika** polu tekstowym wpisz **e-mail** lub **nazwa_użytkownika**, w zależności od pole, które jest używany do jednoznacznego identyfikowania użytkowników w danym wdrożeniu usługi ServiceNow.

            > [!NOTE]
            > Można skonfigurować usługi Azure AD, aby emitować identyfikator użytkownika usługi Azure AD (główna nazwa użytkownika) lub adres e-mail jako unikatowy identyfikator w tokenie SAML, przechodząc do **ServiceNow > atrybuty > logowanie jednokrotne** części witryny Azure portal żądane pola mapowania i **nameidentifier** atrybutu. Wartość przechowywana wybranego atrybutu w usłudze Azure AD (na przykład główna nazwa użytkownika) musi być zgodna wartość przechowywaną w ServiceNow wprowadzony pola (na przykład nazwa_użytkownika)

        * Kliknij pozycję **Testuj połączenie** w prawym górnym rogu strony.

        * Po kliknięciu **Testuj połączenie**, otrzymasz wyskakujące, w którym należy wprowadzić poświadczenia i jest wyświetlany poniżej strony z wynikami. **Wyniki testów wylogowania logowania jednokrotnego** oczekuje się, błąd można zignorować błąd i kliknij przycisk **Aktywuj** przycisku.

          ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/servicenowactivate.png "skonfigurować logowanie jednokrotne")

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurowanie usługi Azure AD logowanie jednokrotne dla usługi ServiceNow Express

1. W witrynie Azure portal na **ServiceNow** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Kliknij przycisk **pojedynczej zmiany trybu logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_general_300.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_general_301.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_general_302.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. W **adres URL logowania** polu tekstowym wpisz wartość, przy użyciu następującego wzorca: `https://<instance-name>.service-now.com/navpage.do`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywisty adres URL logowania i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta usługi ServiceNow](https://www.servicenow.com/support/contact-support.html) do uzyskania tych wartości.

6. Na **certyfikat podpisywania SAML** sekcji, kliknij na **Pobierz** można pobrać **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_certificates.png)

7. Jednym kliknięciem Konfigurowanie usługi zostanie podana dla usługi ServiceNow oznacza to, aby platforma Azure AD automatycznie skonfigurować usługi ServiceNow dla uwierzytelniania opartego na SAML. Aby włączyć tę usługę, przejdź do **Konfigurowanie usługi ServiceNow** kliknij **wyświetlić instrukcje krok po kroku** otworzyć Konfigurowanie logowania jednokrotnego okno.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

8. Wprowadź nazwę wystąpienia usługi ServiceNow, nazwa użytkownika administratora i hasło administratora w **Konfigurowanie logowania jednokrotnego** formularza, a następnie kliknij przycisk **Konfiguruj teraz**. Należy zauważyć, że podana nazwa użytkownika administratora musi mieć **security_admin** rola przypisana w usługi ServiceNow, w tym do pracy. W przeciwnym razie ręcznie skonfigurować usługi ServiceNow, używać usługi Azure AD jako dostawcy tożsamości SAML, kliknij pozycję **ręcznie skonfigurować logowanie jednokrotne** i skopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z sekcji krótki.

    ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/configure.png "skonfigurować adres URL aplikacji")

9. Zaloguj się jako administrator aplikacji Express usługi ServiceNow.

10. W okienku nawigacji po lewej stronie kliknij pozycję **logowania jednokrotnego**.

    ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/ic7694980ex.png "skonfigurować adres URL aplikacji")

11. Na **logowania jednokrotnego** okno dialogowe, kliknij ikonę konfiguracji w prawym górnym rogu, a następnie ustaw następujące właściwości:

    ![Skonfiguruj adres URL aplikacji](./media/servicenow-tutorial/ic7694981ex.png "skonfigurować adres URL aplikacji")

    a. Przełącz **umożliwiają wielu dostawcy logowania jednokrotnego** po prawej stronie.

    b. Przełącz **debugowania Włącz rejestrowanie dla wielu dostawcy integracja logowania jednokrotnego** po prawej stronie.

    c. W **tabeli pola na użytkownika...**  polu tekstowym wpisz **nazwa_użytkownika**.

12. Na **logowania jednokrotnego** okno dialogowe, kliknij przycisk **Dodaj nowy certyfikat**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694973ex.png "skonfigurować logowanie jednokrotne")

13. Na **certyfikaty X.509** okno dialogowe, należy wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694975.png "skonfigurować logowanie jednokrotne")

    a. W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (na przykład: **TestSAML2.0**).

    b. Wybierz **Active**.

    c. Jako **Format**, wybierz opcję **PEM**.

    d. Jako **typu**, wybierz opcję **zaufania Cert Store**.

    e. Otwórz swój certyfikat zakodowany w formacie Base64, pobrany z witryny Azure portal w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu PEM** pola tekstowego.

    f. Kliknij przycisk **aktualizacji**

14. Na **logowania jednokrotnego** okno dialogowe, kliknij przycisk **Dodaj nowy dostawca tożsamości**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694976ex.png "skonfigurować logowanie jednokrotne")

15. Na **Dodaj nowego dostawcę tożsamości** okna dialogowego, w obszarze **Konfigurowanie dostawcy tożsamości**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694982ex.png "skonfigurować logowanie jednokrotne")

    a. W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (na przykład: **SAML 2.0**).

    b. W **adres URL dostawcy tożsamości** pole, Wklej wartość **identyfikator dostawcy tożsamości**, które zostały skopiowane z witryny Azure portal.

    c. W **AuthnRequest dostawcy tożsamości** pole, Wklej wartość **adresu URL żądania uwierzytelniania**, które zostały skopiowane z witryny Azure portal.

    d. W **SingleLogoutRequest dostawcy tożsamości** pole, Wklej wartość **adresu URL usługi wylogowania jednokrotnego**, które zostały skopiowane z witryny Azure portal

    e. Jako **certyfikatu dostawcy tożsamości**, wybranie certyfikatu utworzonego w poprzednim kroku.

16. Kliknij przycisk **Zaawansowane ustawienia**, a następnie w obszarze **dodatkowe właściwości dostawcy tożsamości**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694983ex.png "skonfigurować logowanie jednokrotne")

    a. W **Protokół powiązania dla dostawcy tożsamości SingleLogoutRequest** polu tekstowym wpisz **urn: oasis: nazwy: tc: SAML:2.0:bindings:HTTP-przekierowania**.

    b. W **zasad NameID** polu tekstowym wpisz **urn: oasis: nazwy: tc: SAML:1.1:nameid — format: nieokreślony**.

    c. W **metoda AuthnContextClassRef**, typ `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Usuń zaznaczenie opcji **tworzenie AuthnContextClass**.

17. W obszarze **dodatkowe właściwości dostawcy usług**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694984ex.png "skonfigurować logowanie jednokrotne")

    a. W **strony głównej usługi ServiceNow** pole tekstowe, wpisz adres URL strony głównej wystąpienia usługi ServiceNow.

    > [!NOTE]
    > Strona główna wystąpienia usługi ServiceNow to składa się z Twojego **adres URL dzierżawy usługi ServiceNow** i **/navpage.do** (na przykład: `https://fabrikam.service-now.com/navpage.do`).

    b. W **identyfikator jednostki / wystawca** pole tekstowe, wpisz adres URL dzierżawy usługi ServiceNow.

    c. W **identyfikator URI odbiorców** pole tekstowe, wpisz adres URL dzierżawy usługi ServiceNow.

    d. W **pochylanie zegara** polu tekstowym wpisz **60**.

    e. W **pole użytkownika** polu tekstowym wpisz **e-mail** lub **nazwa_użytkownika**, w zależności od pole, które jest używany do jednoznacznego identyfikowania użytkowników w danym wdrożeniu usługi ServiceNow.

    > [!NOTE]
    > Można skonfigurować usługi Azure AD, aby emitować identyfikator użytkownika usługi Azure AD (główna nazwa użytkownika) lub adres e-mail jako unikatowy identyfikator w tokenie SAML, przechodząc do **ServiceNow > atrybuty > logowanie jednokrotne** części witryny Azure portal żądane pola mapowania i **nameidentifier** atrybutu. Wartość przechowywana wybranego atrybutu w usłudze Azure AD (na przykład główna nazwa użytkownika) musi być zgodna wartość przechowywaną w ServiceNow wprowadzony pola (na przykład nazwa_użytkownika)

    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/servicenow-tutorial/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/servicenow-tutorial/create_aaduser_02.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="create-a-servicenow-test-user"></a>Tworzenie użytkownika testowego usługi ServiceNow

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w usługi ServiceNow. Usługi ServiceNow obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów można znaleźć [tutaj](servicenow-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

> [!NOTE]
> Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej klienta usługi ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do usługi ServiceNow.

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **ServiceNow**.

    ![Link usługi ServiceNow na liście aplikacji](./media/servicenow-tutorial/tutorial_servicenow_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi ServiceNow w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji usługi ServiceNow.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
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
