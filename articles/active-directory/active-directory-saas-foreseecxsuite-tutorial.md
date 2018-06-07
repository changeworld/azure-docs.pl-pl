---
title: 'Samouczek: Azure Active Directory integracji z pakietem CX przewidywany jest | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługi Azure Active Directory i pakiet CX przewidzieć.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.openlocfilehash: 62ce16969f31a8909218495e3f130bc6713622dc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655011"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Samouczek: Integracji Azure Active Directory z przewidzieć Suite CX

Z tego samouczka dowiesz sposobu integracji z usługą Azure Active Directory (Azure AD) przewidzieć Suite CX.

Integrowanie przewidzieć Suite CX z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do zestawu CX przewidzieć.
- Umożliwia użytkownikom automatycznie pobrać zalogowane przewidzieć CX pakietu (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem CX przewidzieć, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Przewidują CX pakiet rejestracji jednokrotnej włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie pakietu CX przewidzieć z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Dodawanie pakietu CX przewidzieć z galerii
Aby skonfigurować integrację usługi Azure AD przewidzieć Suite CX, należy dodać pakiet CX przewidzieć z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać pakiet CX przewidzieć z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **przewidzieć Suite CX**, wybierz pozycję **przewidzieć Suite CX** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Przewidują CX pakiet z listy wyników](./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z pakietem CX przewidywany jest oparty na koncie użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednikiem pakietu przewidzieć CX jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi pakietu przewidzieć CX musi określone.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z pakietem CX przewidzieć, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego przewidzieć Suite CX](#create-a-foresee-cx-suite-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta przewidzieć CX pakiet, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji przewidzieć Suite CX.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z pakietem CX przewidzieć, wykonaj następujące czynności:**

1. W portalu Azure na **przewidzieć Suite CX** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

3. Na **adresy URL i przewidzieć domeny Suite CX** sekcji, jeśli masz **pliku metadanych dostawcy usług**, wykonaj następujące czynności:

    ![Przewidują CX Suite domeny i adres URL pojedynczego logowania jednokrotnego informacji](./media/active-directory-saas-foreseecxsuite-tutorial/upload.png)

    a. Kliknij przycisk **przekazywania pliku metadanych**.

    ![Przewidują CX Suite domeny i adres URL pojedynczego logowania jednokrotnego informacji](./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Polecenie **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazać**.

    c. Po pomyślnym zakończeniu przekazywania **pliku metadanych dostawcy usług** **identyfikator** wartości get automatycznie wypełnione **adresy URL i przewidzieć domeny Suite CX** sekcji pole tekstowe, jak pokazano poniżej:

    ![Przewidują CX Suite domeny i adres URL pojedynczego logowania jednokrotnego informacji](./media/active-directory-saas-foreseecxsuite-tutorial/urlupload.png)

4. Jeśli nie masz **pliku metadanych dostawcy usług**, wykonaj następujące czynności:

    ![Przewidują CX Suite domeny i adres URL pojedynczego logowania jednokrotnego informacji](./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL: `https://cxsuite.foresee.com/`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwe. Zaktualizuj tę wartość z rzeczywistego identyfikatora. Skontaktuj się z [zespołem pomocy technicznej przewidzieć CX pakiet klienta](mailto:support@foresee.com) aby zyskać tę wartość.

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_general_400.png)

7. Skonfigurować logowanie jednokrotne w **przewidzieć Suite CX** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej przewidzieć Suite CX](mailto:support@foresee.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-foreseecxsuite-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-foreseecxsuite-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-foreseecxsuite-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-foreseecxsuite-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-foresee-cx-suite-test-user"></a>Tworzenie użytkownika testowego przewidzieć Suite CX

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta pakietu przewidzieć CX. Praca z [zespołem pomocy technicznej przewidzieć Suite CX](mailto:support@foresee.com) do dodawania użytkowników lub domeny, w których są potrzebne, aby być białej platformy przewidzieć Suite CX. Jeśli domeny zostanie dodany przez zespół, użytkownicy będą uzyskać automatycznie przygotowana do platformy przewidzieć Suite CX. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do zestawu CX przewidzieć.

![Przypisanie roli użytkownika][200]

**Aby przypisać Simona Britta przewidzieć pakietu CX, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **przewidzieć Suite CX**.

    ![Łącze Suite CX przewidywany jest na liście aplikacji](./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka przewidzieć Suite CX w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane przewidzieć CX pakiet aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-foreseecxsuite-tutorial/tutorial_general_203.png

