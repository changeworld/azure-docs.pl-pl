---
title: 'Samouczek: Integracji Azure Active Directory z Coupa | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Coupa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 440caae63d5d4511b024dda951e76f2f4102f6a7
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35943461"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Samouczek: Integracji Azure Active Directory z Coupa

Z tego samouczka dowiesz się integrowanie Coupa z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Coupa zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Coupa.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Coupa (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Coupa, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Coupa logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Coupa z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-coupa-from-the-gallery"></a>Dodawanie Coupa z galerii
Aby skonfigurować integrację usługi Azure AD Coupa, należy dodać Coupa z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Coupa z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Coupa**, wybierz pozycję **Coupa** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Coupa na liście wyników](./media/coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Coupa w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Coupa jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Coupa musi się.

W Coupa, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Coupa, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Coupa](#create-a-coupa-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Coupa połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Coupa.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Coupa, wykonaj następujące czynności:**

1. W portalu Azure na **Coupa** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/coupa-tutorial/tutorial_coupa_samlbase.png)

3. Na **Coupa domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny Coupa pojedynczy informacje logowania jednokrotnego](./media/coupa-tutorial/tutorial_coupa_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `http://<companyname>.Coupa.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `<companyname>.coupahost.com`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.coupahost.com/sp/ACS.saml2`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Coupa](https://success.coupa.com/Support/Contact_Us?) uzyskać te wartości. wartość adresu URL odpowiedzi otrzyma z metadanych, który znajduje się w dalszej części tego samouczka.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/coupa-tutorial/tutorial_coupa_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/coupa-tutorial/tutorial_general_400.png)

6. Zalogować się do witryny firmy Coupa jako administrator.

7. Przejdź do **Instalator \> zabezpieczeniem**.
   
   ![Opcje zabezpieczeń](./media/coupa-tutorial/ic791900.png "środki zabezpieczające.")

8. W **Zaloguj się za pomocą poświadczeń Coupa** sekcji, wykonaj następujące czynności:

    ![Metadane Coupa SP](./media/coupa-tutorial/ic791901.png "Coupa SP metadanych")
    
    a. Wybierz **zalogowanie się przy użyciu SAML**.
    
    b. Aby pobrać pliku metadanych Coupa do komputera, kliknij przycisk **pobieranie i Importowanie metadanych SP**. Otwórz metadanych i skopiuj **AssertionConsumerService indeksu lub adres URL** wartość, Wklej wartości do **adres URL odpowiedzi** textbox w **Coupa domeny i adres URL** sekcji. 
    
    c. Kliknij przycisk **Przeglądaj** przekazać metadanych pobrany z portalu Azure.
    
    d. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/coupa-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/coupa-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/coupa-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/coupa-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-coupa-test-user"></a>Tworzenie użytkownika testowego Coupa

Aby włączyć użytkowników usługi Azure AD zalogować się do Coupa, musi być przygotowana do Coupa.  

* W przypadku Coupa Inicjowanie obsługi to zadanie ręczne.

**Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **Coupa** witryny firmy jako administrator.

2. W menu u góry kliknij **Instalator**, a następnie kliknij przycisk **użytkowników**.
   
   ![Użytkownicy](./media/coupa-tutorial/ic791908.png "użytkowników")

3. Kliknij przycisk **Utwórz**.
   
   ![Tworzenie użytkowników](./media/coupa-tutorial/ic791909.png "tworzenie użytkowników")

4. W **Utwórz użytkownika** sekcji, wykonaj następujące czynności:
   
   ![Szczegóły użytkownika](./media/coupa-tutorial/ic791910.png "szczegóły użytkownika")
   
   a. Typ **logowania**, **imię**, **nazwisko**, **identyfikator rejestracji jednokrotnej**, **E-mail** atrybutów prawidłowe konto usługi Azure Active Directory, które chcesz udostępnić do powiązanych pól tekstowych.

   b. Kliknij przycisk **Utwórz**.   
   
   >[!NOTE]
   >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z łączem do potwierdzenia konta, zanim staje się aktywny. 
   > 

>[!NOTE]
>Możesz użyć innych Coupa użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Coupa do kont użytkowników usługi AAD. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Coupa.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Coupa, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Coupa**.

    ![Łącze Coupa na liście aplikacji](./media/coupa-tutorial/tutorial_coupa_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Coupa w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Coupa.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/coupa-tutorial/tutorial_general_01.png
[2]: ./media/coupa-tutorial/tutorial_general_02.png
[3]: ./media/coupa-tutorial/tutorial_general_03.png
[4]: ./media/coupa-tutorial/tutorial_general_04.png

[100]: ./media/coupa-tutorial/tutorial_general_100.png

[200]: ./media/coupa-tutorial/tutorial_general_200.png
[201]: ./media/coupa-tutorial/tutorial_general_201.png
[202]: ./media/coupa-tutorial/tutorial_general_202.png
[203]: ./media/coupa-tutorial/tutorial_general_203.png

