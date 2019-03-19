---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu rozwiązania Zscaler prywatny dostęp administratora | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i rozwiązania Zscaler prywatnej dostępu administratora.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9cdb842c4cebe48f2f6534417a7fe6aa4686d1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894686"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Samouczek: Integracja usługi Azure Active Directory Administrator dostępu prywatnego rozwiązania Zscaler

W tym samouczku dowiesz się, jak zintegrować rozwiązania Zscaler prywatny dostęp administratora z usługi Azure Active Directory (Azure AD).

Integracja rozwiązania Zscaler prywatny dostęp administratora z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do rozwiązania Zscaler prywatnej dostępu administratora.
- Użytkowników, aby automatycznie uzyskać zalogowanych do rozwiązania Zscaler prywatny dostęp administratora (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu rozwiązania Zscaler prywatnej dostępu administratora, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Administrator dostępu prywatnego rozwiązania Zscaler logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie administratora dostępu prywatnego rozwiązania Zscaler z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Dodawanie administratora dostępu prywatnego rozwiązania Zscaler z galerii
Aby skonfigurować integrację Administrator dostępu rozwiązania Zscaler prywatne w usłudze Azure AD, należy dodać administratora dostępu prywatnego rozwiązania Zscaler z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać administratora dostępu prywatnego rozwiązania Zscaler z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Administrator dostępu prywatnego rozwiązania Zscaler**, wybierz opcję **Administrator dostępu prywatnego rozwiązania Zscaler** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![Administrator dostępu prywatnego rozwiązania Zscaler na liście wyników](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, skonfiguruj i testowanie usługi Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler prywatne Administrator dostępu oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w rozwiązania Zscaler prywatny dostęp administratora do użytkownika w usłudze Azure AD. Oznacza to relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w prywatnej rozwiązania Zscaler dostępu administratora musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu rozwiązania Zscaler prywatnej dostępu administratora, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Administrator dostępu prywatnego rozwiązania Zscaler](#create-a-zscaler-private-access-administrator-test-user)**  — aby odpowiednikiem Britta Simon w prywatnej rozwiązania Zscaler Administrator dostępu połączoną usługę Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji rozwiązania Zscaler prywatnej dostępu administratora.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu rozwiązania Zscaler prywatnej dostępu administratora, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Administrator dostępu prywatnego rozwiązania Zscaler** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

1. Na **Administrator dostępu prywatnego rozwiązania Zscaler domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Administrator dostępu prywatnego rozwiązania Zscaler domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**

    d. W **RelayState** pole tekstowe, wpisz wartość: `idpadminsso`

1.  Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb wykonaj następujące czynności:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [rozwiązania Zscaler prywatny dostęp administratora zespołu pomocy technicznej](https://help.zscaler.com/zpa-submit-ticket) do uzyskania tych wartości.
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

1. W oknie przeglądarki internetowej innej, zaloguj się do rozwiązania Zscaler prywatnej dostępu administratora jako Administrator.

1. W górnej części, kliknij przycisk **administracji** i przejdź do **uwierzytelniania** kliknij sekcję **konfigurację dostawcy tożsamości**.

    ![Administrator dostępu prywatnego rozwiązania Zscaler administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

1. W prawym górnym rogu, kliknij przycisk **Dodawanie konfiguracji dostawcy tożsamości**. 

    ![Administrator dostępu prywatnego rozwiązania Zscaler Addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

1. Na **Dodawanie konfiguracji dostawcy tożsamości** strony należy wykonać następujące czynności:
 
    ![Administrator dostępu prywatnego rozwiązania Zscaler Idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Kliknij przycisk **wybierz plik** do przekazania pobrany plik metadanych z usługi Azure AD w **przekazywania plików metadanych dostawcy tożsamości** pola.

    b. Odczytuje **metadanych tożsamości** z usługi Azure AD i wypełni wszystkie informacje dotyczące pól, jak pokazano poniżej.

    ![Administrator dostępu prywatnego rozwiązania Zscaler Idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Wybierz **logowanie jednokrotne** jako **administratora**.

    d. Wybierz domenę z **domen** pola.
    
    e. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Tworzenie użytkownika testowego Administrator dostępu prywatnego rozwiązania Zscaler

Aby umożliwić użytkownikom usługi Azure AD zalogować się do rozwiązania Zscaler prywatnej dostępu administratora, są aprowizowane do rozwiązania Zscaler prywatnej dostępu administratora. W przypadku rozwiązania Zscaler prywatnej dostępu administratora, inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy rozwiązania Zscaler prywatnej dostępu administratora jako administrator.

1. W górnej części, kliknij przycisk **administracji** i przejdź do **uwierzytelniania** kliknij sekcję **konfigurację dostawcy tożsamości**.

    ![Administrator dostępu prywatnego rozwiązania Zscaler administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

1. Kliknij przycisk **Administratorzy** z menu po lewej stronie.

    ![Administrator Administrator dostępu prywatnego rozwiązania Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

1. W prawym górnym rogu, kliknij przycisk **Dodawanie administratora**:

    ![Administrator dostępu prywatnego rozwiązania Zscaler Dodaj administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

1. W **Dodawanie administratora** strony, wykonaj następujące czynności:

    ![Administrator dostępu prywatnego rozwiązania Zscaler użytkownika administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. W **Username** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **BrittaSimon\@contoso.com**.

    b. W **hasło** pole tekstowe, wpisz hasło.

    c. W **Potwierdź hasło** pole tekstowe, wpisz hasło.

    d. Wybierz **roli** jako **Administrator dostępu prywatnego rozwiązania Zscaler**.

    e. W **E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **BrittaSimon\@contoso.com**.

    f. W **Phone** pole tekstowe, wpisz numer telefonu.

    g. W **strefa czasowa** pola tekstowego, wybierz strefę czasową.

    h. Kliknij pozycję **Zapisz**.  

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do rozwiązania Zscaler prywatny dostęp administratora.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do rozwiązania Zscaler prywatnej dostępu administratora, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Administrator dostępu prywatnego rozwiązania Zscaler**.

    ![Administrator dostępu prywatnego rozwiązania Zscaler łącze na liście aplikacji](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka rozwiązania Zscaler prywatny dostęp administratora na panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Administrator dostępu prywatnego rozwiązania Zscaler.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

