---
title: 'Samouczek: Integracji Azure Active Directory z AnswerHub | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: c4d63b5a51a6cf6626c42b0ef39587f1e760d238
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34338605"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Samouczek: Integracji Azure Active Directory z AnswerHub

Z tego samouczka dowiesz się integrowanie AnswerHub z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD AnswerHub zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do AnswerHub
- Umożliwia użytkownikom automatycznie pobrać zalogowane do AnswerHub (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z AnswerHub, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- AnswerHub logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie AnswerHub z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-answerhub-from-the-gallery"></a>Dodawanie AnswerHub z galerii
Aby skonfigurować integrację usługi Azure AD AnswerHub, należy dodać AnswerHub z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać AnswerHub z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **AnswerHub**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_search.png)

5. W panelu wyników wybierz **AnswerHub**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z AnswerHub w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w AnswerHub jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w AnswerHub musi się.

W AnswerHub, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z AnswerHub, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego AnswerHub](#creating-an-answerhub-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta AnswerHub połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji AnswerHub.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z AnswerHub, wykonaj następujące czynności:**

1. W portalu Azure na **AnswerHub** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_samlbase.png)

3. Na **AnswerHub domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company>.answerhub.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company>.answerhub.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta AnswerHub](mailto:success@answerhub.com) uzyskać te wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-answerhub-tutorial/tutorial_general_400.png)

6. Na **konfiguracji AnswerHub** , kliknij przycisk **skonfigurować AnswerHub** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_configure.png) 

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy AnswerHub jako administrator.
   
    >[!NOTE]
    >Jeśli potrzebujesz pomocy przy konfigurowaniu AnswerHub, skontaktuj się z [zespołem pomocy technicznej firmy AnswerHub](mailto:success@answerhub.com.).
   
8. Przejdź do **administracji**.

9. Kliknij przycisk **użytkowników i grup** kartę.

10. W okienku nawigacji po lewej stronie w **społecznościowych ustawienia** kliknij **Instalatora SAML**.

11. Kliknij przycisk **IDP Config** kartę.

12. Na **IDP Config** karcie, wykonaj następujące czynności:

     ![Instalator SAML](./media/active-directory-saas-answerhub-tutorial/ic785172.png "Instalatora SAML")  
  
     a. W **adres URL logowania IDP** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.
  
     b. W **adresu URL wylogowania IDP** pole tekstowe, Wklej **Sign-Out URL** wartość, która została skopiowana z portalu Azure.
     
     c. W **Format identyfikatora nazwy IDP** pole tekstowe, wprowadź nazwę użytkownika, wartość identyfikatora sam jako wybrane w portalu Azure w **atrybuty użytkownika** sekcji.
  
     d. Kliknij przycisk **klucze i certyfikaty**.

13. Na karcie klucze i certyfikaty wykonaj następujące czynności:
    
     ![Klucze i certyfikaty](./media/active-directory-saas-answerhub-tutorial/ic785173.png "klucze i certyfikaty")  
 
     a. Otwórz z zakodowanego certyfikatu base-64, który został pobrany z portalu Azure w programie Notatnik, skopiuj zawartość go do Schowka, a następnie wklej go do **IDP klucza publicznego (x 509 Format)** pola tekstowego.
  
     b. Kliknij pozycję **Zapisz**.

14. Na **IDP Config** , kliknij pozycję **zapisać**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-answerhub-test-user"></a>Tworzenie użytkownika testowego AnswerHub

Aby umożliwić użytkownikom usługi Azure AD zalogować się do AnswerHub, musi być przygotowana do AnswerHub.  
W przypadku AnswerHub Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **AnswerHub** witryny firmy jako administrator.

2. Przejdź do **administracji**.

3. Kliknij przycisk **użytkownicy i grupy** kartę.

4. W okienku nawigacji po lewej stronie w **Zarządzanie użytkownikami** kliknij **Tworzenie lub importowanie użytkowników**.
   
   ![Użytkownicy i grupy](./media/active-directory-saas-answerhub-tutorial/ic785175.png "użytkownicy i grupy")

5. Typ **adres E-mail**, **Username** i **hasło** prawidłowe konto usługi Azure Active Directory chcesz udostępnić do powiązanych pól tekstowych, a następnie kliknij przycisk **zapisać**.

>[!NOTE]
>Możesz użyć innych AnswerHub użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez AnswerHub do kont użytkowników usługi AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu AnswerHub.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta AnswerHub, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **AnswerHub**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka AnswerHub w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji AnswerHub.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_203.png

