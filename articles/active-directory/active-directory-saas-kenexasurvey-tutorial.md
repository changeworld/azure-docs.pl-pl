---
title: 'Samouczek: Azure Active Directory integrację ze IBM Kenexa ankiety | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i IBM Kenexa ankiety przedsiębiorstwa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 88e0072adeeebaf6c9e964db28a45f6fe038fddf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342182"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Samouczek: Azure Active Directory integrację ze IBM Kenexa ankiety

Z tego samouczka dowiesz integrowanie IBM Kenexa ankiety przedsiębiorstwa z usługi Azure Active Directory (Azure AD).

Integrowanie IBM Kenexa ankiety przedsiębiorstwa z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do IBM Kenexa ankiety przedsiębiorstwa.
- Umożliwia użytkownikom automatycznie logować się do IBM Kenexa ankiety przedsiębiorstwa przy użyciu rejestracji jednokrotnej (SSO) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji: portalu Azure.

Jeśli chcesz dowiedzieć się więcej na temat oprogramowania jako usługa (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z IBM Kenexa ankiety przedsiębiorstwa, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Włączone IBM Kenexa ankiety Enterprise SSO subskrypcji

> [!NOTE]
> Podczas testowania czynności w tym samouczku, firma Microsoft zaleca, nie używaj do środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować logowania jednokrotnego programu Azure AD w środowisku testowym. Scenariusz opisane w samouczku składa się z dwóch głównych elementów:

* Dodawanie IBM Kenexa ankiety przedsiębiorstwa z galerii
* Konfigurowanie i testowania logowania jednokrotnego programu Azure AD

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Dodaj IBM Kenexa ankiety przedsiębiorstwa z galerii
Aby skonfigurować integrację usługi Azure AD IBM Kenexa ankiety przedsiębiorstwa, należy dodać IBM Kenexa ankiety przedsiębiorstwa z galerii do listy zarządzanych aplikacji SaaS.

Aby dodać IBM Kenexa ankiety przedsiębiorstwa z galerii, wykonaj następujące czynności:

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku. 

    ![Przycisk usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać aplikację, kliknij przycisk **nowej aplikacji** przycisku.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **IBM Kenexa ankiety Enterprise**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. Na liście wyników wybierz **IBM Kenexa ankiety Enterprise**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![IBM Kenexa ankiety przedsiębiorstwa na liście wyników](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji Konfiguracja i testowanie logowania jednokrotnego AD Azure z przedsiębiorstwem ankiety Kenexa IBM oparte na koncie użytkownika testu o nazwie "Britta Simona".

Dla logowania jednokrotnego do pracy usługi Azure AD musi zidentyfikować odpowiednikiem użytkownika IBM Kenexa ankiety przedsiębiorstwa w usłudze Azure AD. Innymi słowy usługi Azure AD ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w przedsiębiorstwie ankiety Kenexa IBM.

Do ustanawiania relacji łącza, należy przypisać wartość **nazwy użytkownika** w przedsiębiorstwie ankiety Kenexa IBM jako wartość **Username** w usłudze Azure AD.

Aby skonfigurować i przetestować logowania jednokrotnego AD Azure z przedsiębiorstwem ankiety Kenexa IBM, wykonaj bloków konstrukcyjnych w dwóch następnych sekcjach.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego.

W tej sekcji włączenia funkcji logowania jednokrotnego usługi Azure AD w portalu Azure i konfigurowanie logowania jednokrotnego w aplikacji IBM Kenexa ankiety przedsiębiorstwa w następujący sposób:

1. W portalu Azure na **IBM Kenexa ankiety Enterprise** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![IBM Kenexa ankiety Enterprise Konfigurowanie logowania jednokrotnego łącza][4]

2. W **logowanie jednokrotne** okna dialogowego, **tryb** wybierz opcję **na języku SAML logowania jednokrotnego** do włączenia funkcji logowania jednokrotnego.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. W **IBM Kenexa ankiety Enterprise domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![IBM Kenexa ankiety Enterprise domeny i adres URL z jednym informacje logowania jednokrotnego](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL z następującego wzorca: `https://surveys.kenexa.com/<companycode>`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL z następującego wzorca: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > Poprzednie wartości nie są prawdziwe. Zaktualizuj je z rzeczywistego identyfikatora i adres URL odpowiedzi. Aby uzyskać rzeczywiste wartości, skontaktuj się z [zespołem pomocy technicznej Enterprise ankiety Kenexa IBM](https://www.ibm.com/support/home/?lnk=fcw).

4. W obszarze **certyfikat podpisywania SAML**, kliknij przycisk **certyfikatu (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Łącze pobierania certyfikatu (Base64)](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    IBM Kenexa ankiety aplikacją oczekuje do odbierania potwierdzeń zabezpieczeń potwierdzenia Markup Language (SAML) w określonym formacie, musisz dodać mapowania atrybutów niestandardowych do konfiguracji z atrybutów tokenu SAML. Wartość oświadczenia identyfikatora użytkownika w odpowiedzi musi być zgodna z Identyfikatorem logowania jednokrotnego, który jest skonfigurowany w systemie Kenexa. Aby mapować identyfikator odpowiedniego użytkownika w organizacji jako SSO Internet Datagram Protocol (IDP), pracy z [zespołem pomocy technicznej Enterprise ankiety Kenexa IBM](https://www.ibm.com/support/home/?lnk=fcw). 

    Domyślnie program Azure AD Ustawia identyfikator użytkownika jako wartość głównej nazwy (UPN) użytkownika. Tę wartość można zmienić na **atrybutu** karcie, jak pokazano na poniższym zrzucie ekranu. Integracja działa tylko po zakończeniu mapowanie poprawnie.
    
    ![Okno dialogowe atrybuty użytkownika](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_attribute.png)   

5. Kliknij pozycję **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej przycisk zapisywania](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_400.png)

6. Aby otworzyć **Konfigurowanie logowania jednokrotnego** okna, w obszarze **konfiguracja dla przedsiębiorstw ankiety Kenexa IBM**, kliknij przycisk **skonfigurować IBM Kenexa ankiety Enterprise**. 
 
    ![Łącze skonfigurować IBM Kenexa ankiety Enterprise](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. Kopiuj **Sign-Out adres URL**, **SAML identyfikator jednostki**, i **SAML pojedynczy znak na adres URL usługi** wartości z **krótkimi opisami** sekcji.

8. W **Konfigurowanie logowania jednokrotnego** okna, w obszarze **krótkimi opisami**, kopiowania **Sign-Out adres URL**, **identyfikator jednostki SAML**, i **SAML pojedynczy znak na adres URL usługi** wartości.

9. Do konfigurowania rejestracji Jednokrotnej w **IBM Kenexa ankiety Enterprise** po stronie, Wyślij pobrany **certyfikatu (Base64)**, **Sign-Out adres URL**, **SAML identyfikator jednostki**, i **SAML pojedynczy znak na adres URL usługi** wartości do [zespołem pomocy technicznej Enterprise ankiety Kenexa IBM](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Można odwołać się do wersji krótkie instrukcje zawarte w [portalu Azure](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** po prostu kliknij **logowanie jednokrotne** karcie, a następnie dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji na końcu. Aby dowiedzieć się więcej o funkcji osadzonych dokumentacji, zobacz [usługi Azure AD osadzonych dokumentacji](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji możesz tworzyć użytkownika testowego Simona Britta w portalu Azure w następujący sposób:

![Tworzenie użytkownika testowego usługi Azure AD][100]

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.
 
    ![Przycisk Dodaj](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Tworzenie użytkownika testowego IBM Kenexa ankiety Enterprise

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w przedsiębiorstwie ankiety Kenexa IBM. 

Aby utworzyć użytkowników systemu IBM Kenexa ankiety przedsiębiorstwa i mapowanie Identyfikatora logowania jednokrotnego dla nich, można pracować z [zespołem pomocy technicznej Enterprise ankiety Kenexa IBM](https://www.ibm.com/support/home/?lnk=fcw). Ta wartość Identyfikatora logowania jednokrotnego należy również mapować na wartość identyfikatora użytkownika z usługi Azure AD. To ustawienie domyślne można zmienić na **atrybutu** kartę.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do IBM Kenexa ankiety przedsiębiorstwa.

![Przypisanie roli użytkownika][200] 

Aby przypisać użytkownika Simona Britta IBM Kenexa ankiety przedsiębiorstwa, wykonaj następujące czynności:

1. W portalu Azure Otwórz **aplikacji** widok, przejdź do **katalogu** widok, wybierz opcję **aplikacje przedsiębiorstwa**, a następnie kliknij przycisk **wszystkie aplikacje**.

    !["Aplikacje przedsiębiorstwa" i "Wszystkie aplikacje" łącza][201] 

2. W **aplikacji** listy, wybierz **IBM Kenexa ankiety Enterprise**.

    ![Łącze IBM Kenexa ankiety przedsiębiorstwa na liście aplikacji](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. W okienku po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202] 

4. Kliknij przycisk **Dodaj** przycisk, a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okna dialogowego, **użytkowników** listy, wybierz **Simona Britta**.

6. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku.

7. W **Dodaj przydziału** okno dialogowe, kliknij przycisk **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfigurację programu Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu **IBM Kenexa ankiety Enterprise** kafelka w panelu dostępu należy powinien można automatycznie zalogowany do aplikacji IBM Kenexa ankiety przedsiębiorstwa.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png

 
