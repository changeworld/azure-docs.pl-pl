---
title: 'Samouczek: Integracji Azure Active Directory przy użyciu koncentratora innowacji | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i innowacji koncentratora.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d72e4da0-0123-409b-96c2-e613f3f83fb1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 612cce99b1a510dfb53ec4a6c1e2a40185367db1
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320751"
---
# <a name="tutorial-azure-active-directory-integration-with-innovation-hub"></a>Samouczek: Integracji Azure Active Directory przy użyciu koncentratora innowacji

Z tego samouczka dowiesz sposobu integracji Centrum innowacji w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Centrum innowacji zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Centrum innowacji.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do koncentratora innowacji (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu koncentratora innowacji, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Koncentrator innowacji logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Centrum innowacji z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-innovation-hub-from-the-gallery"></a>Dodawanie Centrum innowacji z galerii
Aby skonfigurować integrację Centrum innowacji w usłudze Azure Active Directory, należy dodać Centrum innowacji z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Centrum innowacji z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **innowacji Centrum**, wybierz pozycję **innowacji Centrum** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Koncentrator innowacji na liście wyników](./media/innovationhub-tutorial/tutorial_innovationhub_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu koncentratora innowacji w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Centrum innowacji jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązane użytkownika w Centrum innowacji musi określone.

Aby skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu koncentratora innowacji, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Centrum innowacji](#create-an-innovation-hub-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta innowacji koncentratora, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Centrum innowacji.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu koncentratora innowacji, wykonaj następujące czynności:**

1. W portalu Azure na **innowacji Centrum** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/innovationhub-tutorial/tutorial_innovationhub_samlbase.png)

3. Na **adresy URL i innowacji Centrum domeny** sekcji, wykonaj następujące czynności:

    ![Adresy URL i innowacji Centrum domeny pojedynczy informacje logowania jednokrotnego](./media/innovationhub-tutorial/tutorial_innovationhub_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<domainname>.innohb.com/auth/saml2/login`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<domainname>.innohb.com`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej innowacji koncentratora klienta](mailto:support@readify.net) uzyskać te wartości.

4. Aplikacja Centrum innowacji oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/innovationhub-tutorial/attribute.png)

5. Kliknij przycisk **widoku i edytować wszystkie atrybuty użytkowników** checkbox w **atrybuty użytkownika** sekcji, aby rozwinąć atrybutów. Wykonaj poniższe kroki na każdym z atrybutów wyświetlanych-

    | Nazwa atrybutu | Wartość atrybutu | Wartość Namespace|
    | ---------------| --------------- |----------------|
    | Nazwa wyświetlana | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/innovationhub-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/innovationhub-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Z **wartości Namespace** listy, wpisz wartość przestrzeni nazw wyświetlany dla danego wiersza.

    e. Kliknij przycisk **OK**.

4. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika.

    ![Łącze pobierania certyfikatu](./media/innovationhub-tutorial/tutorial_innovationhub_certificate.png)

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/innovationhub-tutorial/tutorial_general_400.png)

6. Skonfigurować logowanie jednokrotne w **innowacji Centrum** stronie, musisz wysłać skopiowanych **adres Url metadanych Federacji** do [innowacji Centrum pomocy technicznej zespół](mailto:support@readify.net). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/innovationhub-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/innovationhub-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/innovationhub-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/innovationhub-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-innovation-hub-test-user"></a>Tworzenie użytkownika testowego innowacji Centrum

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta innowacji Centrum. Koncentrator innowacji obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu innowacji koncentratora, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [innowacji Centrum pomocy technicznej zespół](mailto:support@readify.net).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do innowacji koncentratora.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Centrum innowacji, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **innowacji Centrum**.

    ![Łącze Centrum innowacji na liście aplikacji](./media/innovationhub-tutorial/tutorial_innovationhub_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Centrum innowacji w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Centrum innowacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
<!--Image references-->

[1]: ./media/innovationhub-tutorial/tutorial_general_01.png
[2]: ./media/innovationhub-tutorial/tutorial_general_02.png
[3]: ./media/innovationhub-tutorial/tutorial_general_03.png
[4]: ./media/innovationhub-tutorial/tutorial_general_04.png

[100]: ./media/innovationhub-tutorial/tutorial_general_100.png

[200]: ./media/innovationhub-tutorial/tutorial_general_200.png
[201]: ./media/innovationhub-tutorial/tutorial_general_201.png
[202]: ./media/innovationhub-tutorial/tutorial_general_202.png
[203]: ./media/innovationhub-tutorial/tutorial_general_203.png

