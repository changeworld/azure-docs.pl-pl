---
title: 'Samouczek: Integracji Azure Active Directory z przeglądem wyczyść | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i wyczyść przeglądu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: jeedes
ms.openlocfilehash: aa3514d348b812a96b55c4d47950cd064d391dc1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231546"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Samouczek: Integracji Azure Active Directory z przeglądem wyczyść

Z tego samouczka dowiesz się sposobu integracji z usługą Azure Active Directory (Azure AD) Wyczyść przeglądu.

Integracja z usługą Azure AD wyczyść przeglądu zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do przeglądu Wyczyść.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do przeglądu Wyczyść (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z przeglądem Wyczyść, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Wyczyść przeglądu logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie wyczyść przeglądu z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-clear-review-from-the-gallery"></a>Dodawanie wyczyść przeglądu z galerii
Aby skonfigurować integrację usługi Azure AD wyczyść przeglądu, należy dodać wyczyść przeglądu z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać wyczyść przeglądu z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **wyczyść przeglądu**, wybierz pozycję **wyczyść przeglądu** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Wyczyść przeglądu na liście wyników](./media/clearreview-tutorial/tutorial_clearreview_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z przeglądu zwykłego użytkownika testowego o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w przeglądzie wyczyść jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w przeglądzie wyczyść musi się.

W przeglądzie Wyczyść, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z przeglądem Wyczyść, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego wyczyść przeglądu](#create-a-clear-review-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta wyczyść przeglądu, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji wyczyść przeglądu.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z przeglądem Wyczyść, wykonaj następujące czynności:**

1. W portalu Azure na **wyczyść przeglądu** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/clearreview-tutorial/tutorial_clearreview_samlbase.png)

3. Na **adresy URL i wyczyść domeny przeglądu** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IdP zainicjował** tryb:

    ![Wyczyść domeny przeglądu i adresów URL jednym informacje logowania jednokrotnego](./media/clearreview-tutorial/tutorial_clearreview_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<customer name>.clearreview.com/sso/metadata/`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<customer name>.clearreview.com/sso/acs/`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Wyczyść domeny przeglądu i adresów URL jednym informacje logowania jednokrotnego](./media/clearreview-tutorial/tutorial_clearreview_url_sp.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca:`https://<customer name>.clearreview.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej wyczyść przeglądu](https://clearreview.com/contact/) uzyskać te wartości.

5. Wyczyść aplikacji przejrzyj oczekiwać, że wartość identyfikatora unikatowego użytkownika oświadczenia identyfikatora nazwy. Należy zmapować wartość identyfikatora użytkownika do **user.mail**.

    ![W sekcji atrybutu](./media/clearreview-tutorial/attribute.png)


6. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/clearreview-tutorial/tutorial_clearreview_certificate.png)

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/clearreview-tutorial/tutorial_general_400.png)

8. Na **wyczyść Przejrzyj konfigurację** , kliknij przycisk **skonfigurować wyczyść przeglądu** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Wyczyść Przejrzyj konfigurację](./media/clearreview-tutorial/tutorial_clearreview_configure.png) 

9. Skonfigurować logowanie jednokrotne w **wyczyść przeglądu** po stronie Otwórz **wyczyść przeglądu** portalu przy użyciu poświadczeń administratora.

10. Wybierz **Admin** z lewym pasku nawigacyjnym.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

11. Wybierz **zmiany** w dolnej części strony.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

12. Wykonaj następujące kroki **ustawień rejestracji jednokrotnej** strony

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. W **adres URL wystawcy** pole tekstowe, Wklej wartość **identyfikator jednostki SAML** którego została skopiowana z portalu Azure.

    b. W **punktu końcowego SAML** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.    

    c. W **punktu końcowego SLO** pole tekstowe, Wklej wartość **adres URL logowania jednokrotnego usługi** którego została skopiowana z portalu Azure. 

    d. Otwórz pobranego certyfikatu w programie Notatnik i Wklej zawartość **certyfikatu X.509** pola tekstowego.   

13. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/clearreview-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/clearreview-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/clearreview-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/clearreview-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-clear-review-test-user"></a>Tworzenie użytkownika testowego wyczyść przeglądu

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w przeglądzie Wyczyść. We współpracy z [zespołem pomocy technicznej wyczyść przeglądu](https://clearreview.com/contact/) Aby dodać użytkowników na platformie wyczyść przeglądu.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do przeglądu Wyczyść.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta wyczyść przeglądu, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **wyczyść przeglądu**.

    ![Wyczyść przeglądu łącza na liście aplikacji](./media/clearreview-tutorial/tutorial_clearreview_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka przeglądu wyczyść w panelu dostępu należy należy pobrać automatycznie zalogowane do aplikacji wyczyść przeglądu.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clearreview-tutorial/tutorial_general_01.png
[2]: ./media/clearreview-tutorial/tutorial_general_02.png
[3]: ./media/clearreview-tutorial/tutorial_general_03.png
[4]: ./media/clearreview-tutorial/tutorial_general_04.png

[100]: ./media/clearreview-tutorial/tutorial_general_100.png

[200]: ./media/clearreview-tutorial/tutorial_general_200.png
[201]: ./media/clearreview-tutorial/tutorial_general_201.png
[202]: ./media/clearreview-tutorial/tutorial_general_202.png
[203]: ./media/clearreview-tutorial/tutorial_general_203.png
