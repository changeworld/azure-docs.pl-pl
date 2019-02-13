---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą ChronicX® | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ChronicX®.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f3f19be6-6ee8-413c-919c-4884ffe685ca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67ace84ae3c919fdb3ed54d92e63e203d66102c1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201741"
---
# <a name="tutorial-azure-active-directory-integration-with-chronicx"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą ChronicX®

W tym samouczku dowiesz się, jak zintegrować ChronicX® w usłudze Azure Active Directory (Azure AD).

Integrating ChronicX® z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do ChronicX®.
- Użytkowników, aby automatycznie uzyskać zalogowanych do ChronicX® (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą ChronicX®, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- A ChronicX® logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Adding ChronicX® z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-chronicx-from-the-gallery"></a>Adding ChronicX® z galerii
Aby skonfigurować integrację ChronicX® w usłudze Azure AD, należy dodać ChronicX® z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ChronicX® z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **ChronicX®**, wybierz opcję **ChronicX®** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![ChronicX® na liście wyników](./media/chronicx-tutorial/tutorial_chronicx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą ChronicX® oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w ChronicX® do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w ChronicX® musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą ChronicX®, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **Tworzenie użytkownika testowego ChronicX®** — aby odpowiednikiem Britta Simon w ChronicX® połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji ChronicX®.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z ChronicX®, wykonaj następujące czynności:**

1. W witrynie Azure portal na **ChronicX®** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/chronicx-tutorial/tutorial_chronicx_samlbase.png)

1. Na **ChronicX® domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![ChronicX® domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/chronicx-tutorial/tutorial_chronicx_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.chronicx.com/ups/processlogonSSO.jsp`

    b. W **identyfikator** pole tekstowe, wpisz adres URL: `ups.chronicx.com`

    > [!NOTE] 
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta® ChronicX](https://www.casebank.com/contact-us/) można uzyskać wartość. 
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/chronicx-tutorial/tutorial_chronicx_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/chronicx-tutorial/tutorial_general_400.png)

1. Aby skonfigurować logowanie jednokrotne na **ChronicX®** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej ChronicX®](https://www.casebank.com/contact-us/). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/chronicx-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/chronicx-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/chronicx-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/chronicx-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-chronicx-test-user"></a>Tworzenie użytkownika testowego ChronicX®

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w ChronicX®. ChronicX® obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu ChronicX®, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej ChronicX®](https://www.casebank.com/contact-us/).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do ChronicX®.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon ChronicX®, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **ChronicX®**.

    ![Link the ChronicX® na liście aplikacji](./media/chronicx-tutorial/tutorial_chronicx_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ChronicX® w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji ChronicX®.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/chronicx-tutorial/tutorial_general_01.png
[2]: ./media/chronicx-tutorial/tutorial_general_02.png
[3]: ./media/chronicx-tutorial/tutorial_general_03.png
[4]: ./media/chronicx-tutorial/tutorial_general_04.png

[100]: ./media/chronicx-tutorial/tutorial_general_100.png

[200]: ./media/chronicx-tutorial/tutorial_general_200.png
[201]: ./media/chronicx-tutorial/tutorial_general_201.png
[202]: ./media/chronicx-tutorial/tutorial_general_202.png
[203]: ./media/chronicx-tutorial/tutorial_general_203.png

