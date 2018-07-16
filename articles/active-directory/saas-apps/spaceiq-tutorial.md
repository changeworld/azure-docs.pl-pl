---
title: 'Samouczek: Integracja usługi Azure Active Directory z SpaceIQ | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: c9997f27723b399344a18292905b558a9f61d6bd
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051131"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Samouczek: Integracja usługi Azure Active Directory z SpaceIQ

W tym samouczku dowiesz się, jak zintegrować SpaceIQ w usłudze Azure Active Directory (Azure AD).

Integrowanie SpaceIQ z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do SpaceIQ.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do SpaceIQ (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą SpaceIQ, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SpaceIQ logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie SpaceIQ z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-spaceiq-from-the-gallery"></a>Dodawanie SpaceIQ z galerii
Aby skonfigurować integrację SpaceIQ w usłudze Azure AD, należy dodać SpaceIQ z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SpaceIQ z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **SpaceIQ**, wybierz opcję **SpaceIQ** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SpaceIQ na liście wyników](./media/spaceiq-tutorial/tutorial_spaceiq_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą SpaceIQ w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w SpaceIQ do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w SpaceIQ musi można ustanowić.

W SpaceIQ, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą SpaceIQ, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego SpaceIQ](#create-a-spaceiq-test-user)**  — aby odpowiednikiem Britta Simon w SpaceIQ połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SpaceIQ.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z SpaceIQ, wykonaj następujące czynności:**

1. W witrynie Azure portal na **SpaceIQ** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/spaceiq-tutorial/tutorial_spaceiq_samlbase.png)

3. Na **SpaceIQ domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![SpaceIQ domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/spaceiq-tutorial/tutorial_spaceiq_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://api.spaceiq.com`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE] 
    > Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL odpowiedzi i identyfikator, który zostało wyjaśnione w dalszej części tego samouczka.
 
4. Na **certyfikat podpisywania SAML** kliknij **(kodowanie Base64 certyfikatu)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/spaceiq-tutorial/tutorial_spaceiq_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/spaceiq-tutorial/tutorial_general_400.png)

6. Na **konfiguracji SpaceIQ** , kliknij przycisk **skonfigurować SpaceIQ** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Konfiguracja SpaceIQ](./media/spaceiq-tutorial/tutorial_spaceiq_configure.png) 

7.  Otwórz nowe okno przeglądarki, a następnie zaloguj się do środowiska SpaceIQ jako administrator.

8. Po użytkownik jest zalogowany, kliknij na rejestracji układanki w prawym górnym rogu, a następnie kliknij pozycję **"Integracji"**

    ![Ustawienia konta](./media/spaceiq-tutorial/setting1.png) 

9. W obszarze **wszystkich inicjowania obsługi administracyjnej i logowania jednokrotnego**, kliknij pozycję **Azure** Kafelek, aby dodać wystąpienia platformy Azure jako dostawcy tożsamości.

    ![Ikona SAML](./media/spaceiq-tutorial/setting2.png)

10. W **logowania jednokrotnego** okna dialogowego pole, wykonaj następujące czynności:

    ![Ustawienia uwierzytelniania SAML](./media/spaceiq-tutorial/setting3.png)

    a. W **adres URL wystawcy SAML** pole, Wklej **identyfikator jednostki SAML** wartość kopiowana z okna konfiguracji aplikacji usługi Azure AD.
    
    b. Kopiuj **SAML wywołania zwrotnego URL punktu końcowego (tylko do odczytu)** wartość i Wklej wartość w **adres URL odpowiedzi** polu w witrynie Azure portal w obszarze SpaceIQ **domena i adresy URL** sekcji.
    
    c. Kopiuj **języka SAML (tylko do odczytu) identyfikator URI odbiorców** wartość i Wklej wartość w **identyfikator** polu w witrynie Azure portal w obszarze SpaceIQ **domena i adresy URL** sekcji.

    d. Otwórz plik pobranego certyfikatu w programie Notatnik, skopiuj zawartość, a następnie wklej go w **certyfikat X.509** pole.
    
    e. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/spaceiq-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/spaceiq-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/spaceiq-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/spaceiq-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-spaceiq-test-user"></a>Tworzenie użytkownika testowego SpaceIQ

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w SpaceIQ. Praca [zespołem pomocy technicznej SpaceIQ](mailto:eng@spaceiq.com) Aby dodać użytkowników na platformie SpaceIQ. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do SpaceIQ.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon SpaceIQ, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **SpaceIQ**.

    ![Link SpaceIQ na liście aplikacji](./media/spaceiq-tutorial/tutorial_spaceiq_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SpaceIQ w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji SpaceIQ.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spaceiq-tutorial/tutorial_general_01.png
[2]: ./media/spaceiq-tutorial/tutorial_general_02.png
[3]: ./media/spaceiq-tutorial/tutorial_general_03.png
[4]: ./media/spaceiq-tutorial/tutorial_general_04.png

[100]: ./media/spaceiq-tutorial/tutorial_general_100.png

[200]: ./media/spaceiq-tutorial/tutorial_general_200.png
[201]: ./media/spaceiq-tutorial/tutorial_general_201.png
[202]: ./media/spaceiq-tutorial/tutorial_general_202.png
[203]: ./media/spaceiq-tutorial/tutorial_general_203.png

