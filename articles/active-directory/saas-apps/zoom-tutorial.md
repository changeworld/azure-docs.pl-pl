---
title: 'Samouczek: Integracji Azure Active Directory z powiększenia | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i powiększenia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2017
ms.author: jeedes
ms.openlocfilehash: 33b5c918fc5de301d0f6df73bda53e53c1446088
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938838"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Samouczek: Integracji Azure Active Directory z powiększenia

Z tego samouczka dowiesz się integrowanie powiększenia w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD powiększenia zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do powiększenia.
- Umożliwia użytkownikom automatycznie pobrać zalogowane powiększenie (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z powiększenia, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Powiększenie logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie powiększenia z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-zoom-from-the-gallery"></a>Dodawanie powiększenia z galerii
Aby skonfigurować integrację powiększenia do usługi Azure AD, należy dodać powiększenia z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać powiększenia z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **powiększenie**, wybierz pozycję **powiększenie** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Powiększenie na liście wyników](./media/zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z powiększenia w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w powiększenia do użytkownika w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w powiększenia musi się.

Powiększenie, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z powiększenia, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego powiększenia](#create-a-zoom-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta powiększenia połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji powiększenia.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z powiększenia, wykonaj następujące czynności:**

1. W portalu Azure na **powiększenie** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/zoom-tutorial/tutorial_zoom_samlbase.png)

3. Na **powiększenie domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i powiększenia domeny pojedynczy informacje logowania jednokrotnego](./media/zoom-tutorial/tutorial_zoom_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.zoom.us`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `<companyname>.zoom.us`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta powiększenie](https://support.zoom.us/hc) uzyskać te wartości.

4. Aplikacja powiększenia oczekuje potwierdzenia języka SAML w określonym formacie, musisz dodać mapowania atrybutu niestandardowego do konfiguracji atrybuty tokenu SAML. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/zoom-tutorial/tutorial_attribute.png)

5. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na powyższej ilustracji i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu | Wartość Namespace |
    | ------------------- | -----------|--------- |    
    | Adres e-mail | User.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail`|
    | Imię | user.givenname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`|
    | Nazwisko | user.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname `|
    | Numer telefonu | User.telephonenumber | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone`|
    | Dział | User.Department | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department`|

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/zoom-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/zoom-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. W **Namespace** tekstowym, wpisz wartość przestrzeni nazw wyświetlany dla danego wiersza.
    
    e. Kliknij przycisk **OK**. 
 
6. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/zoom-tutorial/tutorial_zoom_certificate.png)

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/zoom-tutorial/tutorial_general_400.png)

8. Na **powiększenie konfiguracji** , kliknij przycisk **skonfigurować powiększenie** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja powiększenia](./media/zoom-tutorial/tutorial_zoom_configure.png)

9. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy powiększenia.

10. Kliknij przycisk **rejestracji jednokrotnej** kartę.
   
    ![Karta rejestracji jednokrotnej](./media/zoom-tutorial/IC784700.png "logowanie jednokrotne")

11. Kliknij przycisk **zabezpieczeniem** karcie, a następnie przejdź do **rejestracji jednokrotnej** ustawienia.

12. W sekcji rejestracji jednokrotnej wykonaj następujące czynności:
   
    ![Pojedynczy znak w sekcji](./media/zoom-tutorial/IC784701.png "logowanie jednokrotne")
   
    a. W **adres URL logowania strony** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.
   
    b. W **adres URL strony wylogowania** pole tekstowe, Wklej wartość **Sign-Out URL** którego została skopiowana z portalu Azure.
     
    c. Otwórz w Notatniku certyfikatu zakodowanego base-64, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikat dostawcy tożsamości** pola tekstowego.

    d. W **wystawcy** pole tekstowe, Wklej wartość **identyfikator jednostki SAML** którego została skopiowana z portalu Azure. 

    e. Kliknij pozycję **Zapisz**.

    > [!NOTE] 
    > Aby uzyskać więcej informacji odwiedź stronę dokumentacji powiększenia [https://zoomus.zendesk.com/hc/en-us/articles/115005887566](https://zoomus.zendesk.com/hc/en-us/articles/115005887566)

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/zoom-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/zoom-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/zoom-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/zoom-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-zoom-test-user"></a>Tworzenie użytkownika testowego powiększenia

Aby umożliwić użytkownikom zalogować się do powiększenia usługi Azure AD, musi być przygotowana do powiększenia. W przypadku powiększenia Inicjowanie obsługi to zadanie ręczne.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby udostępnić konta użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Twojego **powiększenie** witryny firmy jako administrator.
 
2. Kliknij przycisk **zarządzania kontami** , a następnie kliknij pozycję **Zarządzanie użytkownikami**.

3. W sekcji Zarządzanie użytkownikami kliknij **dodawania użytkowników**.
   
    ![Zarządzanie użytkownikami](./media/zoom-tutorial/IC784703.png "Zarządzanie użytkownikami")

4. Na **dodawania użytkowników** wykonaj następujące czynności:
   
    ![Dodawanie użytkowników](./media/zoom-tutorial/IC784704.png "Dodawanie użytkowników")
   
    a. Jako **typ użytkownika**, wybierz pozycję **podstawowe**.

    b. W **wiadomości E-mail** tekstowym, wpisz adres e-mail prawidłowe usługi Azure AD konto ma chcesz udostępnić.

    c. Kliknij pozycję **Add** (Dodaj).

> [!NOTE]
> Możesz użyć innych powiększenia użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez powiększenia do świadczenia usługi Azure Active Directory kont użytkowników.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do powiększenia.

![Przypisanie roli użytkownika][200] 

**Aby przypisać powiększenia Simona Britta, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **powiększenie**.

    ![Łącze powiększenia na liście aplikacji](./media/zoom-tutorial/tutorial_zoom_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka powiększenia w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji powiększenia.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zoom-tutorial/tutorial_general_01.png
[2]: ./media/zoom-tutorial/tutorial_general_02.png
[3]: ./media/zoom-tutorial/tutorial_general_03.png
[4]: ./media/zoom-tutorial/tutorial_general_04.png

[100]: ./media/zoom-tutorial/tutorial_general_100.png

[200]: ./media/zoom-tutorial/tutorial_general_200.png
[201]: ./media/zoom-tutorial/tutorial_general_201.png
[202]: ./media/zoom-tutorial/tutorial_general_202.png
[203]: ./media/zoom-tutorial/tutorial_general_203.png

