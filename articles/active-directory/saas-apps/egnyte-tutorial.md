---
title: 'Samouczek: Integracja usługi Azure Active Directory z Egnyte | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: d52672099c309dfeac641cb3aedaf32c7230b676
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047763"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Samouczek: Integracja usługi Azure Active Directory z Egnyte

W tym samouczku dowiesz się, jak zintegrować Egnyte w usłudze Azure Active Directory (Azure AD).

Integrowanie Egnyte z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Egnyte
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Egnyte (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Egnyte, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Egnyte logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Egnyte z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-egnyte-from-the-gallery"></a>Dodawanie Egnyte z galerii
Aby skonfigurować integrację Egnyte w usłudze Azure AD, należy dodać Egnyte z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Egnyte z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Egnyte**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/egnyte-tutorial/tutorial_egnyte_search.png)

5. W panelu wyników wybierz **Egnyte**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Egnyte w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Egnyte do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Egnyte musi można ustanowić.

W Egnyte, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Egnyte, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Egnyte](#creating-an-egnyte-test-user)**  — aby odpowiednikiem Britta Simon w Egnyte połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Egnyte.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Egnyte, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Egnyte** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/egnyte-tutorial/tutorial_egnyte_samlbase.png)

3. Na **Egnyte domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/egnyte-tutorial/tutorial_egnyte_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) aby zyskać tę wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/egnyte-tutorial/tutorial_egnyte_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/egnyte-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Egnyte** , kliknij przycisk **skonfigurować Egnyte** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/egnyte-tutorial/tutorial_egnyte_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Egnyte.

8. Kliknij przycisk **ustawienia**.
   
   ![Ustawienia](./media/egnyte-tutorial/ic787819.png "ustawienia")

9. W menu, kliknij **ustawienia**.

   ![Ustawienia](./media/egnyte-tutorial/ic787820.png "ustawienia")

10. Kliknij przycisk **konfiguracji** kartę, a następnie kliknij przycisk **zabezpieczeń**.

    ![Zabezpieczenia](./media/egnyte-tutorial/ic787821.png "zabezpieczeń")

11. W **uwierzytelniania rejestracji jednokrotnej** sekcji, wykonaj następujące czynności:

    ![Rejestracja jednokrotna uwierzytelnianie](./media/egnyte-tutorial/ic787822.png "pojedynczego logowania dla uwierzytelniania")   
    
    a. Jako **uwierzytelniania rejestracji jednokrotnej**, wybierz opcję **SAML 2.0**.
   
    b. Jako **dostawcy tożsamości**, wybierz opcję **AzureAD**.
   
    c. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowane z witryny Azure portal do **adres URL logowania dostawcy tożsamości** pola tekstowego.
   
    d. Wklej **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal do **Identyfikatora jednostki dostawcy tożsamości** pola tekstowego.
      
    e. Otwórz swój certyfikat zakodowany base-64 w Notatniku pobranego z witryny Azure portal, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu dostawcy tożsamości** pola tekstowego.
   
    f. Jako **domyślne mapowanie użytkownika**, wybierz opcję **adres E-mail**.
   
    g. Jako **Użyj wartości wystawcy specyficznego dla domeny**, wybierz opcję **wyłączone**.
   
    h. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/egnyte-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/egnyte-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/egnyte-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/egnyte-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-egnyte-test-user"></a>Tworzenie użytkownika testowego Egnyte

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Egnyte, musi być obsługiwana w Egnyte. W przypadku Egnyte Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Egnyte** witryny firmy jako administrator.

2. Przejdź do **ustawienia \> użytkownikami i grupami**.

3. Kliknij przycisk **Dodaj nowego użytkownika**, a następnie wybierz typ użytkownika, które chcesz dodać.
   
   ![Użytkownicy](./media/egnyte-tutorial/ic787824.png "użytkowników")

4. W **nowego użytkownika standardowego** sekcji, wykonaj następujące czynności:
   
   ![Nowego użytkownika standardowego](./media/egnyte-tutorial/ic787825.png "nowego użytkownika standardowego")   

   a. Typ **E-mail**, **Username**i inne informacje szczegółowe dotyczące prawidłowego konta usługi Azure Active Directory do aprowizowania.
   
   b. Kliknij pozycję **Zapisz**.
    
    >[!NOTE]
    >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z powiadomieniem.
    >

>[!NOTE]
>Można użyć jakichkolwiek innych Egnyte użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Egnyte do aprowizacji kont użytkowników usługi AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Egnyte.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Egnyte, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Egnyte**.

    ![Konfigurowanie logowania jednokrotnego](./media/egnyte-tutorial/tutorial_egnyte_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Egnyte w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Egnyte.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/egnyte-tutorial/tutorial_general_01.png
[2]: ./media/egnyte-tutorial/tutorial_general_02.png
[3]: ./media/egnyte-tutorial/tutorial_general_03.png
[4]: ./media/egnyte-tutorial/tutorial_general_04.png

[100]: ./media/egnyte-tutorial/tutorial_general_100.png

[200]: ./media/egnyte-tutorial/tutorial_general_200.png
[201]: ./media/egnyte-tutorial/tutorial_general_201.png
[202]: ./media/egnyte-tutorial/tutorial_general_202.png
[203]: ./media/egnyte-tutorial/tutorial_general_203.png

