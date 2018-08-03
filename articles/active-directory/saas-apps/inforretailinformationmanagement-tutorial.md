---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą sprzedaży detalicznej Infor — Zarządzanie informacji | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i handlu detalicznego Infor — Zarządzanie informacji.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5ff49168-ef81-4169-8e5e-dc86e24dd5e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 8676ae32de72a52f88d212d225610053b7ee5c4c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441480"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-retail--information-management"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą sprzedaży detalicznej Infor — Zarządzanie informacji

W tym samouczku dowiesz się, jak zintegrować Infor handlu detalicznego — Zarządzanie informacji w usłudze Azure Active Directory (Azure AD).

Integrowanie sieci Infor sprzedaży — Zarządzanie informacji z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do sieci Infor sprzedaży — Zarządzanie informacji.
- Użytkowników, aby automatycznie uzyskać zalogowanych do sprzedaży detalicznej Infor — informacji zarządzania (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD za pomocą sprzedaży detalicznej Infor — Zarządzanie informacji, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Handlu detalicznego Infor — Zarządzanie informacji logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Infor handlu detalicznego — Zarządzanie informacji z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-infor-retail--information-management-from-the-gallery"></a>Dodawanie Infor handlu detalicznego — Zarządzanie informacji z galerii
Aby skonfigurować integrację handlu detalicznego Infor — Zarządzanie informacji w usłudze Azure AD, należy dodać handlu detalicznego Infor — Zarządzanie informacji z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać handlu detalicznego Infor — Zarządzanie informacji z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **handlu detalicznego Infor — Zarządzanie informacji**, wybierz opcję **handlu detalicznego Infor — Zarządzanie informacji** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![Handlu detalicznego infor — Zarządzanie informacji z listy wyników](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą sprzedaży detalicznej Infor — Zarządzanie informacje w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w sprzedaży detalicznej Infor — Zarządzanie informacji do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w sprzedaży detalicznej Infor — Zarządzanie informacji musi nawiązać.

W sprzedaży detalicznej Infor — Zarządzanie informacji, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą sprzedaży detalicznej Infor — Zarządzanie informacji, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Utwórz handlu detalicznego Infor — Zarządzanie informacji użytkownika testowego](#create-an-infor-retail--information-management-test-user)**  — aby odpowiednikiem Britta Simon w sprzedaży detalicznej Infor — Zarządzanie informacji, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w sieci sprzedaży detalicznej Infor — aplikacji do zarządzania informacji.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z sieci Infor sprzedaży — Zarządzanie informacji, wykonaj następujące czynności:**

1. W witrynie Azure portal na **handlu detalicznego Infor — Zarządzanie informacji** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_samlbase.png)

1. Na **Infor handlu detalicznego — informacje o zarządzanie domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w trybie zainicjował dostawcy tożsamości należy wykonać następujące czynności:

    ![Infor handlu detalicznego — informacje o zarządzanie domena i adresy URL pojedynczy informacje logowania dostawcy tożsamości](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następujących wzorców: 
    |   |
    | -- |
    | `https://<company name>.mingle.infor.com` |
    | `http://<company name>.mingledev.infor.com` |

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.mingle.infor.com/sp/ACS.saml2`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Infor handlu detalicznego — informacje o domenie zarządzania i adresy URL pojedynczego logowania jednokrotnego informacji SP](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.mingle.infor.com/<company code>`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [handlu detalicznego Infor — zespół obsługi klienta zarządzania informacji](mailto:innovate@infor.com) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/inforretailinformationmanagement-tutorial/tutorial_general_400.png)
    
1. Aby skonfigurować logowanie jednokrotne na **handlu detalicznego Infor — Zarządzanie informacji** stronie, musisz wysłać pobrany **XML metadanych** do [handlu detalicznego Infor — informacje o zarządzanie zespołem pomocy technicznej](mailto:innovate@infor.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/inforretailinformationmanagement-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/inforretailinformationmanagement-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/inforretailinformationmanagement-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/inforretailinformationmanagement-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-infor-retail--information-management-test-user"></a>Utwórz handlu detalicznego Infor — Zarządzanie informacji użytkownika testowego

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w sprzedaży detalicznej Infor — Zarządzanie informacji. Skontaktuj się z [handlu detalicznego Infor — informacje o zarządzanie zespołem pomocy technicznej](mailto:innovate@infor.com) Aby dodać użytkowników w sprzedaży detalicznej Infor — platforma zarządzania informacji.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do sieci Infor sprzedaży — Zarządzanie informacji.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon na detaliczną Infor — Zarządzanie informacji, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **handlu detalicznego Infor — Zarządzanie informacji**.

    ![Infor handlu detalicznego — Zarządzanie informacji link na liście aplikacji](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu Infor handlu detalicznego — Kafelek Zarządzanie informacji w panelu dostępu, możesz powinien pobrać automatycznie zalogowanych do sieci sprzedaży detalicznej Infor — aplikacji do zarządzania informacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_01.png
[2]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_02.png
[3]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_03.png
[4]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_04.png

[100]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_100.png

[200]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_200.png
[201]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_201.png
[202]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_202.png
[203]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_203.png

