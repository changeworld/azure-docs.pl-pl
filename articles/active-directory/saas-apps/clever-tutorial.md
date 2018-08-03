---
title: 'Samouczek: Integracja usługi Azure Active Directory z Clever | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Clever.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 483d03fcc72e0a93111d10b0221164459de27d12
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431865"
---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>Samouczek: Integracja usługi Azure Active Directory z Clever

W tym samouczku dowiesz się, jak zintegrować Clever w usłudze Azure Active Directory (Azure AD).

Integrowanie Clever z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Clever.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Clever (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Clever, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Sprytne logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Clever z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-clever-from-the-gallery"></a>Dodawanie Clever z galerii
Aby skonfigurować integrację Clever w usłudze Azure AD, należy dodać Clever z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Clever z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Clever**, wybierz opcję **Clever** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Sprytne na liście wyników](./media/clever-tutorial/tutorial_clever_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Clever w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Clever do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Clever musi można ustanowić.

W Clever, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Clever, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Sprytne](#create-a-clever-test-user)**  — aby odpowiednikiem Britta Simon w Clever połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne do aplikacji inteligentne.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Clever, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Clever** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/clever-tutorial/tutorial_clever_samlbase.png)

1. Na **Sprytne domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Sprytne domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/clever-tutorial/tutorial_clever_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://clever.com/in/<companyname>`

    b. W **identyfikator** pole tekstowe, wpisz adres URL: `https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Sprytne](https://clever.com/about/contact/) aby zyskać tę wartość.

1. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.
    
    ![Konfigurowanie logowania jednokrotnego](./media/clever-tutorial/tutorial_metadataurl.png)

1. Sprytne aplikacja oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych, aby Twoje **atrybuty tokenu języka SAML** konfiguracji.

    Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie logowania jednokrotnego](./media/clever-tutorial/tutorial_clever_07.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu  | Wartość atrybutu |
    | --------------- | -------------------- |
    | clever.Teacher.Credentials.district_username|user.userprincipalname|
    | clever.student.Credentials.district_username| user.userprincipalname |
    | Imię  | user.givenname |
    | nazwisko  | user.surname |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/clever-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie logowania jednokrotnego](./media/clever-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Pozostaw **Namespace** puste pole tekstowe.
    
    d. Kliknij przycisk **OK**.
    
1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/clever-tutorial/tutorial_general_400.png)

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do swojej witryny Sprytne firmy.

1. Na pasku narzędzi kliknij **błyskawiczne logowania**.

    ![Błyskawiczne logowania](./media/clever-tutorial/ic798984.png "błyskawiczne logowania")

    > [!NOTE]
    > Przed przetestowaniem logowanie jednokrotne, należy skontaktować się z [zespołem pomocy technicznej klienta Sprytne](https://clever.com/about/contact/) do włączenia funkcji logowania jednokrotnego Office 365 w zapleczu.

1. Na **błyskawiczne logowania** strony, wykonaj następujące czynności:
    
      ![Błyskawiczne logowania](./media/clever-tutorial/ic798985.png "błyskawiczne logowania")
    
      a. Typ **adres URL logowania**.
    
      >[!NOTE]
      >**Adres URL logowania** jest wartość niestandardową. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Sprytne](https://clever.com/about/contact/) aby zyskać tę wartość.
    
      b. Jako **System obsługi tożsamości**, wybierz opcję **usług AD FS**.

      c. W **adres URL metadanych** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** wartości, które zostały skopiowane z witryny Azure portal.
    
      d. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/clever-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/clever-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/clever-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/clever-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-clever-test-user"></a>Tworzenie użytkownika testowego inteligentne

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Clever, musi być obsługiwana w Clever.

W przypadku Clever, pracować [zespołem pomocy technicznej klienta Sprytne](https://clever.com/about/contact/) Aby dodać użytkowników do Sprytne platformy. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

>[!NOTE]
>Można użyć jakichkolwiek innych Sprytne użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Clever można uaktywniać ich konta usługi Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Clever.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Clever, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **Clever**.

    ![Clever łącze na liście aplikacji](./media/clever-tutorial/tutorial_clever_app.png)

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Sprytne w panelu dostępu, możesz powinien pobrać automatycznie zalogowanych do aplikacji inteligentne.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/clever-tutorial/tutorial_general_01.png
[2]: ./media/clever-tutorial/tutorial_general_02.png
[3]: ./media/clever-tutorial/tutorial_general_03.png
[4]: ./media/clever-tutorial/tutorial_general_04.png

[100]: ./media/clever-tutorial/tutorial_general_100.png

[200]: ./media/clever-tutorial/tutorial_general_200.png
[201]: ./media/clever-tutorial/tutorial_general_201.png
[202]: ./media/clever-tutorial/tutorial_general_202.png
[203]: ./media/clever-tutorial/tutorial_general_203.png

