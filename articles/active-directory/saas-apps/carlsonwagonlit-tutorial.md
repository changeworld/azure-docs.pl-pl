---
title: 'Samouczek: Integracja usługi Azure Active Directory z podróży Wagonlit Carlson | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Carlson Wagonlit podróży.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2745e165-94ab-43b1-970a-4547b4e5b501
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: beec63eaba78302fd795fb757f9bee48bab60c3c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168939"
---
# <a name="tutorial-azure-active-directory-integration-with-carlson-wagonlit-travel"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Carlson Wagonlit podróży

W tym samouczku dowiesz się, jak zintegrować Carlson Wagonlit podróży z usługą Azure Active Directory (Azure AD).

Integrowanie Carlson Wagonlit podróży z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Carlson Wagonlit podróży.
- Użytkowników, aby automatycznie uzyskać zalogowanych do podróży Wagonlit Carlson (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Carlson Wagonlit podróży, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Podróże Wagonlit Carlson logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Carlson Wagonlit podróży z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-carlson-wagonlit-travel-from-the-gallery"></a>Dodawanie Carlson Wagonlit podróży z galerii
Aby skonfigurować integrację Carlson Wagonlit podróży w usłudze Azure AD, należy dodać Carlson Wagonlit podróży z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Carlson Wagonlit podróży z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **Carlson Wagonlit podróży**, wybierz opcję **podróży Wagonlit Carlson** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Podróże Wagonlit Carlson na liście wyników](./media/carlsonwagonlit-tutorial/tutorial_carlsonwagonlittravel_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Carlson Wagonlit podróży w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w podróży Wagonlit Carlson do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w podróży Wagonlit Carlson musi zostać ustanowione.

W podróży Wagonlit Carlson, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Carlson Wagonlit podróży, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego podróży Wagonlit Carlson](#create-a-carlson-wagonlit-travel-test-user)**  — aby odpowiednikiem Britta Simon w podróży Wagonlit Carlson, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Carlson Wagonlit podróży.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Carlson Wagonlit podróży, wykonaj następujące czynności:**

1. W witrynie Azure portal na **podróży Wagonlit Carlson** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/carlsonwagonlit-tutorial/tutorial_carlsonwagonlittravel_samlbase.png)

3. Na **Carlson Wagonlit podróży domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Carlson Wagonlit podróży domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/carlsonwagonlit-tutorial/tutorial_carlsonwagonlittravel_url.png)

    W **identyfikator** polu tekstowym wpisz wartość: `cwt-stage`

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/carlsonwagonlit-tutorial/tutorial_carlsonwagonlittravel_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/carlsonwagonlit-tutorial/tutorial_general_400.png)

6. Aby skonfigurować logowanie jednokrotne na **podróży Wagonlit Carlson** stronie, musisz wysłać pobrany **XML metadanych** do [Carlson Wagonlit podróży zespołu pomocy technicznej](http://www.carlsonwagonlit.in/content/cwt/in/en/technical-assistance.html). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/carlsonwagonlit-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/carlsonwagonlit-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/carlsonwagonlit-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/carlsonwagonlit-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-carlson-wagonlit-travel-test-user"></a>Tworzenie użytkownika testowego Carlson Wagonlit podróży

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w podróży Wagonlit Carlson. Praca z [Carlson Wagonlit podróży zespołu pomocy technicznej](http://www.carlsonwagonlit.in/content/cwt/in/en/technical-assistance.html) Aby dodać użytkowników w platformę turystyczną Wagonlit Carlson. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Carlson Wagonlit podróży.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Carlson Wagonlit podróży, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **podróży Wagonlit Carlson**.

    ![Łącze Carlson Wagonlit podróży, na liście aplikacji](./media/carlsonwagonlit-tutorial/tutorial_carlsonwagonlittravel_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Carlson Wagonlit podróży w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Carlson Wagonlit podróży.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/carlsonwagonlittravel-tutorial/tutorial_general_01.png
[2]: ./media/carlsonwagonlittravel-tutorial/tutorial_general_02.png
[3]: ./media/carlsonwagonlittravel-tutorial/tutorial_general_03.png
[4]: ./media/carlsonwagonlittravel-tutorial/tutorial_general_04.png

[100]: ./media/carlsonwagonlittravel-tutorial/tutorial_general_100.png

[200]: ./media/carlsonwagonlittravel-tutorial/tutorial_general_200.png
[201]: ./media/carlsonwagonlittravel-tutorial/tutorial_general_201.png
[202]: ./media/carlsonwagonlittravel-tutorial/tutorial_general_202.png
[203]: ./media/carlsonwagonlittravel-tutorial/tutorial_general_203.png

