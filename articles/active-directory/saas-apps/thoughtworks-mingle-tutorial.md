---
title: 'Samouczek: Integracji Azure Active Directory z Thoughtworks Mingle | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Thoughtworks Mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: f4729440bf523cef3f2721111852f4e5f445b5fb
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35940446"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Samouczek: Integracji Azure Active Directory z Thoughtworks Mingle

Z tego samouczka dowiesz się integrowanie Thoughtworks Mingle w usłudze Azure Active Directory (Azure AD).

Integrowanie Thoughtworks Mingle z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Thoughtworks Mingle
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Thoughtworks Mingle (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Thoughtworks Mingle, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Thoughtworks Mingle logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Thoughtworks Mingle z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Dodawanie Thoughtworks Mingle z galerii
Aby skonfigurować integrację Thoughtworks Mingle do usługi Azure AD, należy dodać Thoughtworks Mingle z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Thoughtworks Mingle z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Thoughtworks Mingle**, wybierz pozycję **Thoughtworks Mingle** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Thoughtworks Mingle na liście wyników](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Thoughtworks Mingle w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Thoughtworks Mingle jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Thoughtworks Mingle musi się.

W Thoughtworks Mingle, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Thoughtworks Mingle, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Thoughtworks Mingle](#create-a-thoughtworks-mingle-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Thoughtworks Mingle połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Thoughtworks Mingle.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Thoughtworks Mingle, wykonaj następujące czynności:**

1. W portalu Azure na **Thoughtworks Mingle** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

3. Na **Thoughtworks Mingle domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny Mingle Thoughtworks pojedynczy informacje logowania jednokrotnego](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Zaktualizuj tę wartość z adresem URL logowania rzeczywistych. Skontaktuj się z [zespołem pomocy technicznej klienta Mingle Thoughtworks](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) można uzyskać wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/thoughtworks-mingle-tutorial/tutorial_general_400.png)

6. Zaloguj się do Twojego **Thoughtworks Mingle** witryny firmy jako administrator.

7. Kliknij przycisk **Admin** , a następnie kliknij **konfiguracji logowania jednokrotnego**.
   
    ![Karta Admin](./media/thoughtworks-mingle-tutorial/ic785157.png "konfiguracji logowania jednokrotnego")

8. W **konfiguracji logowania jednokrotnego** sekcji, wykonaj następujące czynności:
   
    ![Konfiguracja rejestracji Jednokrotnej](./media/thoughtworks-mingle-tutorial/ic785158.png "konfiguracji logowania jednokrotnego")
    
    a. Aby przekazać plik metadanych, kliknij przycisk **wybierz plik**. 

    b. Kliknij przycisk **zapisać zmiany**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](./media/thoughtworks-mingle-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/thoughtworks-mingle-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Przycisk Dodaj](./media/thoughtworks-mingle-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Tworzenie użytkownika testowego Thoughtworks Mingle

Dla użytkowników usługi Azure AD można było się zalogować muszą mieć przydzielone do Thoughtworks Mingle aplikacji, przy użyciu nazwy użytkowników usługi Azure Active Directory. W przypadku Thoughtworks Mingle Inicjowanie obsługi to zadanie ręczne.

**Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Thoughtworks Mingle jako administrator.

2. Kliknij przycisk **profilu**.
   
    ![Pierwszy projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "pierwszego projektu")

3. Kliknij przycisk **Admin** , a następnie kliknij pozycję **użytkowników**.
   
    ![Użytkownicy](./media/thoughtworks-mingle-tutorial/ic785161.png "użytkowników")

4. Kliknij przycisk **nowego użytkownika**.
   
    ![Nowy użytkownik](./media/thoughtworks-mingle-tutorial/ic785162.png "nowego użytkownika")

5. Na **nowego użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
   
    ![Okno dialogowe nowego użytkownika](./media/thoughtworks-mingle-tutorial/ic785163.png "nowego użytkownika")  
 
    a. Typ **nazwy logowania**, **Nazwa wyświetlana**, **hasła wybierz**, **Potwierdź hasło** prawidłowy Azure AD konta chcesz udostępnić do powiązanych pól tekstowych. 

    b. Jako **typ użytkownika**, wybierz pozycję **pełnej**.

    c. Kliknij przycisk **utworzyć ten profil**.

>[!NOTE]
>Możesz użyć innych Thoughtworks Mingle użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Thoughtworks Mingle do kont użytkowników usługi AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do Thoughtworks Mingle.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Thoughtworks Mingle, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Thoughtworks Mingle**.

    ![Łącze Thoughtworks Mingle na liście aplikacji](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Thoughtworks Mingle w panelu dostępu użytkownik powinien uzyskać automatycznie zalogowane Thoughtworks Mingle aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/thoughtworks-mingle-tutorial/tutorial_general_203.png

