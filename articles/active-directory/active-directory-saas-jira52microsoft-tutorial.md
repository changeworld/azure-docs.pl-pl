---
title: 'Samouczek: Integracji Azure Active Directory z usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: 20d08fa6aac5a55480d2c11eb6e4f746ff863f78
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-microsoft-azure-active-directory-single-sign-on-for-jira-52"></a>Samouczek: Integracji Azure Active Directory z usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2

Z tego samouczka dowiesz się sposobu integracji usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 w usłudze Azure Active Directory (Azure AD).

Integrowanie usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla 5.2 JIRA (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="description"></a>Opis

Korzystać z serwera Atlassian JIRA konta Microsoft Azure Active Directory, aby włączyć logowanie jednokrotne. W ten sposób wszyscy użytkownicy organizacji można używać poświadczeń usługi Azure AD do logowania do aplikacji JIRA. Ten dodatek plug-in używa SAML 2.0 dla Federacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Podstawowe JIRA i 5.2 oprogramowania należy zainstalowany i skonfigurowany na Windows 64-bitowej wersji
- Serwer JIRA jest obsługujące protokół HTTPS
- Należy pamiętać, że obsługiwane wersje dla wtyczki JIRA są wymienione w poniższej sekcji.
- JIRA serwer jest dostępny w Internecie, szczególnie do strony logowania usługi AD platformy Azure do uwierzytelniania i powinien otrzymywać token z usługi Azure AD
- Poświadczenia administratora są konfigurowane w JIRA
- WebSudo jest wyłączona w JIRA
- Użytkownika testowego utworzone w JIRA aplikacji serwera

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego programu JIRA. Przetestowanie integracji w rozwoju lub środowisko przejściowe aplikacji, a następnie użyj środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

**Obsługiwane wersje:**

*   Podstawowe JIRA i oprogramowania: 5.2
*   JIRA obsługuje również 6.0 i 7.2.0. Aby uzyskać więcej informacji, kliknij przycisk [Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA](./active-directory-saas-jiramicrosoft-tutorial.md)

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-microsoft-azure-active-directory-single-sign-on-for-jira-52-from-the-gallery"></a>Dodawanie usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 z galerii
Aby skonfigurować integrację usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 do usługi Azure AD, należy dodać Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2**, wybierz pozycję **Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2** z panelu wyników kliknięcie  **Dodaj** przycisk, aby dodać aplikację.

    ![Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 na liście wyników](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla 5.2 JIRA w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla użytkownika testowego JIRA 5.2](#create-a-singlesign-onforjira5.2-test-user)**  — mają odpowiednika Simona Britta w Microsoft Azure Active Directory rejestracji jednokrotnej dla 5.2 JIRA, połączonej z usługi Azure AD reprezentację użytkownika .
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w sieci Microsoft Azure Active Directory logowanie jednokrotne dla aplikacji JIRA 5.2.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2, wykonaj następujące czynności:**

1. W portalu Azure na **Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_samlbase.png)

3. Na **Microsoft Azure Active Directory rejestracji jednokrotnej dla domeny 5.2 JIRA i adresy URL** sekcji, wykonaj następujące czynności:

    ![Microsoft Azure Active Directory rejestracji jednokrotnej JIRA 5.2 domeny i adres URL pojedynczego logowania jednokrotnego informacji](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<domain:port>/`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Port jest opcjonalny w przypadku, gdy jest nazwane adres URL. Te wartości są odbierane podczas konfigurowania Jira dodatek, który znajduje się w dalszej części tego samouczka.

4. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-msaadssojira5.2-tutorial/tutorial_metadataurl.png)
     
5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_400.png)

6. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do Twojego wystąpienia JIRA.

7. Umieść kursor na koło zębate, a następnie kliknij przycisk **dodatki**.
    
    ![Konfigurowanie rejestracji jednokrotnej](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon1.png)

8. W sekcji Karta dodatki, kliknij przycisk **Zarządzanie dodatkami**.

    ![Konfigurowanie rejestracji jednokrotnej](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon7.png)

9. Pobierz dodatek z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56521). Ręcznie przekazać wtyczki, używając Microsoft **przekazać dodatek** menu. Pobieranie wtyczki nie jest objęta [Umowa serwisowa usługi Microsoft](https://www.microsoft.com/en-us/servicesagreement/).

    ![Konfigurowanie rejestracji jednokrotnej](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon12.png)

10. Po zainstalowaniu dodatku plug-in pojawia się w **użytkownik zainstalował** sekcji dodatków. Kliknij przycisk **Konfiguruj** do skonfigurowania nowej wtyczki.

    ![Konfigurowanie rejestracji jednokrotnej](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon13.png)

11. Wykonaj następujące kroki na stronie konfiguracji:

    ![Konfigurowanie rejestracji jednokrotnej](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon52.png)

    > [!TIP]
    > Upewnij się, że istnieje tylko jeden certyfikat mapowany aplikacji tak, aby nie było błędu rozpoznawania metadanych. Jeśli dostępnych jest wiele certyfikatów na rozpoznawanie metadanych, administrator pobiera wystąpił błąd.

    a. W **adres URL metadanych** pole tekstowe, Wklej **adres Url metadanych Federacji aplikacji** wartość, która została skopiowana z portalu Azure i kliknij przycisk **rozwiązać** przycisku. Adres URL metadanych IdP odczytuje i wypełnienie wszystkich pól informacji.

    b. Kopiuj **identyfikator, adres URL odpowiedzi i zaloguj się na adres URL** wartości i wklej je w **identyfikator, adres URL odpowiedzi i zaloguj się na adres URL** odpowiednio do pól tekstowych **Microsoft Azure Active Directory rejestracji jednokrotnej JIRA 5.2 domeny i adresów URL** sekcji z portalu Azure.

    c. W **nazwa przycisku logowania** wpisz nazwę przycisku przez organizację nowych użytkowników na ekranie logowania.

    d. W **lokalizacje identyfikator użytkownika SAML** wybierz opcję **identyfikator użytkownika jest w elemencie NameIdentifier instrukcji podmiotu** lub **identyfikator użytkownika jest w elemencie atrybutu**.  Ten identyfikator ma być JIRA identyfikator użytkownika. Jeśli identyfikator użytkownika nie jest zgodny, następnie system uniemożliwi użytkownikom logować się.

    > [!Note]
    > Domyślna lokalizacja SAML użytkownika identyfikator to identyfikator nazwy. Można to zmienić opcję atrybutu i wprowadź odpowiednią nazwę. 
    
    e. W przypadku wybrania **identyfikator użytkownika jest w elemencie atrybutu** opcji, a następnie w **nazwa atrybutu** pole tekstowe wpisz nazwę atrybutu, gdy oczekiwano identyfikatora użytkownika. 

    f. Jeśli korzystasz z domeny federacyjnej (na przykład usług AD FS itp.) z usługą Azure AD, należy kliknąć opcję **Włączanie odnajdowania obszaru macierzystego** opcji i skonfigurować **nazwy domeny**.
    
    g. W **nazwy domeny** wpisz nazwę domeny, w tym miejscu w przypadku logowania za pomocą usług AD FS.

    h. Sprawdź **włączyć pojedynczego Wyloguj** chcesz wylogować się z usługi Azure AD, gdy użytkownik zaloguje z JIRA. 

    i. Kliknij przycisk **zapisać** przycisk, aby zapisać ustawienia.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat instalacji i rozwiązywania problemów można znaleźć [MS JIRA logowania jednokrotnego łącznika Admin przewodnik](ms-confluence-jira-plugin-adminguide.md) oraz istnieją również [— często zadawane pytania](ms-confluence-jira-plugin-faq.md) dla pomocy użytkownika

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-microsoft-azure-active-directory-single-sign-on-for-jira-52-test-user"></a>Tworzenie usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla użytkownika testowego JIRA 5.2

Aby włączyć użytkowników usługi Azure AD zalogować się do serwera lokalnego JIRA, muszą mieć przydzielone do usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2. Dla usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do serwera lokalnego JIRA jako administrator.

2. Umieść kursor na koło zębate, a następnie kliknij przycisk **Zarządzanie użytkownikami**.

    ![Dodawanie pracownika](.\media\active-directory-saas-msaadssojira5.2-tutorial\user1.png) 

3. Nastąpi przekierowanie do strony dostępu administratora, aby wprowadzić **hasło** i kliknij przycisk **Potwierdź** przycisku.

    ![Dodawanie pracownika](.\media\active-directory-saas-msaadssojira5.2-tutorial\user2.png) 

4. W obszarze **Zarządzanie użytkownikami** sekcji, kliknij pozycję **Utwórz użytkownika**.

    ![Dodawanie pracownika](.\media\active-directory-saas-msaadssojira5.2-tutorial\user3.png) 

5. Na **"Tworzenie nowego użytkownika"** okna dialogowego strony, należy wykonać następujące czynności:

    ![Dodawanie pracownika](.\media\active-directory-saas-msaadssojira5.2-tutorial\user4.png) 

    a. W **adres E-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak Brittasimon@contoso.com.

    b. W **imię i nazwisko** pole tekstowe, pełna nazwa typu użytkownika, takich jak Simona Britta.

    c. W **Username** tekstowym, wpisz adres e-mail użytkownika, takich jak Brittasimon@contoso.com.

    d. W **hasło** tekstowym, wpisz hasło użytkownika.

    e. Kliknij przycisk **tworzenia użytkownika**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do usługi Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2**.

    ![Microsoft Azure Active Directory rejestracji jednokrotnej dla łącza JIRA 5.2 na liście aplikacji](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Test rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu Microsoft Azure Active Directory rejestracji jednokrotnej dla JIRA 5.2 kafelka w panelu dostępu można powinien pobrać automatycznie zalogowane do programu Microsoft Azure Active Directory logowania jednokrotnego dla aplikacji JIRA 5.2.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_01.png
[2]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_02.png
[3]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_03.png
[4]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_04.png

[100]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_100.png

[200]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_200.png
[201]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_201.png
[202]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_202.png
[203]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_203.png
