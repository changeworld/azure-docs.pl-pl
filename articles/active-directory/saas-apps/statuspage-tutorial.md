---
title: 'Samouczek: Integracji Azure Active Directory z Strona stanu | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Strona stanu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 948b55a4a72837c9fb6bdfbc0e45dc0c3ba81feb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224756"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Samouczek: Integracji Azure Active Directory z Strona stanu

Z tego samouczka dowiesz się integrowanie Strona stanu w usłudze Azure Active Directory (Azure AD).

Integrowanie Strona stanu z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Strona stanu
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Strona stanu (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Strona stanu, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Strona stanu logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Strona Dodawanie stanu z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-statuspage-from-the-gallery"></a>Strona Dodawanie stanu z galerii
Aby skonfigurować integrację usługi Azure AD Strona stanu, należy dodać Strona stanu z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Strona stanu z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Strona stanu**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_search.png)

5. W panelu wyników wybierz **Strona stanu**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Strona stanu w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Strona stanu jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Strona stanu.

Strona stanu, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Strona stanu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Strona stanu](#creating-a-statuspage-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Strona stanu powiązaną z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji strona stanu.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Strona stanu, wykonaj następujące czynności:**

1. W portalu Azure na **Strona stanu** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/statuspage-tutorial/tutorial_statuspage_samlbase.png)

3. Na **domeny Strona stanu i adresów URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/statuspage-tutorial/tutorial_statuspage_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: 
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

    > [!NOTE]
    > Skontaktuj się z zespołem pomocy technicznej Strona stanu na [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)żądania metadanych niezbędnych do skonfigurowania rejestracji jednokrotnej. 
    >
    >a. Metadane, skopiuj wartości wystawcy, a następnie wklej go do **identyfikator** pola tekstowego.
    >
    >b. Z metadanych, skopiuj adres URL odpowiedzi, a następnie wklej go do **adres URL odpowiedzi** pola tekstowego.

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/statuspage-tutorial/tutorial_statuspage_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/statuspage-tutorial/tutorial_general_400.png)

6. Na **Strona stanu konfiguracji** kliknij **strona Konfigurowanie stanu** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/statuspage-tutorial/tutorial_statuspage_configure.png) 

7. W innym oknie przeglądarki należy zalogować się jako administrator do witryny firmy Strona stanu.

8. Na głównym pasku narzędzi, kliknij przycisk **Zarządzaj kontem**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/statuspage-tutorial/tutorial_statuspage_06.png) 

10. Kliknij przycisk **rejestracji jednokrotnej** kartę. 
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/statuspage-tutorial/tutorial_statuspage_07.png) 

11. Na stronie Ustawienia logowania jednokrotnego wykonaj następujące czynności:
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/statuspage-tutorial/tutorial_statuspage_08.png) 

    ![Konfigurowanie rejestracji jednokrotnej](./media/statuspage-tutorial/tutorial_statuspage_09.png) 
 
    a. W **logowania jednokrotnego, docelowy adres URL** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    b. Otwórz w Notatniku pobranego certyfikatu, skopiuj zawartość, a następnie wklej go do **certyfikatu** pola tekstowego. 

    c. Kliknij przycisk **Zapisywanie konfiguracji**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-statuspage-test-user"></a>Tworzenie użytkownika testowego Strona stanu

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Strona stanu.

Strona stanu obsługę w czasie. Została już włączona w [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on).

**Aby utworzyć użytkownika o nazwie Simona Britta w Strona stanu, wykonaj następujące czynności:**

1. Logowanie do witryny firmy Strona stanu jako administrator.

2. W menu u góry kliknij **Zarządzaj kontem**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/statuspage-tutorial/tutorial_statuspage_06.png)

3. Kliknij przycisk **członków zespołu** kartę. 
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

4. Kliknij przycisk **członek zespołu Dodaj**. 
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

5. Typ **adres E-mail**, **imię**, i **nazwisko** z prawidłowym użytkownikiem ustanawiane do powiązanych pól tekstowych. 
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

6. Jako **roli**, wybierz **Administrator klienta**.

7. Kliknij przycisk **Utwórz konto**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Strona stanu.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Strona stanu, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Strona stanu**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/statuspage-tutorial/tutorial_statuspage_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Strona stanu w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane Strona stanu aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/statuspage-tutorial/tutorial_general_01.png
[2]: ./media/statuspage-tutorial/tutorial_general_02.png
[3]: ./media/statuspage-tutorial/tutorial_general_03.png
[4]: ./media/statuspage-tutorial/tutorial_general_04.png

[100]: ./media/statuspage-tutorial/tutorial_general_100.png

[200]: ./media/statuspage-tutorial/tutorial_general_200.png
[201]: ./media/statuspage-tutorial/tutorial_general_201.png
[202]: ./media/statuspage-tutorial/tutorial_general_202.png
[203]: ./media/statuspage-tutorial/tutorial_general_203.png

