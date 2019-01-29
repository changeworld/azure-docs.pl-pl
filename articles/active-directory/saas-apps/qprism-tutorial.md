---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą QPrism | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i QPrism.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: b59efe3faa00a172e5948f18b06337eedd2a1f6b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55184805"
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą QPrism

W tym samouczku dowiesz się, jak zintegrować QPrism w usłudze Azure Active Directory (Azure AD).

Integrowanie QPrism z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do QPrism.
- Aby umożliwić użytkownikom automatycznie pobrać zalogować się do QPrism (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby uzyskać więcej szczegółów na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą QPrism, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- QPrism logowanie jednokrotne włączone subskrypcji

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska usługi Azure AD w wersji próbnej, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie QPrism z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-qprism-from-the-gallery"></a>Dodaj QPrism z galerii
Aby skonfigurować integrację QPrism w usłudze Azure AD, należy dodać QPrism z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać QPrism z galerii:**

1. W [witrynie Azure Portal](https://portal.azure.com) w okienku po lewej stronie wybierz pozycję **Azure Active Directory**. 

    ![Przycisk Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw][2]
    
1. Aby dodać nową aplikację, w górnej części okna dialogowego, wybierz **nową aplikację**.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **QPrism**i wybierz **QPrism** z panelu wyników. Następnie kliknij przycisk **Dodaj** umożliwiające dodanie aplikacji.

    ![QPrism na liście wyników](./media/qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą QPrism, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, który użytkownik odpowiednika w QPrism użytkownika w usłudze Azure AD. Innymi słowy musi być połączona relację między użytkownika usługi Azure AD i powiązanych użytkownika w QPrism.

Ustanowienie w QPrism tę relację, przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username**.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą QPrism, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on) aby umożliwić użytkownikom korzystać z tej funkcji.
1. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. [Tworzenie użytkownika testowego QPrism](#create-a-qprism-test-user) mieć odpowiednikiem Britta Simon QPrism, który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. [Testowanie logowania jednokrotnego](#test-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji QPrism.

1. W witrynie Azure portal na **QPrism** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/qprism-tutorial/tutorial_qprism_samlbase.png)

1. W **QPrism domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![QPrism domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/qprism-tutorial/tutorial_qprism_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL, który używa następującego wzorca: `https://<customer domain>.qmyzone.com/login`

    b. W **identyfikator** pole tekstowe, wpisz adres URL, który używa następującego wzorca: `https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości z identyfikatorem rzeczywiste i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta QPrism](mailto:qsupport-ce@quatrro.com) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

     ![Link do pobierania certyfikatu](./media/qprism-tutorial/tutorial_qprism_certificate.png)

1. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego przycisk zapisywania](./media/qprism-tutorial/tutorial_general_400.png)
    
1. Aby skonfigurować logowanie jednokrotne na **QPrism** stronie, musisz wysłać **adres Url metadanych Federacji aplikacji** do [zespołem pomocy technicznej QPrism](mailto:qsupport-ce@quatrro.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD:**

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk Azure Active Directory](./media/qprism-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](./media/qprism-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, w górnej części **wszyscy użytkownicy** okno dialogowe, wybierz opcję **Dodaj**.

    ![Przycisk Dodaj](./media/qprism-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](./media/qprism-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-qprism-test-user"></a>Tworzenie użytkownika testowego QPrism

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w QPrism. Praca z [zespołem pomocy technicznej QPrism](mailto:qsupport-ce@quatrro.com) do dodawania użytkowników na platformie QPrism. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do QPrism.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon QPrism:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu. Przejdź do **aplikacje dla przedsiębiorstw**i wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **QPrism**.

    ![Link QPrism na liście aplikacji](./media/qprism-tutorial/tutorial_qprism_app.png)  

1. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”][202]

1. Wybierz pozycję **Dodaj**. Następnie w obszarze **Dodaj przydziału**, wybierz opcję **użytkowników i grup**.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** w **użytkowników** listy.

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **wybierz**.

1. W obszarze **Dodaj przydziału**, wybierz opcję **przypisać**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

W panelu dostępu po wybraniu kafelka QPrism użytkownik powinien uzyskać automatycznie zalogować się do aplikacji QPrism.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/qprism-tutorial/tutorial_general_01.png
[2]: ./media/qprism-tutorial/tutorial_general_02.png
[3]: ./media/qprism-tutorial/tutorial_general_03.png
[4]: ./media/qprism-tutorial/tutorial_general_04.png

[100]: ./media/qprism-tutorial/tutorial_general_100.png

[200]: ./media/qprism-tutorial/tutorial_general_200.png
[201]: ./media/qprism-tutorial/tutorial_general_201.png
[202]: ./media/qprism-tutorial/tutorial_general_202.png
[203]: ./media/qprism-tutorial/tutorial_general_203.png

