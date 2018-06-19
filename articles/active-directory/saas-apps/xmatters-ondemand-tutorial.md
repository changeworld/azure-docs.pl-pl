---
title: 'Samouczek: Integracji Azure Active Directory z xMatters OnDemand | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i xMatters na żądanie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 7d68864a6712823ca1d4a55691b54e8fbb9c968f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942638"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Samouczek: Integracji Azure Active Directory z xMatters na żądanie

Z tego samouczka dowiesz się integrowanie xMatters OnDemand w usłudze Azure Active Directory (Azure AD).

Integrowanie xMatters na żądanie z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do xMatters na żądanie
- Umożliwia użytkownikom automatycznie pobrać zalogowane do xMatters OnDemand (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z xMatters na żądanie, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- XMatters OnDemand logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie xMatters na żądanie z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Dodawanie xMatters na żądanie z galerii
Aby skonfigurować integrację usługi Azure AD xMatters na żądanie, należy dodać xMatters na żądanie z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać xMatters na żądanie z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **xMatters OnDemand**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

5. W panelu wyników wybierz **xMatters OnDemand**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z xMatters OnDemand oparta na koncie użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, jaki użytkownik odpowiednikiem w xMatters OnDemand jest użytkownikiem w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w xMatters OnDemand musi się.

W xMatters na żądanie, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z xMatters na żądanie, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego OnDemand xMatters](#creating-a-xmatters-ondemand-test-user)**  — mają odpowiednika Simona Britta w xMatters OnDemand połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w sieci xMatters aplikacji na żądanie.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z xMatters na żądanie, wykonaj następujące czynności:**

1. W portalu Azure na **xMatters OnDemand** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Konfigurowanie rejestracji jednokrotnej](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

3. Na **xMatters OnDemand domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [xMatters OnDemand obsługuje zespołu](https://www.xmatters.com/company/contact-us/) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu lokalnie jako **c:\\XMatters OnDemand.cer**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)

    > [!IMPORTANT]
    > Konieczne przekazywanie certyfikatu [xMatters OnDemand obsługuje zespołu](https://www.xmatters.com/company/contact-us/). Ten certyfikat musi się do przekazania przez zespół pomocy technicznej xMatters przed może zakończyć pojedynczego konfigurację logowania jednokrotnego. 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/xmatters-ondemand-tutorial/tutorial_general_400.png)

6. Na **xMatters konfiguracji OnDemand** , kliknij przycisk **skonfigurować xMatters OnDemand** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy XMatters OnDemand.

8. Na pasku narzędzi u góry kliknij **Admin**, a następnie kliknij przycisk **szczegóły firmy** na pasku nawigacyjnym po lewej stronie.

    ![Administrator](./media/xmatters-ondemand-tutorial/IC776795.png "administratora")

9. Na **Konfiguracja SAML** wykonaj następujące czynności:

    ![Konfiguracja SAML](./media/xmatters-ondemand-tutorial/IC776796.png "Konfiguracja SAML")

    a. Wybierz **Włącz SAML**.

    b. W **identyfikator dostawcy tożsamości** pole tekstowe, Wklej **identyfikator jednostki SAML** wartość, która została skopiowana z portalu Azure.

    c. W **jeden znak na adres URL** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure.

    d. W **pojedynczego adresu URL wylogowania** pole tekstowe, Wklej **Sign-Out URL**, które zostały skopiowane z portalu Azure.

    e. Na stronie Szczegóły firmy u góry, kliknij przycisk **Zapisz zmiany**.

    ![Szczegóły firmy](./media/xmatters-ondemand-tutorial/IC776797.png "firmy szczegóły")

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-xmatters-ondemand-test-user"></a>Tworzenie użytkownika testowego OnDemand xMatters

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w xMatters OnDemand. xMatters OnDemand obsługę automatycznego użytkownika, który jest domyślnie włączone. Więcej informacji można znaleźć [tutaj](xmatters-ondemand-provisioning-tutorial.md) na temat konfigurowania użytkowników automatycznego inicjowania obsługi administracyjnej.

**Jeśli trzeba ręcznie utworzyć użytkownika, należy wykonać następujące kroki:**

1. Zaloguj się do Twojego **XMatters OnDemand** dzierżawy.

2.  Kliknij przycisk **użytkowników** kartę, a następnie kliknij przycisk **Dodaj użytkownika**.

    ![Użytkownicy](./media/xmatters-ondemand-tutorial/IC781048.png "użytkowników")

3. W **dodać użytkownika** sekcji, wykonaj następujące czynności:

    ![Dodawanie użytkownika](./media/xmatters-ondemand-tutorial/IC781049.png "Dodawanie użytkownika")

    a. Wybierz **Active**.

    b. W **identyfikator użytkownika** pole tekstowe, typ identyfikatora użytkownika użytkownika, takich jak Brittasimon@contoso.com.

    c. W **imię** tekstowym, wpisz imię użytkownika, takich jak Britta.

    d. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak Simona.

    e. W **lokacji** pole tekstowe, wprowadź prawidłową witryną Azure prawidłowe konto AD, które chcesz udostępnić.

    f. Kliknij pozycję **Zapisz**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do xMatters OnDemand.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta xMatters na żądanie, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **xMatters OnDemand**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu xMatters kafelka na żądanie w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do Twojej aplikacji OnDemand xMatters.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](xmatters-ondemand-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/xmatters-ondemand-tutorial/tutorial_general_203.png
