---
title: 'Samouczek: Konfigurowanie piaskownicy Salesforce do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory| Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a piaskownicą salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48df954f680c6774b0aa189453615156f4f0b4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063275"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie piaskownicy Salesforce do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroków, które należy wykonać w obszarze sandbox salesforce i usługi Azure AD, aby automatycznie aprowizować i usuwać z nich konta użytkowników z usługi Ad platformy Azure do piaskownicy Salesforce.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

*   Dzierżawa usługi Azure Active directory.
*   Prawidłowa dzierżawa piaskownicy Salesforce dla work lub Salesforce Sandbox for Education. Możesz skorzystać z bezpłatnego konta próbnego dla każdej z tych usług.
*   Konto użytkownika w piaskownicy Salesforce z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-salesforce-sandbox"></a>Przypisywanie użytkowników do piaskownicy Salesforce

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do aplikacji izolak Salesforce. Po wykonaniu tej decyzji możesz przypisać tych użytkowników do aplikacji Salesforce Sandbox, postępując zgodnie z instrukcjami w [aplikacji Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Ważne wskazówki dotyczące przypisywania użytkowników do piaskownicy Salesforce

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do izolowania Salesforce do testowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do piaskownicy Salesforce należy wybrać prawidłową rolę użytkownika. Rola "Dostęp domyślny" nie działa w przypadku inicjowania obsługi administracyjnej.

> [!NOTE]
> Ta aplikacja importuje role niestandardowe z piaskownicy Salesforce w ramach procesu inicjowania obsługi administracyjnej, który klient może chcieć wybrać podczas przypisywania użytkowników.

## <a name="enable-automated-user-provisioning"></a>Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tej sekcji można zainicjować połączenie usługi Azure AD z interfejsem API obsługi administracyjnej konta użytkownika salesforce i skonfigurowanie usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w obszarze izolowanym Salesforce na podstawie użytkownika i grupy w usłudze Azure AD.

>[!Tip]
>Można również włączyć logowanie jednokrotne oparte na SAML dla piaskownicy Salesforce, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika

Celem tej sekcji jest określenie sposobu włączania inicjowania obsługi administracyjnej kont użytkowników usługi Active Directory do piaskownicy Salesforce.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

1. Jeśli urządzenie izolowane Salesforce zostało już skonfigurowane do logowania jednokrotnego, wyszukaj wystąpienie pola izolowanego Salesforce za pomocą pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **piaskownicę Salesforce** w galerii aplikacji. Wybierz obszar izolowania Salesforce z wyników wyszukiwania i dodaj go do listy aplikacji.

1. Wybierz wystąpienie izolowania Salesforce, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

1. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Inicjowania obsługi](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. W sekcji **Poświadczenia administratora** podaj następujące ustawienia konfiguracji:
   
    a. W polach tekstowych **Nazwa administratora** wpisz nazwę konta izolowania Salesforce, do których przypisano profil **Administratora systemu** w Salesforce.com.
   
    b. W polach tekstowych **Hasło administratora** wpisz hasło dla tego konta.

1. Aby uzyskać token zabezpieczający Salesforce Sandbox, otwórz nową kartę i zaloguj się na to samo konto administratora Salesforce Sandbox. W prawym górnym rogu strony kliknij swoje imię i nazwisko, a następnie kliknij pozycję **Ustawienia**.

     ![Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników")

1. W lewym okienku nawigacji kliknij pozycję **Moje dane osobowe,** aby rozwinąć powiązaną sekcję, a następnie kliknij pozycję **Resetuj mój token zabezpieczający**.
  
    ![Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników")

1. Na stronie **Resetowanie tokenu zabezpieczającego** kliknij przycisk **Resetuj token zabezpieczający.**

    ![Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników")

1. Sprawdź skrzynkę odbiorczą poczty e-mail skojarzoną z tym kontem administratora. Poszukaj wiadomości e-mail od salesforce Sandbox.com zawierającej nowy token zabezpieczający.

1. Skopiuj token, przejdź do okna usługi Azure AD i wklej go do pola **Tajny token.**

1. W witrynie Azure portal kliknij pozycję **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją izolowatą Salesforce.

1. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru.

1. Kliknij **pozycję Zapisz.**  
    
1.  W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z piaskownicą salesforce.**

1. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do piaskownicy salesforce. Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w piaskownicy Salesforce do operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

1. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla piaskownicy Salesforce, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji Ustawienia

1. Kliknij **pozycję Zapisz.**

Rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do piaskownicy Salesforce w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do dzienników działań inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji Salesforce Sandbox.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
