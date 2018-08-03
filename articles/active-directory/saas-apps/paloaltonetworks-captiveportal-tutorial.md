---
title: 'Samouczek: Integracja usługi Azure Active Directory z Palo Alto Networks — Portal wewnętrzne | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Palo Alto Networks — wewnętrzne portalu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: fa47eaea590ecb84386a6e0ce4eff0a6933be554
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444205"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Samouczek: Integracja usługi Azure Active Directory z Palo Alto Networks — Portal wewnętrzne

W tym samouczku dowiesz się, jak zintegrować Palo Alto Networks — wewnętrzne portalu przy użyciu usługi Azure Active Directory (Azure AD).

Integracja sieci Palo Alto — wewnętrzne portalu przy użyciu usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Palo Alto Networks — wewnętrzne portalu.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Palo Alto Networks — wewnętrzne portalu (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z Palo Alto Networks — wewnętrzne portalu, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Palo Alto Networks — wewnętrzne Portal logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Palo Alto Networks — wewnętrzne portalu z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Dodawanie Palo Alto Networks — wewnętrzne portalu z galerii
Aby skonfigurować integrację Palo Alto Networks — wewnętrzne Portal w usłudze Azure AD, należy dodać Palo Alto Networks — wewnętrzne portalu z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Palo Alto Networks — wewnętrzne portalu z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Palo Alto Networks — wewnętrzne Portal**, wybierz opcję **Palo Alto Networks — wewnętrzne Portal** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację .

    ![Palo Alto sieci — wewnętrzne portalu na liście wyników](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Palo Alto Networks — wewnętrzne portalu, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Palo Alto Networks — wewnętrzne portalu dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Palo Alto Networks — wewnętrzne Portal musi można ustanowić.

W sieciach Palo Alto — wewnętrzne Portal, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Palo Alto Networks — wewnętrzne Portal, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Utwórz Palo Alto Networks — wewnętrzne portalu użytkownika testowego](#create-a-palo-alto-networks---captive-portal-test-user)**  — aby odpowiednikiem Britta Simon w Palo Alto Networks — wewnętrzne portalu, który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w sieci Palo Alto — Portal wewnętrznych aplikacji.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Palo Alto Networks — wewnętrzne portalu, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Palo Alto Networks — wewnętrzne Portal** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_samlbase.png)

1. Na **Palo Alto Networks — wewnętrzne Portal domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Palo Alto sieci — wewnętrzne domeny portalu i adresy URL pojedynczego logowania jednokrotnego informacji](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<Customer Firewall Hostname>/SAML20/SP`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL odpowiedzi. Skontaktuj się z pomocą [Palo Alto Networks - zespołem pomocy technicznej wewnętrzne Portal](https://support.paloaltonetworks.com/support) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_400.png)

1. Otwórz witrynę Palo Alto jako administrator w innym oknie przeglądarki.

1. Kliknij pozycję **urządzenia**.

    ![Konfigurowanie Palo Alto logowania jednokrotnego](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

1. Wybierz **dostawcy tożsamości SAML** w lewym obszarze nawigacji paska, a następnie kliknij przycisk Importuj, aby zaimportować plik metadanych.

    ![Konfigurowanie Palo Alto logowania jednokrotnego](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

1. Wykonaj następujące czynności na okno importu

    ![Konfigurowanie Palo Alto logowania jednokrotnego](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. W **nazwa profilu** pola tekstowego, podaj nazwę np. interfejs użytkownika administratora usługi Azure AD.
    
    b. W **metadanych dostawcy tożsamości**, kliknij przycisk **Przeglądaj** i wybierz plik metadata.xml, który został pobrany z witryny Azure portal
    
    c. Kliknij przycisk **OK**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
  
### <a name="create-a-palo-alto-networks---captive-portal-test-user"></a>Utwórz Palo Alto Networks — wewnętrzne portalu użytkownika testowego

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w Palo Alto Networks — wewnętrzne portalu. Palo Alto Networks — wewnętrzne Portal obsługuje just-in-time aprowizacji, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu Palo Alto Networks — wewnętrzne Portal, jeśli go jeszcze nie istnieje. 

> [!NOTE]
> Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [Palo Alto Networks - zespołem pomocy technicznej wewnętrzne Portal](https://support.paloaltonetworks.com/support).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Palo Alto Networks — wewnętrzne portalu.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do Palo Alto Networks — wewnętrzne Portal, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Palo Alto Networks — wewnętrzne Portal**.

    ![Sieci Palo Alto — Portal wewnętrzne łącza na liście aplikacji](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Podczas konfigurowania portalu wewnętrzne zaporą na maszynie Wirtualnej Windows.  Aby przetestować logowanie jednokrotne w portalu wewnętrzne, zaloguj się na maszynie Wirtualnej Windows przy użyciu protokołu RDP. Z w ramach sesji protokołu RDP, otwórz przeglądarkę do dowolnej witryny sieci web, powinno zostać automatycznie otwarte adres url logowania jednokrotnego i monit o uwierzytelnienie. Po zakończeniu uwierzytelniania, użytkownik powinien móc navgiate do witryn sieci web. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_203.png

