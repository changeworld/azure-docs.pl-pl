---
title: 'Samouczek: Integracji Azure Active Directory z Inkling | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Inkling.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 64c7ee45-ee8a-42f7-bf04-fd0e00833ea9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 20a3212a0d4832bd64a5ca83dbef0e6fe4e2fa47
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340063"
---
# <a name="tutorial-azure-active-directory-integration-with-inkling"></a>Samouczek: Integracji Azure Active Directory z Inkling

Z tego samouczka dowiesz się integrowanie Inkling z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Inkling zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Inkling
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Inkling (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu zarządzania Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Inkling, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Inkling jednokrotnego włączone subskrypcji


> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.


Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Inkling z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne


## <a name="adding-inkling-from-the-gallery"></a>Dodawanie Inkling z galerii
Aby skonfigurować integrację usługi Azure AD Inkling, należy dodać Inkling z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Inkling z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Inkling**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_001.png)

5. W panelu wyników wybierz **Inkling**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Inkling w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Inkling jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Inkling musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w Inkling.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Inkling, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Inkling](#creating-an-inkling-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Inkling połączonego z jej reprezentacji usługi Azure AD.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu zarządzania Azure i skonfigurować logowanie jednokrotne w aplikacji Inkling.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Inkling, wykonaj następujące czynności:**

1. W portalu zarządzania Azure na **Inkling** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okna dialogowego, jako **tryb** wybierz **na języku SAML logowania jednokrotnego** Włącz funkcji logowania jednokrotnego.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-inkling-tutorial/tutorial_general_300.png)
    
3. Na **Inkling domeny i adres URL** sekcji, wykonaj następujące czynności:
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_01.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://api.inkling.com/saml/v2/metadata/<user-id>`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://api.inkling.com/saml/v2/acs/<user-id>`

    > [!NOTE] 
    > Należy pamiętać, że nie są one rzeczywiste wartości. Należy zaktualizować te wartości z rzeczywistego identyfikatora i adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej Inkling](mailto:press@inkling.com) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **Utwórz nowy certyfikat**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-inkling-tutorial/tutorial_general_400.png)    

5. Na **Tworzenie nowego świadectwa** okna dialogowego, kliknij ikonę kalendarza i wybierz **Data wygaśnięcia**. Następnie kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-inkling-tutorial/tutorial_general_500.png)

6. Na **certyfikat podpisywania SAML** wybierz opcję **uaktywnić nowego świadectwa** i kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_02.png)

7. W oknie podręcznym **certyfikat przerzucania** okna, kliknij przycisk **OK**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-inkling-tutorial/tutorial_general_600.png)

8. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_03.png) 

9. Aby uzyskać logowania jednokrotnego skonfigurowane dla aplikacji, skontaktuj się z [Inkling zespołem pomocy technicznej](mailto:press@inkling.com) i udostępniać je z pobrane **metadanych**. 


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-inkling-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-inkling-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-inkling-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-inkling-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**. 



### <a name="creating-an-inkling-test-user"></a>Tworzenie użytkownika testowego Inkling

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Inkling. We współpracy z [zespołem pomocy technicznej Inkling](mailto:press@inkling.com) Aby dodać użytkowników do platformy Inkling.


### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do udostępnienia jej Inkling za pomocą usługi Azure rejestracji jednokrotnej.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Inkling, wykonaj następujące czynności:**

1. Otwórz widok aplikacji w portalu zarządzania Azure, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Inkling**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-inkling-tutorial/tutorial_inkling_50.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    


### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Inkling w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Inkling.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-inkling-tutorial/tutorial_general_203.png