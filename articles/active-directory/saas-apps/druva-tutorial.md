---
title: 'Samouczek: Integracja usługi Azure Active Directory z Druva | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i druva działają.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2017
ms.author: jeedes
ms.openlocfilehash: e536663669cadc0352a52c7f4f24ed9669661d2d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042983"
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Samouczek: Integracja usługi Azure Active Directory z Druva

W tym samouczku dowiesz się, jak zintegrować druva działają z usługą Azure Active Directory (Azure AD).

Integrowanie druva działają z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do druva działają.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Druva (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Druva, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Druva logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie druva działają z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-druva-from-the-gallery"></a>Dodawanie druva działają z galerii
Aby skonfigurować integrację druva działają w usłudze Azure AD, należy dodać druva działają z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać druva działają z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Druva**, wybierz opcję **Druva** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Druva działają na liście wyników](./media/druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą druva działają w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w druva działają dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Druva musi można ustanowić.

W Druva, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Druva, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Druva](#create-a-druva-test-user)**  — aby odpowiednikiem Britta Simon w Druva, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji druva działają.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Druva, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Druva** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/druva-tutorial/tutorial_druva_samlbase.png)

3. Na **Druva domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/druva-tutorial/tutorial_druva_url.png)

    W **identyfikator** polu tekstowym wpisz wartość ciągu: `druva-cloud`
    
4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/druva-tutorial/tutorial_druva_url1.png)
    
    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://cloud.druva.com/home`

5. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/druva-tutorial/tutorial_druva_certificate.png) 

6. Twoja aplikacja Druva oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych, aby Twoje **atrybuty tokenu języka SAML** konfiguracji. 

    ![Konfigurowanie logowania jednokrotnego](./media/druva-tutorial/tutorial_druva_attribute.png)

7. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, pokazany na wcześniejszej ilustracji, używając i wykonaj następujące czynności:

    | Nazwa atrybutu      | Wartość atrybutu      |
    | ------------------- | -------------------- |
    | zsynchronizowana\_uwierzytelniania\_tokenu |Wprowadź token wygenerowaną wartość |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.
    
    ![Konfigurowanie logowania jednokrotnego](./media/druva-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie logowania jednokrotnego](./media/druva-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza. Token wygenerowaną wartość zostało wyjaśnione w dalszej części samouczka.
    
    d. Kliknij przycisk **OK**.    

8. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/druva-tutorial/tutorial_general_400.png)

9. Na **konfiguracji Druva** , kliknij przycisk **skonfigurować Druva** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/druva-tutorial/tutorial_druva_configure.png) 

10. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy druva działają.

11. Przejdź do **zarządzanie \> ustawienia**.

    ![Ustawienia](./media/druva-tutorial/ic795091.png "ustawienia")

12. W oknie dialogowym Ustawienia rejestracji jednokrotnej wykonaj następujące czynności:

    ![Pojedynczy ustawień logowania jednokrotnego](./media/druva-tutorial/ic795092.png "pojedynczy ustawień logowania jednokrotnego")
    
    a. W **adres URL logowania dostawcy identyfikator** pola tekstowego, Wklej wartość **pojedynczy znak na adres URL usługi**, który skopiowano z witryny Azure portal.
        
    b. W **adres URL wylogowania dostawcy identyfikator** pole tekstowe, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal
        
    c. Otwórz swój certyfikat zakodowany base-64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **identyfikator certyfikatu dostawcy** textbox
     
    d. Aby otworzyć **ustawienia** kliknij **Zapisz**.

13. Na **ustawienia** kliknij **Generuj Token logowania jednokrotnego**.

    ![Ustawienia](./media/druva-tutorial/ic795093.png "ustawienia")

14. Na **pojedynczego logowania jednokrotnego tokenu uwierzytelniania** okno dialogowe, należy wykonać następujące czynności:

    ![Token logowania jednokrotnego](./media/druva-tutorial/ic795094.png "Token logowania jednokrotnego")
    
    a. Kliknij przycisk **kopiowania**, Wklej skopiowany wartości w **wartość** polu tekstowym w **Dodawanie atrybutu** sekcji w witrynie Azure portal.
    
    b. Kliknij przycisk **Zamknij**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/druva-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/druva-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/druva-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/druva-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-druva-test-user"></a>Tworzenie użytkownika testowego Druva

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Druva, musi być obsługiwana w druva działają. W przypadku Druva Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Druva** witryny firmy jako administrator.

2. Przejdź do **zarządzanie \> użytkowników**.
   
   ![Zarządzanie użytkownikami](./media/druva-tutorial/ic795097.png "Zarządzanie użytkownikami")

3. Kliknij przycisk **tworzenia nowych**.
   
   ![Zarządzanie użytkownikami](./media/druva-tutorial/ic795098.png "Zarządzanie użytkownikami")

4. W oknie dialogowym Tworzenie nowego użytkownika wykonaj następujące czynności:
   
   ![Tworzenie ze NewUser](./media/druva-tutorial/ic795099.png "tworzenie ze NewUser")
   
   a. W **adres E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.
   
   b. W **nazwa** polu tekstowym wprowadź nazwę użytkownika, takich jak **BrittaSimon**.
   
   c. Kliknij przycisk **Utwórz użytkownika**.

>[!NOTE]
>Można użyć jakichkolwiek innych Druva użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Druva, aby aprowizować konta użytkownika usługi Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do druva działają.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Druva, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Druva**.

    ![Link druva działają na liście aplikacji](./media/druva-tutorial/tutorial_druva_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka druva działają w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji druva działają.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/druva-tutorial/tutorial_general_01.png
[2]: ./media/druva-tutorial/tutorial_general_02.png
[3]: ./media/druva-tutorial/tutorial_general_03.png
[4]: ./media/druva-tutorial/tutorial_general_04.png

[100]: ./media/druva-tutorial/tutorial_general_100.png

[200]: ./media/druva-tutorial/tutorial_general_200.png
[201]: ./media/druva-tutorial/tutorial_general_201.png
[202]: ./media/druva-tutorial/tutorial_general_202.png
[203]: ./media/druva-tutorial/tutorial_general_203.png

