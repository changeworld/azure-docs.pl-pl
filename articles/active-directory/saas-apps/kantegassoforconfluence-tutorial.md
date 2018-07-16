---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Kantega, aby uzyskać Confluence | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Kantega Usługa rejestracji Jednokrotnej dla Confluence.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d0d99c14-a6ca-45f2-bb84-633126095e7a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 5468b278d16cbc0373cd268f45820fbc2a207370
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046791"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Kantega, aby uzyskać Confluence

W tym samouczku dowiesz się, jak zintegrować Kantega Usługa rejestracji Jednokrotnej dla Confluence za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie Kantega Usługa rejestracji Jednokrotnej dla Confluence z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego Kantega Confluence
- Użytkowników, aby automatycznie uzyskać zalogowanych do Kantega Usługa rejestracji Jednokrotnej dla Confluence (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Kantega, aby uzyskać Confluence, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Usługa rejestracji Jednokrotnej Kantega dla Confluence logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Kantega Usługa rejestracji Jednokrotnej dla Confluence z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Dodawanie Kantega Usługa rejestracji Jednokrotnej dla Confluence z galerii
Aby skonfigurować integrację Kantega Usługa rejestracji Jednokrotnej dla Confluence w usłudze Azure AD, należy dodać Kantega Usługa rejestracji Jednokrotnej dla Confluence z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Kantega Usługa rejestracji Jednokrotnej dla Confluence z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Kantega Usługa rejestracji Jednokrotnej dla Confluence**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_search.png)

5. W panelu wyników wybierz **Kantega Usługa rejestracji Jednokrotnej dla Confluence**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, dla Confluence, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Kantega Usługa rejestracji Jednokrotnej dla Confluence do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Kantega Usługa rejestracji Jednokrotnej dla Confluence musi zostać nawiązane.

W Kantega Usługa rejestracji Jednokrotnej dla Confluence, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, aby uzyskać Confluence, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie Kantega Usługa rejestracji Jednokrotnej dla użytkownika testowego Confluence](#creating-a-kantega-sso-for-confluence-test-user)**  — aby odpowiednikiem Britta Simon w Kantega Usługa rejestracji Jednokrotnej dla Confluence, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w Usługa rejestracji Jednokrotnej w sieci Kantega Confluence aplikacji.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, aby uzyskać Confluence, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Kantega Usługa rejestracji Jednokrotnej dla Confluence** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_samlbase.png)

3. W **tożsamości** zainicjować tryb, w **Kantega Usługa rejestracji Jednokrotnej Confluence domen i adresów URL** sekcji wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_url1.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. W **SP** inicjowane trybu wyboru **Pokaż zaawansowane ustawienia adresu URL** i wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_url2.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Te wartości są odbierane podczas konfigurowania wtyczki Confluence, które zostało wyjaśnione w dalszej części tego samouczka.

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/tutorial_general_400.png)
    
7. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **portalu administracyjnego Confluence** jako administrator.

8. Umieść kursor na koła zębatego, a następnie kliknij przycisk **dodatki**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon1.png)

9. W obszarze **ATLASSIAN MARKETPLACE** kliknij pozycję **Znajdź nowe dodatki**. 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon.png)

10. Wyszukiwanie **Kantega Usługa rejestracji Jednokrotnej dla protokołu Kerberos SAML Confluence** i kliknij przycisk **zainstalować** przycisk, aby zainstalować nowe wtyczki SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon2.png)

11. Rozpocznie się instalacja dodatku plug-in.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon3.png)

12. Po zakończeniu instalacji. Kliknij przycisk **Zamknij**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon33.png)

13. Kliknij pozycję **Zarządzaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon34.png)
    
14. Kliknij przycisk **Konfiguruj** do skonfigurowania nowej wtyczki.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon35.png)

15. Ten nowy dodatek plug-in można także znaleźć w obszarze **użytkowników i zabezpieczeń** kartę.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon36.png)
    
