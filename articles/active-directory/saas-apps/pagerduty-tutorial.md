---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu usługi PagerDuty | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 2ac5dee8fe9a27ffeed717e010cade522b9fefc0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046504"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu usługi PagerDuty

W tym samouczku dowiesz się, jak zintegrować usługi PagerDuty w usłudze Azure Active Directory (Azure AD).

Integrowanie usługi PagerDuty z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi PagerDuty
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do usługi PagerDuty (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu usługi PagerDuty, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Usługi PagerDuty logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi PagerDuty z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-pagerduty-from-the-gallery"></a>Dodawanie usługi PagerDuty z galerii
Aby skonfigurować integrację usługi PagerDuty w usłudze Azure AD, należy dodać usługi PagerDuty z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi PagerDuty z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **usługi PagerDuty**, wybierz opcję **PagerDuty** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowania jednokrotnego przy użyciu usługi PagerDuty w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze PagerDuty z użytkownikiem w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze PagerDuty musi zostać ustanowione.

W usłudze PagerDuty, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu usługi PagerDuty, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego usługi PagerDuty](#create-a-pagerduty-test-user)**  — aby odpowiednikiem Britta Simon w usłudze PagerDuty połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi PagerDuty.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu usługi PagerDuty, wykonaj następujące czynności:**

1. W witrynie Azure portal na **PagerDuty** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

3. Na **PagerDuty domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domen usługi PagerDuty pojedynczego logowania jednokrotnego informacji](./media/pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenant-name>.pagerduty.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta usługi PagerDuty](https://www.pagerduty.com/support/) do uzyskania tych wartości.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/pagerduty-tutorial/tutorial_pagerduty_certificate.png)

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/pagerduty-tutorial/tutorial_general_400.png)

6. Na **konfiguracji usługi PagerDuty** kliknij **skonfigurować usługi PagerDuty** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja usługi PagerDuty](./media/pagerduty-tutorial/tutorial_pagerduty_configure.png)

7. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Pagerduty, jako administrator.

8. W menu u góry kliknij **ustawienia konta**.

    ![Ustawienia konta](./media/pagerduty-tutorial/ic778535.png "ustawienia konta")

9. Kliknij przycisk **logowanie jednokrotne**.

    ![Logowanie jednokrotne](./media/pagerduty-tutorial/ic778536.png "logowanie jednokrotne")

10. Na **włączyć logowanie jednokrotne (SSO)** strony, wykonaj następujące czynności:

    ![Włącz logowanie jednokrotne](./media/pagerduty-tutorial/ic778537.png "włączyć logowanie jednokrotne")

    a. Otwórz swój certyfikat zakodowany base-64, pobranego z witryny Azure portal w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikat X.509** textbox
  
    b. W **adres URL logowania** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
  
    c. W **adres URL wylogowania** pola tekstowego, Wklej **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    d. Wybierz **Zezwalaj na nazwę użytkownika/hasło logowania**.

    e. Wybierz **wymaga DOKŁADNEGO porównania kontekstu uwierzytelniania** pola wyboru.

    f. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](./media/pagerduty-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "All users" linki](./media/pagerduty-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Przycisk Dodaj](./media/pagerduty-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/pagerduty-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-pagerduty-test-user"></a>Tworzenie użytkownika testowego usługi PagerDuty

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do usługi PagerDuty, musi być obsługiwana w usłudze PagerDuty.  
W przypadku usługi PagerDuty Inicjowanie obsługi administracyjnej jest zadanie ręczne.

>[!NOTE]
>Można użyć jakichkolwiek innych usługi Pagerduty użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez usługi Pagerduty do świadczenia usługi Azure Active Directory kont użytkowników.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Pagerduty** dzierżawy.

2. W menu u góry kliknij **użytkowników**.

3. Kliknij przycisk **dodawania użytkowników**.
   
    ![Dodawanie użytkowników](./media/pagerduty-tutorial/ic778539.png "Dodawanie użytkowników")

4.  Na **wezwać zespół** okno dialogowe, należy wykonać następujące czynności:
   
    ![Zaproś zespołowi](./media/pagerduty-tutorial/ic778540.png "wezwać zespół")

    a. Typ **pierwszą i ostatnią nazwę** użytkownika, takich jak **Britta Simon**. 
   
    b. Wprowadź **E-mail** adres użytkownika, takie jak **brittasimon@contoso.com**.
   
    c. Kliknij przycisk **Dodaj**, a następnie kliknij przycisk **Wyślij zaproszenia**.
   
    >[!NOTE]
    >Wszystkie dodani użytkownicy otrzymają zaproszenie do utworzenia konta usługi PagerDuty.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do usługi PagerDuty.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon usługi PagerDuty, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **PagerDuty**.

    ![Link usługi PagerDuty na liście aplikacji](./media/pagerduty-tutorial/tutorial_pagerduty_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi PagerDuty w Panelyou dostępu należy pobrać automatycznie zalogowanych do aplikacji usługi PagerDuty.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/pagerduty-tutorial/tutorial_general_203.png
