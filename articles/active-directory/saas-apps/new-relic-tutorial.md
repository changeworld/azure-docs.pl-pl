---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą oprogramowania New Relic | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory, a narzędzie New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: f91c2f5a2f12e449e6f47475bd08c1613268658d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041895"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą oprogramowania New Relic

W tym samouczku dowiesz się, jak zintegrować usługi New Relic z usługą Azure Active Directory (Azure AD).

Integrowanie usługi New Relic z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi New Relic.
- Użytkowników, aby automatycznie uzyskać zalogowanych do usługi New Relic (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą oprogramowania New Relic, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Narzędzie New Relic logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi New Relic w galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-new-relic-from-the-gallery"></a>Dodawanie usługi New Relic w galerii
Aby skonfigurować integrację usługi New Relic do usługi Azure AD, należy dodać usługi New Relic w galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi New Relic w galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **usługi New Relic**, wybierz opcję **usługi New Relic** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Narzędzie New Relic na liście wyników](./media/new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą oprogramowania New Relic w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w dodatku New Relic do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w dodatku New Relic musi nawiązać.

New Relic, poprzez przypisywanie wartości **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą oprogramowania New Relic, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego usługi New Relic](#create-a-new-relic-test-user)**  — aby odpowiednikiem Britta Simon w dodatku New Relic jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji New Relic.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą oprogramowania New Relic, wykonaj następujące czynności:**

1. W witrynie Azure portal na **usługi New Relic** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/new-relic-tutorial/tutorial_new-relic_samlbase.png)

3. Na **adresy URL i nowej domeny Relic** sekcji, wykonaj następujące czynności:

    ![Nowe Relic domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/new-relic-tutorial/tutorial_new-relic_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` — Pamiętaj zastąpić identyfikatora konto usługi New Relic

    b. W **identyfikator** polu tekstowym wpisz wartość: `rpm.newrelic.com`

4. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/new-relic-tutorial/tutorial_new-relic_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/new-relic-tutorial/tutorial_general_400.png)

6. Na **nową konfigurację Relic** , kliknij przycisk **Konfigurowanie oprogramowania New Relic** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Nowa konfiguracja Relic](./media/new-relic-tutorial/tutorial_new-relic_configure.png) 

7. W oknie przeglądarki internetowej innej, zaloguj się na swoje **usługi New Relic** witryny firmy jako administrator.

8. W menu u góry kliknij **ustawienia konta**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797036.png "ustawienia konta")

9. Kliknij przycisk **zabezpieczeń i uwierzytelniania** kartę, a następnie kliknij przycisk **logowanie jednokrotne** kartę.
   
    ![Logowanie jednokrotne](./media/new-relic-tutorial/ic797037.png "logowanie jednokrotne")

10. Na stronie okna dialogowego SAML wykonaj następujące czynności:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
   a. Kliknij przycisk **wybierz plik** do przekazania pobranego certyfikatu usługi Azure Active Directory.

   b. W **adres URL logowania zdalnego** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.
   
   c. W **docelowa adres URL wylogowania** pole tekstowe, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

   d. Kliknij przycisk **Zapisz moje zmiany**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/new-relic-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/new-relic-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/new-relic-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/new-relic-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-new-relic-test-user"></a>Tworzenie użytkownika testowego usługi New Relic

Aby umożliwić użytkownikom usługi Azure Active Directory, zaloguj się do usługi New Relic, musi być obsługiwana w dodatku New Relic. W przypadku usługi New Relic Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto usługi New Relic, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **usługi New Relic** witryny firmy jako administrator.

2. W menu u góry kliknij **ustawienia konta**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797040.png "ustawienia konta")

3. W **konta** w okienku po lewej stronie kliknij pozycję **Podsumowanie**, a następnie kliknij przycisk **Dodaj użytkownika**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797041.png "ustawienia konta")

4. Na **aktywni użytkownicy** okno dialogowe, należy wykonać następujące czynności:
   
    ![Aktywni użytkownicy](./media/new-relic-tutorial/ic797042.png "aktywni użytkownicy")
   
    a. W **E-mail** pole tekstowe, wpisz adres e-mail w prawidłowym użytkownikiem usługi Azure Active Directory do aprowizowania.

    b. Jako **roli** wybierz **użytkownika**.

    c. Kliknij przycisk **Dodaj tego użytkownika**.

>[!NOTE]
>Można użyć jakichkolwiek innych New Relic użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez narzędzie New Relic do aprowizacji kont użytkowników usługi AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do usługi New Relic.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon usługi New Relic, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **usługi New Relic**.

    ![Link usługi New Relic na liście aplikacji](./media/new-relic-tutorial/tutorial_new-relic_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi New Relic w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji New Relic.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/new-relic-tutorial/tutorial_general_01.png
[2]: ./media/new-relic-tutorial/tutorial_general_02.png
[3]: ./media/new-relic-tutorial/tutorial_general_03.png
[4]: ./media/new-relic-tutorial/tutorial_general_04.png

[100]: ./media/new-relic-tutorial/tutorial_general_100.png

[200]: ./media/new-relic-tutorial/tutorial_general_200.png
[201]: ./media/new-relic-tutorial/tutorial_general_201.png
[202]: ./media/new-relic-tutorial/tutorial_general_202.png
[203]: ./media/new-relic-tutorial/tutorial_general_203.png

