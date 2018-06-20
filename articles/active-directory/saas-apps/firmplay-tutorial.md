---
title: 'Samouczek: Integracji Azure Active Directory z FirmPlay - propagowanie pracownika dla Rekrutacja | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i FirmPlay - propagowanie pracownika dla Rekrutacja.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: dacc133f1c6d15aa1fc886ab9086109951478929
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219904"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Samouczek: Integracji Azure Active Directory z FirmPlay - propagowanie pracownika dla Rekrutacja

Z tego samouczka dowiesz się integrowanie FirmPlay - propagowanie pracownika dla Rekrutacja w usłudze Azure Active Directory (Azure AD).

Integrowanie FirmPlay - propagowanie pracownika dla Rekrutacja z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do FirmPlay - propagowanie pracownika dla Rekrutacja
- Umożliwia użytkownikom automatycznie pobrać zalogowane do FirmPlay - propagowanie pracownika dla Rekrutacja (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu zarządzania Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z FirmPlay - propagowanie pracownika dla Rekrutacja, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- FirmPlay - propagowanie pracownika dla rekrutacji jednokrotnego włączone subskrypcji


> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.


Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie FirmPlay - propagowanie pracownika dla Rekrutacja z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Dodawanie FirmPlay - propagowanie pracownika dla Rekrutacja z galerii
Aby skonfigurować integrację FirmPlay - propagowanie pracownika dla Rekrutacja do usługi Azure AD, należy dodać FirmPlay - propagowanie pracownika dla Rekrutacja z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać FirmPlay - propagowanie pracownika dla Rekrutacja z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **FirmPlay - propagowanie pracownika dla Rekrutacja**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/tutorial_firmplay_001.png)

5. W panelu wyników wybierz **FirmPlay - propagowanie pracownika dla Rekrutacja**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z FirmPlay - propagowanie pracownika dla Rekrutacja w oparciu o nazwie "Britta Simona" użytkownika testowego.

Logowanie jednokrotne do pracy usługi Azure AD musi wiedzieć, jaki użytkownik odpowiednika w FirmPlay — propagowanie pracownika dla Rekrutacja jest użytkownikiem w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w FirmPlay - propagowanie pracownika dla rekrutacji powinien być określony.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w FirmPlay - propagowanie pracownika dla Rekrutacja.

Do konfigurowania i testowania usługi Azure AD rejestracji jednokrotnej z FirmPlay - propagowanie pracownika dla Rekrutacja, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie FirmPlay - propagowanie pracownika dla użytkownika testowego Rekrutacja](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta FirmPlay: propagowanie pracownika dla rekrutacji, który jest połączony z jej reprezentacji usługi Azure AD.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji włączyć usługi Azure AD rejestracji jednokrotnej w portalu zarządzania Azure i skonfigurować logowanie jednokrotne w sieci FirmPlay - propagowanie pracownika Rekrutacja aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z FirmPlay - propagowanie pracownika dla Rekrutacja, wykonaj następujące czynności:**

1. W portalu zarządzania Azure na **FirmPlay - propagowanie pracownika dla Rekrutacja** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okna dialogowego, jako **tryb** wybierz **na języku SAML logowania jednokrotnego** Włącz funkcji logowania jednokrotnego.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/firmplay-tutorial/tutorial_firmplay_01.png)

3. Na **FirmPlay - propagowanie pracownika rekrutacji domeny i adresów URL** sekcji w **na adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<your-subdomain>.firmplay.com/`

    ![Konfigurowanie rejestracji jednokrotnej](./media/firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Należy pamiętać, że nie jest rzeczywistą wartość. Należy zaktualizować tę wartość rzeczywista logowania na adres URL. Skontaktuj się z [FirmPlay - propagowanie pracowników zespołu pomocy technicznej Rekrutacja](mailto:engineering@firmplay.com) aby zyskać tę wartość. 

4. Na **certyfikat podpisywania SAML** kliknij **Utwórz nowy certyfikat**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/firmplay-tutorial/tutorial_firmplay_03.png)     

5. Na **Tworzenie nowego świadectwa** okna dialogowego, kliknij ikonę kalendarza i wybierz **Data wygaśnięcia**. Następnie kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/firmplay-tutorial/tutorial_general_300.png)

6. Na **certyfikat podpisywania SAML** wybierz opcję **uaktywnić nowego świadectwa** i kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/firmplay-tutorial/tutorial_firmplay_04.png)

7. W oknie podręcznym **certyfikat przerzucania** okna, kliknij przycisk **OK**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/firmplay-tutorial/tutorial_general_400.png)

8. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (base64)** , a następnie zapisz plik certyfikatu na tym komputerze. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/firmplay-tutorial/tutorial_firmplay_05.png) 

9. Na **FirmPlay - propagowanie pracownika rekrutacji konfiguracji** , kliknij przycisk **skonfigurować FirmPlay - propagowanie pracownika dla Rekrutacja** otworzyć **Konfigurowanie logowania jednokrotnego** okno dialogowe.

    ![Konfigurowanie rejestracji jednokrotnej](./media/firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Konfigurowanie rejestracji jednokrotnej](./media/firmplay-tutorial/tutorial_firmplay_07.png)

10. Aby uzyskać logowania jednokrotnego skonfigurowane dla aplikacji, skontaktuj się z [FirmPlay - propagowanie pracowników zespołu pomocy technicznej Rekrutacja](mailto:engineering@firmplay.com) i podaj następujące: 

    • Pobrany **plik certyfikatu**

    • **Adres URL usługi rejestracji jednokrotnej SAML**

    • **Identyfikator jednostki SAML**

    • **Adresu URL wylogowania**
  

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/firmplay-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Tworzenie FirmPlay - propagowanie pracownika dla użytkownika testowego Rekrutacja

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w FirmPlay - propagowanie pracownika dla Rekrutacja. We współpracy z [FirmPlay - propagowanie pracowników zespołu pomocy technicznej Rekrutacja](mailto:engineering@firmplay.com) Aby dodać użytkowników w FirmPlay - propagowanie pracownika Rekrutacja platformy.


### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do udostępnienia jej FirmPlay - propagowanie pracownika dla Rekrutacja za pomocą usługi Azure rejestracji jednokrotnej.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta FirmPlay - propagowanie pracownika dla Rekrutacja, wykonaj następujące czynności:**

1. Otwórz widok aplikacji w portalu zarządzania Azure, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **FirmPlay - propagowanie pracownika dla Rekrutacja**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/firmplay-tutorial/tutorial_firmplay_50.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    


### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu FirmPlay - propagowanie pracownika Rekrutacja kafelka w panelu dostępu należy należy pobrać automatycznie zalogowane do Twojej FirmPlay - propagowanie pracownika Rekrutacja aplikacji.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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