---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Vodeclic | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Vodeclic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: 8f53082a2f6a33e934c2e3138d08725ffdf4fa2b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815416"
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Vodeclic

W tym samouczku dowiesz się, jak zintegrować Vodeclic w usłudze Azure Active Directory (Azure AD).

Integrowanie Vodeclic z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Vodeclic.
- Aby umożliwić użytkownikom automatycznie pobrać zalogować się do Vodeclic (logowanie jednokrotne, lub logowania jednokrotnego) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Vodeclic, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Subskrypcja z obsługą logowania jednokrotnego Vodeclic

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli masz środowisko wersji próbnej usługi Azure AD [bezpłatna wersja próbna miesięcznej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Vodeclic z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-vodeclic-from-the-gallery"></a>Dodaj Vodeclic z galerii
Aby skonfigurować integrację Vodeclic w usłudze Azure AD, należy dodać Vodeclic z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Vodeclic z galerii, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw][2]
    
1. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Vodeclic**. Wybierz **Vodeclic** z panel wyników, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Vodeclic na liście wyników](./media/vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Vodeclic w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, który użytkownik odpowiednika w Vodeclic użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w Vodeclic.

W Vodeclic, należy podać wartość **Username** taką samą wartość jak **nazwa_użytkownika** w usłudze Azure AD. Łącze między dwóch użytkowników ma ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Vodeclic, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on) aby umożliwić użytkownikom korzystać z tej funkcji.
1. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. [Tworzenie użytkownika testowego Vodeclic](#create-a-vodeclic-test-user) mieć odpowiednikiem Britta Simon Vodeclic połączonego z usługi Azure AD reprezentacja użytkownika.
1. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. [Testowanie logowania jednokrotnego](#test-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Vodeclic.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Vodeclic, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Vodeclic** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. W **logowanie jednokrotne** okno dialogowe, w obszarze **Single-Sign-on tryb**, wybierz opcję **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

1. Jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, w **Vodeclic domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Vodeclic domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. W **identyfikator** wpisz adres URL z następującym wzorcem: `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. W **adres URL odpowiedzi** wpisz adres URL z następującym wzorcem: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

1. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, wybierz opcję **Pokaż zaawansowane ustawienia adresu URL** pole wyboru, a następnie wykonaj następujące czynności:

    ![Vodeclic domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/vodeclic-tutorial/tutorial_vodeclic_url1.png)

    W **adres URL logowania** wpisz adres URL z następującym wzorcem: `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizować te wartości z identyfikatorem rzeczywisty adres URL odpowiedzi i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Vodeclic](mailto:hotline@vodeclic.com) do uzyskania tych wartości.

1. W **certyfikat podpisywania SAML** zaznacz **XML metadanych**. Następnie zapisz plik metadanych na tym komputerze.

    ![Link do pobierania certyfikatu](./media/vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

1. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/vodeclic-tutorial/tutorial_general_400.png)
    
1. Aby skonfigurować logowanie jednokrotne na **Vodeclic** po stronie, Wyślij pobrany **XML metadanych** do [zespołem pomocy technicznej Vodeclic](mailto:hotline@vodeclic.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** karty i dostępu do osadzonego Dokumentacja usługi za pośrednictwem **konfiguracji** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzone w [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk Azure Active Directory](./media/vodeclic-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**. Następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](./media/vodeclic-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/vodeclic-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](./media/vodeclic-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-vodeclic-test-user"></a>Tworzenie użytkownika testowego Vodeclic

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Vodeclic. Praca z [zespołem pomocy technicznej Vodeclic](mailto:hotline@vodeclic.com) Aby dodać użytkowników na platformie Vodeclic. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

> [!NOTE]
> Zgodnie z wymaganiami aplikacji może być konieczne uzyskanie swojej maszyny, na liście dozwolonych. W tym stanie, musisz udostępnić swój publiczny adres IP z [zespołem pomocy technicznej Vodeclic](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Vodeclic.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Vodeclic, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu. Następnie przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Vodeclic**.

    ![Link Vodeclic na liście aplikacji](./media/vodeclic-tutorial/tutorial_vodeclic_app.png)  

1. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”][202]

1. Wybierz przycisk **Add** (Dodaj). Następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okno dialogowe.

    ![Okienko Dodawanie przypisania][203]

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** w **użytkowników** listy.

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **wybierz** przycisku.

1. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu kafelka Vodeclic w panelu dostępu, możesz pobrać automatycznie zalogowany do aplikacji Vodeclic.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/vodeclic-tutorial/tutorial_general_203.png

