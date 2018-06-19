---
title: 'Samouczek: Integracji Azure Active Directory z SAP HANA | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 65d474e78cf9a2a504b2e573c96f3cbf818bb017
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938686"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Samouczek: Integracji Azure Active Directory z SAP HANA

Z tego samouczka dowiesz się sposobu integracji z usługą Azure Active Directory (Azure AD) SAP HANA.

Po zintegrowaniu SAP HANA z usługą Azure AD można uzyskać następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do programu SAP HANA.
- Można umożliwić użytkownikom automatycznie pobrać zalogowaniem się do SAP HANA z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji--portalu Azure.

Aby uzyskać więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z SAP HANA, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SAP HANA subskrypcją rejestracji jednokrotnej (SSO) włączone
- Wystąpienie HANA, które działa na wszelkich publicznych IaaS lokalnymi, maszyny Wirtualnej platformy Azure lub SAP dużych wystąpień na platformie Azure
- Interfejs sieci web administracji XSA, jak również zainstalowana w wystąpieniu HANA Studio HANA

> [!NOTE]
> Nie zaleca się używanie środowiska produkcyjnego programu SAP HANA do testowania czynności w tym samouczku. Testowanie integracji najpierw w rozwoju lub środowisko przejściowe aplikacji, a następnie użyć środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- [Uzyskaj bezpłatną wersję próbną miesięcznego](https://azure.microsoft.com/pricing/free-trial/) usługi Azure AD, jeśli nie masz jeszcze środowisko wersji próbnej usługi Azure AD.

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisane w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie SAP HANA z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-sap-hana-from-the-gallery"></a>Dodaj SAP HANA z galerii
Aby skonfigurować integrację usługi Azure AD SAP HANA, należy dodać SAP HANA z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SAP HANA z galerii, należy wykonać następujące czynności:**

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **SAP HANA**. Następnie wybierz **SAP HANA** z panelu wyników. Na koniec wybierz **Dodaj** przycisk, aby dodać aplikację. 

    ![Nowa aplikacja](./media/saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SAP HANA oparte na koncie użytkownika testu o nazwie "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, komu użytkownika odpowiednika w SAP HANA jest użytkownikiem w usłudze Azure AD. Innymi słowy należy ustanowić powiązanie użytkownika usługi Azure AD i danemu użytkownikowi w SAP HANA.

W przypadku SAP HANA udzielić **Username** wartość taką samą wartość **nazwy użytkownika** w usłudze Azure AD. Ten krok pozwala ustanowić połączenie między dwóch użytkowników.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SAP HANA, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on) umożliwienie użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user) do testowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. [Tworzenie użytkownika testowego SAP HANA](#creating-a-sap-hana-test-user) w celu zapewnienia odpowiednikiem Simona Britta SAP HANA, połączonej z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user) umożliwiające Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. [Test rejestracji jednokrotnej](#testing-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji SAP HANA.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z SAP HANA, należy wykonać następujące czynności:**

1. W portalu Azure na **SAP HANA** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. W **logowanie jednokrotne** okna dialogowego, w obszarze **na języku SAML logowania jednokrotnego**, wybierz pozycję **tryb**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/saphana-tutorial/tutorial_saphana_samlbase.png)

