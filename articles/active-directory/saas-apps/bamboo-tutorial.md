---
title: 'Samouczek: Azure integracji usługi Active Directory za pomocą logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5353cc921d4fc07770737bb70d02361fa0e5f438
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198443"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Samouczek: Usługa Azure integracji usługi Active Directory za pomocą logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH

W tym samouczku dowiesz się, jak zintegrować logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH w usłudze Azure Active Directory (Azure AD).

Integracja logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego SAML Bambus przez rozwiązania GmbH.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integracji z usługą Azure AD za pomocą logowania jednokrotnego SAML dla Bambus przy rozdzielczości GmbH, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Logowanie Jednokrotne SAML do Bambus przez rozwiązania logowania jednokrotnego GmbH włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie logowania jednokrotnego SAML dla Bambus przy rozdzielczości GmbH z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Dodawanie logowania jednokrotnego SAML dla Bambus przy rozdzielczości GmbH z galerii
Aby skonfigurować integrację logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH w usłudze Azure AD, należy dodać logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać logowania jednokrotnego SAML dla Bambus za rozdzielczość GmbH z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH**, wybierz opcję **logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![Logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH na liście wyników](./media/bamboo-tutorial/tutorial_bamboo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML dla Bambus według rozdzielczości, GmbH, zależnie od użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, jakie odpowiednika użytkownika w logowania jednokrotnego SAML dla Bambus według rozdzielczości GmbH jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkownika w logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH musi zostać ustanowione.

W logowania jednokrotnego SAML Bambus przez rozwiązania GmbH, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Utwórz logowanie Jednokrotne SAML do Bambus przez użytkownika testowego GmbH rozpoznawania](#create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user)**  — aby odpowiednikiem Britta Simon w logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal, a podczas konfigurowania logowania jednokrotnego w usługi logowania jednokrotnego SAML dla Bambus rozpoznawania GmbH aplikacji.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH, wykonaj następujące czynności:**

1. W witrynie Azure portal na **logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/bamboo-tutorial/tutorial_bamboo_samlbase.png)

1. Na **logowania jednokrotnego SAML Bambus przez rozwiązania GmbH domen i adresów URL** sekcji, jeśli chcesz skonfigurować aplikację w trybie zainicjował dostawcy tożsamości należy wykonać następujące czynności:

    ![Adresy URL i logowania jednokrotnego SAML dla Bambus przez rozpoznawanie domeny GmbH pojedynczy informacje logowania jednokrotnego](./media/bamboo-tutorial/tutorial_bamboo_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Adresy URL i logowania jednokrotnego SAML dla Bambus przez rozpoznawanie domeny GmbH pojedynczy informacje logowania jednokrotnego](./media/bamboo-tutorial/tutorial_bamboo_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołu pomocy technicznej logowania jednokrotnego SAML dla Bambus przez rozwiązania klienta GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/bamboo-tutorial/tutorial_bamboo_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/bamboo-tutorial/tutorial_general_400.png)

1. Zaloguj się do usługi logowania jednokrotnego SAML dla Bambus przez witrynę firmy GmbH rozpoznawania jako administrator.

1. Po prawej stronie głównego paska narzędzi, kliknij przycisk **ustawienia** > **dodatki**.

    ![Ustawienia](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Przejdź do sekcji Zabezpieczenia, kliknij pozycję **SAML SingleSignOn** na pasku menu.

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Na stronie **konfiguracji wtyczki SIngleSignOn języka SAML** kliknij pozycję **Dodaj dostawcę tożsamości**. 

    ![Dodawanie dostawcy tożsamości](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Na stronie **wybierania dostawcy tożsamości języka SAML** wykonaj następujące kroki:

    ![Dostawca tożsamości](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. W obszarze **Typ dostawcy tożsamości** ustaw wartość **AZURE AD**.

    b. W polu tekstowym **Nazwa** wpisz nazwę.

    c. W polu tekstowym **Opis** wpisz opis.

    d. Kliknij przycisk **Dalej**.

1. Na **konfiguracji dostawcy tożsamości** kliknij **dalej**.

    ![Konfiguracja tożsamości](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1.  Na **Importuj metadane dostawcy tożsamości SAML** kliknij **Załaduj plik** do przekazania **XML METADANYCH** pliku, który został pobrany z witryny Azure Portal.

    ![Idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Kliknij przycisk **Dalej**.

1. Kliknij pozycję **Zapisz ustawienia**.

    ![Zapisywanie](./media/bamboo-tutorial/tutorial_bamboo_save.png)
    
> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/bamboo-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/bamboo-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/bamboo-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/bamboo-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Utwórz logowanie Jednokrotne SAML do Bambus przez użytkownika testowego GmbH rozwiązania

Celem tej sekcji jest utworzenie użytkownika wymagane Britta Simon w logowania jednokrotnego SAML Bambus przez rozwiązania GmbH. Logowania jednokrotnego SAML Bambus przez rozwiązania GmbH obsługę just-in-time, a użytkownicy mogą być tworzone ręcznie, skontaktuj się z pomocą [zespołu pomocy technicznej logowania jednokrotnego SAML dla Bambus przez rozwiązania klienta GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) zgodnie z wymaganiami.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon logowania jednokrotnego SAML dla Bambus rozpoznawania GmbH, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH**.

    ![Logowania jednokrotnego SAML dla Bambus przez łącze GmbH rozwiązania na liście aplikacji](./media/bamboo-tutorial/tutorial_bamboo_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Kliknięcie logowania jednokrotnego SAML dla Bambus rozpoznawania GmbH kafelka w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do usługi logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

