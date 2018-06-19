---
title: 'Samouczek: Azure Active Directory integracji z oprogramowaniem HR Cezanne | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między oprogramowaniem HR Cezanne i Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: a1e7d2ee41c9334c5dc5c64014d5bb2946bfe865
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35940445"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Samouczek: Azure Active Directory integracji z oprogramowaniem HR Cezanne

Z tego samouczka dowiesz się integrowanie Cezanne HR oprogramowania z usługi Azure Active Directory (Azure AD).

Integrowanie Cezanne HR oprogramowania z usługi Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do oprogramowania HR Cezanne.
- Umożliwia użytkownikom automatycznie pobrać zalogowane Cezanne HR oprogramowania (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem HR Cezanne, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Oprogramowanie HR Cezanne logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie oprogramowania HR Cezanne z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Dodawanie oprogramowania HR Cezanne z galerii
Aby skonfigurować integrację usługi Azure AD Cezanne HR oprogramowania, należy dodać Cezanne HR oprogramowania z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Cezanne HR oprogramowanie z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Cezanne HR oprogramowania**, wybierz pozycję **oprogramowania HR Cezanne** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Cezanne HR oprogramowania z listy wyników](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem HR Cezanne w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednikiem Cezanne HR oprogramowanie jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w oprogramowaniu HR Cezanne musi określone.

W oprogramowaniu HR Cezanne, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem HR Cezanne, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego oprogramowania HR Cezanne](#create-a-cezannehrsoftware-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Cezanne HR oprogramowania, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w używanej aplikacji Cezanne HR.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem HR Cezanne, wykonaj następujące czynności:**

1. W portalu Azure na **oprogramowania HR Cezanne** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. Na **Cezanne HR oprogramowania domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i Cezanne HR oprogramowania domeny pojedynczy informacje logowania jednokrotnego](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. W **identyfikator** tekstowym, wpisz adres URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta oprogramowania HR Cezanne](https://cezannehr.com/services/support/) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/cezannehrsoftware-tutorial/tutorial_general_400.png)

6. Na **konfiguracji oprogramowania HR Cezanne** , kliknij przycisk **Konfigurowanie oprogramowania HR Cezanne** otworzyć **Konfigurowanie logowania jednokrotnego** okna.

    ![Cezanne HR konfiguracji oprogramowania](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

7. Przewiń w dół do **krótkimi opisami** sekcji. Kopiuj **SAML pojedynczy znak na adres URL usługi i identyfikator jednostki SAML** z **sekcji krótkimi opisami.**

    ![Cezanne HR konfiguracji oprogramowania](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

8. W oknie przeglądarki innej witryny sieci web logowanie do dzierżawy Cezanne HR oprogramowania jako administrator.

9. W lewym okienku nawigacji, kliknij polecenie **konfiguracji systemu**. Przejdź do **ustawienia zabezpieczeń**. Następnie przejdź do **konfiguracji rejestracji jednokrotnej**.

    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

10. W **Zezwalaj użytkownikom na logowanie przy użyciu następującej usługi pojedynczego logowania jednokrotnego (SSO)** panelu wyboru **SAML 2.0** i wybierz **Advanced Configuration** opcji.

    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

11. Kliknij przycisk **Dodaj nowy** przycisku.

    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

12. Wykonaj następujące czynności na **dostawców tożsamości SAML 2.0** sekcji.

    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Wprowadź nazwę dostawcy tożsamości jako **Nazwa wyświetlana**.

    b. W **identyfikator** pole tekstowe, Wklej wartość **identyfikator jednostki SAML** którego została skopiowana z portalu Azure. 

    c. Zmień **powiązania SAML** "Post".

    d. W **punktu końcowego usługi tokenu zabezpieczającego** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    e. W polu tekstowym Nazwa atrybutu Identyfikatora użytkownika, wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Kliknij przycisk **przekazać** ikonę, aby przekazać certyfikat pobrany z portalu Azure.
    
    g. Kliknij przycisk **OK**. 

13. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/cezannehrsoftware-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/cezannehrsoftware-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/cezannehrsoftware-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Tworzenie użytkownika testowego Cezanne HR oprogramowania

Aby włączyć użytkowników usługi Azure AD zalogować się do Cezanne HR oprogramowania, muszą mieć przydzielone do Cezanne HR oprogramowania. W przypadku oprogramowania HR Cezanne Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1.  Zaloguj się do witryny firmy Cezanne HR oprogramowania jako administrator.

2.  W lewym okienku nawigacji, kliknij polecenie **konfiguracji systemu**. Przejdź do **Zarządzanie użytkownikami**. Następnie przejdź do **Dodaj nowego użytkownika**.

    ![Nowy użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "nowego użytkownika")

3.  Na **dane osobowe** sekcji, wykonaj następujące czynności:

    ![Nowy użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "nowego użytkownika")
    
    a. Ustaw **wewnętrznego użytkownika** jako OFF.
    
    b. W **imię** tekstowym, wpisz imię użytkownika, takich jak **Britta**.  
 
    c. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak **Simona**.
    
    d. W **E-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak Brittasimon@contoso.com.

4.  Na **informacje o koncie** sekcji, wykonaj następujące czynności:

    ![Nowy użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "nowego użytkownika")
    
    a. W **Username** tekstowym, wpisz adres e-mail użytkownika, takich jak Brittasimon@contoso.com.
    
    b. W **hasło** tekstowym, wpisz hasło użytkownika.
    
    c. Wybierz **HR Professional** jako **roli zabezpieczeń**.
    
    d. Kliknij przycisk **OK**.

5. Przejdź do **rejestracji jednokrotnej** i wybierz **Dodaj nowy** w **SAML 2.0 identyfikatory** obszaru.

    ![Użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "użytkownika")

6. Wybierz dostawcę tożsamości dla **dostawcy tożsamości** i w polu tekstowym **identyfikator użytkownika**, wprowadź adres e-mail konta Simona Britta.

    ![Użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "użytkownika")
    
7. Kliknij przycisk **zapisać** przycisku.

    ![Użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "użytkownika")

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do oprogramowania HR Cezanne.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Cezanne HR oprogramowania, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Cezanne HR oprogramowania**.

    ![Łącze Cezanne HR oprogramowania na liście aplikacji](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Cezanne HR oprogramowania w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji oprogramowania HR Cezanne.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/cezannehrsoftware-tutorial/tutorial_general_203.png

