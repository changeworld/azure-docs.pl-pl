---
title: 'Samouczek: Integracja usługi Azure Active Directory z RFPIO | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 59b05814be0be9042e7507cc8d928b5f5feb80ad
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051765"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Samouczek: Integracja usługi Azure Active Directory z RFPIO

W tym samouczku dowiesz się, jak zintegrować RFPIO w usłudze Azure Active Directory (Azure AD).

Integrowanie RFPIO z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, kto w usłudze Azure AD, kto ma dostęp do RFPIO.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do RFPIO (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą RFPIO, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD.
- RFPIO pojedynczy znak na włączona subskrypcji.

> [!NOTE]
> Nie zaleca się używać w środowisku produkcyjnym do testowania kroki opisane w tym samouczku.

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie RFPIO z galerii.
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne.

## <a name="add-rfpio-from-the-gallery"></a>Dodaj RFPIO z galerii
Aby skonfigurować integrację RFPIO w usłudze Azure AD, należy dodać RFPIO z galerii z listą zarządzanych aplikacji SaaS.

### <a name="to-add-rfpio-from-the-gallery"></a>Aby dodać RFPIO z galerii

1. W  **[witryny Azure portal](https://portal.azure.com)**, w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, wybierz **nową aplikację** przycisk u góry okno dialogowe.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **RFPIO**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/tutorial_rfpio_search.png)

5. W panelu wyników wybierz **RFPIO**, a następnie wybierz pozycję **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą RFPIO w oparciu o użytkownika testu o nazwie "Britta Simon".

Aby uzyskać logowanie jednokrotne do pracy usługi Azure AD musi wiedzieć, co to jest relacja między użytkownikiem odpowiednika w RFPIO i użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w RFPIO musi można ustanowić.

W RFPIO, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą RFPIO, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**— aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**— do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego RFPIO](#creating-a-rfpio-test-user)**  — aby odpowiednikiem Britta Simon w RFPIO połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**— Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji RFPIO.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z RFPIO, wykonaj następujące czynności:**

1. W witrynie Azure portal na **RFPIO** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. Na **RFPIO domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/tutorial_rfpio_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://www.rfpio.com`

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**.

    c. W **tan przekaźnika** polu tekstowym wprowadź wartość ciągu. Skontaktuj się z pomocą [zespołu pomocy technicznej RFPIO](https://www.rfpio.com/contact/) aby zyskać tę wartość. 

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb: 

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/tutorial_rfpio_url2.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://www.app.rfpio.com`

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/tutorial_general_400.png)

7. W oknie przeglądarki internetowej innej, zaloguj się do **RFPIO** witryny sieci Web jako administrator.

8. Kliknij listę rozwijaną dolnym lewym rogu.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app1.png)

9. Kliknij pozycję **ustawień organizacji**. 

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app2.png)

10. Kliknij pozycję **funkcje i integracji**.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app4.png)

11. W **konfiguracji logowania jednokrotnego SAML** kliknij **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app3.png)

12. W tej części należy wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app5.png)
    
    a. Skopiuj zawartość **pobierane metadane XML** i wklej go w **Konfiguracja tożsamości** pola.

    > [!NOTE]
    >Aby skopiować zawartość pobrana **XML metadanych** użyj **Notatnik ++** lub zastosowanie mieszanej **edytora XML**. 

    b. Kliknij przycisk **zweryfikować**.

    c. Po kliknięciu przycisku **zweryfikować**, przerzucania **SAML(Enabled)** pozycji włączone.

    d. Kliknij przycisk **przesłać**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-rfpio-test-user"></a>Tworzenie użytkownika testowego RFPIO

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do RFPIO, musi być obsługiwana w RFPIO.  
W przypadku RFPIO Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy RFPIO jako administrator.

2. Kliknij listę rozwijaną dolnym lewym rogu.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app1.png)

3. Kliknij pozycję **ustawień organizacji**. 

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app2.png)

4. Kliknij przycisk **członków zespołu**.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app6.png)

5. Kliknij pozycję **Dodaj członków**.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app7.png)

6. W **Dodawanie nowych elementów członkowskich** sekcji. Wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app8.png)

    a. Wprowadź **adres E-mail** w **Podaj jeden adres e-mail dla każdego wiersza** pola.

    b. Wybierz pół godziny **roli** zgodnie z wymaganiami użytkownika.

    c. Kliknij przycisk **Dodaj członków**.
        
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail, a także następujące łącze, aby potwierdzić swoje konto, zanim stanie się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do RFPIO.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon RFPIO, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **RFPIO**.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/tutorial_rfpio_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka RFPIO w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji RFPIO.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/rfpio-tutorial/tutorial_general_01.png
[2]: ./media/rfpio-tutorial/tutorial_general_02.png
[3]: ./media/rfpio-tutorial/tutorial_general_03.png
[4]: ./media/rfpio-tutorial/tutorial_general_04.png

[100]: ./media/rfpio-tutorial/tutorial_general_100.png

[200]: ./media/rfpio-tutorial/tutorial_general_200.png
[201]: ./media/rfpio-tutorial/tutorial_general_201.png
[202]: ./media/rfpio-tutorial/tutorial_general_202.png
[203]: ./media/rfpio-tutorial/tutorial_general_203.png

