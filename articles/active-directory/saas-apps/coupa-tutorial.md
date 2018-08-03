---
title: 'Samouczek: Integracja usługi Azure Active Directory z Coupa | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Coupa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: jeedes
ms.openlocfilehash: 4bf40f76f5a8f788305b4dc9f91523f53fb59acf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448088"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Samouczek: Integracja usługi Azure Active Directory z Coupa

W tym samouczku dowiesz się, jak zintegrować Coupa w usłudze Azure Active Directory (Azure AD).

Integrowanie Coupa z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Coupa.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Coupa (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Coupa, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Coupa logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Coupa z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-coupa-from-the-gallery"></a>Dodawanie Coupa z galerii
Aby skonfigurować integrację Coupa w usłudze Azure AD, należy dodać Coupa z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Coupa z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Coupa**, wybierz opcję **Coupa** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Coupa na liście wyników](./media/coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Coupa w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Coupa do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Coupa musi można ustanowić.

W Coupa, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Coupa, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Coupa](#create-a-coupa-test-user)**  — aby odpowiednikiem Britta Simon w Coupa połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Coupa.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Coupa, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Coupa** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/coupa-tutorial/tutorial_coupa_samlbase.png)

1. Na **Coupa domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Coupa domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/coupa-tutorial/tutorial_coupa_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Wartość adres URL logowania nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Coupa](https://success.coupa.com/Support/Contact_Us?) aby zyskać tę wartość.

    b. W **identyfikator** pole tekstowe, wpisz adres URL:

    | Środowisko  | Adres URL |
    |:-------------|----|
    | Piaskownica | `devsso35.coupahost.com`|
    | Produkcja | `prdsso40.coupahost.com`|
    | | |

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL:

    | Środowisko | Adres URL |
    |------------- |----|
    | Piaskownica | `https://devsso35.coupahost.com/sp/ACS.saml2`|
    | Produkcja | `https://prdsso40.coupahost.com/sp/ACS.saml2`|
    | | |

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/coupa-tutorial/tutorial_coupa_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/coupa-tutorial/tutorial_general_400.png)

1. Zaloguj się do witryny firmy Coupa jako administrator.

1. Przejdź do **instalacji \> kontroli zabezpieczeń**.

   ![Środki kontroli bezpieczeństwa](./media/coupa-tutorial/ic791900.png "kontroli zabezpieczeń")

1. W **Zaloguj się przy użyciu poświadczeń Coupa** sekcji, wykonaj następujące czynności:

    ![Metadane Coupa SP](./media/coupa-tutorial/ic791901.png "Coupa SP metadanych")

    a. Wybierz **Zaloguj się za pośrednictwem protokołu SAML**.

    b. Kliknij przycisk **Przeglądaj** do przekazania metadanych pobranego z witryny Azure portal.

    c. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/coupa-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/coupa-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/coupa-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/coupa-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-coupa-test-user"></a>Tworzenie użytkownika testowego Coupa

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Coupa, musi być obsługiwana w Coupa.  

* W przypadku Coupa Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Coupa** witryny firmy jako administrator.

1. W menu u góry kliknij **instalacji**, a następnie kliknij przycisk **użytkowników**.

   ![Użytkownicy](./media/coupa-tutorial/ic791908.png "użytkowników")

1. Kliknij pozycję **Utwórz**.

   ![Tworzenie użytkowników](./media/coupa-tutorial/ic791909.png "tworzenie użytkowników")

1. W **User Create** sekcji, wykonaj następujące czynności:

   ![Szczegóły użytkownika](./media/coupa-tutorial/ic791910.png "szczegóły użytkownika")

   a. Typ **logowania**, **imię**, **nazwisko**, **identyfikator rejestracji jednokrotnej**, **E-mail** atrybutów prawidłowe konto usługi Azure Active Directory, które chcesz aprowizować do powiązanych pól tekstowych.

   b. Kliknij pozycję **Utwórz**.

   >[!NOTE]
   >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem do potwierdzenia konta, zanim stanie się aktywny.
   >

>[!NOTE]
>Można użyć jakichkolwiek innych Coupa użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Coupa do aprowizacji kont użytkowników usługi AAD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Coupa.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Coupa, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **Coupa**.

    ![Link Coupa na liście aplikacji](./media/coupa-tutorial/tutorial_coupa_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Coupa w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Coupa.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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
