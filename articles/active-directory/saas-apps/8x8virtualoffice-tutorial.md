---
title: 'Samouczek: Integracja usługi Azure Active Directory z 8 x 8 wirtualnych Office | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między platformy Azure Active Directory oraz 8 x 8 wirtualnego pakietu Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 53db637bf7ad47896747b491fcbe31123fdb104e
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741814"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Samouczek: Integracja usługi Azure Active Directory z pakietem Office wirtualnej 8 x 8

W tym samouczku dowiesz się, jak zintegrować 8 x 8 wirtualnych Office z usługą Azure Active Directory (Azure AD).

Integrowanie 8 x 8 wirtualnych pakietu Office z usługą Azure AD oferuje następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do pakietu Office wirtualnej 8 x 8.
- Użytkowników, aby automatycznie uzyskać zalogowanych do 8 x 8 wirtualnych Office (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z 8 x 8 wirtualnych z pakietem Office, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- 8 x 8 wirtualnych Office logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie 8 x 8 wirtualnych Office za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Dodawanie 8 x 8 wirtualnych Office za pomocą galerii

Aby skonfigurować integrację pakietu Office wirtualnej 8 x 8 w usłudze Azure AD, należy dodać 8 x 8 wirtualnych Office za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać 8 x 8 wirtualnych Office z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **8 x 8 wirtualnych Office**, wybierz opcję **8 x 8 wirtualnych Office** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![8 x 8 wirtualnych Office na liście wyników](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne z 8 x 8 wirtualnych pakietu Office na podstawie których użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, jakie użytkownik odpowiednika w 8 x 8 wirtualnych Office jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w 8 x 8 wirtualnych Office musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z 8 x 8 wirtualnych z pakietem Office, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego wirtualnego Office 8 x 8](#creating-a-8x8-virtual-office-test-user)**  — aby odpowiednikiem Britta Simon w 8 x 8 wirtualnych biuro, które jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w 8 x 8 wirtualnych aplikacji pakietu Office.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z pakietem Office wirtualnej 8 x 8, wykonaj następujące czynności:**

1. W witrynie Azure portal na **8 x 8 wirtualnych Office** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    ![8 x 8 adresy URL i domeny wirtualnych Office pojedynczego logowania jednokrotnego informacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://sso.8x8.com/saml2`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL: `https://sso.8x8.com/saml2`

5. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikatu (Raw)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

6. Na **Konfigurowanie 8 x 8 wirtualnych Office** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![8 x 8 wirtualnych Office Konfiguracja](common/configuresection.png)

7. Logowanie jednokrotne do swojej dzierżawy Office wirtualnego 8 x 8 jako administrator.

8. Wybierz **Mgr konta Office wirtualnego** na panelu aplikacji.

    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Wybierz **firm** konto, aby zarządzać, a następnie kliknij przycisk **Sign In** przycisku.

    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Kliknij przycisk **kont** karty na liście menu.

    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Kliknij przycisk **Single Sign On** się na liście kont.
  
    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Wybierz **Single Sign On** w ramach metod uwierzytelniania i kliknij przycisk **SAML**.

    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. W **SAML logowania jednokrotnego** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. W **adres URL logowania** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    b. W **adresu URL wylogowania** pola tekstowego, Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal.

    c. W **adres URL wystawcy** pola tekstowego, Wklej **usługi Azure AD identyfikator** wartości, które zostały skopiowane z witryny Azure portal.

    d. Kliknij przycisk **Przeglądaj** przycisk, aby przekazać certyfikat, który został pobrany z witryny Azure portal.

    e. Kliknij przycisk **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
  
### <a name="creating-a-8x8-virtual-office-test-user"></a>Tworzenie użytkownika testowego wirtualnego Office 8 x 8

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w pakiecie Office wirtualnej 8 x 8. 8 x 8 wirtualnych Office obsługę just-in-time, który jest domyślnie włączona.

Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu 8 x 8 wirtualnych Office, jeśli go jeszcze nie istnieje.

> [!NOTE]
> Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespół pomocy technicznej usługi Office wirtualnego 8 x 8](https://www.8x8.com/about-us/contact-us).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do pakietu Office wirtualnej 8 x 8.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **8 x 8 wirtualnych Office**.

    ![Konfigurowanie logowania jednokrotnego](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Office wirtualnego 8 x 8 w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do 8 x 8 wirtualnych aplikacji pakietu Office.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
