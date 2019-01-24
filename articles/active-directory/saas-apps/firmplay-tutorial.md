---
title: 'Samouczek: Integracja usługi Azure Active Directory z FirmPlay - propagowanie pracowników dla Rekrutacja | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i FirmPlay - propagowanie pracowników dla Rekrutacja.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: 929494d5d802dbc545c750386a286029c4bf962d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54809806"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Samouczek: Integracja usługi Azure Active Directory z FirmPlay - propagowanie pracowników dla Rekrutacja

W tym samouczku dowiesz się, jak zintegrować FirmPlay - propagowanie pracowników dla Rekrutacja w usłudze Azure Active Directory (Azure AD).

Integrowanie FirmPlay - propagowanie pracowników dla Rekrutacja z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do FirmPlay - propagowanie pracowników dla Rekrutacja
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do FirmPlay - propagowanie pracowników dla Rekrutacja (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — portalu zarządzania platformy Azure

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z FirmPlay - propagowanie pracowników dla Rekrutacja, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- FirmPlay - propagowanie pracowników dla rekrutacji logowania jednokrotnego włączonych subskrypcji


> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.


Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie FirmPlay - propagowanie pracowników dla Rekrutacja z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Dodawanie FirmPlay - propagowanie pracowników dla Rekrutacja z galerii
Aby skonfigurować integrację FirmPlay - propagowanie pracowników dla Rekrutacja w usłudze Azure AD, należy dodać FirmPlay - propagowanie pracowników dla Rekrutacja z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać FirmPlay - propagowanie pracowników dla Rekrutacja z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania systemu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **FirmPlay - propagowanie pracowników dla Rekrutacja**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/tutorial_firmplay_001.png)

1. W panelu wyników wybierz **FirmPlay - propagowanie pracowników dla Rekrutacja**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą FirmPlay - propagowanie pracowników dla Rekrutacja w oparciu o użytkownika testu o nazwie "Britta Simon".

Logowanie jednokrotne do pracy usługi Azure AD musi wiedzieć, jakie użytkownik odpowiednika w FirmPlay — propagowanie pracowników dla Rekrutacja jest dla użytkownika w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i powiązanego użytkownika w FirmPlay - propagowanie pracowników dla rekrutacji powinien być określony.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w FirmPlay - propagowanie pracowników dla Rekrutacja.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą FirmPlay - propagowanie pracowników dla Rekrutacja, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie FirmPlay - propagowanie pracowników dla użytkownika testowego Rekrutacja](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)**  — aby odpowiednikiem Britta Simon w FirmPlay: Propagowanie pracowników dla rekrutacji oznacza to połączone reprezentację usługi Azure AD, jej.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowanie jednokrotne w portalu zarządzania platformy Azure i skonfigurować logowanie jednokrotne w swojej FirmPlay - propagowanie pracowników Rekrutacja aplikacji.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z FirmPlay - propagowanie pracowników dla Rekrutacja, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure na **FirmPlay - propagowanie pracowników dla Rekrutacja** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.
 
    ![Konfigurowanie logowania jednokrotnego](./media/firmplay-tutorial/tutorial_firmplay_01.png)

1. Na **FirmPlay - propagowanie pracowników rekrutacji domen i adresów URL** sekcji w **na adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<your-subdomain>.firmplay.com/`

    ![Konfigurowanie logowania jednokrotnego](./media/firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Należy pamiętać, że nie jest rzeczywistą wartość. Musisz zaktualizować tę wartość przy użyciu rzeczywistego logowania na adres URL. Skontaktuj się z pomocą [FirmPlay - propagowanie pracowników do zespołu pomocy technicznej Rekrutacja](mailto:engineering@firmplay.com) aby zyskać tę wartość. 

1. Na **certyfikat podpisywania SAML** kliknij **Utwórz nowy certyfikat**.

    ![Konfigurowanie logowania jednokrotnego](./media/firmplay-tutorial/tutorial_firmplay_03.png)     

1. Na **Utwórz nowy certyfikat** okno dialogowe, kliknij ikonę kalendarza, a następnie wybierz pozycję **datę wygaśnięcia**. Następnie kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/firmplay-tutorial/tutorial_general_300.png)

1. Na **certyfikat podpisywania SAML** zaznacz **Ustaw nowy certyfikat jako aktywny** i kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/firmplay-tutorial/tutorial_firmplay_04.png)

1. W oknie podręcznym **certyfikat przerzucania** okna, kliknij przycisk **OK**.

    ![Konfigurowanie logowania jednokrotnego](./media/firmplay-tutorial/tutorial_general_400.png)

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (base64)** , a następnie zapisz plik certyfikatu na komputerze. 

    ![Konfigurowanie logowania jednokrotnego](./media/firmplay-tutorial/tutorial_firmplay_05.png) 

1. Na **FirmPlay - propagowanie pracowników rekrutacji konfiguracji** , kliknij przycisk **skonfigurować FirmPlay - propagowanie pracowników dla Rekrutacja** otworzyć **Konfigurowanie logowania jednokrotnego** okno dialogowe.

    ![Konfigurowanie logowania jednokrotnego](./media/firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Konfigurowanie logowania jednokrotnego](./media/firmplay-tutorial/tutorial_firmplay_07.png)

1. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, skontaktuj się z [FirmPlay - propagowanie pracowników do zespołu pomocy technicznej Rekrutacja](mailto:engineering@firmplay.com) i udostępniać następujące czynności: 

    • Pobrany **plik certyfikatu**

    • **Adres URL usługi rejestracji logowania jednokrotnego SAML**

    • **Identyfikator jednostki SAML**

    • **Adres URL wylogowania**
  

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania platformy Azure o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Tworzenie FirmPlay - propagowanie pracowników dla użytkownika testowego Rekrutacja

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w FirmPlay - propagowanie pracowników dla Rekrutacja. Skontaktuj się z [FirmPlay - propagowanie pracowników do zespołu pomocy technicznej Rekrutacja](mailto:engineering@firmplay.com) Aby dodać użytkowników w FirmPlay - propagowanie pracowników Rekrutacja platformy.


### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do użycia platformy Azure logowania jednokrotnego przez udostępnienie jej FirmPlay - propagowanie pracowników dla Rekrutacja.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon FirmPlay - propagowanie pracowników dla Rekrutacja, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure powoduje ono otwarcie widoku aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **FirmPlay - propagowanie pracowników dla Rekrutacja**.

    ![Konfigurowanie logowania jednokrotnego](./media/firmplay-tutorial/tutorial_firmplay_50.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    


### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu FirmPlay - propagowanie pracowników Rekrutacja kafelka w panelu dostępu, możesz powinna uzyskać automatycznie zalogowanych do Twojej FirmPlay - propagowanie pracowników Rekrutacja aplikacji.


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/firmplay-tutorial/tutorial_general_01.png
[2]: ./media/firmplay-tutorial/tutorial_general_02.png
[3]: ./media/firmplay-tutorial/tutorial_general_03.png
[4]: ./media/firmplay-tutorial/tutorial_general_04.png

[100]: ./media/firmplay-tutorial/tutorial_general_100.png

[200]: ./media/firmplay-tutorial/tutorial_general_200.png
[201]: ./media/firmplay-tutorial/tutorial_general_201.png
[202]: ./media/firmplay-tutorial/tutorial_general_202.png
[203]: ./media/firmplay-tutorial/tutorial_general_203.png