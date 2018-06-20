---
title: 'Samouczek: Integracji Azure Active Directory z maszynami Wirtualnymi IQNavigator | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i IQNavigator maszyn wirtualnych.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: e1219134b398219d653a87b18aa9ed582f84a33d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226275"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Samouczek: Integracji Azure Active Directory z IQNavigator maszyny Wirtualne

Z tego samouczka dowiesz się integrowanie IQNavigator maszyn wirtualnych w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD IQNavigator maszyn wirtualnych zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do maszyn wirtualnych IQNavigator
- Umożliwia użytkownikom automatycznie pobrać podpisany w przypadku maszyn wirtualnych IQNavigator (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z maszynami Wirtualnymi IQNavigator, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Maszyny Wirtualne IQNavigator logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna tutaj [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie IQNavigator maszyn wirtualnych z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Dodawanie IQNavigator maszyn wirtualnych z galerii
Aby skonfigurować integrację usługi Azure AD IQNavigator maszyn wirtualnych, należy dodać IQNavigator maszyn wirtualnych z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać IQNavigator maszyn wirtualnych z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **maszyn wirtualnych IQNavigator**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_search.png)

5. W panelu wyników wybierz **maszyn wirtualnych IQNavigator**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i test usługi Azure AD rejestracji jednokrotnej z maszynami Wirtualnymi IQNavigator w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednikiem na maszynach wirtualnych IQNavigator jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi na maszynach wirtualnych IQNavigator musi się.

Na maszynach wirtualnych IQNavigator, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z maszynami Wirtualnymi IQNavigator, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego maszyn wirtualnych IQNavigator](#creating-a-iqnavigator-vms-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta IQNavigator maszyn wirtualnych, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji IQNavigator maszyn wirtualnych.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z maszynami Wirtualnymi IQNavigator, wykonaj następujące czynności:**

1. W portalu Azure na **maszyn wirtualnych IQNavigator** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Konfigurowanie rejestracji jednokrotnej](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_samlbase.png)

3. Na **IQNavigator domeny maszyn wirtualnych i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL:`iqn.com`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, wykonaj następujące kroki:

    ![Konfigurowanie rejestracji jednokrotnej](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url1.png)

    W **przekazywania stanu** tekstowym, wpisz adres URL, używając następującego wzorca:`https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Zaktualizować te wartości z faktycznym stanem adres URL odpowiedzi i przekazywania. Skontaktuj się z [zespołem pomocy technicznej klienta maszyn wirtualnych IQNavigator](https://www.beeline.com/iqn-product-support/) uzyskać te wartości.

5. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/iqnavigatorvms-tutorial/tutorial_metadataurl.png)

6. Aplikacja IQNavigator oczekiwać, że wartość identyfikatora unikatowego użytkownika oświadczenia identyfikatora nazwy. Klienta można mapować poprawną wartość oświadczenia identyfikatora nazwy. W takim przypadku zamapowaniu możemy użytkownika. UserPrincipalName w celu demonstracyjnej. Jednak zgodnie z ustawieniami w Twojej organizacji należy mapować poprawną wartość dla niego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_attribute.png)

7. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/iqnavigatorvms-tutorial/tutorial_general_400.png)

8. Na **konfiguracji maszyn wirtualnych IQNavigator** , kliknij przycisk **Konfigurowanie maszyn wirtualnych IQNavigator** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_configure.png)

9. Aby skonfigurować logowanie jednokrotne w **IQNavigator maszyn wirtualnych** stronie, musisz wysłać **adres Url metadanych Federacji aplikacji**, **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi**do [IQNavigator maszyn wirtualnych z pomocą techniczną](https://www.beeline.com/iqn-product-support/). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_03.png)

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-iqnavigator-vms-test-user"></a>Tworzenie użytkownika testowego IQNavigator maszyny Wirtualne

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w IQNavigator maszyn wirtualnych. Praca z [IQNavigator maszyn wirtualnych z pomocą techniczną](https://www.beeline.com/iqn-product-support/) Aby dodać użytkowników w ramach konta IQNavigator maszyn wirtualnych.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do IQNavigator maszyn wirtualnych.

![Przypisz użytkownika][200]

**Aby przypisać Simona Britta IQNavigator maszyn wirtualnych, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **maszyn wirtualnych IQNavigator**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka IQNavigator maszyn wirtualnych w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji IQNavigator maszyn wirtualnych.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/iqnavigatorvms-tutorial/tutorial_general_01.png
[2]: ./media/iqnavigatorvms-tutorial/tutorial_general_02.png
[3]: ./media/iqnavigatorvms-tutorial/tutorial_general_03.png
[4]: ./media/iqnavigatorvms-tutorial/tutorial_general_04.png

[100]: ./media/iqnavigatorvms-tutorial/tutorial_general_100.png

[200]: ./media/iqnavigatorvms-tutorial/tutorial_general_200.png
[201]: ./media/iqnavigatorvms-tutorial/tutorial_general_201.png
[202]: ./media/iqnavigatorvms-tutorial/tutorial_general_202.png
[203]: ./media/iqnavigatorvms-tutorial/tutorial_general_203.png

