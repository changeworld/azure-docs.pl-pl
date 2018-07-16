---
title: 'Samouczek: Integracja usługi Azure Active Directory z TargetProcess | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: ae49e60188c554a2eaa0221c05b49ca67c835f0c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055940"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Samouczek: Integracja usługi Azure Active Directory z TargetProcess

W tym samouczku dowiesz się, jak zintegrować TargetProcess w usłudze Azure Active Directory (Azure AD).

Integrowanie TargetProcess z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do TargetProcess
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do TargetProcess (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą TargetProcess, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- TargetProcess logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodaj TargetProcess z galerii
2. Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

## <a name="add-targetprocess-from-the-gallery"></a>Dodaj TargetProcess z galerii
Aby skonfigurować integrację TargetProcess w usłudze Azure AD, należy dodać TargetProcess z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać TargetProcess z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **TargetProcess**, wybierz opcję **TargetProcess** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![TargetProcess Dodaj z galerii](./media/target-process-tutorial/tutorial_target-process_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą TargetProcess w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w TargetProcess do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w TargetProcess musi można ustanowić.

W TargetProcess, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą TargetProcess, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego TargetProcess](#create-a-targetprocess-test-user)**  — aby odpowiednikiem Britta Simon w TargetProcess połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji TargetProcess.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z TargetProcess, wykonaj następujące czynności:**

1. W witrynie Azure portal na **TargetProcess** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Logowanie na podstawie protokołu SAML](./media/target-process-tutorial/tutorial_target-process_samlbase.png)

3. Na **TargetProcess domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Sekcja TargetProcess domena i adresy URL](./media/target-process-tutorial/tutorial_target-process_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.tpondemand.com/`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.tpondemand.com/`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta TargetProcess](mailto:support@targetprocess.com) do uzyskania tych wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Sekcji certyfikat podpisywania SAML](./media/target-process-tutorial/tutorial_target-process_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Przycisk Zapisz](./media/target-process-tutorial/tutorial_general_400.png)

6. Na **konfiguracji TargetProcess** , kliknij przycisk **skonfigurować TargetProcess** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Sekcja konfiguracji TargetProcess](./media/target-process-tutorial/tutorial_target-process_configure.png) 

7. Logowanie jednokrotne do aplikacji TargetProcess jako administrator.

8. W menu u góry kliknij **Instalatora**.
   
    ![Konfigurowanie](./media/target-process-tutorial/tutorial_target_process_05.png)

9. Kliknij przycisk **ustawienia**.
   
    ![Ustawienia](./media/target-process-tutorial/tutorial_target_process_06.png) 

10. Kliknij przycisk **logowanie jednokrotne**.
   
    ![Kliknij przycisk logowania jednokrotnego](./media/target-process-tutorial/tutorial_target_process_07.png) 

11. W oknie Ustawienia logowania jednokrotnego, wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/target-process-tutorial/tutorial_target_process_08.png)
    
    a. Kliknij przycisk **włączyć rejestrację jednokrotną**.
    
    b. W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    c. Otwórz pobranego certyfikatu w programie Notatnik, skopiuj zawartość, a następnie wklej go do **certyfikatu** pola tekstowego.
    
    d. Kliknij przycisk **Włączanie udostępniania JIT**.

    e. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/target-process-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Aby wyświetlić listę użytkowników](./media/target-process-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Przycisk dodawania](./media/target-process-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Sekcja użytkownika](./media/target-process-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-targetprocess-test-user"></a>Tworzenie użytkownika testowego TargetProcess

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w TargetProcess.

TargetProcess obsługę just-in-time. Została już włączona w [usługi Azure AD Konfigurowanie logowania jednokrotnego](#configuring-azure-ad-single-sign-on).

Brak elementu akcji dla Ciebie w tej sekcji.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do TargetProcess.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon TargetProcess, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **TargetProcess**.

    ![TargetProcess listy aplikacji](./media/target-process-tutorial/tutorial_target-process_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka TargetProcess w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji TargetProcess. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/target-process-tutorial/tutorial_general_01.png
[2]: ./media/target-process-tutorial/tutorial_general_02.png
[3]: ./media/target-process-tutorial/tutorial_general_03.png
[4]: ./media/target-process-tutorial/tutorial_general_04.png

[100]: ./media/target-process-tutorial/tutorial_general_100.png

[200]: ./media/target-process-tutorial/tutorial_general_200.png
[201]: ./media/target-process-tutorial/tutorial_general_201.png
[202]: ./media/target-process-tutorial/tutorial_general_202.png
[203]: ./media/target-process-tutorial/tutorial_general_203.png

