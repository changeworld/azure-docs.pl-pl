---
title: 'Samouczek: Integracji Azure Active Directory z Convercent | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Convercent.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9c9d290-0e13-490b-b559-0be772d6a690
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: jeedes
ms.openlocfilehash: c0fa4c3f76fa1ceaab6ec09f46d6a0f55391c1cc
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938818"
---
# <a name="tutorial-azure-active-directory-integration-with-convercent"></a>Samouczek: Integracji Azure Active Directory z Convercent

Z tego samouczka dowiesz się integrowanie Convercent z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Convercent zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Convercent
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Convercent (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Convercent, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Convercent jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Convercent z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-convercent-from-the-gallery"></a>Dodawanie Convercent z galerii
Aby skonfigurować integrację usługi Azure AD Convercent, należy dodać Convercent z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Convercent z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Convercent**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/convercent-tutorial/tutorial_convercent_search.png)

5. W panelu wyników wybierz **Convercent**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/convercent-tutorial/tutorial_convercent_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Convercent na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Convercent jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Convercent musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w Convercent.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Convercent, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Convercent](#creating-a-convercent-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Convercent połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Convercent.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Convercent, wykonaj następujące czynności:**

1. W portalu Azure na **Convercent** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/convercent-tutorial/tutorial_convercent_samlbase.png)

3. Na **Convercent domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP zainicjował tryb**, wykonaj następujące kroki:

    ![Konfigurowanie rejestracji jednokrotnej](./media/convercent-tutorial/tutorial_convercent_url.png)

    W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<instancename>.convercent.com/`
 
4. Jeśli chcesz skonfigurować aplikację w **SP zainicjował tryb**na **Convercent domeny i adres URL** sekcji należy wykonać następujące czynności:
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/convercent-tutorial/tutorial_convercent_url1.png)

     a. Kliknij przycisk **"Pokaż zaawansowane ustawienia adresu URL."** 

     b. W **na adres URL logowania** tekstowym, wpisz wartość, przy użyciu następującego wzorca: `https://<instancename>.convercent.com/`

     c. W **stan przekazywania** tekstowym, wpisz wartość, przy użyciu następującego wzorca: `https://<instancename>.convercent.com/`

    > [!NOTE] 
    > Wartości te nie są wartości rzeczywistych. Zaktualizować te wartości z rzeczywistego identyfikatora, na adres URL logowania i stan przekazywania. Skontaktuj się z [zespołem pomocy technicznej klienta Convercent](http://support.convercent.com) uzyskać te wartości.

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/convercent-tutorial/tutorial_convercent_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/convercent-tutorial/tutorial_general_400.png)

7. Aby uzyskać logowania jednokrotnego skonfigurowane dla aplikacji, skontaktuj się z [zespołem pomocy technicznej Convercent](mailto:support@convercent.com) i udostępnia je z pobranego **XML metadanych**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/convercent-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/convercent-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/convercent-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/convercent-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-convercent-test-user"></a>Tworzenie użytkownika testowego Convercent

Praca z [zespołem pomocy technicznej Convercent](mailto:support@convercent.com) Aby dodać użytkowników do platformy Convercent.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Convercent.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Convercent, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Convercent**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/convercent-tutorial/tutorial_convercent_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Convercent w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Convercent.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/convercent-tutorial/tutorial_general_01.png
[2]: ./media/convercent-tutorial/tutorial_general_02.png
[3]: ./media/convercent-tutorial/tutorial_general_03.png
[4]: ./media/convercent-tutorial/tutorial_general_04.png

[100]: ./media/convercent-tutorial/tutorial_general_100.png

[200]: ./media/convercent-tutorial/tutorial_general_200.png
[201]: ./media/convercent-tutorial/tutorial_general_201.png
[202]: ./media/convercent-tutorial/tutorial_general_202.png
[203]: ./media/convercent-tutorial/tutorial_general_203.png

