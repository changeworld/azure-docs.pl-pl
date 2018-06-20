---
title: 'Samouczek: Integracji Azure Active Directory z NetDocuments | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 787a2794148cdcc27db7ec2da705944b60666762
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213573"
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Samouczek: Integracji Azure Active Directory z NetDocuments

Z tego samouczka dowiesz się integrowanie NetDocuments z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD NetDocuments zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do NetDocuments
- Umożliwia użytkownikom automatycznie pobrać zalogowane do NetDocuments (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z NetDocuments, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- NetDocuments logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie NetDocuments z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-netdocuments-from-the-gallery"></a>Dodawanie NetDocuments z galerii
Aby skonfigurować integrację usługi Azure AD NetDocuments, należy dodać NetDocuments z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać NetDocuments z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **NetDocuments**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/netdocuments-tutorial/tutorial_netdocuments_search.png)

5. W panelu wyników wybierz **NetDocuments**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/netdocuments-tutorial/tutorial_netdocuments_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z NetDocuments w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w NetDocuments jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w NetDocuments musi się.

W NetDocuments, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z NetDocuments, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego NetDocuments](#creating-a-netdocuments-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta NetDocuments połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji NetDocuments.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z NetDocuments, wykonaj następujące czynności:**

1. W portalu Azure na **NetDocuments** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/netdocuments-tutorial/tutorial_netdocuments_samlbase.png)

3. Na **NetDocuments domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/netdocuments-tutorial/tutorial_netdocuments_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [NetDocuments obsługuje zespołu](https://support.netdocuments.com/hc/) uzyskać te wartości.
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/netdocuments-tutorial/tutorial_netdocuments_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/netdocuments-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy NetDocuments jako administrator.

7. Przejdź do **Admin**.

8. Kliknij przycisk **Dodawanie i usuwanie użytkowników i grup**.
   
    ![Repozytorium](./media/netdocuments-tutorial/ic795047.png "repozytorium")

9. Kliknij przycisk **skonfiguruj zaawansowane opcje uwierzytelniania**.
    
    ![Skonfiguruj zaawansowane opcje uwierzytelniania](./media/netdocuments-tutorial/ic795048.png "skonfiguruj zaawansowane opcje uwierzytelniania")

10. Na **tożsamości federacyjnych** okna dialogowego, wykonaj następujące czynności:
   
    ![Federacyjna Identitty](./media/netdocuments-tutorial/ic795049.png "federacyjnych Identitty")
   
    a. Jako **typ serwera tożsamości federacyjnych**, wybierz pozycję **Active Directory Federation Services**.
   
    b. Kliknij przycisk **wybierz plik**, aby przekazać plik metadanych pobranych, który został pobrany z portalu Azure.
   
    c. Kliknij przycisk **OK**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/netdocuments-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/netdocuments-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/netdocuments-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/netdocuments-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-netdocuments-test-user"></a>Tworzenie użytkownika testowego NetDocuments

Aby umożliwić użytkownikom usługi Azure AD zalogować się do NetDocuments, musi być przygotowana do NetDocuments.  
W przypadku NetDocuments Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. SING do Twojej **NetDocuments** witryny firmy jako administrator.

2. W menu u góry kliknij **Admin**.
   
    ![Administrator](./media/netdocuments-tutorial/ic795051.png "administratora")

3. Kliknij przycisk **Dodawanie i usuwanie użytkowników i grup**.
   
    ![Repozytorium](./media/netdocuments-tutorial/ic795047.png "repozytorium")

4. W **adres E-mail** tekstowym, wpisz adres e-mail prawidłowe konto usługi Azure Active Directory, aby udostępnić, a następnie kliknij przycisk **Dodaj użytkownika**.
   
    ![Adres e-mail](./media/netdocuments-tutorial/ic795053.png "adres E-mail")
   
   >[!NOTE]
   >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail zawierającą łącze do potwierdzenia konta, zanim staje się aktywny. Możesz użyć innych NetDocuments użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez NetDocuments do świadczenia usługi Azure Active Directory kont użytkowników.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu NetDocuments.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta NetDocuments, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **NetDocuments**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/netdocuments-tutorial/tutorial_netdocuments_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka NetDocuments w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji NetDocuments.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/netdocuments-tutorial/tutorial_general_01.png
[2]: ./media/netdocuments-tutorial/tutorial_general_02.png
[3]: ./media/netdocuments-tutorial/tutorial_general_03.png
[4]: ./media/netdocuments-tutorial/tutorial_general_04.png

[100]: ./media/netdocuments-tutorial/tutorial_general_100.png

[200]: ./media/netdocuments-tutorial/tutorial_general_200.png
[201]: ./media/netdocuments-tutorial/tutorial_general_201.png
[202]: ./media/netdocuments-tutorial/tutorial_general_202.png
[203]: ./media/netdocuments-tutorial/tutorial_general_203.png

