---
title: 'Samouczek: Integracji Azure Active Directory przy użyciu usługi TINFOIL SECURITY | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i usługę TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 3cd9d0c24ecb7dda01a4d36c013ebd4f6a484ddc
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938787"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Samouczek: Integracji Azure Active Directory przy użyciu usługi TINFOIL SECURITY

W tym samouczku Dowiedz się jak zintegrować usługę TINFOIL SECURITY w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD usługę TINFOIL SECURITY zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do usługę TINFOIL SECURITY
- Umożliwia użytkownikom automatycznie pobrać zalogowane do usługę TINFOIL SECURITY (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu usługi TINFOIL SECURITY, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Usługa TINFOIL SECURITY logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodaj usługę TINFOIL SECURITY z galerii
2. Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

## <a name="add-tinfoil-security-from-the-gallery"></a>Dodaj usługę TINFOIL SECURITY z galerii
Aby skonfigurować integrację usługi Azure AD usługę TINFOIL SECURITY, należy dodać usługę TINFOIL SECURITY z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługę TINFOIL SECURITY z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **usługę TINFOIL SECURITY**, wybierz pozycję **usługę TINFOIL SECURITY** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Usługa TINFOIL SECURITY z galerii](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usługę TINFOIL SECURITY w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić odpowiednikiem użytkownika na usługę TINFOIL SECURITY dla użytkownika w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w usługę TINFOIL SECURITY.

Usługa TINFOIL SECURITY przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu usługi TINFOIL SECURITY, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego usługę TINFOIL SECURITY](#create-a-tinfoil-security-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta usługę TINFOIL SECURITY, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji usługę TINFOIL SECURITY.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu usługi TINFOIL SECURITY, wykonaj następujące czynności:**

1. W portalu Azure na **usługę TINFOIL SECURITY** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![SAML na podstawie logowania jednokrotnego](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

3. Na **TINFOIL SECURITY domeny i adres URL** sekcji, użytkownik nie trzeba wykonywać żadnych czynności, jak aplikacja już jest wstępna Integracja z usługą Azure.

    ![Konfigurowanie rejestracji jednokrotnej](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


4. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość.

    ![Sekcja certyfikat podpisywania SAML](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

5. Aby dodać mapowania wymaganego atrybutu, wykonaj następujące czynności:
    
    ![Atrybuty](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "atrybutów")
    
    | Nazwa atrybutu    |   Wartość atrybutu |
    | ------------------- | -------------------- |
    | AccountID | UXXXXXXXXXXXXX |
    
    a. Kliknij przycisk **Dodaj atrybut użytkownika**.
    
    ![Dodaj atrybut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "atrybutów")
    
    ![Dodaj atrybut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "atrybutów")
    
    b. W **nazwa atrybutu** pole tekstowe, typ **accountid**.
    
    c. W **wartość atrybutu** pole tekstowe, wklej identyfikator konta wartość, która zostanie wyświetlony później w samouczku.
    
    d. Kliknij przycisk **OK**.    

6. Kliknij przycisk **zapisać** przycisku.

    ![Przyciskiem Zapisz](./media/tinfoil-security-tutorial/tutorial_general_400.png)

7. Na **TINFOIL SECURITY Configuration** kliknij **skonfigurować usługę TINFOIL SECURITY** otworzyć **konfigurowania rejestracji** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja zabezpieczeń TINFOIL](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

8. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy usługę TINFOIL SECURITY jako administrator.

9. Na pasku narzędzi u góry kliknij **Moje konto**.
   
    ![Pulpit nawigacyjny](./media/tinfoil-security-tutorial/ic798971.png "pulpitu nawigacyjnego")

10. Kliknij przycisk **zabezpieczeń**.
   
    ![Zabezpieczenia](./media/tinfoil-security-tutorial/ic798972.png "zabezpieczeń")

11. Na **rejestracji jednokrotnej** konfiguracji wykonaj następujące czynności:
   
    ![Logowanie jednokrotne](./media/tinfoil-security-tutorial/ic798973.png "logowanie jednokrotne")
   
    a. Wybierz **Włącz SAML**.
   
    b. Kliknij przycisk **ręcznej konfiguracji**.
   
    c. W **adresu URL przesyłania SAML** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure
   
    d. W **odcisk palca certyfikatu SAML** pole tekstowe, Wklej wartość **odcisk palca** , które zostały skopiowane z **certyfikat podpisywania SAML** sekcji.
  
    e. Kopiuj **swój identyfikator konta** i Wklej wartość w **wartość atrybutu** pole tekstowe, w obszarze **Dodawanie atrybutu** sekcji w portalu Azure.
   
    f. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tinfoil-security-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Użytkownicy i grupy -> Wszyscy użytkownicy ](./media/tinfoil-security-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Użytkownik](./media/tinfoil-security-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tinfoil-security-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Tworzenie użytkownika testowego usługę TINFOIL SECURITY

Aby włączyć użytkowników usługi Azure AD zalogować się do usługę TINFOIL SECURITY, musi być przygotowana do usługę TINFOIL SECURITY. W przypadku usługę TINFOIL SECURITY Inicjowanie obsługi to zadanie ręczne.

**Aby uzyskać dostęp użytkownik zainicjowano obsługę administracyjną, wykonaj następujące czynności:**

1. Jeśli użytkownik jest część konta organizacji, musisz [skontaktuj się z zespołem pomocy technicznej usługę TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) uzyskać konto użytkownika utworzone.

2. Jeśli użytkownik jest zwykłych użytkowników TINFOIL SECURITY SaaS, użytkownik może dodawać współpracownika dla każdego użytkownika witryny. Spowoduje to zainicjowanie procesu, aby wysłać zaproszenie na określony adres e-mail, aby utworzyć nowe konto użytkownika usługę TINFOIL SECURITY.

> [!NOTE]
> Inne narzędzia do tworzenia konta użytkownika usługę TINFOIL SECURITY lub interfejsów API dostarczonych przez usługę TINFOIL SECURITY służy do obsługi administracyjnej kont użytkowników usługi Azure AD.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu usługę TINFOIL SECURITY.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta usługę TINFOIL SECURITY, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **usługę TINFOIL SECURITY**.

    ![Wybierz usługę TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka usługę TINFOIL SECURITY w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji usługę TINFOIL SECURITY. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/tinfoil-security-tutorial/tutorial_general_203.png

