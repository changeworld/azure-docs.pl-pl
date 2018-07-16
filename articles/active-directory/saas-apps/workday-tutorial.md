---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Workday | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: jeedes
ms.openlocfilehash: 641f2e19791be48d014eeb61fb2848fc2a33a9cc
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042048"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Samouczek: Integracja usługi Azure Active Directory z produktem Workday

W tym samouczku dowiesz się, jak zintegrować pracy z usługą Azure Active Directory (Azure AD).

Integrowanie pracy z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do produktu Workday.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do produktu Workday (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Workday, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Dzień roboczy logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie produktu Workday za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-workday-from-the-gallery"></a>Dodawanie produktu Workday za pomocą galerii
Aby skonfigurować integrację z produktu Workday do usługi Azure AD, należy dodać produktu Workday za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać produktu Workday w galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Workday**, wybierz opcję **Workday** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Dzień roboczy na liście wyników](./media/workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą produktu Workday w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w produkcie Workday do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w produkcie Workday musi zostać nawiązane.

W produkcie Workday, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z aplikacją Workday, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Workday](#create-a-workday-test-user)**  — aby odpowiednikiem Britta Simon w produkcie Workday, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji produktu Workday.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z aplikacją Workday, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Workday** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/workday-tutorial/tutorial_workday_samlbase.png)

3. Na **Workday, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![WORKDAY domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/workday-tutorial/tutorial_workday_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. W **identyfikator** pole tekstowe, wpisz adres URL: `http://www.workday.com`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonaj następujące kroki:

    ![WORKDAY domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/workday-tutorial/tutorial_workday_url1.png)

    W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://impl.workday.com/<tenant>/login-saml.htmld`
     
    > [!NOTE] 
    > Te wartości nie są rzeczywiste. Rzeczywisty adres URL logowania i adres URL odpowiedzi, należy zaktualizować te wartości. Adres URL odpowiedzi muszą mieć poddomeny na przykład: www, wd2, wd3, wd3 impl, wd5, wd5 impl). Przy użyciu polecenia podobnego "*http://www.myworkday.com*" działa, ale "*http://myworkday.com*" nie ma. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) do uzyskania tych wartości.  

5. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/workday-tutorial/tutorial_workday_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/workday-tutorial/tutorial_general_400.png)
    
7. Na **konfiguracji produktu Workday** kliknij **Konfigurowanie produktu Workday** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja WORKDAY](./media/workday-tutorial/tutorial_workday_configure.png) 

8. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Workday.

9. W **pole wyszukiwania** wyszukiwania o nazwie **Edytuj Konfiguracja dzierżawy — zabezpieczenia** u góry po lewej stronie strony głównej.
   
    ![Edytuj zabezpieczenia dzierżawy](./media/workday-tutorial/IC782925.png "Edytuj zabezpieczenia dzierżawy")

10. W **adresy URL przekierowania** sekcji, wykonaj następujące czynności:
   
    ![Adresy URL przekierowania](./media/workday-tutorial/IC7829581.png "adresy URL przekierowania")
   
    a. Kliknij przycisk **Dodaj wiersz**.
   
    b. W **Przekierowywanie adresu URL logowania do** pole tekstowe i **adresu URL przekierowania Mobile** polu tekstowym wpisz **adres URL logowania** zostały wprowadzone na **Workday, domena i adresy URL** części witryny Azure portal.
   
    c. W witrynie Azure portal na **Konfigurowanie logowania jednokrotnego** okna, kopia **adres URL wylogowania**, a następnie wklej go do **adres URL przekierowania wylogowania** pola tekstowego.

    d. W **używane w środowiskach** pola tekstowego, wybierz nazwę środowiska.  

    >[!NOTE]
    > Wartość atrybutu środowisko jest powiązana wartość adres URL dzierżawy:  
    >-Czy nazwa domeny adres URL dzierżawy produktu Workday rozpoczyna się od impl na przykład: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*), **środowiska** atrybut należy skonfigurować do implementacji.  
    >— Jeśli nazwa domeny rozpoczyna się czymś innym, musisz skontaktować się z [zespołem pomocy technicznej klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) można pobrać dopasowywania **środowiska** wartość.

11. W **konfiguracji SAML** sekcji, wykonaj następujące czynności:
   
    ![Ustawienia języka SAML](./media/workday-tutorial/IC782926.png "ustawienia języka SAML")
   
    a.  Wybierz **włączyć uwierzytelnianie SAML**.
   
    b.  Kliknij przycisk **Dodaj wiersz**.

