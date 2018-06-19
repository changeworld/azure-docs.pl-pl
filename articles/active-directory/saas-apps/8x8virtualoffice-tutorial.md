---
title: 'Samouczek: Azure Active Directory integracji z pakietem Office wirtualnej 8 x 8 | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między Azure Active Directory oraz 8 x 8 wirtualnych pakietu Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 7e3b7ef7a4f4eb3969b8e8058a0060d23638cc6f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938748"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Samouczek: Azure Active Directory integracji z pakietem Office wirtualnej 8 x 8

Z tego samouczka dowiesz się integrowanie 8 x 8 wirtualnych Office z usługą Azure Active Directory (Azure AD).

Integrowanie 8 x 8 wirtualnych Office z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do pakietu Office wirtualnej 8 x 8
- Umożliwia użytkownikom automatycznie pobrać zalogowane do 8 x 8 wirtualnych Office (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem Office wirtualnej 8 x 8, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- 8 x 8 wirtualnych Office logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie pakietu Office wirtualnej 8 x 8 z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Dodawanie pakietu Office wirtualnej 8 x 8 z galerii
Aby skonfigurować integrację usługi Azure AD Office wirtualnej 8 x 8, należy dodać 8 x 8 wirtualnych Office z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać 8 x 8 Office wirtualnych z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **8 x 8 wirtualnych Office**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_search.png)

5. W panelu wyników wybierz **8 x 8 wirtualnych Office**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z 8 x 8 Office wirtualnych opartych na użytkownika testowego o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, jakie użytkownika odpowiednika w 8 x 8 Office wirtualnego użytkownika w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w 8 x 8 wirtualnych Office musi określone.

W pakiecie Office wirtualnej 8 x 8, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z pakietem Office wirtualnej 8 x 8, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego wirtualnego Office 8 x 8](#creating-a-8x8-virtual-office-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta 8 x 8 wirtualnej biuro, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji pakietu Office wirtualnego 8 x 8.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z pakietem Office wirtualnej 8 x 8, wykonaj następujące czynności:**

1. W portalu Azure na **8 x 8 wirtualnych Office** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_samlbase.png)

3. Na **8 x 8 wirtualnych Office domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca:

    | `https://sso.8x8.com/<companyname>` |
    | `https://www.8x8.com/<companyname>` |
    | `https://sso.8x8pilot.com/<companyname>` |

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca:

    | `https://<subdomain>.8x8.com/saml2` |
    | `https://<subdomain>.8x8pilot.com/saml2`|

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej wirtualnego Office 8 x 8](https://www.8x8.com/about-us/contact-us) uzyskać te wartości.
 


4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/8x8virtualoffice-tutorial/tutorial_general_400.png)

6. Na **konfiguracji wirtualnej 8 x 8** , kliknij przycisk **Office wirtualnego Konfiguruj 8 x 8** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_configure.png) 

7. Logowanie do dzierżawy 8 x 8 Office wirtualnego jako administrator.

8. Wybierz **Mgr konta Office wirtualnego** w aplikacji panelu.
   
    ![Konfigurowanie strony aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Wybierz **firm** konta do zarządzania i kliknij przycisk **logowania** przycisku.
   
    ![Konfigurowanie strony aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Kliknij przycisk **kont** kartę na liście menu.
   
    ![Konfigurowanie strony aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Kliknij przycisk **rejestracji jednokrotnej** na liście kont.
   
    ![Konfigurowanie strony aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Wybierz **rejestracji jednokrotnej** w obszarze metodę uwierzytelniania i kliknięcie **SAML**.
    
    ![Konfigurowanie strony aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. Kopia **adres URL logowania jednokrotnego SAML**, **pojedynczy uwagi się adres URL usługi** i **adres URL wystawcy** z usługi Azure AD do **Zaloguj się w adresie URL**, **Wyloguj adresu URL** i **adres URL wystawcy** w pakiecie Office wirtualnego 8 x 8. 
    
    ![Konfigurowanie strony aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)
    
14. Kliknij przycisk **przeglądarki** przycisk, aby przekazać certyfikat, który został pobrany z usługi Azure AD, a następnie kliknij przycisk **zapisać** przycisku.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-8x8-virtual-office-test-user"></a>Tworzenie użytkownika testowego wirtualnego Office 8 x 8

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w pakiecie Office wirtualnej 8 x 8. 8 x 8 wirtualnych Office obsługę w czasie, który jest domyślnie włączone.

Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu 8 x 8 wirtualnych Office, jeśli go jeszcze nie istnieje. 

>[!NOTE]
>Jeśli trzeba ręcznie utworzyć użytkownika, należy skontaktować się [zespołem pomocy technicznej wirtualnego Office 8 x 8](https://www.8x8.com/about-us/contact-us). 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do pakietu Office wirtualnej 8 x 8.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Office wirtualnej 8 x 8, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **8 x 8 wirtualnych Office**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka wirtualnego Office 8 x 8 w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji pakietu Office wirtualnego 8 x 8.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/8x8virtualoffice-tutorial/tutorial_general_04.png

[100]: ./media/8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/8x8virtualoffice-tutorial/tutorial_general_201.png
[202]: ./media/8x8virtualoffice-tutorial/tutorial_general_202.png
[203]: ./media/8x8virtualoffice-tutorial/tutorial_general_203.png

