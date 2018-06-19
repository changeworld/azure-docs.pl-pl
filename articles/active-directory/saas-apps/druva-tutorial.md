---
title: 'Samouczek: Integracji Azure Active Directory z Druva | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2017
ms.author: jeedes
ms.openlocfilehash: 65633fc083798157f949b90c8424e656fa27a2f5
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941081"
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Samouczek: Integracji Azure Active Directory z Druva

Z tego samouczka dowiesz się integrowanie Druva z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Druva zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Druva.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Druva (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Druva, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Druva logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Druva z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-druva-from-the-gallery"></a>Dodawanie Druva z galerii
Aby skonfigurować integrację usługi Azure AD Druva, należy dodać Druva z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Druva z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Druva**, wybierz pozycję **Druva** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Druva na liście wyników](./media/druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Druva w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Druva jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Druva musi się.

W Druva, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Druva, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Druva](#create-a-druva-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Druva połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Druva.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Druva, wykonaj następujące czynności:**

1. W portalu Azure na **Druva** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/druva-tutorial/tutorial_druva_samlbase.png)

3. Na **Druva domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Konfigurowanie rejestracji jednokrotnej](./media/druva-tutorial/tutorial_druva_url.png)

    W **identyfikator** tekstowym, wpisz wartość ciągu: `druva-cloud`
    
4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Konfigurowanie rejestracji jednokrotnej](./media/druva-tutorial/tutorial_druva_url1.png)
    
    W **adres URL logowania** tekstowym, wpisz adres URL: `https://cloud.druva.com/home`

5. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/druva-tutorial/tutorial_druva_certificate.png) 

6. Aplikacja Druva oczekuje potwierdzenia języka SAML w określonym formacie, musisz dodać mapowania atrybutu niestandardowego do Twojej **atrybuty tokenu SAML** konfiguracji. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/druva-tutorial/tutorial_druva_attribute.png)

7. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na powyższej ilustracji i wykonaj następujące czynności:

    | Nazwa atrybutu      | Wartość atrybutu      |
    | ------------------- | -------------------- |
    | zsynchronizowana\_uwierzytelniania\_tokenu |Wprowadź wartość tokenu w wygenerowanym |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/druva-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/druva-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza. Wartość tokenu wygenerowanego znajduje się w dalszej części samouczka.
    
    d. Kliknij przycisk **OK**.    

8. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/druva-tutorial/tutorial_general_400.png)

9. Na **konfiguracji Druva** , kliknij przycisk **skonfigurować Druva** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/druva-tutorial/tutorial_druva_configure.png) 

10. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Druva.

11. Przejdź do **zarządzanie \> ustawienia**.

    ![Ustawienia](./media/druva-tutorial/ic795091.png "ustawienia")

12. W oknie dialogowym Ustawienia rejestracji jednokrotnej wykonaj następujące czynności:

    ![Single Sign-On ustawienia](./media/druva-tutorial/ic795092.png "Single Sign-On ustawienia")
    
    a. W **adres URL logowania dostawcy identyfikator** pole tekstowe, Wklej wartość **pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.
        
    b. W **adres URL wylogowania dostawcy identyfikator** pole tekstowe, Wklej wartość **Sign-Out adres URL**, które zostały skopiowane z portalu Azure
        
    c. Otwórz w Notatniku certyfikatu zakodowanego base-64, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikat dostawcy identyfikator** pole tekstowe
     
    d. Aby otworzyć **ustawienia** kliknij przycisk **zapisać**.

13. Na **ustawienia** kliknij przycisk **generowania tokenu rejestracji Jednokrotnej**.

    ![Ustawienia](./media/druva-tutorial/ic795093.png "ustawienia")

14. Na **pojedynczego logowania jednokrotnego tokenu uwierzytelniania** okna dialogowego, wykonaj następujące czynności:

    ![Token rejestracji Jednokrotnej](./media/druva-tutorial/ic795094.png "tokenu rejestracji Jednokrotnej")
    
    a. Kliknij przycisk **kopiowania**, wklej skopiowane wartości w **wartość** textbox w **Dodawanie atrybutu** sekcji w portalu Azure.
    
    b. Kliknij przycisk **Zamknij**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/druva-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/druva-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/druva-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/druva-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-druva-test-user"></a>Tworzenie użytkownika testowego Druva

Aby umożliwić użytkownikom zalogować się do Druva usługi Azure AD, musi być przygotowana do Druva. W przypadku Druva Inicjowanie obsługi to zadanie ręczne.

**Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **Druva** witryny firmy jako administrator.

2. Przejdź do **zarządzanie \> użytkowników**.
   
   ![Zarządzaj użytkownikami](./media/druva-tutorial/ic795097.png "Zarządzanie użytkownikami")

3. Kliknij przycisk **tworzenia nowych**.
   
   ![Zarządzaj użytkownikami](./media/druva-tutorial/ic795098.png "Zarządzanie użytkownikami")

4. W oknie dialogowym Tworzenie nowego użytkownika wykonaj następujące czynności:
   
   ![Utwórz NewUser](./media/druva-tutorial/ic795099.png "utworzyć NewUser")
   
   a. W **adres E-mail** pole tekstowe, wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.
   
   b. W **nazwa** pole tekstowe, wprowadź nazwę użytkownika, takich jak **BrittaSimon**.
   
   c. Kliknij przycisk **Utwórz użytkownika**.

>[!NOTE]
>Możesz użyć innych Druva użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Druva do udostępnienia konta użytkownika usługi Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Druva.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Druva, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Druva**.

    ![Łącze Druva na liście aplikacji](./media/druva-tutorial/tutorial_druva_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Druva w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Druva.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
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

