---
title: 'Samouczek: Integracji Azure Active Directory z Jamf Pro | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 687453b37e15f5d3dd1fa161f89b6d18f90ba279
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Samouczek: Integracji Azure Active Directory z Jamf Pro

Z tego samouczka dowiesz się integrowanie Jamf Pro z usługi Azure Active Directory (Azure AD).

Integrowanie Jamf Pro z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Jamf Pro.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Jamf Pro (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Jamf Pro, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Jamf Pro logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Jamf Pro z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-jamf-pro-from-the-gallery"></a>Dodawanie Jamf Pro z galerii
Aby skonfigurować integrację usługi Azure AD Jamf Pro, należy dodać Jamf Pro z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Jamf Pro z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Jamf Pro**, wybierz pozycję **Jamf Pro** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Jamf Pro na liście wyników](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i test usługi Azure AD rejestracji jednokrotnej z Jamf Pro w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Jamf Pro jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Jamf Pro.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Jamf Pro, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Jamf Pro](#create-a-jamf-pro-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Jamf Pro, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Jamf Pro.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Jamf Pro, wykonaj następujące czynności:**

1. W portalu Azure na **Jamf Pro** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

3. Na **Jamf Pro domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny Pro Jamf pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. W **identyfikator jednostki** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.jamfcloud.com/saml/SSO`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny Pro Jamf pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Zostanie pobrana wartość identyfikatora rzeczywiste **rejestracji jednokrotnej** sekcji w portalu Jamf Pro, który znajduje się w dalszej części tego samouczka. Można wyodrębnić rzeczywiste **poddomeny** wartość od wartości identyfikatora i używać go **poddomeny** informacje w adres URL logowania i adres URL odpowiedzi.

5. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy Jamf Pro jako administrator.

8. Polecenie **ikonę ustawień** w prawym górnym rogu strony.

    ![Konfiguracja Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

9. Polecenie **jednokrotne**.

    ![Konfiguracja Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure2.png)

10. Przewiń w dół maksymalnie **dostawcy tożsamości** w obszarze **rejestracji jednokrotnej** sekcji, a następnie wykonaj następujące czynności:

    ![Konfiguracja Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure3.png)

    a. Wybierz **innych** jako opcję z **dostawcy tożsamości** listy rozwijanej.

    b. W **inny DOSTAWCA** pole tekstowe, wprowadź **usługi Azure AD**.

    c. Wybierz **adres URL metadanych** jako opcję z **źródła METADANYCH dostawcy tożsamości** listy rozwijanej i Wklej w pole tekstowe następujące, **adres Url metadanych Federacji aplikacji** wartość, która Skopiowano z portalu Azure.

    d. Kopiuj **identyfikator jednostki** vlaue i wklej ją do **identyfikator jednostki** textbox w **Jamf Pro domeny i adres URL** sekcji z portalu Azure.

    >[!NOTE]
    > W tym miejscu `aadsso` jest częścią domeny podrzędnej (czyli w celu odwołania). Użyj tej wartości do ukończenia adres URL logowania jednokrotnego i adres URL odpowiedzi w **Jamf Pro domeny i adres URL** sekcji z portalu Azure.

    e. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-jamf-pro-test-user"></a>Tworzenie użytkownika testowego Jamf Pro

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Jamf Pro, muszą mieć przydzielone do Jamf Pro. W przypadku Jamf Pro Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Jamf Pro jako administrator.

2. Polecenie **ikonę ustawień** w prawym górnym rogu strony.

    ![Dodawanie pracownika](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

3. Polecenie **Jamf Pro kont użytkowników i grupy**.

    ![Dodawanie pracownika](./media/active-directory-saas-jamfprosamlconnector-tutorial/user1.png)

4. Kliknij przycisk **Nowy**.

    ![Dodawanie pracownika](./media/active-directory-saas-jamfprosamlconnector-tutorial/user2.png)

5. Wybierz **tworzenia konta Standard**.

    ![Dodawanie pracownika](./media/active-directory-saas-jamfprosamlconnector-tutorial/user3.png)

6. Na **nowe konto** dailog, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/active-directory-saas-jamfprosamlconnector-tutorial/user4.png)

    a. W **USERNAME** tekstowym, wpisz pełną nazwę BrittaSimon.

    b. Wybierz odpowiednie opcje zgodnie z organizacji do **poziom dostępu**, **Ustaw UPRAWNIENIE**oraz **stan dostępu**.
    
    c. W **imię i nazwisko** tekstowym, wpisz pełną nazwę Simona Britta.

    d. W **adres E-mail** tekstowym, wpisz adres e-mail konta Simona Britta.

    e. W **hasło** tekstowym, wpisz hasło użytkownika.

    f. W **POTWIERDŹ hasło** tekstowym, wpisz hasło użytkownika.

    g. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Jamf Pro.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Jamf Pro, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Jamf Pro**.

    ![Łącze Jamf Pro na liście aplikacji](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Jamf Pro w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Jamf Pro.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_203.png

