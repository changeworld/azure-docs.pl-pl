---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą OnTrack | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i OnTrack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: d2cafba2-3b4a-4471-ba34-80f6a96ff2b9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: 6da60497ccb2a88cdba2a848a82c3fb0fc174525
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808531"
---
# <a name="tutorial-azure-active-directory-integration-with-ontrack"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą OnTrack

W tym samouczku dowiesz się, jak zintegrować OnTrack w usłudze Azure Active Directory (Azure AD).

Integrowanie OnTrack z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do OnTrack.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do OnTrack (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą OnTrack, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- OnTrack logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie OnTrack z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-ontrack-from-the-gallery"></a>Dodawanie OnTrack z galerii
Aby skonfigurować integrację OnTrack w usłudze Azure AD, należy dodać OnTrack z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać OnTrack z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **OnTrack**, wybierz opcję **OnTrack** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![OnTrack na liście wyników](./media/ontrack-tutorial/tutorial_ontrack_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą OnTrack w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w OnTrack do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w OnTrack musi można ustanowić.

W OnTrack, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą OnTrack, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego OnTrack](#create-an-ontrack-test-user)**  — aby odpowiednikiem Britta Simon w OnTrack połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji OnTrack.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z OnTrack, wykonaj następujące czynności:**

1. W witrynie Azure portal na **OnTrack** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/ontrack-tutorial/tutorial_ontrack_samlbase.png)

1. Na **OnTrack domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![OnTrack domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/ontrack-tutorial/tutorial_ontrack_url.png)

    a. W **identyfikator** polu tekstowym
    
    Środowisko testowe wpisz adres URL: `https://staging.insigniagroup.com/sso`

    W środowisku produkcyjnym wpisz adres URL: `https://oeaccessories.com/sso`

    b. W **adres URL odpowiedzi** polu tekstowym
    
    Środowisko testowe wpisz adres URL: `https://indie.staging.insigniagroup.com/sso/autonation.aspx`

    W środowisku produkcyjnym wpisz adres URL: `https://igaccessories.com/sso/autonation.aspx`

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link do pobierania certyfikatu](./media/ontrack-tutorial/tutorial_ontrack_certificate.png)

1. Aplikacja OnTrack oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. 

    ![Konfigurowanie logowania jednokrotnego](./media/ontrack-tutorial/tutorial_attribute.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, pokazany na wcześniejszej ilustracji, używając i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | ----------------|    
    | Rola użytkownika      | "42F432" |
    | Hyperion kodu  | "12345" |

    > [!NOTE]
    > **Rola użytkownika** i **kodu Hyperion** atrybuty są mapowane z rolą użytkownika Autonation i Dealer kod odpowiednio. Te wartości są jedynie przykładowe, należy użyć poprawny kod dla integracji usługi. Możesz skontaktować się ze [obsługi Autonation](mailto:CustomerService@insigniagroup.com) dla tych wartości.
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/ontrack-tutorial/tutorial_attribute_04.png) 

    ![Konfigurowanie logowania jednokrotnego](./media/ontrack-tutorial/tutorial_attribute_05.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/ontrack-tutorial/tutorial_general_400.png)

1. Aby skonfigurować logowanie jednokrotne na **OnTrack** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej OnTrack](mailto:CustomerService@insigniagroup.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/ontrack-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/ontrack-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/ontrack-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/ontrack-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-ontrack-test-user"></a>Tworzenie użytkownika testowego OnTrack

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w OnTrack. Praca z [zespołem pomocy technicznej OnTrack](mailto:CustomerService@insigniagroup.com) Aby dodać użytkowników na platformie OnTrack. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do OnTrack.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon OnTrack, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **OnTrack**.

    ![Link OnTrack na liście aplikacji](./media/ontrack-tutorial/tutorial_ontrack_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka OnTrack w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji OnTrack.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ontrack-tutorial/tutorial_general_01.png
[2]: ./media/ontrack-tutorial/tutorial_general_02.png
[3]: ./media/ontrack-tutorial/tutorial_general_03.png
[4]: ./media/ontrack-tutorial/tutorial_general_04.png

[100]: ./media/ontrack-tutorial/tutorial_general_100.png

[200]: ./media/ontrack-tutorial/tutorial_general_200.png
[201]: ./media/ontrack-tutorial/tutorial_general_201.png
[202]: ./media/ontrack-tutorial/tutorial_general_202.png
[203]: ./media/ontrack-tutorial/tutorial_general_203.png

