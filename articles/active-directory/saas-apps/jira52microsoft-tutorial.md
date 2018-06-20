---
title: 'Samouczek: Integracji Azure Active Directory z logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 7ea727d5fb004ca224407a8b1c93a6f11448b5e7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231359"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Samouczek: Integracji Azure Active Directory z logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2)

Z tego samouczka dowiesz się integrowanie logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2).
- Umożliwia użytkownikom automatycznie pobrać zalogowane do logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>Opis

Korzystać z serwera Atlassian JIRA konta Microsoft Azure Active Directory, aby włączyć logowanie jednokrotne. W ten sposób wszyscy użytkownicy organizacji można używać poświadczeń usługi Azure AD do logowania do aplikacji JIRA. Ten dodatek plug-in używa SAML 2.0 dla Federacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2), potrzebne są następujące elementy:

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
*   JIRA obsługuje również 6.0 i 7.8. Aby uzyskać więcej informacji, kliknij przycisk [logowania jednokrotnego SAML JIRA przez firmę Microsoft](jiramicrosoft-tutorial.md)

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Dodawanie logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) z galerii
Aby skonfigurować integrację logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) do usługi Azure AD, należy dodać logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2)**, wybierz pozycję **logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2)** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) na liście wyników](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2).

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Utwórz logowania jednokrotnego SAML JIRA przez użytkownika testowego programu Microsoft (wersji 5.2)](#create-a-jira-saml-sso-by-microsoft-v52-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i konfigurowanie rejestracji jednokrotnej w sieci logowania jednokrotnego SAML JIRA przez aplikację do wersji Microsoft (5.2).

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2), wykonaj następujące czynności:**

1. W portalu Azure na **logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_samlbase.png)

