---
title: 'Samouczek: Azure Active Directory integracji z sieciami Skyhigh | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Skyhigh sieci.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 48d6ddd1-4d3e-4019-8234-5e5212684d9c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: jeedes
ms.openlocfilehash: cdea99a7e2814673e5ba533fbfd58dbb01ad240b
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35943510"
---
# <a name="tutorial-azure-active-directory-integration-with-skyhigh-networks"></a>Samouczek: Azure Active Directory integracji z sieciami Skyhigh

Z tego samouczka dowiesz się Integrowanie sieci Skyhigh w usłudze Azure Active Directory (Azure AD).

Integracja sieci Skyhigh z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do sieci Skyhigh.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do sieci Skyhigh (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z sieciami Skyhigh, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Sieci Skyhigh logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie sieci Skyhigh z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-skyhigh-networks-from-the-gallery"></a>Dodawanie sieci Skyhigh z galerii
Aby skonfigurować integrację usługi Azure AD Skyhigh sieci, należy dodać Skyhigh sieci z poziomu galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Skyhigh sieci z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **sieci Skyhigh**, wybierz pozycję **sieci Skyhigh** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Skyhigh sieci na liście wyników](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z sieciami Skyhigh w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w sieciach Skyhigh jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w sieciach Skyhigh musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z sieciami Skyhigh, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego sieci Skyhigh](#create-a-skyhigh-networks-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta sieci Skyhigh, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i konfigurowania rejestracji jednokrotnej w sieci Skyhigh aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z sieciami Skyhigh, wykonaj następujące czynności:**

1. W portalu Azure na **sieci Skyhigh** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_samlbase.png)

3. Na **Skyhigh sieci domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domen sieci Skyhigh pojedynczy informacje logowania jednokrotnego](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url.png)

    a. W **identyfikator jednostki** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domen sieci Skyhigh pojedynczy informacje logowania jednokrotnego](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta sieci Skyhigh](mailto:support@skyhighnetworks.com) uzyskać te wartości. 

5. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/skyhighnetworks-tutorial/tutorial_general_400.png)
    
7. Na **konfiguracji sieci Skyhigh** , kliknij przycisk **Konfigurowanie sieci Skyhigh** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja sieci Skyhigh](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_configure.png) 

8. Aby skonfigurować logowanie jednokrotne w **Skyhigh sieci** stronie, musisz wysłać pobrany **certyfikatu (Base64), adres URL Sign-Out identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** do [ Skyhigh sieci obsługuje zespołu](mailto:support@skyhighnetworks.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/skyhighnetworks-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/skyhighnetworks-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/skyhighnetworks-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/skyhighnetworks-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-skyhigh-networks-test-user"></a>Tworzenie użytkownika testowego Skyhigh sieci

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w sieciach Skyhigh. Praca z [Skyhigh sieci obsługuje zespołu](mailto:support@skyhighnetworks.com) Aby dodać użytkowników do platformy Skyhigh sieci. Użytkownicy muszą być tworzone i aktywowana, aby użyć rejestracji jednokrotnej

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do sieci Skyhigh.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Skyhigh sieci, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **sieci Skyhigh**.

    ![Link sieci Skyhigh na liście aplikacji](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Skyhigh sieci w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Skyhigh sieci.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skyhighnetworks-tutorial/tutorial_general_01.png
[2]: ./media/skyhighnetworks-tutorial/tutorial_general_02.png
[3]: ./media/skyhighnetworks-tutorial/tutorial_general_03.png
[4]: ./media/skyhighnetworks-tutorial/tutorial_general_04.png

[100]: ./media/skyhighnetworks-tutorial/tutorial_general_100.png

[200]: ./media/skyhighnetworks-tutorial/tutorial_general_200.png
[201]: ./media/skyhighnetworks-tutorial/tutorial_general_201.png
[202]: ./media/skyhighnetworks-tutorial/tutorial_general_202.png
[203]: ./media/skyhighnetworks-tutorial/tutorial_general_203.png

