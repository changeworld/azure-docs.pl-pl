---
title: 'Samouczek: Integracji Azure Active Directory z SAP Business ByDesign | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 23a0f202cabf39036a3a405894c814655e3f3cd9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Samouczek: integracja usługi Azure Active Directory z oprogramowaniem SAP Business ByDesign

Z tego samouczka dowiesz się integrowanie SAP Business ByDesign z usługi Azure Active Directory (Azure AD).

Integrowanie SAP Business ByDesign z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do programu SAP Business ByDesign.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do SAP Business ByDesign (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z SAP Business ByDesign, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SAP Business ByDesign logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie SAP Business ByDesign z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Dodawanie SAP Business ByDesign z galerii
Aby skonfigurować integrację usługi Azure AD SAP Business ByDesign, należy dodać SAP Business ByDesign z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SAP Business ByDesign z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **SAP Business ByDesign**, wybierz pozycję **SAP Business ByDesign** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SAP Business ByDesign na liście wyników](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SAP Business ByDesign w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w SAP Business ByDesign jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w SAP Business ByDesign musi się.

W SAP Business ByDesign, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SAP Business ByDesign, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego SAP Business ByDesign](#create-an-sap-business-bydesign-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta SAP Business ByDesign, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji SAP Business ByDesign.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z SAP Business ByDesign, wykonaj następujące czynności:**

1. W portalu Azure na **SAP Business ByDesign** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

3. Na **adresy URL i SAP Business ByDesign domeny** sekcji, wykonaj następujące czynności:

    ![Adresy URL i SAP Business ByDesign domeny pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<servername>.sapbydesign.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej SAP Business ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) uzyskać te wartości.

4. Na **atrybuty użytkownika** sekcji, wykonaj następujące czynności:

    ![SAP Business ByDesign atrybutów sekcji](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. W **identyfikator użytkownika** listy, wybierz **ExtractMailPrefix()** funkcji.
    
    b. Z **poczty** wybierz atrybut użytkownika, którego chcesz użyć implementacji. Na przykład jeśli ma być używany jako identyfikator użytkownika unikatowy identyfikator pracownika, a wartość atrybutu są przechowywane w ExtensionAttribute2, wybierz user.extensionattribute2.     

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_400.png)

7. Na **SAP Business ByDesign konfiguracji** kliknij **skonfigurować SAP Business ByDesign** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![SAP Business ByDesign konfiguracji](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

8. Aby uzyskać logowania jednokrotnego skonfigurowane dla aplikacji, wykonaj następujące czynności:
   
    a. Zaloguj się do portalu usługi SAP Business ByDesign z uprawnieniami administratora.
   
    b. Przejdź do **aplikacji i typowych zadań zarządzania użytkownika** i kliknij przycisk **dostawcy tożsamości** kartę.
   
    c. Kliknij przycisk **nowego dostawcy tożsamości** i wybierz plik XML metadanych, który został pobrany z portalu Azure. Przez importowanie metadanych, system automatycznie wysyła certyfikat wymagany podpis, jak i certyfikat szyfrowania.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Aby uwzględnić **adres URL usługi klienta potwierdzenia** do żądania SAML, wybierz **zawierają potwierdzenie konsumenta adres URL usługi**.
   
    e. Kliknij przycisk **aktywacji rejestracji jednokrotnej**.
   
    f. Zapisz zmiany.
   
    g. Kliknij przycisk **systemie** kartę.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Wklej **SAML pojedynczy znak na adres URL usługi**, która została skopiowana z portalu Azure do **Azure AD znaku w adresie URL** pola tekstowego.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Określ, czy pracownik może ręcznie wybrać między zalogowanie się przy użyciu Identyfikatora użytkownika i hasła lub logowania jednokrotnego, wybierając **wybór dostawcy tożsamości ręcznego**.
   
    j. W **adres URL logowania jednokrotnego** sekcji, podaj adres URL, które mają być używane przez pracowników do logowania się do systemu. 
    W URL wysyłane do listy rozwijanej pracownika można wybrać jedną z następujących opcji:
   
    **Adres URL — Usługa rejestracji Jednokrotnej**
   
    System wysyła tylko adres URL normalne systemu do pracownika. Pracownik nie może zalogować się przy użyciu logowania jednokrotnego i musi użyć hasła lub zamiast tego certyfikatu.
   
    **ADRES URL LOGOWANIA JEDNOKROTNEGO** 
   
    System wysyła tylko adres URL logowania jednokrotnego do pracownika. Pracownik może zalogować się przy użyciu logowania jednokrotnego. Żądanie uwierzytelnienia jest przekierowywane przez dostawców tożsamości.
   
    **Automatyczne wybieranie**
   
    Jeśli logowania jednokrotnego nie jest aktywne, system wysyła system normalny adres URL do pracownika. Jeśli usługa rejestracji Jednokrotnej jest aktywny, system sprawdza, czy pracownik ma hasło. Jeśli hasło jest dostępny, zarówno adres URL logowania jednokrotnego i adres URL logowania jednokrotnego nie są wysyłane do pracownika. Jeśli pracownik nie ma hasła, adres URL logowania jednokrotnego są wysyłane do pracownika.
   
    k. Zapisz zmiany.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Tworzenie użytkownika testowego SAP Business ByDesign

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w SAP Business ByDesign. We współpracy z [zespołem pomocy technicznej SAP Business ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) Aby dodać użytkowników do platformy SAP Business ByDesign. 

> [!NOTE]
> Upewnij się, że wartość NameID powinna być zgodna z pola Nazwa użytkownika na platformie programu SAP Business ByDesign.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do programu SAP Business ByDesign.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta SAP Business ByDesign, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **SAP Business ByDesign**.

    ![Łącze SAP Business ByDesign na liście aplikacji](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka SAP Business ByDesign w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji SAP Business ByDesign.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png

