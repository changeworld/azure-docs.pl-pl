---
title: 'Samouczek: Integracji Azure Active Directory z regulują Data3Sixty Infogix | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i kontrolować Data3Sixty Infogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: aa3109b8-bdbe-45ae-933a-2eb4dc03855c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 3acdfbc7665bec6cfb8821f33e642f29eec84be1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214920"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Samouczek: Integracji Azure Active Directory z regulują Data3Sixty Infogix

Z tego samouczka dowiesz integrowanie regulują Data3Sixty Infogix w usłudze Azure Active Directory (Azure AD).

Integrowanie regulują Data3Sixty Infogix z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do kierowania Data3Sixty Infogix.
- Umożliwia użytkownikom automatycznie pobrać zalogowane Infogix Data3Sixty regulujące (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Infogix Data3Sixty decydować, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Decydować Data3Sixty Infogix logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie regulują Data3Sixty Infogix z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Dodawanie regulują Data3Sixty Infogix z galerii
Aby skonfigurować integrację regulują Data3Sixty Infogix do usługi Azure AD, należy dodać regulują Data3Sixty Infogix z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać regulują Data3Sixty Infogix z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **regulują Data3Sixty Infogix**, wybierz pozycję **regulują Data3Sixty Infogix** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Infogix Data3Sixty będą zarządzały sposobem na liście wyników](./media/infogix-tutorial/tutorial_infogix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Infogix Data3Sixty decydować, w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w regulują Data3Sixty Infogix jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w regulują Data3Sixty Infogix musi określone.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Infogix Data3Sixty decydować, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego regulują Data3Sixty Infogix](#create-an-infogix-data3sixty-govern-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Infogix Data3Sixty regulują połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji regulują Data3Sixty Infogix.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Infogix Data3Sixty decydować, wykonaj następujące czynności:**

1. W portalu Azure na **regulują Data3Sixty Infogix** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/infogix-tutorial/tutorial_infogix_samlbase.png)

3. Na **Infogix Data3Sixty regulują domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i Infogix Data3Sixty regulują domeny pojedynczy informacje logowania jednokrotnego](./media/infogix-tutorial/tutorial_infogix_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL: `https://data3sixty.com/ui`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.data3sixty.com/sso/acs`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i Infogix Data3Sixty regulują domeny pojedynczy informacje logowania jednokrotnego](./media/infogix-tutorial/tutorial_infogix_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.data3sixty.com`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta regulują Data3Sixty Infogix](mailto:data3sixtysupport@infogix.com) uzyskać te wartości.

5. Decydować Data3Sixty Infogix aplikacji oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **atrybuty użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie rejestracji jednokrotnej attb](./media/infogix-tutorial/tutorial_infogix_attribute.png)
    
6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano w obrazie i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |    
    | Imię           | user.givenname |
    | nazwisko        | user.surname |
    | nazwa użytkownika       | User.mail    |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/infogix-tutorial/tutorial_attribute_04.png)

    ![Skonfiguruj Addattb rejestracji jednokrotnej](./media/infogix-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Pozostaw **Namespace** puste.
    
    e. Kliknij przycisk **OK**.

7. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/infogix-tutorial/tutorial_infogix_certificate.png)

8. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/infogix-tutorial/tutorial_general_400.png)
    
9. Na **Infogix Data3Sixty regulują konfiguracji** , kliknij przycisk **skonfigurować regulują Data3Sixty Infogix** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Infogix Data3Sixty regulują konfiguracji](./media/infogix-tutorial/tutorial_infogix_configure.png) 

10. Do konfigurowania rejestracji jednokrotnej na **regulują Data3Sixty Infogix** stronie, musisz wysłać pobrany **certyfikatu (Raw), adres URL Sign-Out identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** do [ Decydować Data3Sixty Infogix zespołem pomocy technicznej](mailto:data3sixtysupport@infogix.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/infogix-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/infogix-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/infogix-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/infogix-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-infogix-data3sixty-govern-test-user"></a>Tworzenie użytkownika testowego regulują Data3Sixty Infogix


Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w regulują Data3Sixty Infogix. Decydować Data3Sixty Infogix obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu Infogix Data3Sixty decydować, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [Infogix Data3Sixty regulują zespołem pomocy technicznej](mailto:data3sixtysupport@infogix.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do kierowania Data3Sixty Infogix.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Infogix Data3Sixty decydować, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **regulują Data3Sixty Infogix**.

    ![Łącze Infogix Data3Sixty będą zarządzały sposobem na liście aplikacji](./media/infogix-tutorial/tutorial_infogix_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka regulują Data3Sixty Infogix w panelu dostępu należy należy pobrać automatycznie zalogowane regulują Data3Sixty Infogix aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/infogix-tutorial/tutorial_general_01.png
[2]: ./media/infogix-tutorial/tutorial_general_02.png
[3]: ./media/infogix-tutorial/tutorial_general_03.png
[4]: ./media/infogix-tutorial/tutorial_general_04.png

[100]: ./media/infogix-tutorial/tutorial_general_100.png

[200]: ./media/infogix-tutorial/tutorial_general_200.png
[201]: ./media/infogix-tutorial/tutorial_general_201.png
[202]: ./media/infogix-tutorial/tutorial_general_202.png
[203]: ./media/infogix-tutorial/tutorial_general_203.png