3. W **adresy URL i SAP HANA domeny** sekcji, wykonaj następujące czynności:

    ![Domena i adres URL z jednym informacje logowania jednokrotnego](./media/saphana-tutorial/tutorial_saphana_url.png)

    a. W **identyfikator** pola, wpisz następujące polecenie: `HA100` 

    b. W **adres URL odpowiedzi** wpisz adres URL z następującego wzorca: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Zaktualizować te wartości z rzeczywistego identyfikatora i adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta SAP HANA](https://cloudplatform.sap.com/contact.html) uzyskać te wartości. 

4. W **certyfikat podpisywania SAML** zaznacz **XML metadanych**. Następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Jeśli certyfikat nie jest aktywne, następnie uaktywnij ją, wybierając **uaktywnić nowego świadectwa** pole wyboru w usłudze Azure AD. 

5. Aplikacja SAP HANA oczekuje potwierdzenia języka SAML w określonym formacie. Poniższy zrzut ekranu przedstawia przykład tego formatu. 

    W tym miejscu możemy zamapowany **identyfikator użytkownika** z **ExtractMailPrefix()** funkcji **user.mail**. Dzięki temu wartość prefiksu adres e-mail użytkownika, który jest identyfikator unikatowy użytkownika. Ten identyfikator użytkownika są wysyłane do aplikacji SAP HANA w każdym pomyślnym odpowiedzi.

    ![Konfigurowanie rejestracji jednokrotnej](./media/saphana-tutorial/attribute.png)

6. W **atrybuty użytkownika** sekcji **logowanie jednokrotne** okna dialogowego należy wykonać następujące czynności:

    a. W **identyfikator użytkownika** listy rozwijanej wybierz **ExtractMailPrefix**.
    
    b. W **poczty** listy rozwijanej wybierz **user.mail**.

7. Wybierz ikonę **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej przycisk zapisywania](./media/saphana-tutorial/tutorial_general_400.png)
    
8. Aby skonfigurować rejestrację jednokrotną po stronie SAP HANA, zaloguj się do Twojego **konsoli sieci Web XSA HANA** , przechodząc do odpowiednich punkt końcowy HTTPS.

    > [!NOTE]
    > W konfiguracji domyślnego adresu URL przekierowuje żądanie do rejestrowania ekranu, co wymaga poświadczeń uwierzytelnionego użytkownika bazy danych SAP HANA. Użytkownik, który loguje się musi mieć uprawnienia do wykonywania zadań administracyjnych SAML.

9. W interfejsie sieci Web XSA, przejdź do **dostawca tożsamości SAML**. Z tego miejsca, wybierz **+** przycisk w dolnej części ekranu, aby wyświetlić **dodać informacje dostawcy tożsamości** okienka. Następnie wykonaj następujące czynności:

    ![Dodaj dostawcę tożsamości](./media/saphana-tutorial/sap1.png)

    a. W **dodać informacje dostawcy tożsamości** okienku Wklej zawartość XML metadanych (który został pobrany z portalu Azure) do **metadanych** pole.

    ![Dodaj ustawienia dostawcy tożsamości](./media/saphana-tutorial/sap2.png)

    b. Jeśli zawartość dokumentu XML są prawidłowe, proces analizowania wyodrębnia informacje, które są wymagane dla **podmiotu, identyfikator jednostki i wystawcy** pól w **ogólne dane** obszar ekranu. Również wyodrębnia informacje, które są niezbędne dla pól adres URL w **docelowego** ekranu obszaru, na przykład **podstawowego adresu URL i adresu URL SingleSignOn (*)** pola.

    ![Dodaj ustawienia dostawcy tożsamości](./media/saphana-tutorial/sap3.png)

    c. W **nazwa** pole **danych ogólne** obszar ekranu, wprowadź nazwę dla nowego logowania jednokrotnego SAML dostawcy tożsamości.

    > [!NOTE]
    > Nazwa SAML IDP jest wymagana i musi być unikatowa. Wygląda na to, na liście dostępnych IDPs SAML wyświetlanym po wybraniu SAML jako metodę uwierzytelniania dla aplikacji SAP HANA XS do użycia. Na przykład, możesz to zrobić w **uwierzytelniania** obszar narzędzia do administrowania artefaktu XS ekranu.

10. Wybierz **zapisać** zapisać szczegóły dostawca tożsamości SAML oraz dodawania nowych IDP SAML do listy znanych IDPs SAML.

    ![Przyciskiem Zapisz](./media/saphana-tutorial/sap4.png)

