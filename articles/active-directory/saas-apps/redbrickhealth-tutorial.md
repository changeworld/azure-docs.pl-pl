---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą programu RedBrick Health | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i RedBrick kondycji.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jeedes
ms.openlocfilehash: d852b30568acff4f1d56a1e208528e8c90b5b1f0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051782"
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą programu RedBrick Health

W tym samouczku dowiesz się, jak zintegrować RedBrick kondycji za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie RedBrick Health z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do RedBrick kondycji.
- Użytkowników, aby automatycznie uzyskać zalogowanych do RedBrick kondycji (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą programu RedBrick Health, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Kondycja RedBrick logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie RedBrick kondycji z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-redbrick-health-from-the-gallery"></a>Dodawanie RedBrick kondycji z galerii
Aby skonfigurować integrację RedBrick kondycji do usługi Azure AD, należy dodać RedBrick kondycji z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać RedBrick kondycji z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **RedBrick kondycji**, wybierz opcję **RedBrick kondycji** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![RedBrick kondycji na liście wyników](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą RedBrick kondycji, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w RedBrick kondycji jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika RedBrick kondycji musi można ustanowić.

W RedBrick kondycji, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą programu RedBrick Health, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego kondycji RedBrick](#create-a-redbrick-health-test-user)**  — aby odpowiednikiem Britta Simon w RedBrick kondycji, który jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji RedBrick kondycji.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą programu RedBrick Health, wykonaj następujące czynności:**

1. W witrynie Azure portal na **RedBrick kondycji** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_samlbase.png)

3. Na **RedBrick kondycji domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![RedBrick kondycji domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `http://www.redbrickhealth.com`
    
    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL: `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`
    
    W środowisku produkcyjnym: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Kliknij przycisk **Pokaż zaawansowane ustawienia adresu URL**.
    
    ![RedBrick kondycji domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url1.png)

    d. W **tan przekaźnika** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`
    
    > [!NOTE] 
    > Wartość stanu przekazywania nie jest prawdziwe. Zaktualizuj tę wartość z faktycznym stanem usługi Relay. Skontaktuj się z pomocą [zespołem pomocy technicznej RedBrick kondycji](https://home.redbrickhealth.com/contact/) aby zyskać tę wartość.

4. Aplikacja RedBrick kondycji oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Te oświadczenia są zależnie od klienta i zależy od wymagań klienta. Następujące opcjonalne oświadczenia są przykładem tylko, które można skonfigurować dla aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkownika** sekcji na stronie integracji aplikacji.

    ![Konfigurowanie logowania jednokrotnego](./media/redbrickhealth-tutorial/attribute.png)

5. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:

    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| ----------------|
    | Nazwa główna | ********** |
    | identyfikator klienta | ********** |
    | Identyfikator uczestnika | ********** |
    
    > [!NOTE]
    > Te wartości są jedynie w celu odwołania. Musisz zdefiniować atrybuty dla każdego wymagania Twojej organizacji. Skontaktuj się z pomocą [zespołem pomocy technicznej RedBrick kondycji](https://home.redbrickhealth.com/contact/) Aby uzyskać więcej informacji na temat wymagane oświadczenia.
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.
    
    ![Konfigurowanie logowania jednokrotnego](./media/redbrickhealth-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie logowania jednokrotnego](./media/redbrickhealth-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Pozostaw **Namespace** puste.
    
    e. Kliknij przycisk **OK**.

6. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_certificate.png) 

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/redbrickhealth-tutorial/tutorial_general_400.png)

8. Na **RedBrick konfiguracji kondycji** kliknij **skonfigurować RedBrick kondycji** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Konfiguracja redBrick kondycji](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_configure.png) 

9. Aby skonfigurować logowanie jednokrotne na **RedBrick kondycji** stronie, musisz wysłać pobrany **Certificate(Base64)** i **identyfikator jednostki SAML** do [RedBrick kondycji zespołu pomocy technicznej](https://home.redbrickhealth.com/contact/). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/redbrickhealth-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/redbrickhealth-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/redbrickhealth-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/redbrickhealth-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-redbrick-health-test-user"></a>Tworzenie użytkownika testowego RedBrick kondycji

W tej sekcji utworzysz użytkownika o nazwie Britta Simon RedBrick kondycji. Praca z [zespołem pomocy technicznej RedBrick kondycji](https://home.redbrickhealth.com/contact/) Aby dodać użytkowników na platformie RedBrick kondycji. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do RedBrick kondycji.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon RedBrick kondycji, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **RedBrick kondycji**.

    ![Link RedBrick kondycji na liście aplikacji](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka kondycji RedBrick w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do RedBrick kondycji aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/redbrickhealth-tutorial/tutorial_general_01.png
[2]: ./media/redbrickhealth-tutorial/tutorial_general_02.png
[3]: ./media/redbrickhealth-tutorial/tutorial_general_03.png
[4]: ./media/redbrickhealth-tutorial/tutorial_general_04.png

[100]: ./media/redbrickhealth-tutorial/tutorial_general_100.png

[200]: ./media/redbrickhealth-tutorial/tutorial_general_200.png
[201]: ./media/redbrickhealth-tutorial/tutorial_general_201.png
[202]: ./media/redbrickhealth-tutorial/tutorial_general_202.png
[203]: ./media/redbrickhealth-tutorial/tutorial_general_203.png

