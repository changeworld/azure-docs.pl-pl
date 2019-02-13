---
title: 'Samouczek: Integracja usługi Azure Active Directory transferu MOVEit — Integracja usługi Azure AD | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i MOVEit Transfer — Integracja usługi Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b50bf623046094509170b5b5efc091013499b51b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169840"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Samouczek: Integracja usługi Azure Active Directory transferu MOVEit — Integracja usługi Azure AD

W tym samouczku dowiesz się, jak zintegrować MOVEit Transfer — Integracja usługi Azure AD z usługą Azure Active Directory (Azure AD).

Integrowanie MOVEit Transfer — Integracja usługi Azure AD z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do MOVEit Transfer — Integracja usługi Azure AD.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do MOVEit Transfer — Integracja usługi Azure AD (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z MOVEit Transfer — Integracja usługi Azure AD, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- MOVEit Transfer — usługa Azure AD integracja logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie MOVEit Transfer — Integracja usługi Azure AD za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Dodawanie MOVEit Transfer — Integracja usługi Azure AD za pomocą galerii
Aby skonfigurować integrację MOVEit Transfer — Integracja usługi Azure AD w usłudze Azure AD, należy dodać MOVEit Transfer — Integracja usługi Azure AD za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać MOVEit Transfer — Integracja usługi Azure AD z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **MOVEit Transfer — Integracja usługi Azure AD**, wybierz opcję **MOVEit Transfer — Integracja usługi Azure AD** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![MOVEit Transfer — Integracja usługi Azure AD na liście wyników](./media/moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą MOVEit Transfer — Integracja usługi Azure AD, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w MOVEit Transfer — Integracja usługi Azure AD dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w MOVEit Transfer — Integracja usługi Azure AD musi można ustanowić.

W MOVEit Transfer — Integracja usługi Azure AD, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą MOVEit Transfer — Integracja usługi Azure AD, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Utwórz MOVEit Transfer — użytkownik test integracji usługi Azure AD](#create-a-moveit-transfer---azure-ad-integration-test-user)**  — aby odpowiednikiem Britta Simon w MOVEit Transfer — Integracja usługi Azure AD, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w transferu MOVEit - aplikacji integracji usługi Azure AD.

**Aby skonfigurować usługę Azure AD w logowanie jednokrotne za pomocą MOVEit Transfer — Integracja usługi Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal na **MOVEit Transfer — Integracja usługi Azure AD** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

1. Na **MOVEit Transfer — Integracja usługi Azure AD domeny i adresów URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://contoso.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://contoso.com/<tenatid>`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Można się odwołać te wartości później w **adres URL metadanych dostawcy usługi** sekcji lub skontaktuj się z [MOVEit Transfer — zespół obsługi klienta integracji usługi Azure AD](https://community.ipswitch.com/s/support) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/moveittransfer-tutorial/tutorial_general_400.png)
    
1. Zaloguj się jako administrator dzierżawy MOVEit transferu.

1. W okienku nawigacji po lewej stronie kliknij **ustawienia**.

    ![Po stronie sekcji w aplikacji ustawienia](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

1. Kliknij przycisk **pojedynczego logować** łącza, która jest w trakcie **zasad zabezpieczeń -> Uwierzytelnianie użytkownika**.

    ![Po stronie aplikacji na zasady zabezpieczeń](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

1. Kliknij link adres URL metadanych, aby pobrać dokumentu metadanych.

    ![Adres URL metadanych dostawcy usługi](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Sprawdź **entityID** odpowiada **identyfikator** w **MOVEit Transfer — Integracja usługi Azure AD domeny i adresów URL** sekcji.
    * Sprawdź **AssertionConsumerService** pasuje do adresu URL lokalizacji **adres URL odpowiedzi** w **MOVEit Transfer — Integracja usługi Azure AD domeny i adresów URL** sekcji.
    
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

1. Kliknij przycisk **Dodawanie dostawcy tożsamości** przycisk, aby dodać nowego dostawcę tożsamości federacyjnych.

    ![Dodawanie dostawcy tożsamości](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

1. Kliknij przycisk **Przeglądaj...**  aby wybrać plik metadanych, który został pobrany z witryny Azure portal, a następnie przycisk **Dodawanie dostawcy tożsamości** można przekazać pobranego pliku.

    ![Dostawcy tożsamości SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

1. Wybierz pozycję "**tak**" jako **włączone** w **edytowanie federacyjnej ustawień dostawcy tożsamości...**  strony, a następnie kliknij przycisk **Zapisz**.

    ![Ustawienia dostawcy tożsamości federacyjnych](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

1. W **edytowanie ustawień tożsamości federacyjnych dostawca użytkownika** strony, wykonaj następujące czynności:
    
    ![Edytuj ustawienia dostawcy tożsamości federacyjnych](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Wybierz **identyfikatora SAML NameID** jako **nazwa logowania**.
    
    b. Wybierz **innych** jako **imię i nazwisko** i **nazwa atrybutu** textbox Umieść wartość: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Wybierz **innych** jako **wiadomości E-mail** i **nazwa atrybutu** textbox Umieść wartość: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Wybierz **tak** jako **automatycznego tworzenia konta logowaniu**.
    
    e. Kliknij przycisk **Save** (Zapisz).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/moveittransfer-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/moveittransfer-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/moveittransfer-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/moveittransfer-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Utwórz MOVEit Transfer — użytkownik test integracji usługi Azure AD

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w MOVEit Transfer — Integracja usługi Azure AD. MOVEit Transfer — Integracja usługi Azure AD obsługę just-in-time, które mają włączone. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu MOVEit Transfer — Integracja usługi Azure AD, jeśli go jeszcze nie istnieje.

>[!NOTE]
>Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [MOVEit Transfer — zespół obsługi klienta integracji usługi Azure AD](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do MOVEit Transfer — Integracja usługi Azure AD.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon transferu MOVEit — Integracja usługi Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **MOVEit Transfer — Integracja usługi Azure AD**.

    ![MOVEit Transfer — Integracja usługi Azure AD łącze na liście aplikacji](./media/moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu MOVEit Transfer — Integracja usługi Azure AD kafelka w panelu dostępu użytkownik powinien uzyskać automatycznie zalogowanych do transferu MOVEit - aplikacji integracji usługi Azure AD. 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/moveittransfer-tutorial/tutorial_general_203.png