16. W **SAML** sekcji. Wybierz **usługi Azure Active Directory (Azure AD)** z **dostawcy tożsamości Dodaj** listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon4.png)

17. Wybierz poziom subskrypcji jako **podstawowe**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon5.png)     

18. Na **właściwości aplikacji** sekcję, wykonać następujące kroki: 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Kopiuj **identyfikator URI Identyfikatora aplikacji** wartości i używać go jako **identyfikator, adres URL odpowiedzi i adres URL logowania** na **Kantega Usługa rejestracji Jednokrotnej Confluence domen i adresów URL** sekcji w witrynie Azure portal.

    b. Kliknij przycisk **Dalej**.

19. Na **Importuj metadane** sekcję, wykonać następujące kroki: 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Wybierz **plik metadanych na moim komputerze**i przekazywania pliku metadanych, który został pobrany z witryny Azure portal.

    b. Kliknij przycisk **Dalej**.

20. Na **nazwę i logowania jednokrotnego lokalizację** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon8.png)
    
    a. Dodaj nazwę dostawcy tożsamości w **nazwę dostawcy tożsamości** (np. usługi Azure AD) w polu tekstowym.

    b. Kliknij przycisk **Dalej**.

21. Zweryfikuj certyfikat podpisywania, a następnie kliknij przycisk **dalej**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon9.png)

22. Na **kont użytkowników Confluence** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Wybierz **tworzenie użytkowników w katalogu wewnętrznego Confluence firmy, w razie potrzeby** i wprowadź nazwę odpowiedniej grupy użytkowników (może być wiele nie. grup rozdzielone przecinkami).

    b. Kliknij przycisk **Dalej**.

23. Kliknij przycisk **Zakończ**.   

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon11.png)

24. Na **znane domen dla usługi Azure AD** sekcję, wykonać następujące kroki: 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. Wybierz **znane domen** z lewego panelu strony.

    b. Wprowadź nazwę domeny w **znane domen** pola tekstowego.

    c. Kliknij pozycję **Zapisz**. 

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforconfluence-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforconfluence-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforconfluence-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforconfluence-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-kantega-sso-for-confluence-test-user"></a>Tworzenie Kantega Usługa rejestracji Jednokrotnej dla użytkownika testowego Confluence

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Confluence, musi być obsługiwana w Confluence. W przypadku logowania jednokrotnego Kantega dla Confluence Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej Kantega Usługa rejestracji Jednokrotnej dla witryny firmy Confluence jako administrator.

2. Umieść kursor na koła zębatego, a następnie kliknij przycisk **Zarządzanie użytkownikami**.

    ![Dodawanie pracownika](./media/kantegassoforconfluence-tutorial/user1.png) 

3. W sekcji Użytkownicy kliknij **Add Users** kartę. Na **"Dodaj użytkownika"** okna dialogowego strony, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/kantegassoforconfluence-tutorial/user2.png) 

    a. W **Username** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

    b. W **imię i nazwisko** polu tekstowym wpisz pełną nazwę użytkownika, takich jak Britta Simon.

    c. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

    d. W **hasło** pole tekstowe, wpisz hasło dla użytkownika.

    e. Kliknij przycisk **Potwierdź hasło** wprowadź ponownie hasło.
    
    f. Kliknij przycisk **Dodaj** przycisku.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Kantega Usługa rejestracji Jednokrotnej dla Confluence.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Kantega Usługa rejestracji Jednokrotnej dla Confluence, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Kantega Usługa rejestracji Jednokrotnej dla Confluence**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu przycisku logowania jednokrotnego Kantega Confluence kafelka w panelu dostępu użytkownik powinien uzyskać automatycznie zalogowanych do usługi logowania jednokrotnego Kantega Confluence aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforconfluence-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforconfluence-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforconfluence-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforconfluence-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforconfluence-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforconfluence-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforconfluence-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforconfluence-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforconfluence-tutorial/tutorial_general_203.png

