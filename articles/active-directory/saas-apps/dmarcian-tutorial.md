---
title: 'Samouczek: Integracja usługi Azure Active Directory z dmarcian | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2018
ms.author: jeedes
ms.openlocfilehash: 677c40932a557b8a15a51b947794b4281801f65a
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45637653"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Samouczek: Integracja usługi Azure Active Directory z dmarcian

W tym samouczku dowiesz się, jak zintegrować dmarcian w usłudze Azure Active Directory (Azure AD).

Integrowanie dmarcian z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do dmarcian.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do dmarcian (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą dmarcian, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Dmarcian logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie dmarcian z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-dmarcian-from-the-gallery"></a>Dodawanie dmarcian z galerii
Aby skonfigurować integrację dmarcian w usłudze Azure AD, należy dodać dmarcian z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać dmarcian z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje dla przedsiębiorstw][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **dmarcian**, wybierz opcję **dmarcian** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![dmarcian na liście wyników](./media/dmarcian-tutorial/tutorial_dmarcian_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą dmarcian w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w dmarcian do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w dmarcian musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą dmarcian, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego dmarcian](#create-a-dmarcian-test-user)**  — aby mają odpowiednika w pozycji Britta simon w dmarcian połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji dmarcian.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z dmarcian, wykonaj następujące czynności:**

1. W witrynie Azure portal na **dmarcian** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/dmarcian-tutorial/tutorial_dmarcian_samlbase.png)

3. Na **dmarcian domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![dmarcian domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/dmarcian-tutorial/tutorial_dmarcian_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian.com-eu/sso/saml/<ACCOUNT_ID>/sp.xml ` |
    | `https://dmarcian.com-ap/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian.com-eu/login/<ACCOUNT_ID>/handle/ `|
    | `https://dmarcian.com-ap/login/<ACCOUNT_ID>/handle/`|

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![dmarcian domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/dmarcian-tutorial/tutorial_dmarcian_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian.com-eu/login/<ACCOUNT_ID>` |
    | `https://dmarcian.com-ap/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Te wartości zostaną zaktualizowane o rzeczywisty identyfikator, adres URL odpowiedzi i URL logowania jednokrotnego, co zostało wyjaśnione w dalszej części tego samouczka. 

5. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    ![Link pobierania certyfikatu](./media/dmarcian-tutorial/tutorial_dmarcian_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/dmarcian-tutorial/tutorial_general_400.png)
    
7. W oknie przeglądarki internetowej innej Zaloguj się do dmarcian jako Administrator zabezpieczeń.

8. Kliknij pozycję **profilu** w prawym górnym rogu, a następnie przejdź do **preferencje**.

    ![Preferencje ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

9. Przewiń w dół i kliknij pozycję **logowania jednokrotnego** sekcji, a następnie kliknij pozycję **Konfiguruj**.

    ![Pojedynczej precyzji ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

10. Na **SAML logowania jednokrotnego** strony zestawu **stan** jako **włączone** i wykonaj następujące czynności:

    ![Uwierzytelnianie ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * W obszarze **Dodaj dmarcian do dostawcy tożsamości** , kliknij przycisk **KOPIOWANIA** do skopiowania **adres URL usługi konsumenta potwierdzenie** wystąpienia i wklej go w  **Adres URL odpowiedzi** polu tekstowym w **dmarcian sekcji domena i adresy URL** w witrynie Azure portal.

    * W obszarze **Dodaj dmarcian do dostawcy tożsamości** kliknij **kopii** do skopiowania **identyfikator jednostki** wystąpienia i wklej go w **identyfikator**polu tekstowym w **dmarcian sekcji domena i adresy URL** w witrynie Azure portal.

    * W obszarze **skonfigurować uwierzytelnianie** sekcji w **metadanych dostawcy tożsamości** Wklej w pole tekstowe **adres Url metadanych Federacji aplikacji**, który skopiowano z witryny Azure portal.

    * W obszarze **skonfigurować uwierzytelnianie** sekcji w **instrukcje atrybutu** pola tekstowego, wklej adres url `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    * W obszarze **Konfigurowanie adresu URL logowania** sekcji, skopiuj **adres URL logowania** wystąpienia i wklej go w **adres URL logowania** polu tekstowym w **dmarcian domena i adresy URL w sekcji** w witrynie Azure portal.

        > [!Note]
        > Możesz zmodyfikować **adres URL logowania** zgodnie z Twojej organizacji.

    * Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/dmarcian-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/dmarcian-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/dmarcian-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/dmarcian-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-dmarcian-test-user"></a>Tworzenie użytkownika testowego dmarcian

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do dmarcian, musi być obsługiwana w dmarcian. W dmarcian Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dmarcian jako Administrator zabezpieczeń.

2. Kliknij pozycję **profilu** w prawym górnym rogu, a następnie przejdź do **Zarządzanie użytkownikami**.

    ![Użytkownik ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. W prawej części **logowania jednokrotnego użytkowników** sekcji, kliknij pozycję **Dodaj nowego użytkownika**.

    ![Dodaj użytkownika ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Na **Dodaj nowego użytkownika** okna podręcznego, wykonaj następujące czynności:

    ![Nowy użytkownik ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. W **nowy adres E-mail użytkownika** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    b. Jeśli chcesz nadać uprawnienia administratora dla użytkownika, wybierz opcję **wprowadzić użytkownika Administrator**.

    c. Kliknij przycisk **Dodaj użytkownika**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do dmarcian.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon dmarcian, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **dmarcian**.

    ![Link dmarcian na liście aplikacji](./media/dmarcian-tutorial/tutorial_dmarcian_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka dmarcian w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji dmarcian.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dmarcian-tutorial/tutorial_general_01.png
[2]: ./media/dmarcian-tutorial/tutorial_general_02.png
[3]: ./media/dmarcian-tutorial/tutorial_general_03.png
[4]: ./media/dmarcian-tutorial/tutorial_general_04.png

[100]: ./media/dmarcian-tutorial/tutorial_general_100.png

[200]: ./media/dmarcian-tutorial/tutorial_general_200.png
[201]: ./media/dmarcian-tutorial/tutorial_general_201.png
[202]: ./media/dmarcian-tutorial/tutorial_general_202.png
[203]: ./media/dmarcian-tutorial/tutorial_general_203.png

