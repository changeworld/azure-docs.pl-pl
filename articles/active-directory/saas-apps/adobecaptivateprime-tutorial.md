---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą programu Adobe Captivate Prime | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Adobe Captivate Północnej.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f95b226-1465-47f4-b8b7-de4b0772abbc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: jeedes
ms.openlocfilehash: bbeae2cadde3e64f17b20eafabaf5e2dbf5a5cc6
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044079"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Samouczek: Integracja usługi Azure Active Directory z Adobe Captivate Prime

W tym samouczku dowiesz się, jak zintegrować Adobe Captivate Prime przy użyciu usługi Azure Active Directory (Azure AD).

Integrowanie Adobe Captivate Prime z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Adobe Captivate Prime.
- Użytkowników, aby automatycznie uzyskać zalogowanych do Adobe Captivate Prime (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z Adobe Captivate Prime, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Adobe Captivate pierwsze logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Adobe Captivate Prime z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>Dodawanie Adobe Captivate Prime z galerii
Aby skonfigurować integrację programu Adobe Captivate Prime w usłudze Azure AD, należy dodać Adobe Captivate Prime z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Adobe Captivate Prime z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Adobe Captivate Prime**, wybierz opcję **Adobe Captivate Prime** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Adobe Captivate pierwsze na liście wyników](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Prime Captivate Adobe oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Adobe Captivate Prime jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Adobe Captivate Prime musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Adobe Captivate Prime, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Adobe Captivate Prime](#create-an-adobe-captivate-prime-test-user)**  — aby odpowiednikiem Britta Simon w Captivate Prime Adobe, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Adobe Captivate Prime.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Adobe Captivate Prime, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Adobe Captivate Prime** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_samlbase.png)

3. Na **Adobe Captivate Prime domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Adobe Captivate Prime domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://captivateprime.adobe.com`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL: `https://captivateprime.adobe.com/saml/SSO`

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/adobecaptivateprime-tutorial/tutorial_general_400.png)

6. Przejdź do **właściwości** kartę, skopiuj **adres URL dostępu użytkownika** i wklej go w Notatniku.

    ![Link dostępu użytkownika](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

7. Aby skonfigurować logowanie jednokrotne na **Adobe Captivate Prime** stronie, musisz wysłać pobrany **XML metadanych** i skopiowany **adres URL dostępu użytkownika** do [Adobe Captivate zespołu pomocy technicznej Prime](mailto:captivateprimesupport@adobe.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/adobecaptivateprime-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/adobecaptivateprime-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/adobecaptivateprime-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/adobecaptivateprime-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-an-adobe-captivate-prime-test-user"></a>Tworzenie użytkownika testowego Adobe Captivate Prime

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Adobe Captivate Prime. Praca z [zespołem pomocy technicznej firmy Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com) Aby dodać użytkowników na platformie Adobe Captivate Prime. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Adobe Captivate Prime.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Adobe Captivate Prime, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Adobe Captivate Prime**.

    ![Łącze Adobe Captivate pierwsze na liście aplikacji](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Adobe Captivate Prime w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Adobe Captivate Prime.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobecaptivateprime-tutorial/tutorial_general_01.png
[2]: ./media/adobecaptivateprime-tutorial/tutorial_general_02.png
[3]: ./media/adobecaptivateprime-tutorial/tutorial_general_03.png
[4]: ./media/adobecaptivateprime-tutorial/tutorial_general_04.png

[100]: ./media/adobecaptivateprime-tutorial/tutorial_general_100.png

[200]: ./media/adobecaptivateprime-tutorial/tutorial_general_200.png
[201]: ./media/adobecaptivateprime-tutorial/tutorial_general_201.png
[202]: ./media/adobecaptivateprime-tutorial/tutorial_general_202.png
[203]: ./media/adobecaptivateprime-tutorial/tutorial_general_203.png

