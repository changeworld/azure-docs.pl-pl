---
title: 'Samouczek: Integracji Azure Active Directory z MaxxPoint | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i MaxxPoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 15ba026e-96fc-4ae8-b135-0169da810e99
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 6be07aa807501072f232d4371c7a4ad4d3fac10b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225473"
---
# <a name="tutorial-azure-active-directory-integration-with-maxxpoint"></a>Samouczek: Integracji Azure Active Directory z MaxxPoint

Z tego samouczka dowiesz się integrowanie MaxxPoint z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD MaxxPoint zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do MaxxPoint
- Umożliwia użytkownikom automatycznie pobrać zalogowane do MaxxPoint (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z MaxxPoint, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- MaxxPoint jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie MaxxPoint z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne


## <a name="adding-maxxpoint-from-the-gallery"></a>Dodawanie MaxxPoint z galerii
Aby skonfigurować integrację usługi Azure AD MaxxPoint, należy dodać MaxxPoint z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać MaxxPoint z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego, aby dodać nową aplikację.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **MaxxPoint**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/maxxpoint-tutorial/tutorial_maxxpoint_001.png)

5. W panelu wyników wybierz **MaxxPoint**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/maxxpoint-tutorial/tutorial_maxxpoint_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z MaxxPoint w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w MaxxPoint jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w MaxxPoint musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w MaxxPoint.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z MaxxPoint, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego MaxxPoint](#creating-a-maxxpoint-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta MaxxPoint połączonego z jej reprezentacji usługi Azure AD.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji MaxxPoint.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z MaxxPoint, wykonaj następujące czynności:**

1. W portalu Azure na **MaxxPoint** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/maxxpoint-tutorial/tutorial_general_300.png)

3. Na **MaxxPoint domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP zainicjował tryb**, nie trzeba wykonywać żadnych czynności.

    ![Konfigurowanie rejestracji jednokrotnej](./media/maxxpoint-tutorial/tutorial_maxxpoint_02.png)
    
4. Na **MaxxPoint domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **SP zainicjował tryb**, wykonaj następujące czynności:
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/maxxpoint-tutorial/tutorial_maxxpoint_03.png)

    a. Kliknij przycisk **Pokaż zaawansowane ustawienia adresu URL** opcji

    b. W **na adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://maxxpoint.westipc.com/default/sso/login/entity/<customer-id>-azure`

    > [!NOTE] 
    > Należy pamiętać, że nie jest rzeczywistą wartość. Należy zaktualizować tę wartość rzeczywista logowania na adres URL. Wywołanie MaxxPoint zespołu na **888-728-0950** aby zyskać tę wartość.

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/maxxpoint-tutorial/tutorial_maxxpoint_06.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/maxxpoint-tutorial/tutorial_general_400.png)

7. Uzyskanie logowania jednokrotnego skonfigurowane dla aplikacji, należy wywołać MaxxPoint zespołem pomocy technicznej dla **888-728-0950** i będą pomocne podczas dalszej dostarczania ich pobrany **XML metadanych** pliku. 

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/maxxpoint-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/maxxpoint-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/maxxpoint-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/maxxpoint-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**. 

### <a name="creating-a-maxxpoint-test-user"></a>Tworzenie użytkownika testowego MaxxPoint

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w MaxxPoint. Skontaktuj się z działem MaxxPoint zespołem pomocy technicznej na **888-728-0950** Aby dodać użytkowników w aplikacji MaxxPoint.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do udostępnienia jej MaxxPoint za pomocą usługi Azure rejestracji jednokrotnej.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta MaxxPoint, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **MaxxPoint**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/maxxpoint-tutorial/tutorial_maxxpoint_50.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka MaxxPoint w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji MaxxPoint.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/maxxpoint-tutorial/tutorial_general_01.png
[2]: ./media/maxxpoint-tutorial/tutorial_general_02.png
[3]: ./media/maxxpoint-tutorial/tutorial_general_03.png
[4]: ./media/maxxpoint-tutorial/tutorial_general_04.png

[100]: ./media/maxxpoint-tutorial/tutorial_general_100.png

[200]: ./media/maxxpoint-tutorial/tutorial_general_200.png
[201]: ./media/maxxpoint-tutorial/tutorial_general_201.png
[202]: ./media/maxxpoint-tutorial/tutorial_general_202.png
[203]: ./media/maxxpoint-tutorial/tutorial_general_203.png