---
title: 'Samouczek: Integracji Azure Active Directory z Atomic Learning | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługi Azure Active Directory i uczenie się Atomic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 495f54a6-e6c4-41b0-aafa-a6283d33efc8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: jeedes
ms.openlocfilehash: 785a2af9cf736bad0aa0520898664c2939720a78
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212121"
---
# <a name="tutorial-azure-active-directory-integration-with-atomic-learning"></a>Samouczek: Integracji Azure Active Directory z Atomic Learning

Z tego samouczka dowiesz się integrowanie Atomic Learning z usługą Azure Active Directory (Azure AD).

Integrowanie Atomic Learning z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do uczenia Atomic
- Umożliwia użytkownikom automatycznie pobrać zalogowane do uczenia niepodzielne (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu Atomic Learning, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Atomic Learning jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Atomic uczenia z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-atomic-learning-from-the-gallery"></a>Dodawanie Atomic uczenia z galerii
Aby skonfigurować integrację Atomic Learning z usługą Azure AD, należy dodać Atomic uczenia z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Atomic uczenia z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Atomic Learning**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/atomiclearning-tutorial/tutorial_atomiclearning_search.png)

5. W panelu wyników wybierz **Atomic Learning**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/atomiclearning-tutorial/tutorial_atomiclearning_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu Atomic Learning na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w uczeniu Atomic jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w uczeniu Atomic musi się.

W uczeniu Atomic przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu Atomic Learning, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Atomic Learning](#creating-an-atomic-learning-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Atomic zapoznanie się z jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Atomic Learning.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu Atomic Learning, wykonaj następujące czynności:**

1. W portalu Azure na **Atomic Learning** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/atomiclearning-tutorial/tutorial_atomiclearning_samlbase.png)

3. Na **Atomic domeny uczenie i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/atomiclearning-tutorial/tutorial_atomiclearning_url.png)

     W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://secure2.atomiclearning.com/sso/shibboleth/<companyname>`
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Learning Atomic](mailto:cs@atomiclearning.com) aby zyskać tę wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/atomiclearning-tutorial/tutorial_atomiclearning_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/atomiclearning-tutorial/tutorial_general_400.png)

6. Skonfigurować logowanie jednokrotne w **Atomic Learning** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej Atomic Learning](mailto:cs@atomiclearning.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/atomiclearning-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/atomiclearning-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/atomiclearning-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/atomiclearning-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-atomic-learning-test-user"></a>Tworzenie użytkownika testowego Atomic Learning

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w uczeniu Atomic. Atomic Learning obsługę w czasie, który jest domyślnie włączone. 

Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu Atomic uczenia, jeśli nie istnieje jeszcze przy użyciu adresu e-mail użytkownika.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do uczenia Atomic.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Atomic uczenia, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Atomic Learning**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/atomiclearning-tutorial/tutorial_atomiclearning_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Atomic Learning w panelu dostępu należy należy pobrać automatycznie zalogowane do aplikacji Atomic Learning.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/atomiclearning-tutorial/tutorial_general_01.png
[2]: ./media/atomiclearning-tutorial/tutorial_general_02.png
[3]: ./media/atomiclearning-tutorial/tutorial_general_03.png
[4]: ./media/atomiclearning-tutorial/tutorial_general_04.png

[100]: ./media/atomiclearning-tutorial/tutorial_general_100.png

[200]: ./media/atomiclearning-tutorial/tutorial_general_200.png
[201]: ./media/atomiclearning-tutorial/tutorial_general_201.png
[202]: ./media/atomiclearning-tutorial/tutorial_general_202.png
[203]: ./media/atomiclearning-tutorial/tutorial_general_203.png

