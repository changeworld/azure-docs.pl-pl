---
title: 'Samouczek: Integracji Azure Active Directory z usługi New Relic | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i usługi New Relic.
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
ms.openlocfilehash: a311a99522f5a47290cbf60993a2d1aa1997dcca
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215632"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Samouczek: Integracji Azure Active Directory z usługi New Relic

Z tego samouczka dowiesz się Integrowanie usługi New Relic w usłudze Azure Active Directory (Azure AD).

Integrowanie usługi New Relic z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do usługi New Relic.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do usługi New Relic (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługi New Relic, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Usługi New Relic logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie usługi New Relic w galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-new-relic-from-the-gallery"></a>Dodawanie usługi New Relic w galerii
Aby skonfigurować integrację usługi New Relic w usłudze Azure Active Directory, należy dodać usługi New Relic w galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługi New Relic w galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **usługi New Relic**, wybierz pozycję **usługi New Relic** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Usługi New Relic na liście wyników](./media/new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usługi New Relic w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w usługi New Relic dla użytkownika w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w usługi New Relic.

Usługi New Relic, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usługi New Relic, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego usługi New Relic](#create-a-new-relic-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta usługi New Relic jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji usługi New Relic.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z usługi New Relic, wykonaj następujące czynności:**

1. W portalu Azure na **usługi New Relic** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/new-relic-tutorial/tutorial_new-relic_samlbase.png)

3. Na **adresy URL i nowej domeny Relic** sekcji, wykonaj następujące czynności:

    ![Nowe adresy URL i domeny Relic pojedynczy informacje logowania jednokrotnego](./media/new-relic-tutorial/tutorial_new-relic_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` — należy zastąpić identyfikatorem nowe konto Relic

    b. W **identyfikator** tekstowym, wpisz wartość: `rpm.newrelic.com`

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/new-relic-tutorial/tutorial_new-relic_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/new-relic-tutorial/tutorial_general_400.png)

6. Na **nowej konfiguracji Relic** kliknij **skonfigurować usługi New Relic** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Nowa konfiguracja Relic](./media/new-relic-tutorial/tutorial_new-relic_configure.png) 

7. W oknie przeglądarki innej witryny sieci web, zaloguj się na Twojej **usługi New Relic** witryny firmy jako administrator.

8. W menu u góry kliknij **ustawienia konta**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797036.png "ustawienia konta")

9. Kliknij przycisk **zabezpieczeń i uwierzytelniania** , a następnie kliknij pozycję **jednokrotne** kartę.
   
    ![Logowanie jednokrotne](./media/new-relic-tutorial/ic797037.png "logowanie jednokrotne")

10. Na stronie okna dialogowego SAML wykonaj następujące czynności:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
   a. Kliknij przycisk **wybierz plik** przekazać pobrany certyfikat usługi Azure Active Directory.

   b. W **adres URL logowania zdalnego** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.
   
   c. W **lądowanie adresu URL wylogowania** pole tekstowe, Wklej wartość **Sign-Out adres URL**, które zostały skopiowane z portalu Azure.

   d. Kliknij przycisk **Zapisz moje zmiany**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/new-relic-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/new-relic-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/new-relic-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/new-relic-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-new-relic-test-user"></a>Tworzenie użytkownika testowego usługi New Relic

Aby włączyć usługi Azure Active Directory użytkownikom na logowanie się do usługi New Relic, musi być przygotowana do usługi New Relic. W przypadku usługi New Relic Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika do usługi New Relic, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **usługi New Relic** witryny firmy jako administrator.

2. W menu u góry kliknij **ustawienia konta**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797040.png "ustawienia konta")

3. W **konta** w okienku po lewej stronie kliknij **Podsumowanie**, a następnie kliknij przycisk **Dodaj użytkownika**.
   
    ![Ustawienia konta](./media/new-relic-tutorial/ic797041.png "ustawienia konta")

4. Na **aktywni użytkownicy** okna dialogowego, wykonaj następujące czynności:
   
    ![Aktywni użytkownicy](./media/new-relic-tutorial/ic797042.png "aktywni użytkownicy")
   
    a. W **E-mail** tekstowym, wpisz adres e-mail chcesz udostępnić prawidłowym użytkownikiem usługi Azure Active Directory.

    b. Jako **roli** wybierz **użytkownika**.

    c. Kliknij przycisk **Dodaj tego użytkownika**.

>[!NOTE]
>Można użyć dowolnego inne usługi New Relic użytkownika konta narzędzia do tworzenia lub interfejsów API dostarczonych przez usługi New Relic do kont użytkowników usługi AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do usługi New Relic.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta do usługi New Relic, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **usługi New Relic**.

    ![Łącze usługi New Relic na liście aplikacji](./media/new-relic-tutorial/tutorial_new-relic_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka usługi New Relic w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji usługi New Relic.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
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

