---
title: 'Samouczek: Integracji Azure Active Directory z FilesAnywhere | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i FilesAnywhere.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: jeedes
ms.openlocfilehash: ac2b40087548c02d34afa7e86dd6e97ef10ac20e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35943489"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Samouczek: Integracji Azure Active Directory z FilesAnywhere

Z tego samouczka dowiesz się integrowanie FilesAnywhere z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD FilesAnywhere zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do FilesAnywhere
- Umożliwia użytkownikom automatycznie pobrać zalogowane do FilesAnywhere (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu zarządzania Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z FilesAnywhere, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- FilesAnywhere jednokrotnego włączone subskrypcji


> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.


Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie FilesAnywhere z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne


## <a name="adding-filesanywhere-from-the-gallery"></a>Dodawanie FilesAnywhere z galerii
Aby skonfigurować integrację usługi Azure AD FilesAnywhere, należy dodać FilesAnywhere z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać FilesAnywhere z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **FilesAnywhere**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_search.png)

5. W panelu wyników wybierz **FilesAnywhere**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z FilesAnywhere w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w FilesAnywhere jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w FilesAnywhere musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w FilesAnywhere.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z FilesAnywhere, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego FilesAnywhere](#creating-a-filesanywhere-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta FilesAnywhere połączonego z jej reprezentacji usługi Azure AD.
3. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
4. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu zarządzania Azure i skonfigurować logowanie jednokrotne w aplikacji FilesAnywhere.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z FilesAnywhere, wykonaj następujące czynności:**

1. W portalu zarządzania Azure na **FilesAnywhere** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okna dialogowego, jako **tryb** wybierz **na języku SAML logowania jednokrotnego** Włącz funkcji logowania jednokrotnego.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_samlbase.png)

3. Na **FilesAnywhere domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP zainicjował tryb**:

    ![Konfigurowanie rejestracji jednokrotnej](./media/filesanywhere-tutorial/tutorial_filesanywhere_url.png)
    
    a. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company name>.filesanywhere.com/saml20.aspx?c=215`
> [!NOTE]
> Należy pamiętać, że wartość **215** jest **clientid** i jest tylko przykładowe. Należy zastąpić wartość rzeczywista clientid.

4. Na **FilesAnywhere domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **SP zainicjował tryb**, wykonaj następujące czynności:
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/filesanywhere-tutorial/tutorial_filesanywhere_url1.png)

    a. Polecenie **Pokaż zaawansowane ustawienia adresu URL** opcji

    b. W **na adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<sub domain>.filesanywhere.com/`

    > [!NOTE] 
    > Należy pamiętać, że nie są one rzeczywiste wartości. Należy zaktualizować te wartości rzeczywistych na adres URL logowania i adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej FilesAnywhere](mailto:support@FilesAnywhere.com) uzyskać te wartości. 

5. Aplikacja FilesAnywhere oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/filesanywhere-tutorial/tutorial_filesanywhere_attribute.png)
    
    Gdy użytkownicy zarejestrowaniu z FilesAnywhere otrzymują wartość **clientid** atrybutu z [zespołu FilesAnywhere](mailto:support@FilesAnywhere.com). Należy dodać atrybut "Identyfikator klienta" z unikatowych wartości dostarczone przez FilesAnywhere. Te atrybuty pokazanym powyżej są wymagane.
    > [!NOTE] 
    > Należy pamiętać, że wartość **2331** z **clientid** jest tylko przykładowe. Należy podać rzeczywiste wartości.


6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |    
    | ClientID | *"uniquevalue"* |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **Ok**

7. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/filesanywhere-tutorial/tutorial_general_400.png)

8. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_certificate.png) 

9. Na **konfiguracji FilesAnywhere** , kliknij przycisk **skonfigurować FilesAnywhere** otworzyć **Konfigurowanie logowania jednokrotnego** okna.

    ![Konfigurowanie rejestracji jednokrotnej](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configure.png) 

    ![Konfigurowanie rejestracji jednokrotnej](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configuresignon.png)

10. Aby uzyskać pełną konfiguracji logowania jednokrotnego dla aplikacji na końcu FilesAnywhere, skontaktuj się z [zespołem pomocy technicznej FilesAnywhere](mailto:support@FilesAnywhere.com) i podaj pobrany tokenu SAML podpisywania certyfikatu i jednego znaku w rejestracji jednokrotnej (SSO) adres URL.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**. 



### <a name="creating-a-filesanywhere-test-user"></a>Tworzenie użytkownika testowego FilesAnywhere

Aplikacja obsługuje tylko w czasie Inicjowanie obsługi użytkowników i uwierzytelnianie użytkowników zostaną utworzone w aplikacji automatycznie. 


### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do udostępnienia jej FilesAnywhere za pomocą usługi Azure rejestracji jednokrotnej.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta FilesAnywhere, wykonaj następujące czynności:**

1. Otwórz widok aplikacji w portalu zarządzania Azure, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **FilesAnywhere**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    


### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka FilesAnywhere w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji FilesAnywhere.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/FilesAnywhere-tutorial/tutorial_general_01.png
[2]: ./media/FilesAnywhere-tutorial/tutorial_general_02.png
[3]: ./media/FilesAnywhere-tutorial/tutorial_general_03.png
[4]: ./media/FilesAnywhere-tutorial/tutorial_general_04.png

[100]: ./media/FilesAnywhere-tutorial/tutorial_general_100.png

[200]: ./media/FilesAnywhere-tutorial/tutorial_general_200.png
[201]: ./media/FilesAnywhere-tutorial/tutorial_general_201.png
[202]: ./media/FilesAnywhere-tutorial/tutorial_general_202.png
[203]: ./media/FilesAnywhere-tutorial/tutorial_general_203.png
