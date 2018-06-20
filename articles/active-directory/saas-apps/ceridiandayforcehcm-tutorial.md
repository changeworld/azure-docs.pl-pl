---
title: 'Samouczek: Integracji Azure Active Directory z Ceridian Dayforce HCM | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Ceridian Dayforce HCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 47ed72667b46edb06a7d07cbc971810cb97e6f90
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211615"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Samouczek: Integracji Azure Active Directory z Ceridian Dayforce HCM

Z tego samouczka dowiesz się integrowanie Ceridian Dayforce HCM z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Ceridian Dayforce HCM zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Ceridian Dayforce HCM.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do HCM Dayforce Ceridian (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Ceridian Dayforce HCM, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Ceridian Dayforce HCM jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Ceridian Dayforce HCM z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Dodawanie Ceridian Dayforce HCM z galerii
Aby skonfigurować integrację usługi Azure AD Ceridian Dayforce HCM, należy dodać Ceridian Dayforce HCM z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Ceridian Dayforce HCM z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Ceridian Dayforce HCM**, wybierz pozycję **Ceridian Dayforce HCM** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![HCM Dayforce Ceridian na liście wyników](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z HCM Dayforce Ceridian w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Ceridian Dayforce HCM jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Ceridian Dayforce HCM musi się.

W Ceridian Dayforce HCM, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Ceridian Dayforce HCM, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Ceridian Dayforce HCM](#create-a-ceridian-dayforce-hcm-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Ceridian Dayforce HCM, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Ceridian Dayforce HCM.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Ceridian Dayforce HCM, wykonaj następujące czynności:**

1. W portalu Azure na **Ceridian Dayforce HCM** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_samlbase.png)

3. Na **Ceridian Dayforce HCM domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_url.png)
    
    a. W **na adres URL logowania** tekstowym, wpisz adres URL używany przez użytkowników do logowania jednokrotnego do aplikacji Ceridian Dayforce HCM.
    
    | Środowisko | Adres URL |
    | :-- | :-- |
    | W przypadku środowiska produkcyjnego | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Dla testu | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |
    
    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca:
    
    | Środowisko | Adres URL |
    | :-- | :-- |
    | W przypadku środowiska produkcyjnego | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Dla testu | `https://fs-test.dayforcehcm.com/sp` |
    
    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL używany przez usługę Azure AD można opublikować odpowiedzi.
    
    | Środowisko | Adres URL |
    | :-- | :-- |
    | W przypadku środowiska produkcyjnego | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Dla testu | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |
    
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta HCM Dayforce Ceridian](https://www.ceridian.com/contact-us/index.html) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_certificate.png) 

5. Aplikacja Ceridian Dayforce HCM oczekuje potwierdzenia języka SAML w określonym formacie. Praca z [zespołem pomocy technicznej Ceridian Dayforce HCM](https://www.ceridian.com/contact-us/index.html) najpierw, aby zidentyfikować identyfikator użytkownika. Firma Microsoft zaleca używanie **"name"** atrybut jako identyfikator użytkownika. Możesz zarządzać wartości tych atrybutów z **atrybuty użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.  

    ![Konfigurowanie rejestracji jednokrotnej](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu  | Wartość atrybutu |
    | --------------- | -------------------- |    
    | name  | User.extensionattribute2 |    

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/ceridiandayforcehcm-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/ceridiandayforcehcm-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. W **wartość** wybierz atrybut użytkownika, którego chcesz użyć implementacji.
    Na przykład, jeśli chcesz użyć identyfikatorem EmployeeID jako użytkownik Unikatowy identyfikator i przechowywanych wartości atrybutu w ExtensionAttribute2, następnie wybierz **user.extensionattribute2**.
    
    d. Kliknij przycisk **OK**.

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/ceridiandayforcehcm-tutorial/tutorial_general_400.png)
    
8. Na **Ceridian Dayforce HCM konfiguracji** kliknij **skonfigurować HCM Dayforce Ceridian** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Ceridian Dayforce HCM konfiguracji](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_configure.png) 

9. Skonfigurować logowanie jednokrotne w **Ceridian Dayforce HCM** stronie, musisz wysłać pobrany **XML metadanych** i **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** do [zespołem pomocy technicznej Ceridian Dayforce HCM](https://www.ceridian.com/contact-us/index.html).

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/ceridiandayforcehcm-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/ceridiandayforcehcm-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/ceridiandayforcehcm-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/ceridiandayforcehcm-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-ceridian-dayforce-hcm-test-user"></a>Tworzenie użytkownika testowego Ceridian Dayforce HCM

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Ceridian Dayforce HCM. Praca z [zespołem pomocy technicznej Ceridian Dayforce HCM](https://www.ceridian.com/contact-us/index.html) można pobrać użytkowników dodanych w aplikacji Ceridian Dayforce HCM. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Ceridian Dayforce HCM.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Ceridian Dayforce HCM, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Ceridian Dayforce HCM**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Ceridian Dayforce HCM.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Ceridian Dayforce HCM, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Ceridian Dayforce HCM**.

    ![Łącze Ceridian Dayforce HCM na liście aplikacji](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.  
Po kliknięciu kafelka Ceridian Dayforce HCM w panelu dostępu użytkownik powinien uzyskać automatycznie zalogowane do aplikacji Ceridian Dayforce HCM. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_04.png

[100]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_201.png
[202]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_202.png
[203]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_203.png

