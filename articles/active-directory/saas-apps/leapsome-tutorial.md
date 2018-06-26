---
title: 'Samouczek: Integracji Azure Active Directory z Leapsome | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.openlocfilehash: b23a93db7912aa25b420157241c41533f4f48a27
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938443"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Samouczek: Integracji Azure Active Directory z Leapsome

Z tego samouczka dowiesz się integrowanie Leapsome z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Leapsome zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Leapsome.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Leapsome (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Leapsome, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Leapsome logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Leapsome z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-leapsome-from-the-gallery"></a>Dodawanie Leapsome z galerii
Aby skonfigurować integrację usługi Azure AD Leapsome, należy dodać Leapsome z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Leapsome z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Leapsome**, wybierz pozycję **Leapsome** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Leapsome na liście wyników](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Leapsome w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Leapsome jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Leapsome musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Leapsome, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Leapsome](#create-a-leapsome-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Leapsome połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Leapsome.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Leapsome, wykonaj następujące czynności:**

1. W portalu Azure na **Leapsome** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

3. Na **Leapsome domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny Leapsome pojedynczy informacje logowania jednokrotnego](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL: `https://www.leapsome.com`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny Leapsome pojedynczy informacje logowania jednokrotnego](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`
     
    > [!NOTE] 
    > Poprzedni adres URL odpowiedzi i logowania jednokrotnego wartość adresu URL nie jest rzeczywistą wartość. Zamierzasz wykonać aktualizację je razem z rzeczywistymi wartościami, który znajduje się w dalszej części tego samouczka.

5. Aplikacja Leapsome oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **atrybuty użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu | Przestrzeń nazw |
    | ---------------| --------------- | --------- |   
    | Imię | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | nazwisko | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | tytuł | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Obraz | Adres URL obrazu pracownika | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > Wartość atrybutu obraz nie jest prawdziwe. Zaktualizuj tę wartość z adresem URL rzeczywistego obrazu. Aby uzyskać to wartość, skontaktuj się z [zespołem pomocy technicznej klienta Leapsome](mailto:support@leapsome.com).
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. W **Namespace** tekstowym, wpisz identyfikator uri przestrzeni nazw dla tego wiersza.
    
    e. Kliknij przycisk **Ok**

7. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

8. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/leapsome-tutorial/tutorial_general_400.png)
    
9. Na **konfiguracji Leapsome** , kliknij przycisk **skonfigurować Leapsome** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja Leapsome](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

10. W oknie przeglądarki innej witryny sieci web Zaloguj się do Leapsome jako Administrator zabezpieczeń.

11. W górnym rogu kliknij pozycję Ustawienia logo, a następnie kliknij **ustawienia administratora**. 

    ![Zestaw Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

12. Na pasku menu po lewej stronie kliknij **pojedynczy znak na rejestracji jednokrotnej (SSO)** i na **na języku SAML rejestracji jednokrotnej (SSO)** strony wykonaj następujące czynności:
    
    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Wybierz **na podstawie Włącz SAML logowania jednokrotnego**.

    b. Kopia **adres URL logowania (wskaż użytkowników w tym miejscu Rozpocznij logowania)** i wklej go do **adres URL logowania** textbox w **Leapsome domeny i adres URL** sekcji z portalu Azure.

    c. Kopiuj **adres URL odpowiedzi (recieves odpowiedzi od dostawcy tożsamości)** i wklej go do **adres URL odpowiedzi służący** textbox w **Leapsome domeny i adres URL** sekcji z portalu Azure.

    d. W **(udostępniony przez dostawcę tożsamości) adres URL logowania SSO** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, który został skopiowany z portalu Azure.

    e. Skopiuj certyfikat został pobrany z portalu Azure, bez — certyfikat rozpoczęcia i zakończenia certyfikatu — komentarze, a następnie wklej je **certyfikatu (udostępniony przez dostawcę tożsamości)** pola tekstowego.

    f. Kliknij przycisk **ustawienia logowania jednokrotnego aktualizacji**.
    
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/leapsome-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/leapsome-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/leapsome-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/leapsome-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-leapsome-test-user"></a>Tworzenie użytkownika testowego Leapsome

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Leapsome. Praca z [zespołem pomocy technicznej klienta Leapsome](mailto:support@leapsome.com) do dodawania użytkowników lub domeny, które muszą być białej Leapsome platformy. Jeśli domena jest dodawany przez zespół, użytkownicy będą uzyskać automatycznie przygotowana do platformy Leapsome. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Leapsome.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Leapsome, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Leapsome**.

    ![Łącze Leapsome na liście aplikacji](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Leapsome w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Leapsome.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png

