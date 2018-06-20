---
title: 'Samouczek: Azure Active Directory integracji z pakietem G | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługi Azure Active Directory i pakiet G.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: jeedes
ms.openlocfilehash: ce1549231157712ff7421b85685ae796388e77f9
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225099"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Samouczek: Azure Active Directory integracji z pakietem G

Z tego samouczka dowiesz sposobu integracji z usługą Azure Active Directory (Azure AD) pakiet G.

Integracja z usługą Azure AD G Suite zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do zestawu G.
- Umożliwia użytkownikom automatycznie pobrać zalogowane pakietu G (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem G, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Pakiet G logowanie jednokrotne włączone subskrypcji
- Subskrypcja usługi Google Apps lub subskrypcji usługi Google Cloud Platform.

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Często zadawane pytania
1.  **Pytanie: czy obsługi tej integracji Usługa rejestracji Jednokrotnej w Google Cloud Platform integracji z usługą Azure AD?**
    
    Odp. Tak. Usługi Google Cloud Platform i usługi Google Apps współużytkują tę samą platformę uwierzytelniania. Tak, aby zrobić integracji GCP, należy skonfigurować logowanie Jednokrotne z usługi Google Apps.


2. **Pytanie: czy Chromebooks i innych urządzeniach Chrome zgodne z usługą Azure AD rejestracji jednokrotnej?**
   
    Odpowiedź: tak, użytkownicy będą mogli zalogować się do swoich urządzeń Chromebook przy użyciu swoich poświadczeń usługi Azure AD. Zobacz to [artykuł pomocy technicznej pakietu G](https://support.google.com/chrome/a/answer/6060880) informacji o tym, dlaczego użytkownicy mogą się monit o poświadczenia dwa razy.

3. **Pytanie: czy włączyć logowanie jednokrotne, użytkownicy będą mogli używać swoich poświadczeń usługi Azure AD do logowania się do żadnego produktu Google, takich jak klasy Google, GMail, dysk Google, YouTube i tak dalej?**
   
    Odpowiedź: tak, w zależności od [o zestawie G](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) chcesz włączyć lub wyłączyć w Twojej organizacji.

4. **Pytanie: czy można włączyć logowanie jednokrotne dla tylko podzestaw użytkowników G Suite?**
   
    A: wymaga włączenia logowania jednokrotnego natychmiast nie wszyscy użytkownicy G Suite do uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Ponieważ G pakiet nie obsługuje posiadanie wielu dostawców tożsamości, dostawca tożsamości w danym środowisku G pakiet może być usługi Azure AD lub Google —, ale nie oba jednocześnie.

5. **Pytanie: czy użytkownik jest zalogowany przy użyciu systemu Windows, czy na pewno automatycznie uwierzytelniają pakietu G bez pobierania zostanie wyświetlony monit o podanie hasła?**
   
    Odpowiedź: istnieją dwie opcje dotyczące włączania tego scenariusza. Najpierw użytkownicy mogą zalogować się do urządzeń z systemem Windows 10 za pomocą [usługi Azure Active Directory Join](../device-management-introduction.md). Alternatywnie użytkownicy mogą zalogować się do urządzeń z systemem Windows, które są przyłączone do domeny do lokalnej usługi Active Directory, który został włączony dla pojedynczego logowania do usługi Azure AD za pomocą [Active Directory Federation Services (AD FS)](../connect/active-directory-aadconnect-user-signin.md) wdrożenia. Obie te opcje wymagają należy wykonać czynności opisane w następujących samouczkiem, aby włączyć logowanie jednokrotne między usługą Azure AD i pakiet G.

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie pakietu G z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-g-suite-from-the-gallery"></a>Dodawanie pakietu G z galerii
Aby skonfigurować integrację usługi Azure AD G Suite, należy dodać pakiet G z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać pakiet G z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **G Suite**, wybierz pozycję **G Suite** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Pakiet G na liście wyników](./media/google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z pakietem G w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednikiem pakietu G jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi pakietu G musi określone.

W zestawie G, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z pakietem G, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego zestawu G](#create-a-g-suite-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta G pakiet, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji pakietu G.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z pakietem G, wykonaj następujące czynności:**

1. W portalu Azure na **G Suite** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/google-apps-tutorial/tutorial_googleapps_samlbase.png)

3. Na **G Suite domeny i adres URL** sekcji, aby skonfigurować dla **Gmail** należy wykonać następujące czynności:

    ![Adresy URL i domeny Suite G pojedynczy informacje logowania jednokrotnego](./media/google-apps-tutorial/tutorial_googleapps_urlgmail.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL adresu URL przy użyciu następującego wzorca: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
 
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Suite G](https://www.google.com/contact/) uzyskać te wartości.

4. Na **G Suite domeny i adres URL** sekcji, aby skonfigurować dla **usługi Google Cloud Platform** należy wykonać następujące czynności:

    ![Adresy URL i domeny Suite G pojedynczy informacje logowania jednokrotnego](./media/google-apps-tutorial/tutorial_googleapps_url1.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL adresu URL przy użyciu następującego wzorca: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Suite G](https://www.google.com/contact/) uzyskać te wartości. 

5. Na **certyfikat podpisywania SAML** kliknij **certyfikatu** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/google-apps-tutorial/tutorial_googleapps_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/google-apps-tutorial/tutorial_general_400.png)

7. Na **G pakiet konfiguracji** kliknij **skonfigurować pakiet G** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, SAML pojedynczy znak na adres URL usługi i zmień adres URL hasła** z **sekcji krótkimi opisami.**

    ![G pakietu konfiguracji](./media/google-apps-tutorial/tutorial_googleapps_configure.png) 

8. Otwórz nową kartę w przeglądarce i zaloguj się do [konsoli administracyjnej Suite G](http://admin.google.com/) przy użyciu konta administratora.

9. Kliknij przycisk **zabezpieczeń**. Jeśli nie widzisz łącza może być ukryty w obszarze **więcej formantów** menu u dołu ekranu.
   
    ![Kliknij pozycję Zabezpieczenia.][10]

10. Na **zabezpieczeń** kliknij przycisk **Konfigurowanie rejestracji jednokrotnej (SSO).**
   
    ![Kliknij przycisk logowania jednokrotnego.][11]

11. Wykonaj następujące zmiany w konfiguracji:
   
    ![Konfigurowanie logowania jednokrotnego][12]
   
    a. Wybierz **Instalatora Usługa rejestracji Jednokrotnej z dostawcy tożsamości innych firm**.

    b. W **adres URL logowania strony** pole w zestawie G, Wklej wartość **pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    c. W **adres URL strony wylogowania** pole w zestawie G, Wklej wartość **Sign-Out URL** którego została skopiowana z portalu Azure. 

    d. W **zmienić adres URL hasła** pole w zestawie G, Wklej wartość **zmienić adres URL hasła** którego została skopiowana z portalu Azure. 

    e. W zestawie G dla **certyfikatu weryfikacji**, Przekaż certyfikat, który został pobrany z portalu Azure.

    f. Wybierz **użyć konkretnego wystawcę domeny**.

    g. Kliknij przycisk **zapisać zmiany**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/google-apps-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/google-apps-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/google-apps-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/google-apps-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-g-suite-test-user"></a>Tworzenie użytkownika testowego zestawu G

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w G pakietu oprogramowania. Pakiet G obsługuje automatyczne Inicjowanie obsługi administracyjnej, który jest domyślnie włączone. Brak akcji można w tej sekcji. Jeśli użytkownik nie istnieje w G pakiet oprogramowania, nowy jest tworzony podczas próby dostępu G pakietu oprogramowania.

>[!NOTE] 
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej usługi Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do zestawu G.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta G Suite, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **G Suite**.

    ![Łącze G Suite na liście aplikacji](./media/google-apps-tutorial/tutorial_googleapps_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka G Suite w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane G pakiet aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/googleapps-tutorial/tutorial_general_01.png
[2]: ./media/googleapps-tutorial/tutorial_general_02.png
[3]: ./media/googleapps-tutorial/tutorial_general_03.png
[4]: ./media/googleapps-tutorial/tutorial_general_04.png

[100]: ./media/googleapps-tutorial/tutorial_general_100.png

[200]: ./media/googleapps-tutorial/tutorial_general_200.png
[201]: ./media/googleapps-tutorial/tutorial_general_201.png
[202]: ./media/googleapps-tutorial/tutorial_general_202.png
[203]: ./media/googleapps-tutorial/tutorial_general_203.png
[10]: ./media/googleapps-tutorial/gapps-security.png
[11]: ./media/googleapps-tutorial/security-gapps.png
[12]: ./media/googleapps-tutorial/gapps-sso-config.png

