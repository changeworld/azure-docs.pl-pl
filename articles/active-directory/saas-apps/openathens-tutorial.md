---
title: 'Samouczek: Integracja usługi Azure Active Directory z OpenAthens | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: ddd669bb8ff8e520c5538457789d4f660a82255d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051428"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Samouczek: Integracja usługi Azure Active Directory z OpenAthens

W tym samouczku dowiesz się, jak zintegrować OpenAthens w usłudze Azure Active Directory (Azure AD).

Integrowanie OpenAthens z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do OpenAthens.
- Aby umożliwić użytkownikom automatyczne logowanie się na OpenAthens (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą OpenAthens, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- OpenAthens logowanie jednokrotne włączone subskrypcji

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [bezpłatna wersja próbna miesięcznej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie OpenAthens z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-openathens-from-the-gallery"></a>Dodawanie OpenAthens z galerii
Aby skonfigurować integrację OpenAthens w usłudze Azure AD, należy dodać OpenAthens z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać OpenAthens z galerii**

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie przejdź do **wszystkie aplikacje**.

    ![W okienku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, wybierz **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **OpenAthens**, wybierz opcję **OpenAthens** z panel wyników, a następnie wybierz **Dodaj** przycisku.

    ![OpenAthens na liście wyników](./media/openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą OpenAthens w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w OpenAthens dla użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w OpenAthens.

W OpenAthens, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą OpenAthens, należy wykonać poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on), aby umożliwić użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user), aby przetestować usługę Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. [Tworzenie użytkownika testowego OpenAthens](#create-a-openathens-test-user), aby mieć odpowiednikiem Britta Simon OpenAthens połączonego z usługi Azure AD reprezentacja użytkownika.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user), aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. [Testowanie logowania jednokrotnego](#test-single-sign-on), aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji OpenAthens.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z OpenAthens**

1. W witrynie Azure portal na **OpenAthens** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie pojedynczej linku logowania jednokrotnego][4]

2. Aby włączyć logowanie jednokrotne, w **logowanie jednokrotne** okno dialogowe, wybierz opcję **opartej na SAML logowania jednokrotnego** jako **tryb**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/openathens-tutorial/tutorial_openathens_samlbase.png)

3. W **OpenAthens domena i adresy URL** sekcji, wprowadź wartość `https://login.openathens.net/saml/2/metadata-sp` w **identyfikator** pola tekstowego.

    ![OpenAthens domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_url.png)

4. W **certyfikat podpisywania SAML** zaznacz **XML metadanych**, a następnie zapisz plik metadanych na tym komputerze.

    ![Certyfikat podpisywania AMSL link pobierania](./media/openathens-tutorial/tutorial_openathens_certificate.png) 

5. Wybierz ikonę **Zapisz**.

    ![Logowanie jednokrotne przycisk zapisywania](./media/openathens-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy OpenAthens.

7. Wybierz **połączeń** z listy w obszarze **zarządzania** kartę. 

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application1.png)

8. Wybierz **SAML 1.1/2.0**, a następnie wybierz pozycję **Konfiguruj** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
9. Aby dodać konfigurację, zaznacz **Przeglądaj** przycisk, aby przekazać plik .xml metadanych, który został pobrany z witryny Azure portal, a następnie wybierz **Dodaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application3.png)

10. Wykonaj poniższe kroki w obszarze **szczegóły** kartę.

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. W **mapowania nazwy wyświetlania**, wybierz opcję **Użyj atrybutu**.

    b. W **atrybut nazwy wyświetlania** tekstu wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. W **mapowania unikatowych użytkowników**, wybierz opcję **Użyj atrybutu**.

    d. W **atrybut unikatowego użytkownika** tekstu wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. W **stan**, zaznacz wszystkie trzy pola wyboru.

    f. W **tworzenia kont lokalnych**, wybierz opcję **automatycznie**.

    g. Wybierz **Zapisz zmiany**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), natomiast w przypadku konfigurowania aplikacji. Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** karty i dostępu do osadzonego Dokumentacja usługi za pośrednictwem **konfiguracji** sekcji u dołu. Aby uzyskać więcej informacji o funkcji osadzone dokumentacji, zobacz [dokumentacja embedded usługi Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie "Britta Simon."

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD**

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](./media/openathens-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/openathens-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/openathens-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/openathens-tutorial/create_aaduser_04.png)

    a. W **nazwa** polu tekstowym **BrittaSimon**.

    b. W **nazwa_użytkownika** pole tekstowe, wpisz adres e-mail dla Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pola tekstowego.

    d. Wybierz pozycję **Utwórz**.
  
### <a name="create-an-openathens-test-user"></a>Tworzenie użytkownika testowego OpenAthens

OpenAthens obsługę just-in-time, a użytkownicy są tworzone automatycznie po pomyślnym uwierzytelnieniu. Nie ma potrzeby wykonywania dowolnych akcji w tej sekcji.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej OpenAthens.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon OpenAthens**

1. W witrynie Azure portal, otwórz wyświetlić aplikacje, przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. W **aplikacje** listy wybierz **OpenAthens**.

    ![Link OpenAthens na liście aplikacji](./media/openathens-tutorial/tutorial_openathens_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okienka.

    ![Okienko Dodawanie przypisania][203]

5. W **użytkowników i grup** listy wybierz **Britta Simon**.

6. Wybierz **wybierz** znajdujący się w **użytkowników i grup** listy.

7. Wybierz **przypisać** znajdujący się w **Dodaj przydziału** okienka.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu **OpenAthens** kafelka w panelu dostępu użytkownik powinien automatycznie zalogować się do aplikacji OpenAthens.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* Aby uzyskać listę samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory, zobacz [samouczki integracji aplikacji SaaS do użycia z usługą Azure AD](tutorial-list.md).
* Aby uzyskać więcej informacji na temat uzyskiwania dostępu do aplikacji i logowanie jednokrotne z usługą Azure Active Directory, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

<!--Image references-->

[1]: ./media/openathens-tutorial/tutorial_general_01.png
[2]: ./media/openathens-tutorial/tutorial_general_02.png
[3]: ./media/openathens-tutorial/tutorial_general_03.png
[4]: ./media/openathens-tutorial/tutorial_general_04.png

[100]: ./media/openathens-tutorial/tutorial_general_100.png

[200]: ./media/openathens-tutorial/tutorial_general_200.png
[201]: ./media/openathens-tutorial/tutorial_general_201.png
[202]: ./media/openathens-tutorial/tutorial_general_202.png
[203]: ./media/openathens-tutorial/tutorial_general_203.png

