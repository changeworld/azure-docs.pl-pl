---
title: 'Samouczek: Inicjowanie obsługi administracyjnej dla pingboard - Azure AD'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników do pingboardu.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c64c40d93f9b525ac6adeca276797df65f32ef3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061279"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie pingboard do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroków, które należy wykonać, aby włączyć automatyczne inicjowanie obsługi administracyjnej i usuwanie obsługi administracyjnej kont użytkowników z usługi Azure Active Directory (Azure AD) do pingboard.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

* Dzierżawa usługi Azure AD
* [Konto](https://pingboard.com/pricing) pro-najemcy pingboardu
* Konto użytkownika w pingboardzie z uprawnieniami administratora

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD zależy od [interfejsu API pingboard](https://pingboard.docs.apiary.io/#), który jest dostępny dla Twojego konta.

## <a name="assign-users-to-pingboard"></a>Przypisywanie użytkowników do pingboardu

Usługa Azure AD używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy przypisani do aplikacji w usłudze Azure AD są synchronizowane. 

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy w usłudze Azure AD potrzebują dostępu do aplikacji pingboard. Następnie możesz przypisać tych użytkowników do aplikacji Pingboard, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Ważne wskazówki dotyczące przypisywania użytkowników do pingboardu

Zaleca się przypisanie jednego użytkownika usługi Azure AD do pingboard, aby przetestować konfigurację inicjowania obsługi administracyjnej. Dodatkowych użytkowników można przypisać później.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurowanie inicjowania obsługi administracyjnej użytkownika do pingboard 

W tej sekcji przewodnik po połączeniu usługi Azure AD z interfejsem API inicjowania obsługi administracyjnej konta użytkownika pingboard. Usługę inicjowania obsługi administracyjnej można również skonfigurować w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w pingboardzie opartych na przypisaniach użytkowników w usłudze Azure AD.

> [!TIP]
> Aby włączyć logowanie jednokrotne oparte na saml dla pingboard, postępuj zgodnie z instrukcjami podanymi w [witrynie Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Aby skonfigurować automatyczne udostępnianie obsługi administracyjnej konta użytkownika do pingboardu w usłudze Azure AD

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji Wszystkie**aplikacje** **usługi Azure Active Directory** > **Enterprise Apps.** > 

1. Jeśli pingboard został już skonfigurowany do logowania jednokrotnego, wyszukaj wystąpienie pingboardu za pomocą pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **pingboard** w galerii aplikacji. Wybierz **pingboard** z wyników wyszukiwania i dodaj go do listy aplikacji.

1. Wybierz wystąpienie pingboard, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

1. Ustaw **tryb inicjowania obsługi administracyjnej** na **automatyczny**.

    ![Inicjowanie obsługi administracyjnej pingboard](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. W sekcji **Poświadczenia administratora** należy wykonać następujące czynności:

    a. W **adresie URL dzierżawy**wpisz `https://your_domain.pingboard.com/scim/v2`i zastąp "your_domain" swoją prawdziwą domeną.

    b. Zaloguj się do [pingboardu](https://pingboard.com/) przy użyciu konta administratora.

    d. Wybierz **pozycję Integracje dodatków** > **Integrations** > **usługi Azure Active Directory**.

    d. Przejdź do karty **Konfigurowanie** i wybierz pozycję **Włącz inicjowanie obsługi administracyjnej użytkowników z platformy Azure**.

    e. Skopiuj token w **tokenie na okaziciela OAuth**i wprowadź go w **tajnym tokenie.**

1. W witrynie Azure portal wybierz **opcję Testuj połączenie,** aby przetestować usługę Azure AD, która może połączyć się z aplikacją Pingboard. Jeśli połączenie nie powiedzie się, sprawdź, czy twoje konto pingboard ma uprawnienia administratora, i spróbuj ponownie wykonać krok **Testuj połączenie.**

1. Wprowadź adres e-mail osoby lub grupy, które mają być wyświetlane w wiadomości **e-mail z powiadomieniem.** Zaznacz pole wyboru pod spodem.

1. Wybierz **pozycję Zapisz**.

1. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z pingboardem**.

1. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które mają być synchronizowane z usługi Azure AD do pingboard. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w pingboard do operacji aktualizacji. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany. Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowania atrybutów inicjowania obsługi administracyjnej użytkowników](../app-provisioning/customize-application-attributes.md).

1. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla pingboard, w sekcji **Ustawienia** zmień **stan inicjowania obsługi administracyjnej** **na Włączone**.

1. Wybierz **pozycję Zapisz,** aby rozpocząć początkową synchronizację użytkowników przypisanych do pingboardu.

Synchronizacja początkowa trwa dłużej niż następujące synchronizacji, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Użyj sekcji **Szczegóły synchronizacji,** aby monitorować postęp i śledzić łącza do dzienników działań inicjowania obsługi administracyjnej. Dzienniki opisują wszystkie akcje podjęte przez usługę inicjowania obsługi administracyjnej w aplikacji Pingboard.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raport dotyczący automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](pingboard-tutorial.md)