12. W **dostawcy tożsamości SAML** sekcji, wykonaj następujące czynności:
   
    ![Dostawcy tożsamości SAML](./media/workday-tutorial/IC7829271.png "SAML dostawcy tożsamości")
   
    a. W **nazwę dostawcy tożsamości** polu tekstowym wpisz nazwę dostawcy (na przykład: *SPInitiatedSSO*).
   
    b. W witrynie Azure portal na **Konfigurowanie logowania jednokrotnego** okna, kopia **identyfikator jednostki SAML** wartość, a następnie wklej go do **wystawcy** pola tekstowego.

    ![Dostawcy tożsamości SAML](./media/workday-tutorial/IC7829272.png "SAML dostawcy tożsamości")
   
    c. W witrynie Azure portal na **Konfigurowanie logowania jednokrotnego** okna, kopia **adres URL wylogowania** wartość, a następnie wklej go do **adres URL wylogowania odpowiedzi** pola tekstowego.

    d. W witrynie Azure portal na **Konfigurowanie logowania jednokrotnego** okna, kopia **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartość, a następnie wklej go do **adres URL usługi logowania jednokrotnego dostawcy tożsamości** pola tekstowego.

    e. W **używane w środowiskach** pola tekstowego, wybierz nazwę środowiska.

    f. Kliknij przycisk **certyfikatu klucza publicznego dostawcy tożsamości**, a następnie kliknij przycisk **Utwórz**. 

    ![Tworzenie](./media/workday-tutorial/IC782928.png "tworzenie")

    g. Kliknij przycisk **tworzenie x509 klucza publicznego**. 

    ![Tworzenie](./media/workday-tutorial/IC782929.png "tworzenie")

13. W **klucza publicznego widoku x509** sekcji, wykonaj następujące czynności: 
   
    ![Klucz publiczny widoku x509](./media/workday-tutorial/IC782930.png "widoku x509 klucza publicznego") 
   
    a. W **nazwa** polu tekstowym wpisz nazwę certyfikatu (na przykład: *PPE\_SP*).
   
    b. W **ważny od** polu tekstowym wpisz prawidłowe wartości atrybutu certyfikatu.
   
    c.  W **ważny do** polu tekstowym wpisz prawidłową wartość atrybutu certyfikatu.
   
    > [!NOTE]
    > Możesz uzyskać prawidłowe daty i prawidłowe, do tej pory z pobranego certyfikatu, klikając go dwukrotnie.  Daty są wyświetlane w obszarze **szczegóły** kartę.
    > 
    >
   
    d.  Otwórz swój certyfikat zakodowany base-64 w Notatniku, a następnie skopiuj zawartość go.
   
    e.  W **certyfikatu** pola tekstowego, Wklej zawartość Schowka.
   
    f.  Kliknij przycisk **OK**.

14. Wykonaj następujące czynności: 
   
    ![Konfiguracja logowania jednokrotnego](./media/workday-tutorial/WorkdaySSOConfiguratio.png "konfiguracji logowania jednokrotnego")
   
    a.  W **identyfikator dostawcy usługi** polu tekstowym wpisz **http://www.workday.com**.
   
    b. Wybierz **nie Deflate żądania zainicjowanego przez dostawcę usług uwierzytelniania**.
   
    c. Jako **podpisu żądania uwierzytelnienia**, wybierz opcję **SHA256**. 
   
    ![Metoda podpisu żądania uwierzytelniania](./media/workday-tutorial/WorkdaySSOConfiguration.png "metoda podpisu żądania uwierzytelniania") 
   
    d. Kliknij przycisk **OK**. 
   
    ![OK](./media/workday-tutorial/IC782933.png "OK")

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/workday-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/workday-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/workday-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/workday-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-workday-test-user"></a>Tworzenie użytkownika testowego Workday

W tej sekcji utworzysz użytkownika w usłudze WORKDAY aplikacja o nazwie Britta Simon. Praca z [zespołem pomocy technicznej klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) Aby dodać użytkowników na platformie produktu Workday. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do produktu Workday.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon w produkcie Workday, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Workday**.

    ![Link Workday, na liście aplikacji](./media/workday-tutorial/tutorial_workday_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka produktu Workday w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych z aplikacją Workday.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/workday-tutorial/tutorial_general_01.png
[2]: ./media/workday-tutorial/tutorial_general_02.png
[3]: ./media/workday-tutorial/tutorial_general_03.png
[4]: ./media/workday-tutorial/tutorial_general_04.png

[100]: ./media/workday-tutorial/tutorial_general_100.png

[200]: ./media/workday-tutorial/tutorial_general_200.png
[201]: ./media/workday-tutorial/tutorial_general_201.png
[202]: ./media/workday-tutorial/tutorial_general_202.png
[203]: ./media/workday-tutorial/tutorial_general_203.png
