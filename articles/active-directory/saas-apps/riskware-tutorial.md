---
title: 'Samouczek: Integracji Azure Active Directory z Riskware | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: c284c6c4f642759fd31bf723007bd02dcfa91ae7
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941182"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Samouczek: Integracji Azure Active Directory z Riskware

Z tego samouczka dowiesz się integrowanie Riskware z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Riskware zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Riskware.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Riskware (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Riskware, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Riskware logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Riskware z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-riskware-from-the-gallery"></a>Dodawanie Riskware z galerii
Aby skonfigurować integrację usługi Azure AD Riskware, należy dodać Riskware z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Riskware z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Riskware**, wybierz pozycję **Riskware** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Riskware na liście wyników](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Riskware w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Riskware jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Riskware musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Riskware, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Riskware](#create-a-riskware-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Riskware połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Riskware.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Riskware, wykonaj następujące czynności:**

1. W portalu Azure na **Riskware** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

3. Na **Riskware domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny Riskware pojedynczy informacje logowania jednokrotnego](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. W **Zaloguj się na adres URL** tekstowym, wpisz adres URL, używając następującego wzorca:
    | Środowisko| Wzorzec URL|
    |--|--|
    | Testowanie akceptacyjne przez użytkowników|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PRODUKCYJNEGO| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` | 
    |||

    b. W **identyfikator jednostki** tekstowym, wpisz adres URL:
    | Środowisko| Wzorzec URL|
    |--|--|
    | Testowanie akceptacyjne przez użytkowników| `https://riskcloud.net/uat` |
    | PRODUKCYJNEGO| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` | 
    |||

    > [!NOTE] 
    > Zaloguj się na wartość adresu URL nie jest prawdziwe. Zaktualizuj tę wartość z adresem URL logowania rzeczywistych. Skontaktuj się z [zespołem pomocy technicznej klienta Riskware](mailto:support@pansoftware.com.au) można uzyskać wartość.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/riskware-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Riskware** , kliknij przycisk **skonfigurować Riskware** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja Riskware](./media/riskware-tutorial/tutorial_riskware_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Riskware.

8. W górnym rogu kliknij **konserwacji** aby otworzyć stronę konserwacji. 

    ![Obsługa konfiguracji Riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

9. Na stronie konserwacji, kliknij przycisk **uwierzytelniania**.

    ![Authen Riskware konfiguracji](./media/riskware-tutorial/tutorial_riskware_authen.png)

10. W **konfiguracji uwierzytelniania** wykonaj następujące czynności:

    ![Authenconfig Riskware konfiguracji](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Wybierz **typu** jako **SAML** do uwierzytelniania.

    b. W **kod** tekstowym, wpisz swój kod, takich jak AZURE_UAT.

    c. W **opis** tekstowym, wpisz opis jak konfiguracji AZURE dla logowania jednokrotnego.

    d. W **Zaloguj się na stronie** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    e. W **Wyloguj strony** pole tekstowe, Wklej wartość **Sign-Out URL**, które zostały skopiowane z portalu Azure.

    f. W **pola formularza Post** tekstowym, wpisz nazwę pola w odpowiedź Post, która zawiera SAML, takich jak SamlResponse

    g. W **nazwa tagu XML tożsamości** pole tekstowe, typ atrybutu, który zawiera unikatowy identyfikator w odpowiedzi SAML, takich jak NameID.

    h. Otwórz pobrany **Xml metadanych** z portalu Azure w programie Notatnik, skopiuj certyfikat z pliku metadanych i wklej ją do **certyfikatu** pole tekstowe
    
    i. W **adres URL klienta** pole tekstowe, Wklej wartość **adres URL odpowiedzi**, którego można uzyskać z zespołem pomocy technicznej.

    j. W **wystawcy** pole tekstowe, Wklej wartość **identyfikator**, który można pobrać z zespołem pomocy technicznej.

    > [!Note]
    > Skontaktuj się z [zespołem pomocy technicznej klienta Riskware](mailto:support@pansoftware.com.au) uzyskać te wartości

    k. Wybierz **POST użyj** do przekazania żądania SAML jako parametr post.

    l. Wybierz **żądania SAML użyj** do przekazania logowania jednokrotnego Zezwalaj na żądania SAML dla SP zainicjowane.

    m. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/riskware-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/riskware-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/riskware-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/riskware-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-riskware-test-user"></a>Tworzenie użytkownika testowego Riskware

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Riskware, musi być przygotowana do Riskware. W Riskware Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się jako Administrator zabezpieczeń Riskware.

2. W górnym rogu kliknij **konserwacji** aby otworzyć stronę konserwacji. 

    ![Przechowuje Riskware konfiguracji](./media/riskware-tutorial/tutorial_riskware_maintain.png)

3. Na stronie konserwacji, kliknij przycisk **osób**.

    ![Osoby Riskware konfiguracji](./media/riskware-tutorial/tutorial_riskware_people.png)

4. Na **szczegóły** karcie, wykonaj następujące czynności:
    
    ![Szczegóły konfiguracji Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Wybierz **typ osoby** , takich jak pracownika.

    b. W **imię** pole tekstowe, wprowadź imię użytkownika, takich jak **Britta**.

    c. W **nazwisko** pole tekstowe, wprowadź nazwisko użytkownika, takich jak **Simona**.

5. Na **zabezpieczeń** karcie, wykonaj następujące czynności:    

    ![Riskware konfiguracji zabezpieczeń](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. W obszarze **uwierzytelniania** zaznacz **uwierzytelniania** tryb, w którym ma Instalatora, takich jak konfiguracji AZURE dla logowania jednokrotnego.

    b. W obszarze **szczegóły logowania** sekcji w **identyfikator użytkownika** pole tekstowe, wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    c. W **hasło** pole tekstowe, wprowadź hasło użytkownika.

6. Na **organizacji** karcie, wykonaj następujące czynności:

    ![Org Riskware konfiguracji](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. W obszarze **organizacji** wybierz organizacji jako **Level1** organizacji.
    
    b. W obszarze **pracy głównej osoby** sekcji w **lokalizacji** tekstowym, wpisz lokalizację.

    c. W obszarze **pracownika** zaznacz **stanu** dorywczy, takich jak.

7. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Riskware.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Riskware, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Riskware**.

    ![Łącze Riskware na liście aplikacji](./media/riskware-tutorial/tutorial_riskware_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Riskware w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Riskware.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

