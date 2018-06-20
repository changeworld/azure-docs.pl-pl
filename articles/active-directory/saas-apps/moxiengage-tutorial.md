---
title: 'Samouczek: Integracji Azure Active Directory z Uwzględnij Moxi | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Uwzględnij Moxi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1135a879-8f00-43b0-ac8a-831593d9586d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jeedes
ms.openlocfilehash: 5e77858edc3acfe8491c90eac3b8a146a230556e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213653"
---
# <a name="tutorial-azure-active-directory-integration-with-moxi-engage"></a>Samouczek: Integracji Azure Active Directory z Moxi zaangażowania

Z tego samouczka dowiesz się integrowanie Uwzględnij Moxi w usłudze Azure Active Directory (Azure AD).

Integrowanie Uwzględnij Moxi z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do udziału Moxi
- Umożliwia użytkownikom automatycznie pobrać zalogowane nawiązanie Moxi (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Uwzględnij Moxi, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Uwzględnij Moxi jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie udziału Moxi z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-moxi-engage-from-the-gallery"></a>Dodawanie udziału Moxi z galerii
Aby skonfigurować integrację Uwzględnij Moxi do usługi Azure AD, należy dodać Uwzględnij Moxi z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Uwzględnij Moxi z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Uwzględnij Moxi**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxiengage-tutorial/tutorial_moxiengage_search.png)

5. W panelu wyników wybierz **Uwzględnij Moxi**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxiengage-tutorial/tutorial_moxiengage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Moxi Uwzględnij oparte na koncie użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Uwzględnij Moxi jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w Uwzględnij Moxi musi określone.

Uwzględnij Moxi, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Uwzględnij Moxi, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Uwzględnij Moxi](#creating-a-moxi-engage-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Moxi Uwzględnij połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Uwzględnij Moxi.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Uwzględnij Moxi, wykonaj następujące czynności:**

1. W portalu Azure na **Uwzględnij Moxi** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/moxiengage-tutorial/tutorial_moxiengage_samlbase.png)

3. Na **Moxi Uwzględnij domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/moxiengage-tutorial/tutorial_moxiengage_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://svc.<moxiworks-integration-domain>/service/v1/auth/inbound/saml/aad`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Uwzględnij Moxi](mailto:support@moxiworks.com) aby zyskać tę wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/moxiengage-tutorial/tutorial_moxiengage_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/moxiengage-tutorial/tutorial_general_400.png)

6. Skonfigurować logowanie jednokrotne w **Uwzględnij Moxi** stronie, musisz wysłać pobrany **XML metadanych** do [Moxi Uwzględnij zespołem pomocy technicznej](mailto:support@moxiworks.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxiengage-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxiengage-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxiengage-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxiengage-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-moxi-engage-test-user"></a>Tworzenie użytkownika testowego Uwzględnij Moxi

W tej sekcji można utworzyć użytkownika o nazwie Simona Britta w Moxi zaangażowania. Praca z [Moxi Uwzględnij zespołem pomocy technicznej](mailto:support@moxiworks.com) Aby dodać użytkowników na platformie Moxi zaangażowania.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do Moxi zaangażowania.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta nawiązanie Moxi, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Uwzględnij Moxi**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/moxiengage-tutorial/tutorial_moxiengage_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Uwzględnij Moxi w panelu dostępu, należy pobrać automatycznego logowania do aplikacji Moxi zaangażowania.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/moxiengage-tutorial/tutorial_general_01.png
[2]: ./media/moxiengage-tutorial/tutorial_general_02.png
[3]: ./media/moxiengage-tutorial/tutorial_general_03.png
[4]: ./media/moxiengage-tutorial/tutorial_general_04.png

[100]: ./media/moxiengage-tutorial/tutorial_general_100.png

[200]: ./media/moxiengage-tutorial/tutorial_general_200.png
[201]: ./media/moxiengage-tutorial/tutorial_general_201.png
[202]: ./media/moxiengage-tutorial/tutorial_general_202.png
[203]: ./media/moxiengage-tutorial/tutorial_general_203.png

