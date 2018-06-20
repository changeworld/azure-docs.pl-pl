---
title: 'Samouczek: Integracji Azure Active Directory z Marketo | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i usługi Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: e7ff12938ea66bc218f71018247e70f0ebc1400d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226813"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Samouczek: Integracji Azure Active Directory z usługi Marketo

Z tego samouczka dowiesz się Integrowanie usługi Marketo w usłudze Azure Active Directory (Azure AD).

Integrowanie usługi Marketo w usłudze Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do usługi Marketo
- Umożliwia użytkownikom automatycznie pobrać podpisany w przypadku usługi Marketo (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Marketo, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Usługi Marketo jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie usługi Marketo z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-marketo-from-the-gallery"></a>Dodawanie usługi Marketo z galerii
Aby skonfigurować integrację usługi Marketo w usłudze Azure Active Directory, należy dodać Marketo z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługi Marketo z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Marketo**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/tutorial_marketo_search.png)

5. W panelu wyników wybierz **Marketo**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usługi Marketo oparte na koncie użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Marketo jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Marketo.

W Marketo, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Marketo, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Marketo](#creating-a-marketo-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Marketo połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji usługi Marketo.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Marketo, wykonaj następujące czynności:**

1. W portalu Azure na **Marketo** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_samlbase.png)

3. Na **Marketo domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://saml.marketo.com/sp`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej usługi Marketo](http://investors.marketo.com/contactus.cfm) uzyskać te wartości.
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_general_400.png)

6. Na **konfiguracji usługi Marketo** kliknij **Konfiguruj usługi Marketo** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_configure.png) 

7. Aby uzyskać identyfikator Munchkin aplikacji, zaloguj się przy użyciu poświadczeń administratora usługi Marketo i wykonania następujących czynności:
   
    a. Zaloguj się do usługi Marketo aplikacji przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **Admin** przycisk w okienku nawigacji w górnym.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu integracji i kliknij **łącze Munchkin**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Skopiuj identyfikator Munchkin wyświetlone na ekranie i wykonaj adres URL odpowiedzi, w Kreatorze konfiguracji usługi Azure AD.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_12.png) 

8. Aby skonfigurować logowanie Jednokrotne w aplikacji, wykonaj następujące czynności:
   
    a. Zaloguj się do usługi Marketo aplikacji przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **Admin** przycisk w okienku nawigacji w górnym.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu integracji i kliknij **rejestracji jednokrotnej**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Aby włączyć ustawienia SAML, kliknij przycisk **Edytuj** przycisku.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Włączone** ustawienia logowania jednokrotnego.
   
    f. Wklej **identyfikator jednostki SAML**w **identyfikator wystawcy** pola tekstowego.
   
    g. W **identyfikator jednostki** pole tekstowe, wprowadź adres URL jako `http://saml.marketo.com/sp`.
   
    h. Wybierz lokalizację identyfikator użytkownika jako **element nazwa identyfikatora**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Jeśli Twojego identyfikatora użytkownika nie ma wartości głównej nazwy użytkownika, a następnie zmień wartość na karcie atrybutu.
   
    i. Przekaż certyfikat, który został pobrany z Kreatora konfiguracji usługi Azure AD. **Zapisz** ustawienia.
   
    j. Edytowanie ustawień stron przekierowania.
   
    k. Wklej **SAML pojedynczy znak na adres URL usługi** w **adres URL logowania** pola tekstowego.
   
    l. Wklej **Sign-Out URL** w **adresu URL wylogowania** pola tekstowego.
   
    m. W **adres URL błędu**, kopia programu **adresu URL wystąpienia usługi Marketo** i kliknij przycisk **zapisać** przycisk, aby zapisać ustawienia.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_10.png)

9. Aby włączyć logowanie Jednokrotne dla użytkowników, wykonaj następujące czynności:
   
    a. Zaloguj się do usługi Marketo aplikacji przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **Admin** przycisk w okienku nawigacji w górnym.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do **zabezpieczeń** menu i kliknij przycisk **ustawienia logowania**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Sprawdź **wymagają rejestracji Jednokrotnej** opcji i **zapisać** ustawienia.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-marketo-test-user"></a>Tworzenie użytkownika testowego usługi Marketo

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Marketo. wykonaj następujące kroki, aby utworzyć użytkownika usługi Marketo platformy.

1. Zaloguj się do usługi Marketo aplikacji przy użyciu poświadczeń administratora.

2. Kliknij przycisk **Admin** przycisk w okienku nawigacji w górnym.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Przejdź do **zabezpieczeń** menu i kliknij przycisk **użytkownicy i role**
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Kliknij przycisk **zaprosić nowego użytkownika** link na karcie Użytkownicy
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. W Kreatorze nowego użytkownika z zaproszeniem wypełnij następujące informacje
   
    a. Wprowadź nazwę danego użytkownika **E-mail** adres w polu tekstowym
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Wprowadź **imię** w polu tekstowym
   
    c. Wprowadź **nazwisko** w polu tekstowym
   
    d. Kliknij przycisk **Dalej**

6. W **uprawnienia** wybierz opcję **roli użytkownika** i kliknij przycisk **dalej**
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kliknij przycisk **wysyłania** przycisk, aby wysłać zaproszenie użytkownika
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Użytkownik otrzymuje powiadomienie e-mail i kliknij łącze i Zmień hasło, aby aktywować konto. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do usługi Marketo.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Marketo, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Marketo**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/marketo-tutorial/tutorial_marketo_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Marketo w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji usługi Marketo.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/marketo-tutorial/tutorial_general_01.png
[2]: ./media/marketo-tutorial/tutorial_general_02.png
[3]: ./media/marketo-tutorial/tutorial_general_03.png
[4]: ./media/marketo-tutorial/tutorial_general_04.png

[100]: ./media/marketo-tutorial/tutorial_general_100.png

[200]: ./media/marketo-tutorial/tutorial_general_200.png
[201]: ./media/marketo-tutorial/tutorial_general_201.png
[202]: ./media/marketo-tutorial/tutorial_general_202.png
[203]: ./media/marketo-tutorial/tutorial_general_203.png

