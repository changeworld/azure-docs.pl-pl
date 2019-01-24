---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą InTime | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i InTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: d4e2c6e1-ae5d-4d2c-8ffc-1b24534d376a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: jeedes
ms.openlocfilehash: d41e24c29057fb680aa2465843fbc73a957a5cca
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821298"
---
# <a name="tutorial-azure-active-directory-integration-with-intime"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą InTime

W tym samouczku dowiesz się, jak zintegrować InTime w usłudze Azure Active Directory (Azure AD).

Integrowanie InTime z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do InTime.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do InTime (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą InTime, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- InTime logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie InTime z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-intime-from-the-gallery"></a>Dodawanie InTime z galerii
Aby skonfigurować integrację InTime w usłudze Azure AD, należy dodać InTime z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać InTime z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **InTime**, wybierz opcję **InTime** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![InTime na liście wyników](./media/intime-tutorial/tutorial_intime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą InTime w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w InTime do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w InTime musi można ustanowić.

W InTime, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą InTime, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego InTime](#create-a-intime-test-user)**  — aby odpowiednikiem Britta Simon w InTime połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji InTime.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z InTime, wykonaj następujące czynności:**

1. W witrynie Azure portal na **InTime** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/intime-tutorial/tutorial_intime_samlbase.png)

1. Na **InTime domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![InTime domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/intime-tutorial/tutorial_intime_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://intime6.intimesoft.com/mytime/login/login.xhtml`

    b. W **identyfikator** pole tekstowe, wpisz adres URL: `https://auth.intimesoft.com/auth/realms/master`

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link do pobierania certyfikatu](./media/intime-tutorial/tutorial_intime_certificate.png) 

1. Aplikacja InTime oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia przykład tego działania. Wartość domyślna **identyfikator użytkownika** jest **user.userprincipalname** , ale InTime oczekuje, że to mają być mapowane z adresem e-mail użytkownika. W przypadku którego można użyć **user.mail** atrybutu z listy lub użyj wartości odpowiednich atrybutów, zgodnie z konfiguracją organizacji 

    ![Konfigurowanie atrybutów](./media/intime-tutorial/tutorial_intime_attribute.png)

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/intime-tutorial/tutorial_general_400.png)

1. Na **InTime konfiguracji** kliknij **skonfigurować InTime** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![InTime konfiguracji](./media/intime-tutorial/tutorial_intime_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **InTime** stronie, musisz wysłać pobrany **XML metadanych**, **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** do [ Zespół pomocy technicznej inTime](mailto:hdollard@intimesoft.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/intime-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/intime-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/intime-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/intime-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-intime-test-user"></a>Tworzenie użytkownika testowego InTime

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w InTime. Praca z [zespołem pomocy technicznej InTime](mailto:hdollard@intimesoft.com) Aby dodać użytkowników do InTime platformy. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do InTime.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon InTime, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **InTime**.

    ![InTime łącze na liście aplikacji](./media/intime-tutorial/tutorial_intime_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka InTime w panelu dostępu, należy pobrać na stronie logowania aplikacji InTime. Kliknij przycisk **logowania** przycisk, a następnie szereg dostawców tożsamości, które będą wyświetlane na liście przycisków. Kliknij przycisk **Nazwa dostawcy tożsamości** przez [zespołem pomocy technicznej InTime](mailto:hdollard@intimesoft.com) do logowania do aplikacji InTime. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/intime-tutorial/tutorial_general_01.png
[2]: ./media/intime-tutorial/tutorial_general_02.png
[3]: ./media/intime-tutorial/tutorial_general_03.png
[4]: ./media/intime-tutorial/tutorial_general_04.png

[100]: ./media/intime-tutorial/tutorial_general_100.png

[200]: ./media/intime-tutorial/tutorial_general_200.png
[201]: ./media/intime-tutorial/tutorial_general_201.png
[202]: ./media/intime-tutorial/tutorial_general_202.png
[203]: ./media/intime-tutorial/tutorial_general_203.png

