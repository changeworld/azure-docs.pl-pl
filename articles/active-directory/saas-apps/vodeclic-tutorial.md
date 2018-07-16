---
title: 'Samouczek: Integracja usługi Azure Active Directory z Vodeclic | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Vodeclic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
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
ms.openlocfilehash: dad37a0d66ff1af9ad7e7f3b6a807ef1104da39f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041096"
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Samouczek: Integracja usługi Azure Active Directory z Vodeclic

W tym samouczku dowiesz się, jak zintegrować Vodeclic w usłudze Azure Active Directory (Azure AD).

Integrowanie Vodeclic z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Vodeclic.
- Aby umożliwić użytkownikom automatycznie pobrać zalogować się do Vodeclic (logowanie jednokrotne, lub logowania jednokrotnego) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Vodeclic, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Subskrypcja z obsługą logowania jednokrotnego Vodeclic

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli masz środowisko wersji próbnej usługi Azure AD [bezpłatna wersja próbna miesięcznej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Vodeclic z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-vodeclic-from-the-gallery"></a>Dodaj Vodeclic z galerii
Aby skonfigurować integrację Vodeclic w usłudze Azure AD, należy dodać Vodeclic z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Vodeclic z galerii, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Vodeclic**. Wybierz **Vodeclic** z panel wyników, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Vodeclic na liście wyników](./media/vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Vodeclic w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, który użytkownik odpowiednika w Vodeclic użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w Vodeclic.

W Vodeclic, należy podać wartość **Username** taką samą wartość jak **nazwa_użytkownika** w usłudze Azure AD. Łącze między dwóch użytkowników ma ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Vodeclic, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on) aby umożliwić użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. [Tworzenie użytkownika testowego Vodeclic](#create-a-vodeclic-test-user) mieć odpowiednikiem Britta Simon Vodeclic połączonego z usługi Azure AD reprezentacja użytkownika.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. [Testowanie logowania jednokrotnego](#test-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Vodeclic.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Vodeclic, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Vodeclic** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. W **logowanie jednokrotne** okno dialogowe, w obszarze **Single-Sign-on tryb**, wybierz opcję **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. Jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, w **Vodeclic domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Vodeclic domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. W **identyfikator** wpisz adres URL z następującym wzorcem: `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. W **adres URL odpowiedzi** wpisz adres URL z następującym wzorcem: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, wybierz opcję **Pokaż zaawansowane ustawienia adresu URL** pole wyboru, a następnie wykonaj następujące czynności:

    ![Vodeclic domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/vodeclic-tutorial/tutorial_vodeclic_url1.png)

    W **adres URL logowania** wpisz adres URL z następującym wzorcem: `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizować te wartości z identyfikatorem rzeczywisty adres URL odpowiedzi i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Vodeclic](mailto:hotline@vodeclic.com) do uzyskania tych wartości.

5. W **certyfikat podpisywania SAML** zaznacz **XML metadanych**. Następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/vodeclic-tutorial/tutorial_general_400.png)
    
7. Aby skonfigurować logowanie jednokrotne na **Vodeclic** po stronie, Wyślij pobrany **XML metadanych** do [zespołem pomocy technicznej Vodeclic](mailto:hotline@vodeclic.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** karty i dostępu do osadzonego Dokumentacja usługi za pośrednictwem **konfiguracji** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzone w [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/vodeclic-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**. Następnie wybierz pozycję **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/vodeclic-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/vodeclic-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/vodeclic-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-vodeclic-test-user"></a>Tworzenie użytkownika testowego Vodeclic

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Vodeclic. Praca z [zespołem pomocy technicznej Vodeclic](mailto:hotline@vodeclic.com) Aby dodać użytkowników na platformie Vodeclic. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

> [!NOTE]
> Zgodnie z wymaganiami aplikacji może być konieczne uzyskanie swojej maszyny, na liście dozwolonych. W tym stanie, musisz udostępnić swój publiczny adres IP z [zespołem pomocy technicznej Vodeclic](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Vodeclic.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Vodeclic, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu. Następnie przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Vodeclic**.

    ![Link Vodeclic na liście aplikacji](./media/vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okno dialogowe.

    ![Okienko Dodawanie przypisania][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** w **użytkowników** listy.

6. W **użytkowników i grup** okno dialogowe, wybierz opcję **wybierz** przycisku.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu kafelka Vodeclic w panelu dostępu, możesz pobrać automatycznie zalogowany do aplikacji Vodeclic.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

