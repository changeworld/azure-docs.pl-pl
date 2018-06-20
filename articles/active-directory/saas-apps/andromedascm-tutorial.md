---
title: 'Samouczek: Integracji Azure Active Directory z Andromeda | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Andromeda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jeedes
ms.openlocfilehash: 09f2f854e3b5a70938b6c47bb7dfb5fdf6d52df3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223215"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Samouczek: Integracji Azure Active Directory z Andromeda

Z tego samouczka dowiesz się integrowanie Andromeda z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Andromeda zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Andromeda.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Andromeda (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Andromeda, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Andromeda logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Andromeda z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-andromeda-from-the-gallery"></a>Dodawanie Andromeda z galerii
Aby skonfigurować integrację usługi Azure AD Andromeda, należy dodać Andromeda z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Andromeda z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Andromeda**, wybierz pozycję **Andromeda** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Andromeda na liście wyników](./media/andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Andromeda w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Andromeda jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Andromeda musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Andromeda, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Andromeda](#create-an-andromeda-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Andromeda połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Andromeda.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Andromeda, wykonaj następujące czynności:**

1. W portalu Azure na **Andromeda** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. Na **Andromeda domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny Andromeda pojedynczy informacje logowania jednokrotnego](./media/andromedascm-tutorial/tutorial_andromedascm_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<tenantURL>.ngcxpress.com/`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny Andromeda pojedynczy informacje logowania jednokrotnego](./media/andromedascm-tutorial/tutorial_andromedascm_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`
     
    > [!NOTE] 
    > Powyższe nie jest rzeczywistą wartość. Wartość spowoduje zaktualizowanie o rzeczywisty identyfikator, adres URL odpowiedzi i URL logowania jednokrotnego, który znajduje się w dalszej części tego samouczka.

5. Aplikacja Andromeda oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **atrybuty użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie rejestracji jednokrotnej attb](./media/andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > Czyści podczas konfigurowania te definicje przestrzeni nazw.
    
6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano w obrazie i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | -------------------- |    
    | rola        | Określonej roli aplikacji |
    | type        | Typ aplikacji |
    | Firmy       | Nazwa firmy    |

    > [!NOTE]
    > Nie ma wartości rzeczywistych. Te wartości są tylko w celu demonstracyjnej, użyj ról w Twojej organizacji.

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/andromedascm-tutorial/tutorial_attribute_04.png)

    ![Skonfiguruj Addattb rejestracji jednokrotnej](./media/andromedascm-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Pozostaw **Namespace** puste.
    
    e. Kliknij przycisk **OK**.

7. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/andromedascm-tutorial/tutorial_general_400.png)
    
9. Na **konfiguracji Andromeda** , kliknij przycisk **skonfigurować Andromeda** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Logowanie do witryny firmy Andromeda jako administrator.

11. W górnej części pasku menu kliknij **Admin** i przejdź do **administracji**.

    ![Administrator Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. Po lewej stronie paska narzędzi w obszarze **interfejsów** kliknij **Konfiguracja SAML**.

    ![Andromeda saml](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. Na **Konfiguracja SAML** sekcji strony, należy wykonać następujące czynności:

    ![Andromeda konfiguracji](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Sprawdź **włączenia funkcji logowania jednokrotnego z SAML**.

    b. W obszarze **informacji Andromeda** sekcji, skopiuj **tożsamości SP** i wklej go do **identyfikator** pole tekstowe z **Andromeda domeny i adres URL** sekcji.

    c. Kopiuj **adres URL klienta** i wklej go do **adres URL odpowiedzi** pole tekstowe z **Andromeda domeny i adres URL** sekcji.

    d. Kopiuj **adres URL logowania** i wklej go do **adres URL logowania** pole tekstowe z **Andromeda domeny i adres URL** sekcji.

    e. W obszarze **dostawca tożsamości SAML** sekcji, wpisz nazwę dostawców tożsamości.

    f. W **pojedynczy znak na punkt końcowy** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** , które zostały skopiowane z portalu Azure.

    g. Otwórz pobrany **certyfikatu szyfrowania Base64** z portalu Azure w programie Notatnik, wklej ją do **certyfikat X 509** pola tekstowego.
    
    h. Mapowanie następujące atrybuty z odpowiednią wartość w celu ułatwienia logowania SSO z usługi Azure AD. **Identyfikator użytkownika** atrybut jest wymagany do logowania. Do inicjowania obsługi, **E-mail**, **firmy**, **UserType**, i **roli** są wymagane. W tej sekcji zdefiniowano atrybutów mapowania (nazwy i wartości) które skorelowania zdefiniowane w portalu Azure

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/andromedascm-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/andromedascm-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/andromedascm-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/andromedascm-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-andromeda-test-user"></a>Tworzenie użytkownika testowego Andromeda

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Andromeda. Andromeda obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu Andromeda, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta Andromeda](https://www.ngcsoftware.com/support/).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Andromeda.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Andromeda, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Andromeda**.

    ![Łącze Andromeda na liście aplikacji](./media/andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Andromeda w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Andromeda.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/andromedascm-tutorial/tutorial_general_203.png
