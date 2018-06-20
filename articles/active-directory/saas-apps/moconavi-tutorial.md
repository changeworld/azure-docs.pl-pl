---
title: 'Samouczek: Integracji Azure Active Directory z moconavi | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i moconavi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e1916224-e1c2-426f-b233-0a2518fa41db
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: jeedes
ms.openlocfilehash: 3467b823e6c91d34ebd48c7f8bc29558a79c59e5
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229550"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>Samouczek: Integracji Azure Active Directory z moconavi

Z tego samouczka dowiesz się integrowanie moconavi z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD moconavi zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do moconavi.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do moconavi (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z moconavi, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Moconavi logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie moconavi z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-moconavi-from-the-gallery"></a>Dodawanie moconavi z galerii
Aby skonfigurować integrację usługi Azure AD moconavi, należy dodać moconavi z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać moconavi z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **moconavi**, wybierz pozycję **moconavi** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![moconavi na liście wyników](./media/moconavi-tutorial/tutorial_moconavi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z moconavi w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w moconavi jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w moconavi musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z moconavi, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego moconavi](#create-a-moconavi-test-user)**  — mają odpowiednika Simona Britta w moconavi połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji moconavi.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z moconavi, wykonaj następujące czynności:**

1. W portalu Azure na **moconavi** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/moconavi-tutorial/tutorial_moconavi_samlbase.png)

3. Na **moconavi domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![moconavi domeny i adres URL pojedynczego logowania jednokrotnego informacji](./media/moconavi-tutorial/tutorial_moconavi_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<yourserverurl>/moconavi-saml2/saml/login`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<yourserverurl>/moconavi-saml2`

    C. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<yourserverurl>/moconavi-saml2/saml/SSO`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta moconavi](mailto:support@recomot.co.jp) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/moconavi-tutorial/tutorial_moconavi_certificate.png)

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/moconavi-tutorial/tutorial_general_400.png)

6. Skonfigurować logowanie jednokrotne w **moconavi** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej moconavi](mailto:support@recomot.co.jp). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/moconavi-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/moconavi-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/moconavi-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/moconavi-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-moconavi-test-user"></a>Tworzenie użytkownika testowego moconavi

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w moconavi. Praca z [zespołem pomocy technicznej moconavi](mailto:support@recomot.co.jp) Aby dodać użytkowników do platformy moconavi. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do moconavi.

![Przypisanie roli użytkownika][200]

**Aby przypisać Simona Britta moconavi, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **moconavi**.

    ![Łącze moconavi na liście aplikacji](./media/moconavi-tutorial/tutorial_moconavi_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

1. Zainstaluj moconavi ze sklepu firmy Microsoft.

2. Uruchom moconavi.

3. Kliknij przycisk **połączyć ustawienie** przycisku.

    ![Testowanie rejestracji jednokrotnej](./media/moconavi-tutorial/testing1.png)

4. Wprowadź `https://mcs-admin.moconavi.biz/gateway` do **nawiązywanie połączenia z adresem URL** pola tekstowego, a następnie kliknij przycisk **gotowe** przycisku.

    ![Testowanie rejestracji jednokrotnej](./media/moconavi-tutorial/testing2.png)

5. Na poniższym zrzucie ekranu wykonaj następujące czynności:

    ![Testowanie rejestracji jednokrotnej](./media/moconavi-tutorial/testing3.png)

    a. Wprowadź **klucz uwierzytelniania danych wejściowych**:`azureAD` do **klucz uwierzytelniania danych wejściowych** pola tekstowego.

    b. Wprowadź **dane wejściowe użytkownika identyfikator**: `your ad account` do **dane wejściowe użytkownika identyfikator** pole tekstowe.

    c. Kliknij przycisk **logowania**.

6. Wprowadź hasło do usługi Azure AD **hasło** pola tekstowego, a następnie kliknij przycisk **logowania** przycisku.

    ![Testowanie rejestracji jednokrotnej](./media/moconavi-tutorial/testing4.png)

7. Usługa Azure AD authentication zakończy się pomyślnie, gdy zostanie wyświetlone menu.

    ![Testowanie rejestracji jednokrotnej](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/moconavi-tutorial/tutorial_general_01.png
[2]: ./media/moconavi-tutorial/tutorial_general_02.png
[3]: ./media/moconavi-tutorial/tutorial_general_03.png
[4]: ./media/moconavi-tutorial/tutorial_general_04.png

[100]: ./media/moconavi-tutorial/tutorial_general_100.png

[200]: ./media/moconavi-tutorial/tutorial_general_200.png
[201]: ./media/moconavi-tutorial/tutorial_general_201.png
[202]: ./media/moconavi-tutorial/tutorial_general_202.png
[203]: ./media/moconavi-tutorial/tutorial_general_203.png

