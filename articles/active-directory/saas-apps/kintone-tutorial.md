---
title: 'Samouczek: Integracji Azure Active Directory z Kintone | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: 3497bfd261ec46cd66eb4f0c904200732c168cbf
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231597"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Samouczek: Integracji Azure Active Directory z Kintone

Z tego samouczka dowiesz się integrowanie Kintone z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Kintone zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Kintone
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Kintone (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Kintone, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Kintone logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Kintone z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-kintone-from-the-gallery"></a>Dodawanie Kintone z galerii
Aby skonfigurować integrację usługi Azure AD Kintone, należy dodać Kintone z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Kintone z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Kintone**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/tutorial_kintone_search.png)

5. W panelu wyników wybierz **Kintone**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/tutorial_kintone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Kintone w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Kintone jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Kintone musi się.

W Kintone, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Kintone, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Kintone](#creating-a-kintone-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Kintone połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Kintone.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Kintone, wykonaj następujące czynności:**

1. W portalu Azure na **Kintone** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/kintone-tutorial/tutorial_kintone_samlbase.png)

3. Na **Kintone domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/kintone-tutorial/tutorial_kintone_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.kintone.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://<companyname>.cybozu.com`|
    | `https://<companyname>.kintone.com`|

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Kintone](https://www.kintone.com/contact/) uzyskać te wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/kintone-tutorial/tutorial_kintone_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/kintone-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Kintone** , kliknij przycisk **skonfigurować Kintone** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/kintone-tutorial/tutorial_kintone_configure.png) 

7. W oknie przeglądarki innej witryny sieci web, zaloguj się do Twojego **Kintone** witryny firmy jako administrator.

8. Kliknij przycisk **ustawienia**.
   
    ![Ustawienia](./media/kintone-tutorial/ic785879.png "ustawienia")

9. Kliknij przycisk **użytkownicy i administrowanie systemem**.
   
    ![Użytkownicy i administrowanie systemem](./media/kintone-tutorial/ic785880.png "użytkowników & administracji systemu")

10. W obszarze **Administracja systemu \> zabezpieczeń** kliknij **logowania**.
   
    ![Logowania](./media/kintone-tutorial/ic785881.png "logowania")

11. Kliknij przycisk **uwierzytelnianie Włącz SAML**.
   
    ![Uwierzytelnianie SAML](./media/kintone-tutorial/ic785882.png "uwierzytelnianie SAML")

12. W sekcji uwierzytelnianie SAML wykonaj następujące czynności:
    
    ![Uwierzytelnianie SAML](./media/kintone-tutorial/ic785883.png "uwierzytelnianie SAML")
    
    a. W **adres URL logowania** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.
   
    b. W **adresu URL wylogowania** pole tekstowe, Wklej wartość **Sign-Out URL** którego została skopiowana z portalu Azure.
    
    c. Kliknij przycisk **Przeglądaj** przekazać pobranego certyfikatu.
    
    d. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kintone-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-kintone-test-user"></a>Tworzenie użytkownika testowego Kintone

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Kintone, musi być przygotowana do Kintone.  
W przypadku Kintone Inicjowanie obsługi to zadanie ręczne.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby udostępnić konta użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Twojego **Kintone** witryny firmy jako administrator.

2. Kliknij przycisk **ustawienie**.
   
    ![Ustawienia](./media/kintone-tutorial/ic785879.png "ustawienia")

3. Kliknij przycisk **użytkownicy i administrowanie systemem**.
   
    ![Administracja systemu & użytkownika](./media/kintone-tutorial/ic785880.png "Administracja systemu & użytkownika")

4. W obszarze **Administracja użytkownikami**, kliknij przycisk **działów i użytkownicy**.
   
    ![Dział & użytkowników](./media/kintone-tutorial/ic785888.png "działu & użytkowników")

5. Kliknij przycisk **nowego użytkownika**.
   
    ![Nowi użytkownicy](./media/kintone-tutorial/ic785889.png "nowych użytkowników")

6. W **nowego użytkownika** sekcji, wykonaj następujące czynności:
   
    ![Nowi użytkownicy](./media/kintone-tutorial/ic785890.png "nowych użytkowników")
   
    a. Wpisz **Nazwa wyświetlana**, **nazwa logowania**, **nowe hasło**, **Potwierdź hasło**, **adres E-mail**oraz innych szczegółów prawidłowego konta usługi AAD ustanawiane do powiązanych pól tekstowych.
 
    b. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Możesz użyć innych Kintone użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Kintone do kont użytkowników usługi AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Kintone.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Kintone, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Kintone**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/kintone-tutorial/tutorial_kintone_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Kintone w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Kintone.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kintone-tutorial/tutorial_general_01.png
[2]: ./media/kintone-tutorial/tutorial_general_02.png
[3]: ./media/kintone-tutorial/tutorial_general_03.png
[4]: ./media/kintone-tutorial/tutorial_general_04.png

[100]: ./media/kintone-tutorial/tutorial_general_100.png

[200]: ./media/kintone-tutorial/tutorial_general_200.png
[201]: ./media/kintone-tutorial/tutorial_general_201.png
[202]: ./media/kintone-tutorial/tutorial_general_202.png
[203]: ./media/kintone-tutorial/tutorial_general_203.png

