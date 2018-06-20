---
title: 'Samouczek: Integracji Azure Active Directory z RunMyProcess | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 3f1932d02e6488d4ac4a447f187539ea15e328a1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215208"
---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Samouczek: Integracji Azure Active Directory z RunMyProcess

Z tego samouczka dowiesz się integrowanie RunMyProcess z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD RunMyProcess zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do RunMyProcess
- Umożliwia użytkownikom automatycznie pobrać zalogowane do RunMyProcess (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z RunMyProcess, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- RunMyProcess logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna tutaj:[oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie RunMyProcess z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-runmyprocess-from-the-gallery"></a>Dodawanie RunMyProcess z galerii
Aby skonfigurować integrację usługi Azure AD RunMyProcess, należy dodać RunMyProcess z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać RunMyProcess z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **RunMyProcess**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/runmyprocess-tutorial/tutorial_runmyprocess_search.png)

5. W panelu wyników wybierz **RunMyProcess**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/runmyprocess-tutorial/tutorial_runmyprocess_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z RunMyProcess w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w RunMyProcess jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w RunMyProcess musi się.

W RunMyProcess, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z RunMyProcess, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego RunMyProcess](#creating-a-runmyprocess-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta RunMyProcess połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji RunMyProcess.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z RunMyProcess, wykonaj następujące czynności:**

1. W portalu Azure na **RunMyProcess** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/runmyprocess-tutorial/tutorial_runmyprocess_samlbase.png)

3. Na **RunMyProcess domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/runmyprocess-tutorial/tutorial_runmyprocess_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Zaktualizuj tę wartość z adresem URL logowania rzeczywistych. Skontaktuj się z [zespołem pomocy technicznej klienta RunMyProcess](mailto:support@runmyprocess.com) można uzyskać wartość. 

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/runmyprocess-tutorial/tutorial_runmyprocess_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/runmyprocess-tutorial/tutorial_general_400.png)

6. Na **konfiguracji RunMyProcess** , kliknij przycisk **skonfigurować RunMyProcess** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/runmyprocess-tutorial/tutorial_runmyprocess_configure.png) 

7. W oknie przeglądarki innej witryny sieci web logowanie do dzierżawy RunMyProcess jako administrator.

8. W lewym panelu nawigacyjnym kliknij **konta** i wybierz **konfiguracji**.
   
    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

9. Przejdź do **metodę uwierzytelniania** sekcji i wykonaj następujące czynności:
   
    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Jako **metody**, wybierz pozycję **rejestracji Jednokrotnej z Samlv2**. 

    b. W **przekierowania logowania jednokrotnego** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    c. W **przekierowania wylogowania** pole tekstowe, Wklej wartość **Sign-Out URL**, które zostały skopiowane z portalu Azure.

    d. W **Format identyfikatora nazwy** tekstowym, wpisz wartość **Format identyfikatora nazwy** jako **urn: oasis: nazwy: tc: SAML:1.1:nameid-format: emailAddress**.

    e. Skopiuj zawartość pliku pobranego certyfikatu, a następnie wklej go do **certyfikatu** pola tekstowego. 
 
    f. Kliknij przycisk **zapisać** ikony.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/runmyprocess-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/runmyprocess-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/runmyprocess-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/runmyprocess-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-runmyprocess-test-user"></a>Tworzenie użytkownika testowego RunMyProcess

Aby umożliwić użytkownikom zalogować się do RunMyProcess usługi Azure AD, musi być przygotowana do RunMyProcess. W przypadku RunMyProcess Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy RunMyProcess jako administrator.

2. Kliknij przycisk **konta** i wybierz **użytkowników** w panelu nawigacyjnym po lewej stronie, następnie kliknij przycisk **nowego użytkownika**.
   
    ![Nowy użytkownik](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "nowego użytkownika")

3. W **ustawienia użytkownika** sekcji, wykonaj następujące czynności:
   
    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "profilu") 
  
    a. Typ **nazwa** i **E-mail** prawidłowy Azure konta AD ustanawiane do powiązanych pól tekstowych. 

    b. Wybierz **IDE języka**, **języka**, i **profilu**. 

    c. Wybierz **Wyślij do mnie e-mail tworzenia konta**. 

    d. Kliknij pozycję **Zapisz**.
   
    >[!NOTE]
    >Możesz użyć innych RunMyProcess użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez RunMyProcess do świadczenia usługi Azure Active Directory kont użytkowników. 
    > 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu RunMyProcess.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta RunMyProcess, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **RunMyProcess**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/runmyprocess-tutorial/tutorial_runmyprocess_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu kafelka RunMyProcess w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji RunMyProcess.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/runmyprocess-tutorial/tutorial_general_04.png

[100]: ./media/runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/runmyprocess-tutorial/tutorial_general_201.png
[202]: ./media/runmyprocess-tutorial/tutorial_general_202.png
[203]: ./media/runmyprocess-tutorial/tutorial_general_203.png

