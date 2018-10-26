---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą usługi Snowflake | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi Snowflake.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3488ac27-0417-4ad9-b9a3-08325fe8ea0d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 6e1f76548e2433f9bc4b0b26b0894a92b7ec0aa0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092563"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą usługi Snowflake

W tym samouczku dowiesz się, jak zintegrować Snowflake w usłudze Azure Active Directory (Azure AD).

Integrowanie usługi Snowflake z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, który ma dostęp do usługi Snowflake.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Snowflake (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi Snowflake, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Snowflake logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi Snowflake z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-snowflake-from-the-gallery"></a>Dodawanie usługi Snowflake z galerii

Aby skonfigurować integrację usługi Snowflake w usłudze Azure AD, należy dodać Snowflake z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Snowflake z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Snowflake**, wybierz opcję **Snowflake** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Snowflake na liście wyników](./media/snowflake-tutorial/tutorial_snowflake_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi Snowflake w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Snowflake do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Snowflake musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi Snowflake, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Snowflake](#creating-snowflake-test-user)**  — aby odpowiednikiem Britta Simon w Snowflake połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi Snowflake.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą usługi Snowflake, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Snowflake** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Domena usługi Snowflake i adresy URL pojedynczego logowania jednokrotnego informacji](./media/snowflake-tutorial/tutorial_snowflake_url1.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Domena usługi Snowflake i adresy URL pojedynczego logowania jednokrotnego informacji](./media/snowflake-tutorial/tutorial_snowflake_url2.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    > [!NOTE]
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości.

6. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/snowflake-tutorial/tutorial_snowflake_certificate.png) 

7. Na **Konfigurowanie Snowflake** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja usługi Snowflake](common/configuresection.png)

8. W oknie przeglądarki internetowej innej, zaloguj się do usługi Snowflake jako Administrator zabezpieczeń.

9. **Przełącz rolę** do **ACCOUNTADMIN**, klikając **profilu** w prawej górnej części strony.

    > [!NOTE]
    > To różni się od kontekstu, wybranego w prawym górnym rogu pod nazwą użytkownika
    
    ![Administrator usługi Snowflake ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

10. Otwórz **pobrany certyfikat Base 64** w Notatniku. Skopiuj wartość z zakresu od "---BEGIN CERTIFICATE---" i "---END CERTIFICATE---" i wklej ją w znaki cudzysłowu obok **certyfikatu** poniżej. W **ssoUrl**, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal. Wybierz **wszystkie zapytania** i kliknij przycisk **Uruchom**.

    ![Snowflake sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

    ```
    use role accountadmin;
    alter account set saml_identity_provider = '{
    "certificate": "<Paste the content of downloaded certificate from Azure portal>",
    "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
    "type":"custom",
    "label":"AzureAD"
    }';
    alter account set sso_login_page = TRUE;
    ```

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-snowflake-test-user"></a>Tworzenie użytkownika testowego Snowflake

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do usługi Snowflake, musi być obsługiwana w Snowflake. W Snowflake Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się jako Administrator zabezpieczeń usługi Snowflake.

2. **Przełącz rolę** do **ACCOUNTADMIN**, klikając **profilu** w prawej górnej części strony.  

    ![Administrator usługi Snowflake ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Utwórz użytkownika, uruchamiając poniższe zapytanie SQL zapewnienie "Nazwa logowania" jest ustawiona na nazwę użytkownika usługi Azure AD w arkuszu jak pokazano poniżej.

    ![Snowflake adminsql ](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do usługi Snowflake.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Snowflake**.

    ![Konfigurowanie logowania jednokrotnego](./media/snowflake-tutorial/tutorial_snowflake_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Snowflake w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Snowflake.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