11. W Studio HANA, w ramach właściwości systemu **konfiguracji** karcie, filtrowania ustawień przez **saml**. Następnie Dostosuj **assertion_timeout** z **10 s** do **120 s**.

    ![Ustawienie assertion_timeout](./media/saphana-tutorial/sap7.png)

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com) podczas konfigurowania aplikacji! Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** karcie i dostępu do osadzonego Dokumentacja za pośrednictwem **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzony w [usługi Azure AD osadzonych dokumentacji](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, należy wykonać następujące czynności:**

1. W **portalu Azure**, w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](./media/saphana-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**. Następnie wybierz **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/saphana-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części okna dialogowego.
 
    ![Przycisk Dodaj](./media/saphana-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego należy wykonać następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/saphana-tutorial/create_aaduser_04.png) 

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło**, a następnie Zapisz hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-sap-hana-test-user"></a>Tworzenie użytkownika testowego SAP HANA

Aby umożliwić użytkownikom usługi Azure AD do logowania się na SAP HANA, należy udostępnić je w SAP HANA.
SAP HANA obsługę w czasie, który jest przez domyślnie włączone.

Aby ręcznie utworzyć użytkownika, należy wykonać następujące kroki:

>[!NOTE]
>Możesz zmienić uwierzytelniania zewnętrznego, których użytkownik używa. Mogą one uwierzytelniać za pomocą systemu zewnętrznego, takie jak Kerberos. Aby uzyskać szczegółowe informacje o tożsamości zewnętrznych, skontaktuj się z [administrator domeny](https://cloudplatform.sap.com/contact.html).

1. Otwórz [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) jako administrator, a następnie włącz użytkownika bazy danych dla logowania jednokrotnego SAML.

    ![Utwórz użytkownika](./media/saphana-tutorial/sap5.png)

2. Zaznacz pole wyboru niewidoczne z lewej strony **SAML**, a następnie wybierz **Konfiguruj** łącza.

3. Wybierz **Dodaj** można dodać SAML IDP.  Wybierz odpowiednie IDP SAML, a następnie wybierz **OK**.

4. Dodaj **tożsamości zewnętrznych** (w tym przypadku BrittaSimon) lub wybierz **żadnych**. Następnie wybierz przycisk **OK**.

    >[!Note]
    >Jeśli **żadnych** nie zaznaczono pola wyboru, a następnie nazwę użytkownika w HANA musi dokładnie odpowiadać nazwie użytkownika w głównej nazwy użytkownika przed sufiksem domeny. (Na przykład BrittaSimon@contoso.com staje się BrittaSimon w HANA.)

5. Do celów testowych, przypisać wszystkie **XS** ról do użytkownika.

    ![Przypisywanie ról](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Należy nadać uprawnienia, które są odpowiednie dla Twojej zastosowań.

6. Zapisz użytkownika.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do programu SAP HANA.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta SAP HANA, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji. Przejdź do widoku katalogu, a następnie przejdź do **aplikacje dla przedsiębiorstw**. Wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **SAP HANA**.

    ![Przypisz użytkownika](./media/saphana-tutorial/tutorial_saphana_app.png) 

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202] 

4. Wybierz **Dodaj** przycisku. W **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** w **użytkowników** listy.

6. Kliknij przycisk **wybierz** przycisk **użytkowników i grup** okno dialogowe.

7. Wybierz **przypisać** przycisk **Dodaj przydziału** okno dialogowe.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu kafelka SAP HANA w panelu dostępu użytkownik powinien pobrać automatycznym zalogowaniem się do aplikacji SAP HANA.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saphana-tutorial/tutorial_general_01.png
[2]: ./media/saphana-tutorial/tutorial_general_02.png
[3]: ./media/saphana-tutorial/tutorial_general_03.png
[4]: ./media/saphana-tutorial/tutorial_general_04.png

[100]: ./media/saphana-tutorial/tutorial_general_100.png

[200]: ./media/saphana-tutorial/tutorial_general_200.png
[201]: ./media/saphana-tutorial/tutorial_general_201.png
[202]: ./media/saphana-tutorial/tutorial_general_202.png
[203]: ./media/saphana-tutorial/tutorial_general_203.png

