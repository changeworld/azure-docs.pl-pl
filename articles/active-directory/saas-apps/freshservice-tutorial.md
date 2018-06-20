---
title: 'Samouczek: Integracji Azure Active Directory z Freshservice | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 28a334cc43f8c14d2bf639b715e891cc6bb8c1cd
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214454"
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Samouczek: Integracji Azure Active Directory z Freshservice

Z tego samouczka dowiesz się integrowanie Freshservice z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Freshservice zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Freshservice
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Freshservice (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Freshservice, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Freshservice jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Freshservice z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-freshservice-from-the-gallery"></a>Dodawanie Freshservice z galerii
Aby skonfigurować integrację usługi Azure AD Freshservice, należy dodać Freshservice z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Freshservice z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Freshservice**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/tutorial_freshservice_search.png)

5. W panelu wyników wybierz **Freshservice**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/tutorial_freshservice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Freshservice w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Freshservice jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Freshservice musi się.

W Freshservice, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Freshservice, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Freshservice](#creating-a-freshservice-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Freshservice połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Freshservice.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Freshservice, wykonaj następujące czynności:**

1. W portalu Azure na **Freshservice** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/freshservice-tutorial/tutorial_freshservice_samlbase.png)

3. Na **Freshservice domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/freshservice-tutorial/tutorial_freshservice_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<democompany>.freshservice.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<democompany>.freshservice.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Freshservice](https://support.freshservice.com/) uzyskać te wartości. 
 
4. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartości certyfikatu.

    ![Konfigurowanie rejestracji jednokrotnej](./media/freshservice-tutorial/tutorial_freshservice_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/freshservice-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Freshservice** , kliknij przycisk **skonfigurować Freshservice** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/freshservice-tutorial/tutorial_freshservice_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Freshservice.

8. W menu u góry kliknij **Admin**.
   
    ![Administrator](./media/freshservice-tutorial/ic790814.png "administratora")

9. W **portalu klienta**, kliknij przycisk **zabezpieczeń**.
   
    ![Zabezpieczenia](./media/freshservice-tutorial/ic790815.png "zabezpieczeń")

10. W **zabezpieczeń** sekcji, wykonaj następujące czynności:
   
    ![Jednokrotne](./media/freshservice-tutorial/ic790816.png "jednokrotne")
   
    a. Przełącznik **jednokrotne**.

    b. Wybierz **logowania jednokrotnego SAML**.

    c. W **adres URL logowania SAML** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    d. W **adresu URL wylogowania** pole tekstowe, Wklej wartość **Sign-Out URL** którego została skopiowana z portalu Azure.

    e. W **odcisk palca certyfikatu zabezpieczeń** pole tekstowe, Wklej **odcisk PALCA** wartość certyfikatów, które zostały skopiowane z portalu Azure.

    f. Kliknij pozycję **Zapisz**
   
> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-freshservice-test-user"></a>Tworzenie użytkownika testowego Freshservice

Aby umożliwić użytkownikom usługi Azure AD zalogować się do FreshService, musi być przygotowana do FreshService. W przypadku FreshService Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **FreshService** witryny firmy jako administrator.

2. W menu u góry kliknij **Admin**.
   
    ![Administrator](./media/freshservice-tutorial/ic790814.png "administratora")

3. W **Zarządzanie użytkownikami** kliknij **jednostek żądających**.
   
    ![Jednostek żądających](./media/freshservice-tutorial/ic790818.png "jednostek żądających")

4. Kliknij przycisk **nowy element żądający**.
   
    ![Nowych jednostek żądających](./media/freshservice-tutorial/ic790819.png "nowych jednostek żądających")

5. W **nowy element żądający** sekcji, wykonaj następujące czynności:
   
    ![Nowy element żądający](./media/freshservice-tutorial/ic790820.png "nowy element żądający")   

    a. Wprowadź **imię** i **E-mail** atrybuty prawidłowe konto usługi Azure Active Directory ustanawiane do powiązanych pól tekstowych.

    b. Kliknij pozycję **Zapisz**.
   
    >[!NOTE]
    >Wiadomość e-mail, łącznie z łączem do potwierdzenia konta, zanim staje się aktywny pobiera właściciela konta usługi Azure Active Directory
    >  

>[!NOTE]
>Możesz użyć innych FreshService użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez FreshService do kont użytkowników usługi AAD.
>  

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Freshservice, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Freshservice**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/freshservice-tutorial/tutorial_freshservice_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Freshservice w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Freshservice.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/freshservice-tutorial/tutorial_general_01.png
[2]: ./media/freshservice-tutorial/tutorial_general_02.png
[3]: ./media/freshservice-tutorial/tutorial_general_03.png
[4]: ./media/freshservice-tutorial/tutorial_general_04.png

[100]: ./media/freshservice-tutorial/tutorial_general_100.png

[200]: ./media/freshservice-tutorial/tutorial_general_200.png
[201]: ./media/freshservice-tutorial/tutorial_general_201.png
[202]: ./media/freshservice-tutorial/tutorial_general_202.png
[203]: ./media/freshservice-tutorial/tutorial_general_203.png

