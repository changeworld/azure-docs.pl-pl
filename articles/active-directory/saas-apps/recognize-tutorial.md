---
title: 'Samouczek: Integracji Azure Active Directory z rozpoznawanie | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i rozpoznawanie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: c33b066aa1ba627166021a648a548b9b39d54630
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219061"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Samouczek: Integracji Azure Active Directory z rozpoznawanie

Z tego samouczka dowiesz się sposobu integracji z usługą Azure Active Directory (Azure AD) rozpoznawanie.

Integracja z usługą Azure AD rozpoznawanie zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do uznania
- Umożliwia użytkownikom automatycznie pobrać zalogowane do uznania (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z rozpoznawanie, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Rozpoznaj logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie rozpoznawaj z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-recognize-from-the-gallery"></a>Dodawanie rozpoznawaj z galerii
Aby skonfigurować integrację usługi Azure AD rozpoznawanie, musisz dodać rozpoznawaj z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać rozpoznawaj z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **rozpoznawanie**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/tutorial_recognize_search.png)

5. W panelu wyników wybierz **rozpoznawanie**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z rozpoznawanie w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w rozpoznawanie jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w rozpoznawanie musi się.

Rozpoznaj, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z rozpoznawanie, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego rozpoznawanie](#creating-a-recognize-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta rozpoznawanie połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji rozpoznawanie.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z rozpoznawanie, wykonaj następujące czynności:**

1. W portalu Azure na **rozpoznawanie** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/recognize-tutorial/tutorial_recognize_samlbase.png)

3. Na **adresy URL i rozpoznać domeny** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/recognize-tutorial/tutorial_recognize_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://recognizeapp.com/<your-domain>/saml/sso`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta rozpoznaje](mailto:support@recognizeapp.com) uzyskać adres URL logowania i może uzyskać wartość identyfikatora otwierania adresu URL metadanych dostawcy usług w sekcji Ustawienia logowania jednokrotnego, opisanej w dalszej części tego samouczka. . 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/recognize-tutorial/tutorial_recognize_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/recognize-tutorial/tutorial_general_400.png)

6. Na **rozpoznaje konfiguracji** , kliknij przycisk **skonfigurować rozpoznaje** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/recognize-tutorial/tutorial_recognize_configure.png) 

7. W oknie przeglądarki innej witryny sieci web logowanie do dzierżawy rozpoznawanie jako administrator.

8. W prawym górnym rogu kliknij **Menu**. Przejdź do **firmy Admin**.
   
    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/recognize-tutorial/tutorial_recognize_000.png)

9. W lewym okienku nawigacji, kliknij polecenie **ustawienia**.
   
    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/recognize-tutorial/tutorial_recognize_001.png)

10. Wykonaj następujące czynności na **ustawienia logowania jednokrotnego** sekcji.
   
    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Jako **włączenia logowania jednokrotnego**, wybierz pozycję **ON**.

    b. W **identyfikator jednostki IDP** pole tekstowe, Wklej wartość **identyfikator jednostki SAML** którego została skopiowana z portalu Azure.
    
    c. W **logowania jednokrotnego, docelowy adres url** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.
    
    d. W **Slo docelowy adres url** pole tekstowe, Wklej wartość **Sign-Out URL** którego została skopiowana z portalu Azure. 
    
    e. Otwórz z pobranego **certyfikatu (Base64)** plików w programie Notatnik, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu** pola tekstowego.
    
    f. Kliknij przycisk **Zapisz ustawienia** przycisku. 

11. Obok **ustawienia logowania jednokrotnego** sekcji, skopiuj adres URL, pod **adres url usługi dostawcy metadanych**.
   
    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/recognize-tutorial/tutorial_recognize_003.png)

12. Otwórz **łącze URL metadanych** w obszarze puste przeglądarki, aby pobrać dokumentu metadanych. Następnie skopiuj EntityDescriptor value(entityID) z pliku i wklej go w **identyfikator** textbox w **sekcji rozpoznać domeny i adresy URL** w portalu Azure.
    
    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-recognize-test-user"></a>Tworzenie użytkownika testowego rozpoznawanie

Aby umożliwić użytkownikom zalogować się na rozpoznawanie usługi Azure AD, musi być przygotowana do uznania. W przypadku uznania Inicjowanie obsługi to zadanie ręczne.

Ta aplikacja nie obsługuje udostępniania SCIM, ale ma synchronizacji alternatywny użytkownika, która udostępnia użytkownikom. 

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy rozpoznawanie jako administrator.

2. W prawym górnym rogu kliknij **Menu**. Przejdź do **firmy Admin**.

3. W lewym okienku nawigacji, kliknij polecenie **ustawienia**.

4. Wykonaj następujące czynności na **synchronizacji użytkownika** sekcji.
   
   ![Nowy użytkownik](./media/recognize-tutorial/tutorial_recognize_005.png "nowego użytkownika")
   
   a. Jako **włączona synchronizacja**, wybierz pozycję **ON**.
   
   b. Jako **dostawcy synchronizacji wybierz**, wybierz pozycję **Microsoft / usługi Office 365**.
   
   c. Kliknij przycisk **Przeprowadź synchronizację użytkownika**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do uznania.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta rozpoznawanie, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **rozpoznawanie**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/recognize-tutorial/tutorial_recognize_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka rozpoznawanie w panelu dostępu należy należy pobrać automatycznie zalogowane do uznania aplikacji. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/recognize-tutorial/tutorial_general_01.png
[2]: ./media/recognize-tutorial/tutorial_general_02.png
[3]: ./media/recognize-tutorial/tutorial_general_03.png
[4]: ./media/recognize-tutorial/tutorial_general_04.png

[100]: ./media/recognize-tutorial/tutorial_general_100.png

[200]: ./media/recognize-tutorial/tutorial_general_200.png
[201]: ./media/recognize-tutorial/tutorial_general_201.png
[202]: ./media/recognize-tutorial/tutorial_general_202.png
[203]: ./media/recognize-tutorial/tutorial_general_203.png

