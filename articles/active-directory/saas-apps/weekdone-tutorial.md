---
title: 'Samouczek: Integracji Azure Active Directory z Weekdone | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Weekdone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 34921f9a-5637-4420-ab4c-9beb34421909
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: jeedes
ms.openlocfilehash: a64daea76eb8b20cd8a8bb202705c995915806a4
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942633"
---
# <a name="tutorial-azure-active-directory-integration-with-weekdone"></a>Samouczek: Integracji Azure Active Directory z Weekdone

Z tego samouczka dowiesz się integrowanie Weekdone z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Weekdone zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Weekdone
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Weekdone (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Weekdone, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Weekdone logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Weekdone z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-weekdone-from-the-gallery"></a>Dodawanie Weekdone z galerii
Aby skonfigurować integrację usługi Azure AD Weekdone, należy dodać Weekdone z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Weekdone z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Weekdone**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/weekdone-tutorial/tutorial_weekdone_search.png)

5. W panelu wyników wybierz **Weekdone**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/weekdone-tutorial/tutorial_weekdone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Weekdone w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Weekdone jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Weekdone musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Weekdone, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Weekdone](#creating-a-weekdone-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Weekdone połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Weekdone.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Weekdone, wykonaj następujące czynności:**

1. W portalu Azure na **Weekdone** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/weekdone-tutorial/tutorial_weekdone_samlbase.png)

3. Na **Weekdone domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Konfigurowanie rejestracji jednokrotnej](./media/weekdone-tutorial/tutorial_weekdone_url1.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://weekdone.com/a/<tenant>/metadata`

    > [!NOTE]
    > Plik metadanych z weekdone można pobrać przy użyciu tego samego adresu URL.

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://weekdone.com/a/<tenantname>`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Konfigurowanie rejestracji jednokrotnej](./media/weekdone-tutorial/tutorial_weekdone_url2.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://weekdone.com/a/<tenantname>`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Weekdone](mailto:hello@weekdone.com) uzyskać te wartości. 

5. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/weekdone-tutorial/tutorial_weekdone_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/weekdone-tutorial/tutorial_general_400.png)
    
7. Na **konfiguracji Weekdone** , kliknij przycisk **skonfigurować Weekdone** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/weekdone-tutorial/tutorial_weekdone_configure.png) 

8. Aby skonfigurować logowanie jednokrotne w **Weekdone** stronie, musisz wysłać pobrany **XML metadanych, adres URL Sign-Out identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** do [Weekdone zespołem pomocy technicznej ](mailto:hello@weekdone.com).

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/weekdone-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/weekdone-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/weekdone-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/weekdone-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-weekdone-test-user"></a>Tworzenie użytkownika testowego Weekdone

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Weekdone. Weekdone obsługę w czasie, który jest domyślnie włączone.

Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu Weekdone, jeśli go jeszcze nie istnieje.

>[!NOTE]
>Jeśli trzeba ręcznie utworzyć użytkownika, należy skontaktować się [zespołem pomocy technicznej klienta Weekdone](mailto:hello@weekdone.com).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Weekdone.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Weekdone, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Weekdone**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/weekdone-tutorial/tutorial_weekdone_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu kafelka Weekdone w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Weekdone.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/weekdone-tutorial/tutorial_general_01.png
[2]: ./media/weekdone-tutorial/tutorial_general_02.png
[3]: ./media/weekdone-tutorial/tutorial_general_03.png
[4]: ./media/weekdone-tutorial/tutorial_general_04.png

[100]: ./media/weekdone-tutorial/tutorial_general_100.png

[200]: ./media/weekdone-tutorial/tutorial_general_200.png
[201]: ./media/weekdone-tutorial/tutorial_general_201.png
[202]: ./media/weekdone-tutorial/tutorial_general_202.png
[203]: ./media/weekdone-tutorial/tutorial_general_203.png