3. Na **logowania jednokrotnego SAML JIRA Domain firmy Microsoft i adresy URL** sekcji, wykonaj następujące czynności:

    ![Logowania jednokrotnego SAML JIRA przez Microsoft Domain i adresów URL jednym logowania jednokrotnego informacji](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<domain:port>/`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Port jest opcjonalny w przypadku, gdy jest nazwane adres URL. Te wartości są odbierane podczas konfigurowania Jira dodatek, który znajduje się w dalszej części tego samouczka.

4. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika.

    ![Konfigurowanie rejestracji jednokrotnej](./media/jira52microsoft-tutorial/tutorial_metadataurl.png)

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/jira52microsoft-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do Twojego wystąpienia JIRA.

7. Umieść kursor na koło zębate, a następnie kliknij przycisk **dodatki**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/jira52microsoft-tutorial/addon1.png)

8. W sekcji Karta dodatki, kliknij przycisk **Zarządzanie dodatkami**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/jira52microsoft-tutorial/addon7.png)

9. Pobierz dodatek z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56521). Ręcznie przekazać wtyczki, używając Microsoft **przekazać dodatek** menu. Pobieranie wtyczki nie jest objęta [Umowa serwisowa usługi Microsoft](https://www.microsoft.com/en-us/servicesagreement/).

    ![Konfigurowanie rejestracji jednokrotnej](./media/jira52microsoft-tutorial/addon12.png)

10. Po zainstalowaniu dodatku plug-in pojawia się w **użytkownik zainstalował** sekcji dodatków. Kliknij przycisk **Konfiguruj** do skonfigurowania nowej wtyczki.

    ![Konfigurowanie rejestracji jednokrotnej](./media/jira52microsoft-tutorial/addon13.png)

11. Wykonaj następujące kroki na stronie konfiguracji:

    ![Konfigurowanie rejestracji jednokrotnej](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Upewnij się, że istnieje tylko jeden certyfikat mapowany aplikacji tak, aby nie było błędu rozpoznawania metadanych. Jeśli dostępnych jest wiele certyfikatów na rozpoznawanie metadanych, administrator pobiera wystąpił błąd.

    a. W **adres URL metadanych** pole tekstowe, Wklej **adres Url metadanych Federacji aplikacji** wartość, która została skopiowana z portalu Azure i kliknij przycisk **rozwiązać** przycisku. Adres URL metadanych IdP odczytuje i wypełnienie wszystkich pól informacji.

    b. Kopiuj **identyfikator, adres URL odpowiedzi i zaloguj się na adres URL** wartości i wklej je w **identyfikator, adres URL odpowiedzi i zaloguj się na adres URL** odpowiednio do pól tekstowych **logowania jednokrotnego SAML JIRA domeny firmy Microsoft (wersji 5.2) i adresy URL**  sekcji z portalu Azure.

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
    > Aby uzyskać więcej informacji na temat instalacji i rozwiązywania problemów można znaleźć [MS JIRA logowania jednokrotnego łącznika Admin przewodnik](../ms-confluence-jira-plugin-adminguide.md) oraz istnieją również [— często zadawane pytania](../ms-confluence-jira-plugin-faq.md) dla pomocy użytkownika

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/jira52microsoft-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/jira52microsoft-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/jira52microsoft-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/jira52microsoft-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-jira-saml-sso-by-microsoft-v52-test-user"></a>Utwórz logowania jednokrotnego SAML JIRA przez użytkownika testowego programu Microsoft (wersji 5.2)

Aby włączyć użytkowników usługi Azure AD zalogować się do serwera lokalnego JIRA, muszą mieć przydzielone do JIRA na serwerze lokalnym.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do serwera lokalnego JIRA jako administrator.

2. Umieść kursor na koło zębate, a następnie kliknij przycisk **Zarządzanie użytkownikami**.

    ![Dodawanie pracownika](./media/jira52microsoft-tutorial/user1.png)

3. Nastąpi przekierowanie do strony dostępu administratora, aby wprowadzić **hasło** i kliknij przycisk **Potwierdź** przycisku.

    ![Dodawanie pracownika](./media/jira52microsoft-tutorial/user2.png)

4. W obszarze **Zarządzanie użytkownikami** sekcji, kliknij pozycję **Utwórz użytkownika**.

    ![Dodawanie pracownika](./media/jira52microsoft-tutorial/user3.png) 

5. Na **"Tworzenie nowego użytkownika"** okna dialogowego strony, należy wykonać następujące czynności:

    ![Dodawanie pracownika](./media/jira52microsoft-tutorial/user4.png)

    a. W **adres E-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak Brittasimon@contoso.com.

    b. W **imię i nazwisko** pole tekstowe, pełna nazwa typu użytkownika, takich jak Simona Britta.

    c. W **Username** tekstowym, wpisz adres e-mail użytkownika, takich jak Brittasimon@contoso.com.

    d. W **hasło** tekstowym, wpisz hasło użytkownika.

    e. Kliknij przycisk **tworzenia użytkownika**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2).

![Przypisanie roli użytkownika][200]

**Aby przypisać Simona Britta do logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2), wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2)**.

    ![Logowania jednokrotnego SAML JIRA za pośrednictwem łącza na liście aplikacji firmy Microsoft (wersji 5.2)](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu logowania jednokrotnego SAML JIRA przez Kafelek firmy Microsoft (wersji 5.2) w panelu dostępu należy powinien pobrać automatycznie zalogowane do użytkownika logowania jednokrotnego SAML JIRA przez aplikację do wersji Microsoft (5.2).
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/msaadssojira5.2-tutorial/tutorial_general_01.png
[2]: ./media/msaadssojira5.2-tutorial/tutorial_general_02.png
[3]: ./media/msaadssojira5.2-tutorial/tutorial_general_03.png
[4]: ./media/msaadssojira5.2-tutorial/tutorial_general_04.png

[100]: ./media/msaadssojira5.2-tutorial/tutorial_general_100.png

[200]: ./media/msaadssojira5.2-tutorial/tutorial_general_200.png
[201]: ./media/msaadssojira5.2-tutorial/tutorial_general_201.png
[202]: ./media/msaadssojira5.2-tutorial/tutorial_general_202.png
[203]: ./media/msaadssojira5.2-tutorial/tutorial_general_203.png