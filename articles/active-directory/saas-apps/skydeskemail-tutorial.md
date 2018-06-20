---
title: 'Samouczek: Integracji Azure Active Directory z SkyDesk poczty E-mail | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i SkyDesk wiadomości E-mail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 39821146f953630a7722ff866c60cab8b7d46175
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220838"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Samouczek: Integracji Azure Active Directory z SkyDesk poczty E-mail

Z tego samouczka dowiesz się integrowanie SkyDesk poczty E-mail w usłudze Azure Active Directory (Azure AD).

Integrowanie SkyDesk wiadomości E-mail z usługi Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do poczty E-mail SkyDesk
- Umożliwia użytkownikom automatycznie pobrać zalogowane do poczty E-mail SkyDesk (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z SkyDesk poczty E-mail, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- E-mail SkyDesk logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna tutaj [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie SkyDesk wiadomości E-mail z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-skydesk-email-from-the-gallery"></a>Dodawanie SkyDesk wiadomości E-mail z galerii
Aby skonfigurować integrację SkyDesk poczty e-mail w usłudze Azure Active Directory, należy dodać SkyDesk wiadomości E-mail z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SkyDesk wiadomości E-mail z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **E-mail SkyDesk**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/tutorial_skydeskemail_search.png)

5. W panelu wyników wybierz **E-mail SkyDesk**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/tutorial_skydeskemail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i test usługi Azure AD rejestracji jednokrotnej z SkyDesk poczty E-mail w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w SkyDesk wiadomości E-mail do użytkownika w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w wiadomości E-mail SkyDesk musi się.

W wiadomości E-mail SkyDesk, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SkyDesk poczty E-mail, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego E-mail SkyDesk](#creating-a-skydesk-email-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta SkyDesk wiadomości E-mail, jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji poczty E-mail SkyDesk.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z SkyDesk poczty E-mail, wykonaj następujące czynności:**

1. W portalu Azure na **E-mail SkyDesk** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_samlbase.png)

3. Na **SkyDesk domenę poczty E-mail i adresów URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Zaktualizuj tę wartość z adresem URL logowania rzeczywistych. Skontaktuj się z [zespołem pomocy technicznej klienta poczty E-mail SkyDesk](https://www.skydesk.sg/support/) można uzyskać wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_general_400.png)

6. Na **konfiguracji poczty E-mail SkyDesk** kliknij pozycję **konfigurowanie poczty E-mail SkyDesk** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_configure.png) 

7. Aby włączyć logowanie Jednokrotne w **E-mail SkyDesk**, wykonaj następujące czynności:

    a. Logowanie do konta E-mail SkyDesk jako administrator.

    b. W menu u góry kliknij **Instalator**i wybierz **organizacji**. 
    
      ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
    c. Polecenie **domen** z lewego panelu.
    
      ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Polecenie **Dodawanie domeny**.
    
      ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. Wpisz nazwę domeny, a następnie zweryfikować domenę.
    
      ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Polecenie **uwierzytelnianie SAML** z lewego panelu.
    
      ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

8. Na **uwierzytelnianie SAML** okna dialogowego strony, należy wykonać następujące czynności:
   
      ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
    >[!NOTE]
    >Aby używać uwierzytelniania na podstawie SAML, albo powinien mieć **zweryfikowaną domeną** lub **portal adresu URL** Instalatora. Można ustawić portalu adres URL o unikatowej nazwie.
    > 
    > 
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. W **adres URL logowania** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.
   
    b. W **wylogowania** pole tekstowe adresu URL, Wklej wartość **Sign-Out URL**, które zostały skopiowane z portalu Azure.

    c. **Zmień adres URL hasła** jest opcjonalny, dlatego pozostaw to pole puste.

    d. Polecenie **uzyskać klucz z pliku** wybierz certyfikat pobrany z portalu Azure, a następnie kliknij przycisk **Otwórz** można przekazać certyfikatu.

    e. Jako **algorytm**, wybierz pozycję **RSA**.

    f. Kliknij przycisk **Ok** Aby zapisać zmiany.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/skydeskemail-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-skydesk-email-test-user"></a>Tworzenie użytkownika testowego SkyDesk poczty E-mail

W tej sekcji utworzysz użytkownika o nazwie Simona Britta w SkyDesk wiadomości E-mail.

1. Polecenie **dostępu użytkownika** z lewym panelu w SkyDesk wiadomości E-mail, a następnie wprowadź nazwy użytkownika. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Jeśli musisz utworzyć zbiorczego użytkowników, należy skontaktować się [zespołem pomocy technicznej klienta poczty E-mail SkyDesk](https://www.skydesk.sg/support/).


### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do poczty E-mail SkyDesk.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta SkyDesk poczty E-mail, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **E-mail SkyDesk**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/skydeskemail-tutorial/tutorial_skydeskemail_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu kafelka SkyDesk poczty E-mail w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji poczty E-mail SkyDesk.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/skydeskemail-tutorial/tutorial_general_04.png

[100]: ./media/skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/skydeskemail-tutorial/tutorial_general_201.png
[202]: ./media/skydeskemail-tutorial/tutorial_general_202.png
[203]: ./media/skydeskemail-tutorial/tutorial_general_203.png

