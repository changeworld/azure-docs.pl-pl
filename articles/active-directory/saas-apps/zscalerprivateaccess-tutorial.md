---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą rozwiązania Zscaler prywatny dostęp (ZPA) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i rozwiązania Zscaler prywatny dostęp (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b6471b6bd634c7fe3053fc7748eb925c049c17c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179570"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą rozwiązania Zscaler prywatny dostęp (ZPA)

W tym samouczku dowiesz się, jak zintegrować rozwiązania Zscaler prywatny dostęp (ZPA) z usługą Azure Active Directory (Azure AD).

Integracja rozwiązania Zscaler prywatny dostęp (ZPA) z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do rozwiązania Zscaler prywatny dostęp (ZPA)
- Użytkowników, aby automatycznie uzyskać zalogowanych do rozwiązania Zscaler prywatny dostęp (ZPA) (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — portalu zarządzania platformy Azure

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą rozwiązania Zscaler prywatny dostęp (ZPA), potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Rozwiązania Zscaler prywatny dostęp (ZPA) logowania jednokrotnego włączonych subskrypcji


> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.


Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie rozwiązania Zscaler prywatny dostęp (ZPA) z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Dodawanie rozwiązania Zscaler prywatny dostęp (ZPA) z galerii
Aby skonfigurować integrację z rozwiązania Zscaler prywatny dostęp (ZPA) w usłudze Azure AD, należy dodać rozwiązania Zscaler prywatny dostęp (ZPA) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać rozwiązania Zscaler prywatny dostęp (ZPA) z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania systemu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **rozwiązania Zscaler prywatny dostęp (ZPA)**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

1. W panelu wyników wybierz **rozwiązania Zscaler prywatny dostęp (ZPA)**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowania, w usłudze Azure AD logowania jednokrotnego, za pomocą rozwiązania Zscaler prywatny dostęp (ZPA) w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w rozwiązania Zscaler prywatny dostęp (ZPA) dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w rozwiązania Zscaler prywatny dostęp (ZPA) musi zostać ustanowione.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w rozwiązania Zscaler prywatny dostęp (ZPA).

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler prywatny dostęp (ZPA), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **Tworzenie użytkownika testowego rozwiązania Zscaler prywatny dostęp (ZPA)** — aby mieć w rozwiązania Zscaler prywatny dostęp (ZPA) połączonego z usługi Azure AD reprezentacja jej odpowiednikiem Britta Simon.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowanie jednokrotne w portalu zarządzania platformy Azure i konfigurowanie logowania jednokrotnego w aplikacji rozwiązania Zscaler prywatny dostęp (ZPA).

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler prywatny dostęp (ZPA), wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure na **rozwiązania Zscaler prywatny dostęp (ZPA)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.
 
    ![Konfigurowanie logowania jednokrotnego](./media/zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
1. Na **rozwiązania Zscaler prywatny dostęp (ZPA), domena i adresy URL** sekcji, wykonaj następujące czynności:
    
    ![Konfigurowanie logowania jednokrotnego](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. W **na adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b. W **identyfikator** pole tekstowe, wpisz: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE] 
    > Należy pamiętać, że nie są to rzeczywiste wartości. Musisz zaktualizować te wartości z rzeczywistych na adres URL logowania i identyfikator. W tym miejscu zalecamy przy użyciu unikatowej wartości adresu URL w identyfikatorze. Skontaktuj się z pomocą [zespołem pomocy technicznej rozwiązania Zscaler prywatny dostęp (ZPA)](https://help.zscaler.com/zpa-submit-ticket) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **Utwórz nowy certyfikat**.

    ![Konfigurowanie logowania jednokrotnego](./media/zscalerprivateaccess-tutorial/tutorial_general_400.png)     

1. Na **Utwórz nowy certyfikat** okno dialogowe, kliknij ikonę kalendarza, a następnie wybierz pozycję **datę wygaśnięcia**. Następnie kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/zscalerprivateaccess-tutorial/tutorial_general_500.png)

1. Na **certyfikat podpisywania SAML** zaznacz **Ustaw nowy certyfikat jako aktywny** i kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

1. W oknie podręcznym **certyfikat przerzucania** okna, kliknij przycisk **OK**.

    ![Konfigurowanie logowania jednokrotnego](./media/zscalerprivateaccess-tutorial/tutorial_general_600.png)

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy rozwiązania Zscaler prywatny dostęp (ZPA), jako administrator.

1. Przejdź do **administratora** a następnie kliknij przycisk **konfigurację dostawcy tożsamości**.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

1. W **konfigurację dostawcy tożsamości** kliknij **Dodawanie nowej konfiguracji dostawcy tożsamości**.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

1. W **nowej konfiguracji dostawcy tożsamości** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Kliknij przycisk **wybierz plik** i przekazywanie pliku pobranego metadanych.

    b. Kliknij przycisk **Save** (Zapisz).
    


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania platformy Azure o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscalerprivateaccess-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscalerprivateaccess-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscalerprivateaccess-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**. 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Tworzenie użytkownika testowego rozwiązania Zscaler prywatny dostęp (ZPA)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w rozwiązania Zscaler prywatny dostęp (ZPA). Skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler prywatny dostęp (ZPA)](https://help.zscaler.com/zpa-submit-ticket) Aby dodać użytkowników do platformy rozwiązania Zscaler prywatny dostęp (ZPA).


### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za jej udostępnienie do rozwiązania Zscaler prywatny dostęp (ZPA).

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon do rozwiązania Zscaler prywatny dostęp (ZPA), wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure powoduje ono otwarcie widoku aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **rozwiązania Zscaler prywatny dostęp (ZPA)**.

    ![Konfigurowanie logowania jednokrotnego](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    


### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka rozwiązania Zscaler prywatny dostęp (ZPA) w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji rozwiązania Zscaler prywatny dostęp (ZPA).


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccess-tutorial/tutorial_general_203.png
