---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem OfficeSpace | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i OfficeSpace oprogramowania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 7162645f41d26f1496bdec9c4d694e749bfec6a1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449533"
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem OfficeSpace

W tym samouczku dowiesz się, jak zintegrować OfficeSpace oprogramowania z usługi Azure Active Directory (Azure AD).

Integracja oprogramowania OfficeSpace z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do oprogramowania OfficeSpace.
- Użytkowników, aby automatycznie uzyskać zalogowanych do oprogramowania OfficeSpace (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu oprogramowania OfficeSpace, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Oprogramowanie OfficeSpace logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie oprogramowania OfficeSpace z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-officespace-software-from-the-gallery"></a>Dodawanie oprogramowania OfficeSpace z galerii
Aby skonfigurować integrację OfficeSpace oprogramowania w usłudze Azure AD, należy dodać OfficeSpace oprogramowania z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać OfficeSpace oprogramowania z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **OfficeSpace oprogramowania**, wybierz opcję **OfficeSpace oprogramowania** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Oprogramowanie OfficeSpace na liście wyników](./media/officespace-tutorial/tutorial_officespace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne za pomocą oprogramowania OfficeSpace oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w oprogramowaniu OfficeSpace do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w oprogramowaniu OfficeSpace musi zostać ustanowione.

W oprogramowaniu OfficeSpace, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu oprogramowania OfficeSpace, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego oprogramowania OfficeSpace](#create-a-officespace-software-test-user)**  — aby odpowiednikiem Britta Simon w oprogramowaniu OfficeSpace, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji OfficeSpace oprogramowania.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu oprogramowania OfficeSpace, wykonaj następujące czynności:**

1. W witrynie Azure portal na **oprogramowania OfficeSpace** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/officespace-tutorial/tutorial_officespace_samlbase.png)

1. Na **OfficeSpace oprogramowania domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![OfficeSpace oprogramowania domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/officespace-tutorial/tutorial_officespace_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej OfficeSpace oprogramowanie klienckie](mailto:support@officespacesoftware.com) do uzyskania tych wartości. 

1. Aplikacja oprogramowania OfficeSpace oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie atrybutów](./media/officespace-tutorial/tutorial_officespace_attribute.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, wybierz opcję **user.mail** jako **identyfikator użytkownika** i dla każdego wiersza wyświetlane w poniższej tabeli, wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |    
    | e-mail | User.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie Dodaj ](./media/officespace-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie atrybutów](./media/officespace-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **Ok**
 
1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość certyfikatu.

    ![Link pobierania certyfikatu](./media/officespace-tutorial/tutorial_officespace_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/officespace-tutorial/tutorial_general_400.png)

1. Na **konfiguracji oprogramowania OfficeSpace** , kliknij przycisk **Konfigurowanie oprogramowania OfficeSpace** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja oprogramowania OfficeSpace](./media/officespace-tutorial/tutorial_officespace_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do oprogramowania OfficeSpace dzierżawy z uprawnieniami administratora.

1. Przejdź do **ustawienia** i kliknij przycisk **łączników**.

    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie](./media/officespace-tutorial/tutorial_officespace_002.png)

1. Kliknij przycisk **uwierzytelnianie SAML**.

    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie](./media/officespace-tutorial/tutorial_officespace_003.png)

1. W **uwierzytelnianie SAML** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. W **adres url wylogowania dostawcy** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    b. W **klienta dostawcy tożsamości docelowy adres url** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    c. Wklej **odcisk palca** wartości, który skopiowano z witryny Azure portal, w **odcisk palca certyfikatu tożsamości klienta** pola tekstowego. 

    d. Kliknij przycisk **Zapisz ustawienia**.


> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/officespace-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/officespace-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/officespace-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/officespace-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-officespace-software-test-user"></a>Tworzenie użytkownika testowego OfficeSpace oprogramowania

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon OfficeSpace oprogramowania. Oprogramowanie OfficeSpace obsługę just-in-time, który jest domyślnie włączona.

Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik zostanie utworzony podczas próby dostępu do oprogramowania OfficeSpace, jeśli go jeszcze nie istnieje.

> [!NOTE]
> Jeśli musisz utworzyć ręcznie przez użytkownika, musisz skontaktuj się z pomocą [zespołem pomocy technicznej oprogramowania OfficeSpace](mailto:support@officespacesoftware.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do oprogramowania OfficeSpace za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon OfficeSpace oprogramowania, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **oprogramowania OfficeSpace**.

    ![Łącze oprogramowania OfficeSpace na liście aplikacji](./media/officespace-tutorial/tutorial_officespace_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka OfficeSpace oprogramowania w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji oprogramowania OfficeSpace.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/officespace-tutorial/tutorial_general_01.png
[2]: ./media/officespace-tutorial/tutorial_general_02.png
[3]: ./media/officespace-tutorial/tutorial_general_03.png
[4]: ./media/officespace-tutorial/tutorial_general_04.png

[100]: ./media/officespace-tutorial/tutorial_general_100.png

[200]: ./media/officespace-tutorial/tutorial_general_200.png
[201]: ./media/officespace-tutorial/tutorial_general_201.png
[202]: ./media/officespace-tutorial/tutorial_general_202.png
[203]: ./media/officespace-tutorial/tutorial_general_203.png

