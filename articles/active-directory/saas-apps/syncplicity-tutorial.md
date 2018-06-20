---
title: 'Samouczek: Integracji Azure Active Directory z Syncplicity | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: df0842c494fd69f41c97019c3ded9c2509c2463f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223821"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Samouczek: Integracji Azure Active Directory z Syncplicity

Z tego samouczka dowiesz się integrowanie Syncplicity z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Syncplicity zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Syncplicity
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Syncplicity (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Syncplicity, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Syncplicity logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Syncplicity z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-syncplicity-from-the-gallery"></a>Dodawanie Syncplicity z galerii
Aby skonfigurować integrację usługi Azure AD Syncplicity, należy dodać Syncplicity z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Syncplicity z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Syncplicity**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_search.png)

5. W panelu wyników wybierz **Syncplicity**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Syncplicity na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Syncplicity jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Syncplicity musi się.

W Syncplicity, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Syncplicity, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Syncplicity](#creating-a-syncplicity-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Syncplicity połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Syncplicity.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Syncplicity, wykonaj następujące czynności:**

1. W portalu Azure na **Syncplicity** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

3. Na **Syncplicity domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.syncplicity.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Syncplicity](https://www.syncplicity.com/contact-us) uzyskać te wartości. 
 

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/syncplicity-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Syncplicity** , kliknij przycisk **skonfigurować Syncplicity** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/syncplicity-tutorial/tutorial_syncplicity_configure.png) 

7. Zaloguj się do Twojego **Syncplicity** dzierżawy.

8. W menu u góry kliknij **admin**, wybierz pozycję **ustawienia**, a następnie kliknij przycisk **domeny niestandardowe i logowanie jednokrotne**.
   
    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

9. Na **pojedynczego logowania jednokrotnego (SSO)** okna dialogowego strony, należy wykonać następujące czynności:
   
    ![Logowanie jednokrotne \(logowania jednokrotnego\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. W **domeny niestandardowe** tekstowym, wpisz nazwę domeny.
  
    b. Wybierz **włączone** jako **pojedynczy stan logowania jednokrotnego**.

    c. W **identyfikator jednostki** pole tekstowe, Wklej wartość **identyfikator jednostki SAML** którego została skopiowana z portalu Azure.

    d. W **adres URL logowania strony** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    e. W **adres URL strony wylogowania** pole tekstowe, Wklej **Sign-Out URL** którego została skopiowana z portalu Azure.

    f. W **certyfikat dostawcy tożsamości**, kliknij przycisk **wybierz plik**, a następnie przekaż certyfikat, który został pobrany z portalu Azure. 

    g. Kliknij przycisk **ZAPISAĆ zmiany**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-syncplicity-test-user"></a>Tworzenie użytkownika testowego Syncplicity
Dla użytkowników usługi AAD można było zarejestrować muszą mieć przydzielone do Syncplicity aplikacji. W tej sekcji opisano sposób tworzenia kont użytkowników usługi AAD w Syncplicity.

**Aby udostępnić konta użytkownika do Syncplicity, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **Syncplicity** dzierżawy (na przykład: `https://company.Syncplicity.com`).

2. Kliknij przycisk **admin** i wybierz **kont użytkowników**.

3. Kliknij przycisk **dodać użytkownika**.
   
    ![Zarządzaj użytkownikami](./media/syncplicity-tutorial/ic769764.png "Zarządzanie użytkownikami")

4. Typ **ruch poczty E-mail** konta usługi AAD chcesz udostępnić, wybierz **użytkownika** jako **roli**, a następnie kliknij przycisk **dalej**.
   
    ![Informacji o koncie](./media/syncplicity-tutorial/ic769765.png "informacji o koncie")
   
    >[!NOTE]
    >Właściciel konta usługi AAD pobiera adres e-mail, łącznie z łączem do potwierdzenia i Aktywuj konta. 
    > 

5. Wybierz grupę w Twojej firmie, nowy użytkownik powinien członkiem, a następnie kliknij przycisk **dalej**.
   
    ![Członkostwa w grupie](./media/syncplicity-tutorial/ic769772.png "członkostwa grupy")
   
    >[!NOTE]
    >Jeśli nie ma żadnych grup, na liście, kliknij przycisk **dalej**. 
    > 

6. Wybierz foldery, które chcesz umieścić pod kontrolą firmy Syncplicity na komputerze użytkownika, a następnie kliknij przycisk **dalej**.
   
    ![Foldery Syncplicity](./media/syncplicity-tutorial/ic769773.png "Syncplicity folderów")

>[!NOTE]
>Możesz użyć innych Syncplicity użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Syncplicity do kont użytkowników usługi AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Syncplicity.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Syncplicity, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Syncplicity**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/syncplicity-tutorial/tutorial_syncplicity_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Syncplicity w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Syncplicity.
## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/syncplicity-tutorial/tutorial_general_203.png

