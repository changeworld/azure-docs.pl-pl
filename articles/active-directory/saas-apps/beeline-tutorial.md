---
title: 'Samouczek: Integracji Azure Active Directory z BeeLine | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i BeeLine.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0726859d-1dac-44a0-810b-da56d89039ee
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 714213a644dd9897a388d5a762f75313c548f079
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35940462"
---
# <a name="tutorial-azure-active-directory-integration-with-beeline"></a>Samouczek: Integracji Azure Active Directory z BeeLine

Z tego samouczka dowiesz się integrowanie BeeLine z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD BeeLine zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do BeeLine
- Umożliwia użytkownikom automatycznie pobrać zalogowane do BeeLine (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z BeeLine, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- BeeLine logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie BeeLine z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-beeline-from-the-gallery"></a>Dodawanie BeeLine z galerii
Aby skonfigurować integrację usługi Azure AD BeeLine, należy dodać BeeLine z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać BeeLine z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **BeeLine**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/tutorial_beeline_search.png)

5. W panelu wyników wybierz **BeeLine**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/tutorial_beeline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z BeeLine na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w BeeLine jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w BeeLine musi się.

W BeeLine, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z BeeLine, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego BeeLine](#creating-a-beeline-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta BeeLine połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji BeeLine.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z BeeLine, wykonaj następujące czynności:**

1. W portalu Azure na **BeeLine** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/beeline-tutorial/tutorial_beeline_samlbase.png)

3. Na **BeeLine domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/beeline-tutorial/tutorial_beeline_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://projects.beeline.net/<instancename>`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://projects.beeline.net/<instancename>/SSO_External.ashx`|
    | `https://projects.beeline.net/<companyname>/SSO_External.ashx` |

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej BeeLine](https://www.beeline.com/contact-us/) uzyskać te wartości.
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/beeline-tutorial/tutorial_beeline_certificate.png) 

5. Aplikacja Beeline oczekuje potwierdzenia języka SAML w określonym formacie. We współpracy z [zespołem pomocy technicznej BeeLine](https://www.beeline.com/contact-us/) najpierw, aby zidentyfikować identyfikator użytkownika, który zostanie zamapowane do aplikacji. Należy również wziąć ze wskazówkami [zespołem pomocy technicznej BeeLine](https://www.beeline.com/contact-us/) o atrybut, który będzie używał do tego mapowania. Wartość tego atrybutu z można zarządzać **atrybuty użytkownika** aplikacji. Poniższy zrzut ekranu przedstawia przykład tego. W tym miejscu możemy zamapowaniu **identyfikator użytkownika** oświadczenie z **userprincipalname** atrybut, który zawiera identyfikator unikatowy użytkownika, które zostanie wysłane do aplikacji Beeline w każdym pomyślnej odpowiedzi SAML.

    ![Konfigurowanie rejestracji jednokrotnej](./media/beeline-tutorial/tutorial_attribute.png)    

6. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/beeline-tutorial/tutorial_general_400.png)

7. Na **konfiguracji BeeLine** , kliknij przycisk **skonfigurować BeeLine** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL** i **SAML identyfikator jednostki** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/beeline-tutorial/tutorial_beeline_configure.png) 

8. Skonfigurować logowanie jednokrotne w **BeeLine** stronie, musisz wysłać pobrany **XML metadanych** i **identyfikator jednostki SAML**, **Sign-Out adres URL** do [zespołem pomocy technicznej BeeLine](https://www.beeline.com/contact-us/).

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/beeline-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-beeline-test-user"></a>Tworzenie użytkownika testowego BeeLine

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Beeline. Aplikacja beeline musi wszystkich użytkowników na potrzeby aprowizacji w aplikacji przed wykonaniem rejestracji jednokrotnej. Aby pracować z [zespołem pomocy technicznej BeeLine](https://www.beeline.com/contact-us/) do udostępnienia tych użytkowników do aplikacji. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu BeeLine.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta BeeLine, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **BeeLine**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/beeline-tutorial/tutorial_beeline_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu. Po kliknięciu kafelka Beeline w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Beeline.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/beeline-tutorial/tutorial_general_01.png
[2]: ./media/beeline-tutorial/tutorial_general_02.png
[3]: ./media/beeline-tutorial/tutorial_general_03.png
[4]: ./media/beeline-tutorial/tutorial_general_04.png

[100]: ./media/beeline-tutorial/tutorial_general_100.png

[200]: ./media/beeline-tutorial/tutorial_general_200.png
[201]: ./media/beeline-tutorial/tutorial_general_201.png
[202]: ./media/beeline-tutorial/tutorial_general_202.png
[203]: ./media/beeline-tutorial/tutorial_general_203.png

