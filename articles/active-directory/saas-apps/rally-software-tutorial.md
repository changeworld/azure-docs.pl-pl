---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Rally | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Rally oprogramowania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.openlocfilehash: 2bb9df9fe0cb20cdd50d7ba716ee5cba562f3e1b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448261"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Rally

W tym samouczku dowiesz się, jak zintegrować Rally oprogramowania z usługi Azure Active Directory (Azure AD).

Integracja oprogramowania Rally z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do oprogramowania Rally.
- Użytkowników, aby automatycznie uzyskać zalogowanych do Rally oprogramowania (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu oprogramowania Rally, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Oprogramowanie Rally logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Rally oprogramowania za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-rally-software-from-the-gallery"></a>Dodawanie Rally oprogramowania za pomocą galerii
Aby skonfigurować integrację Rally oprogramowania w usłudze Azure AD, należy dodać Rally oprogramowania z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Rally oprogramowania z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Rally oprogramowania**, wybierz opcję **Rally oprogramowania** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Rally oprogramowania na liście wyników](./media/rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą oprogramowania Rally, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika Rally oprogramowania dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w oprogramowaniu Rally musi zostać ustanowione.

W oprogramowaniu Rally, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu Rally oprogramowania, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Rally oprogramowania](#create-a-rally-software-test-user)**  — aby odpowiednikiem Britta Simon Rally oprogramowania, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Rally oprogramowania.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu oprogramowania Rally, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Rally oprogramowania** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

1. Na **Rally oprogramowania domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Rally oprogramowania domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenant-name>.rally.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej Rally oprogramowanie klienckie](https://help.rallydev.com/) do uzyskania tych wartości. 
 


1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/rally-software-tutorial/tutorial_general_400.png)

1. Na **Rally konfiguracji oprogramowania** kliknij **Konfigurowanie oprogramowania Rally** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Rally konfiguracji oprogramowania](./media/rally-software-tutorial/tutorial_rallysoftware_configure.png) 

1. Zaloguj się do Twojej **Rally oprogramowania** dzierżawy.

1. Na pasku narzędzi u góry kliknij **instalacji**, a następnie wybierz pozycję **subskrypcji**.
   
    ![Subskrypcja](./media/rally-software-tutorial/ic769531.png "subskrypcji")

1. Kliknij przycisk **akcji** przycisku. Wybierz **edytowania subskrypcji** w prawej górnej części paska narzędzi.

1. Na **subskrypcji** strony okna dialogowego, wykonaj następujące czynności, a następnie kliknij **Zapisz i Zamknij**:
   
    ![Uwierzytelnianie](./media/rally-software-tutorial/ic769542.png "uwierzytelniania")
   
    a. Wybierz **wyścigów lub Usługa rejestracji Jednokrotnej uwierzytelnianie** z listy rozwijanej uwierzytelniania.

    b. W **adres URL dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML identyfikator jednostki**, które zostały skopiowane z witryny Azure portal. 

    c. W **SSO, wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/rally-software-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/rally-software-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/rally-software-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/rally-software-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-rally-software-test-user"></a>Tworzenie użytkownika testowego Rally oprogramowania

Dla użytkowników usługi Azure AD można było się zalogować musi być obsługiwana na Rally aplikacji przy użyciu nazwy użytkowników usługi Azure Active Directory.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy Rally oprogramowania.

1. Przejdź do **instalacji \> użytkowników**, a następnie kliknij przycisk **+ Dodaj nowy**.
   
    ![Użytkownicy](./media/rally-software-tutorial/ic781039.png "użytkowników")

1. Wpisz nazwę w polu tekstowym nowego użytkownika, a następnie kliknij przycisk **Dodaj ze szczegółowymi informacjami**.

1. W **Create User** sekcji, wykonaj następujące czynności:
   
    ![Tworzenie użytkownika](./media/rally-software-tutorial/ic781040.png "Tworzenie użytkownika")

    a. W **nazwa_użytkownika** polu tekstowym wpisz nazwę użytkownika, takie jak **Brittsimon**.
   
    b. W **adres E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    c. W **imię** tekstu Wprowadź imię użytkownika, takich jak **Britta**.

    d. W **nazwisko** tekstu wprowadź nazwisko użytkownika, takich jak **Simon**.

    e. Kliknij przycisk **Zapisz i Zamknij**.

   >[!NOTE]
   >Aprowizuj konta użytkownika usługi Azure AD, można użyć innych narzędzi do tworzenia konta użytkownika Rally oprogramowania lub interfejsów API dostarczonych przez oprogramowanie Rally.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do oprogramowania Rally za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Rally oprogramowania, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Rally oprogramowania**.

    ![Łącze Rally oprogramowania, na liście aplikacji](./media/rally-software-tutorial/tutorial_rallysoftware_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Rally oprogramowania w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Rally oprogramowania.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rally-software-tutorial/tutorial_general_01.png
[2]: ./media/rally-software-tutorial/tutorial_general_02.png
[3]: ./media/rally-software-tutorial/tutorial_general_03.png
[4]: ./media/rally-software-tutorial/tutorial_general_04.png

[100]: ./media/rally-software-tutorial/tutorial_general_100.png

[200]: ./media/rally-software-tutorial/tutorial_general_200.png
[201]: ./media/rally-software-tutorial/tutorial_general_201.png
[202]: ./media/rally-software-tutorial/tutorial_general_202.png
[203]: ./media/rally-software-tutorial/tutorial_general_203.png

