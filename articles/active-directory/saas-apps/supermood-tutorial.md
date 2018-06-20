---
title: 'Samouczek: Integracji Azure Active Directory z Supermood | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Supermood.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: 9cd6a373f23c69f920d0e46bad368f17c8d21035
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215002"
---
# <a name="tutorial-azure-active-directory-integration-with-supermood"></a>Samouczek: Integracji Azure Active Directory z Supermood

Z tego samouczka dowiesz się integrowanie Supermood z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Supermood zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Supermood.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Supermood (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Supermood, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Supermood logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Supermood z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-supermood-from-the-gallery"></a>Dodawanie Supermood z galerii
Aby skonfigurować integrację Supermood do usługi Azure AD, należy dodać Supermood z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Supermood z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Supermood**, wybierz pozycję **Supermood** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Supermood na liście wyników](./media/supermood-tutorial/tutorial_supermood_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Supermood w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Supermood jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Supermood musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Supermood, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Supermood](#create-a-supermood-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Supermood połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Supermood.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Supermood, wykonaj następujące czynności:**

1. W portalu Azure na **Supermood** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/supermood-tutorial/tutorial_supermood_samlbase.png)

3. Na **Supermood domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny supermood pojedynczy informacje logowania jednokrotnego](./media/supermood-tutorial/tutorial_supermood_url.png)

    a. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**.

    b. Jeśli chcesz skonfigurować aplikację w **IDP** inicjowane w trybie **stan przekazywania** tekstowym, wpisz adres URL: `https://supermood.co/auth/sso/saml20`

    c. Jeśli chcesz skonfigurować aplikację w **SP** inicjowane w trybie **adres URL logowania** tekstowym, wpisz adres URL: `https://supermood.co/app/#!/loginv2`

4. Aplikacja supermood oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **atrybuty użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/supermood-tutorial/tutorial_supermood_attribute.png)

5. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |    
    | Imię | user.givenname |
    | Nazwisko | user.surname |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/supermood-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/supermood-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Pozostaw **Namespace** puste.
    
    d. Kliknij przycisk **Ok**

6. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika.

    ![Łącze pobierania certyfikatu](./media/supermood-tutorial/tutorial_supermood_certificate.png) 

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/supermood-tutorial/tutorial_general_400.png)

8. Przejdź do panelu administratora Twojej Supermood.co jako administratora zabezpieczeń.

9. Polecenie **Moje konto** (na dole po lewej) i **pojedynczy znak na rejestracji jednokrotnej (SSO)**.

    ![Jeden certyfikat](./media/supermood-tutorial/tutorial_supermood_single.png)
10. Na **konfiguracje Your SAML 2.0**, kliknij przycisk **Dodaj konfiguracji protokołu SAML 2.0 na domenę poczty e-mail**.

    ![Dodaj certyfikat](./media/supermood-tutorial/tutorial_supermood_add.png)

11. Na **Dodaj konfiguracji protokołu SAML 2.0 na domenę poczty e-mail**. sekcja, wykonaj następujące czynności:

    ![Saml certyfikatu](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. W **domenę poczty e-mail dla tego dostawcy tożsamości** tekstowym, wpisz domenę.

    b. W **używać adresu URL metadanych** pole tekstowe, Wklej **adres Url metadanych Federacji aplikacji** którego została skopiowana z portalu Azure.

    c. Kliknij pozycję **Add** (Dodaj).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/supermood-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/supermood-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/supermood-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/supermood-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-supermood-test-user"></a>Tworzenie użytkownika testowego Supermood

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Supermood. Supermood obsługę w czasie, który jest domyślnie włączona dla użytkowników, których wiadomości e-mail należy do domeny, które są dodawane podczas konfiguracji na końcu Supermood. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu Supermood, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Supermood](mailto:hello@supermood.fr).


### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Supermood.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Supermood, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Supermood**.

    ![Łącze Supermood na liście aplikacji](./media/supermood-tutorial/tutorial_supermood_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Supermood w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Supermood.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/supermood-tutorial/tutorial_general_01.png
[2]: ./media/supermood-tutorial/tutorial_general_02.png
[3]: ./media/supermood-tutorial/tutorial_general_03.png
[4]: ./media/supermood-tutorial/tutorial_general_04.png

[100]: ./media/supermood-tutorial/tutorial_general_100.png

[200]: ./media/supermood-tutorial/tutorial_general_200.png
[201]: ./media/supermood-tutorial/tutorial_general_201.png
[202]: ./media/supermood-tutorial/tutorial_general_202.png
[203]: ./media/supermood-tutorial/tutorial_general_203.png

