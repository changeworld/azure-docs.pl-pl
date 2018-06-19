---
title: 'Samouczek: Integracji Azure Active Directory z RFPIO | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: eda20d60d04ba8e50c31697b2f8775f2539088f3
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942797"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Samouczek: Integracji Azure Active Directory z RFPIO

Z tego samouczka dowiesz się integrowanie RFPIO z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD RFPIO zapewnia następujące korzyści:

- Możesz kontrolować, kto w usłudze Azure AD, który ma dostęp do RFPIO.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do RFPIO (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji--portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z RFPIO, potrzebne są następujące elementy:

- Subskrypcja usługi Azure AD.
- RFPIO pojedynczy znak z włączoną subskrypcji.

> [!NOTE]
> Firma Microsoft nie zaleca się używanie środowiska produkcyjnego do testowania czynności w tym samouczku.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisane w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie RFPIO z galerii.
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne.

## <a name="add-rfpio-from-the-gallery"></a>Dodaj RFPIO z galerii
Aby skonfigurować integrację usługi Azure AD RFPIO, należy dodać RFPIO z galerii do listy zarządzanych aplikacji SaaS.

### <a name="to-add-rfpio-from-the-gallery"></a>Aby dodać RFPIO z galerii

1. W  **[portalu Azure](https://portal.azure.com)**, w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **RFPIO**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/tutorial_rfpio_search.png)

5. W panelu wyników wybierz **RFPIO**, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z RFPIO w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić relacji między użytkownikiem odpowiednika w RFPIO a użytkownikiem w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w RFPIO musi się.

W RFPIO, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z RFPIO, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**— aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**— do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego RFPIO](#creating-a-rfpio-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta RFPIO połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**— aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji RFPIO.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z RFPIO, wykonaj następujące czynności:**

1. W portalu Azure na **RFPIO** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. Na **RFPIO domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/tutorial_rfpio_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL: `https://www.rfpio.com`

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**.

    c. W **stan przekazywania** pole tekstowe Wprowadź wartość ciągu. Skontaktuj się z [RFPIO obsługuje zespołu](https://www.rfpio.com/contact/) aby zyskać tę wartość. 

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb: 

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/tutorial_rfpio_url2.png)

    W **Zaloguj się na adres URL** tekstowym, wpisz adres URL: `https://www.app.rfpio.com`

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/tutorial_general_400.png)

7. W oknie przeglądarki innej witryny sieci web, zaloguj się do **RFPIO** witryny sieci Web jako administrator.

8. Kliknij menu rozwijanego lewym rogu dolnej.

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/app1.png)

9. Polecenie **ustawienia organizacji**. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/app2.png)

10. Polecenie **funkcje & integracji**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/app4.png)

11. W **konfiguracji logowania jednokrotnego SAML** kliknij **Edytuj**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/app3.png)

12. W tej sekcji należy wykonać następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/app5.png)
    
    a. Skopiuj zawartość **pobierane metadane XML** i wklej ją do **konfiguracji tożsamości** pola.

    > [!NOTE]
    >Aby skopiować zawartość pobrana **XML metadanych** użyj **Notatnik ++** lub właściwe **edytora XML**. 

    b. Kliknij przycisk **zweryfikować**.

    c. Po kliknięciu przycisku **zweryfikować**, przerzucania **SAML(Enabled)** do włączenia.

    d. Kliknij przycisk **przesłać**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rfpio-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-rfpio-test-user"></a>Tworzenie użytkownika testowego RFPIO

Aby umożliwić użytkownikom usługi Azure AD zalogować się do RFPIO, musi być przygotowana do RFPIO.  
W przypadku RFPIO Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy RFPIO jako administrator.

2. Kliknij menu rozwijanego lewym rogu dolnej.

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/app1.png)

3. Polecenie **ustawienia organizacji**. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/app2.png)

4. Kliknij przycisk **członków zespołu**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/app6.png)

5. Polecenie **Dodaj członków**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/app7.png)

6. W **Dodawanie nowych elementów członkowskich** sekcji. Wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/app8.png)

    a. Wprowadź **adres E-mail** w **Podaj jeden adres e-mail w jednym wierszu** pola.

    b. Wybierz Plese **roli** zgodnie z wymaganiami.

    c. Kliknij przycisk **Dodaj członków**.
        
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail i następuje łącze, aby potwierdzić swoje konto, zanim staje się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu RFPIO.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta RFPIO, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **RFPIO**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/rfpio-tutorial/tutorial_rfpio_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka RFPIO w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji RFPIO.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integracji aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
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

