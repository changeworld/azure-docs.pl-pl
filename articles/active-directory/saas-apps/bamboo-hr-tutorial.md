---
title: 'Samouczek: Integracji Azure Active Directory z BambooHR | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i BambooHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 927af22b3f043db3222b39355187832e299f448e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942731"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Samouczek: Integracji Azure Active Directory z BambooHR

Z tego samouczka dowiesz się integrowanie BambooHR z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD BambooHR zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do BambooHR.
- Można umożliwić użytkownikom automatycznie pobrać zalogowany do BambooHR przy użyciu rejestracji jednokrotnej (SSO) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji, portalu Azure.

Aby uzyskać więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z BambooHR, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Włączyć logowanie Jednokrotne BambooHR subskrypcji

> [!NOTE]
> Podczas testowania czynności w tym samouczku, firma Microsoft zaleca, aby nie używać do środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać bezpłatne, jeden miesiąc wersję próbną](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

Scenariusz, w tym samouczku przedstawiono składa się z dwóch głównych elementów:

1. Dodawanie BambooHR z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-bamboohr-from-the-gallery"></a>Dodaj BambooHR z galerii
Aby skonfigurować integrację usługi Azure AD BambooHR, Dodaj BambooHR z galerii do listy zarządzane aplikacje SaaS w następujący sposób:

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![W okienku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać aplikację, wybierz **nowej aplikacji**.

    !["Nowa aplikacja" przycisku][3]

4. W polu wyszukiwania wpisz **BambooHR**. Na liście wyników wybierz **BambooHR**, a następnie wybierz **Dodaj**.

    ![BambooHR na liście wyników](./media/bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować Azure AD SSO z BambooHR przy użyciu użytkownika testowego "Britta Simona".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, co jego odpowiednik użytkownika znajduje się w BambooHR. Innymi słowy musisz ustanowić łącze relacji między użytkownikiem usługi Azure AD i danemu użytkownikowi w BambooHR.

Ustanowienie relacji łącze w BambooHR, przypisz usługi Azure AD **nazwy użytkownika** wartość jako BambooHR **Username** wartość.

Aby konfiguracja i testowanie usługi Azure AD SSO z BambooHR, należy wykonać bloków konstrukcyjnych w następnych pięciu sekcjach.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji służy do włączenia funkcji logowania jednokrotnego usługi Azure AD w portalu Azure i konfigurowanie logowania jednokrotnego do aplikacji BambooHR w następujący sposób:

1. W portalu Azure na **BambooHR** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. W **logowanie jednokrotne** okna w **tryb** listy rozwijanej wybierz **na języku SAML logowania jednokrotnego**.
 
    ![Okno rejestracji jednokrotnej](./media/bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. W obszarze **BambooHR domeny i adres URL**, wykonaj następujące czynności:

    ![W sekcji BambooHR domeny i adres URL](./media/bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. W **Zaloguj się na adres URL** wpisz adres URL w następującym formacie: `https://<company>.bamboohr.com`.

    b. W **identyfikator** wpisz wartość: `BambooHR-SAML`.

    > [!NOTE] 
    > **Zaloguj się na adres URL** wartość nie jest prawdziwe. Zaktualizuj go z adresem URL rzeczywisty logowania jednokrotnego. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej klienta BambooHR](https://www.bamboohr.com/contact.php). 
 
4. W obszarze **SAML certyfikat podpisywania**, wybierz pozycję **certyfikatu (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Łącze pobierania certyfikatu](./media/bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Wybierz pozycję **Zapisz**.

    ![Przycisk Zapisz](./media/bamboo-hr-tutorial/tutorial_general_400.png)

6. W obszarze **konfiguracji BambooHR**, wybierz pozycję **skonfigurować BambooHR** otworzyć **Konfigurowanie logowania jednokrotnego** okna. W **krótkimi opisami** sekcji, skopiuj **SAML pojedynczy znak na adres URL usługi** do późniejszego użycia.

    ![Konfiguracja BambooHR](./media/bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. W nowym oknie należy zalogować się jako administrator do witryny firmy BambooHR.

8. Na stronie głównej wykonaj następujące czynności:
   
    ![Strona BambooHR rejestracji jednokrotnej](./media/bamboo-hr-tutorial/ic796691.png "rejestracji jednokrotnej")   

    a. Wybierz **aplikacji**.
   
    b. W **aplikacje** okienku wybierz **rejestracji jednokrotnej**.
   
    c. Wybierz **SAML logowania jednokrotnego**.

9. W **SAML logowania jednokrotnego** okienko, wykonaj następujące czynności:
   
    ![W okienku SAML logowania jednokrotnego](./media/bamboo-hr-tutorial/IC796692.png "SAML logowania jednokrotnego")
   
    a. Do **adres Url logowania jednokrotnego logowania** Wklej **SAML pojedynczy znak na adres URL usługi** skopiowany z portalu Azure w kroku 6.
      
    b. W programie Notatnik Otwórz base-64 zakodowanego certyfikatu, który został pobrany z portalu Azure, kopiować jego zawartości, a następnie wklej go do **certyfikatu X.509** pole.
   
    c. Wybierz pozycję **Zapisz**.

> [!TIP]
> Gdy podczas konfigurowania aplikacji, możesz przeczytać zwięzły wersji tych instrukcji w [portalu Azure](https://portal.azure.com). Po dodaniu aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** po prostu zaznacz **rejestracji jednokrotnej** karcie, a następnie przejść osadzone dokumentacji za pośrednictwem **konfiguracji** sekcji u dołu. Aby uzyskać informacje, zobacz [usługi Azure AD osadzonych dokumentacji]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego o nazwie Simona Britta w portalu Azure.

   ![Tworzenie użytkownika testowego usługi Azure AD Simona Britta][100]

Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](./media/bamboo-hr-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/bamboo-hr-tutorial/create_aaduser_02.png)

3. W górnej części **wszyscy użytkownicy** okienku wybierz **Dodaj**.

    ![Przycisk Dodaj](./media/bamboo-hr-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna, wykonaj następujące czynności:

    ![Okno użytkownika](./media/bamboo-hr-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-bamboohr-test-user"></a>Tworzenie użytkownika testowego BambooHR

Aby umożliwić użytkownikom usługi Azure AD do logowania się na BambooHR, skonfigurować je ręcznie w BambooHR w następujący sposób:

1. Zaloguj się do Twojego **BambooHR** lokacji jako administrator.

2. Na pasku narzędzi u góry wybierz **ustawienia**.
   
    ![Przycisk ustawień](./media/bamboo-hr-tutorial/IC796694.png "ustawienie")

3. Wybierz pozycję **Przegląd**.

4. W okienku po lewej stronie wybierz **zabezpieczeń** > **użytkowników**.

5. Wpisz nazwę użytkownika, hasło i adres e-mail prawidłowe usługi Azure AD konta chcesz skonfigurować.

6. Wybierz pozycję **Zapisz**.
        
>[!NOTE]
>Aby skonfigurować konta użytkowników usługi Azure AD, umożliwia także BambooHR narzędzia do tworzenia konta użytkownika lub interfejsów API.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

Włącz Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu BambooHR użytkownika.

![Przypisanie roli użytkownika][200] 

Aby przypisać użytkownika Simona Britta BambooHR, wykonaj następujące czynności:

1. W portalu Azure Otwórz widok aplikacji, przejdź do widoku katalogu, a następnie wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. W **aplikacje dla przedsiębiorstw** listy, wybierz **BambooHR**.

    ![Łącze BambooHR na liście aplikacje dla przedsiębiorstw](./media/bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisk, a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okna w **użytkowników** listy, wybierz **Simona Britta**.

6. Wybierz **wybierz** przycisku.

7. W **Dodaj przydziału** wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Przetestować konfigurację programu Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po wybraniu **BambooHR** kafelka w panelu dostępu użytkownik powinien pobrać automatycznie zalogowany do aplikacji BambooHR.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integracji aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/bamboo-hr-tutorial/tutorial_general_203.png

