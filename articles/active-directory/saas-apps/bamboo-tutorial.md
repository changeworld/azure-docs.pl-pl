---
title: 'Samouczek: Integracji Azure Active Directory z logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: c92d8167b46b185a55ed915b097ea6238e4bde69
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36216195"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Samouczek: Integracji Azure Active Directory z logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH

Z tego samouczka dowiesz się integrowanie logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH w usłudze Azure Active Directory (Azure AD).

Integrowanie logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Logowania jednokrotnego SAML dla Bambus przez rozpoznawanie jednokrotnego GmbH w subskrypcji włączone

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie logowania jednokrotnego SAML dla Bambus przy rozdzielczości GmbH z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Dodawanie logowania jednokrotnego SAML dla Bambus przy rozdzielczości GmbH z galerii
Aby skonfigurować integrację logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH do usługi Azure AD, należy dodać logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać logowania jednokrotnego SAML dla Bambus za rozdzielczość GmbH z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH**, wybierz pozycję **logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![Logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH na liście wyników](./media/bamboo-tutorial/tutorial_bamboo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego SAML dla Bambus przez rozdzielczość GmbH w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, jakiego odpowiednikiem użytkownika w logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH jest użytkownikiem w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH musi się.

W logowania jednokrotnego SAML Bambus przez rozpoznawania GmbH, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Utwórz logowania jednokrotnego SAML dla Bambus przez użytkownika testowego GmbH rozpoznawania](#create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w sieci logowania jednokrotnego SAML dla Bambus przy rozpoznawania GmbH aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH, wykonaj następujące czynności:**

1. W portalu Azure na **logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/bamboo-tutorial/tutorial_bamboo_samlbase.png)

3. Na **logowania jednokrotnego SAML Bambus przez rozpoznawania GmbH domeny i adresów URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w trybie IDP inicjowane:

    ![Adresy URL i logowania jednokrotnego SAML dla Bambus przez rozpoznawanie domeny GmbH pojedynczy informacje logowania jednokrotnego](./media/bamboo-tutorial/tutorial_bamboo_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i logowania jednokrotnego SAML dla Bambus przez rozpoznawanie domeny GmbH pojedynczy informacje logowania jednokrotnego](./media/bamboo-tutorial/tutorial_bamboo_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołu obsługi logowania jednokrotnego SAML dla Bambus przez rozpoznawania klienta GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) uzyskać te wartości. 

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/bamboo-tutorial/tutorial_bamboo_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/bamboo-tutorial/tutorial_general_400.png)

7. Logowanie do użytkownika logowania jednokrotnego SAML dla Bambus przez witrynę firmy GmbH rozpoznawania jako administrator.

8. Po prawej stronie głównym pasku narzędzi kliknij przycisk **ustawienia** > **dodatki**.

    ![Ustawienia](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

9. Przejdź do sekcji zabezpieczeń, kliknij pozycję **SAML SingleSignOn** na pasku menu.

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

10. Na **na stronie konfiguracji wtyczki SIngleSignOn SAML**, kliknij przycisk **dodać idp**. 

    ![Dodaj idp](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

11. Na **wybierz Twój dostawca tożsamości SAML** strony, należy wykonać następujące czynności:

    ![Dostawca tożsamości](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Wybierz **typu Idp** jako **usługi AZURE AD**.

    b. W **nazwa** tekstowym, wpisz nazwę.

    c. W **opis** tekstowym, wpisz opis.

    d. Kliknij przycisk **Dalej**.

12. Na **konfiguracji dostawcy tożsamości** kliknij **dalej**.

    ![Konfiguracja tożsamości](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

13.  Na **Importowanie metadanych Idp SAML** kliknij **Załaduj plik** przekazać **XML METADANYCH** pliku, który został pobrany z portalu Azure.

    ![Idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

14. Kliknij przycisk **Dalej**.

15. Kliknij przycisk **Zapisz ustawienia**.

    ![Zapisz](./media/bamboo-tutorial/tutorial_bamboo_save.png)
    
> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/bamboo-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/bamboo-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/bamboo-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/bamboo-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Utwórz logowania jednokrotnego SAML dla Bambus przez użytkownika testowego GmbH rozwiązania

Celem tej sekcji jest utworzenie użytkownika wymaga Simona Britta w logowania jednokrotnego SAML Bambus rozpoznawania GmbH. Logowania jednokrotnego SAML Bambus przez rozpoznawania GmbH obsługę w czasie, a użytkownicy można także utworzyć ręcznie, skontaktuj się z [zespołu obsługi logowania jednokrotnego SAML dla Bambus przez rozpoznawania klienta GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) zgodnie z wymaganiami.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta logowania jednokrotnego SAML dla Bambus rozpoznawania GmbH, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH**.

    ![Logowania jednokrotnego SAML dla Bambus łączem GmbH rozpoznawania na liście aplikacji](./media/bamboo-tutorial/tutorial_bamboo_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Kliknięcie logowania jednokrotnego SAML dla Bambus rozpoznawania GmbH kafelka w panelu dostępu, możesz powinien pobrać automatycznie zalogowane do użytkownika logowania jednokrotnego SAML dla Bambus przez rozpoznawania GmbH aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bamboo-tutorial/tutorial_general_01.png
[2]: ./media/bamboo-tutorial/tutorial_general_02.png
[3]: ./media/bamboo-tutorial/tutorial_general_03.png
[4]: ./media/bamboo-tutorial/tutorial_general_04.png

[100]: ./media/bamboo-tutorial/tutorial_general_100.png

[200]: ./media/bamboo-tutorial/tutorial_general_200.png
[201]: ./media/bamboo-tutorial/tutorial_general_201.png
[202]: ./media/bamboo-tutorial/tutorial_general_202.png
[203]: ./media/bamboo-tutorial/tutorial_general_203.png

