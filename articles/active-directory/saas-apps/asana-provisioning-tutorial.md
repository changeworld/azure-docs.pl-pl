---
title: 'Samouczek: Inicjowanie obsługi administracyjnej dla asany — usługa Azure AD'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników asany.
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
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: abeac030db419f7fb7d561df5dcd407684f20ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058935"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie asany do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroków, które należy wykonać w asana i usługi Azure Active Directory (Azure AD) do automatycznego aprowizowania i usuwania obsługi administracyjnej kont użytkowników z usługi Azure AD do Asana.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

* Dzierżawa usługi Azure AD
* Dzierżawca Asana z planem [Enterprise](https://www.asana.com/pricing) lub lepiej włączona
* Konto użytkownika w Asanie z uprawnieniami administratora

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD zależy od [interfejsu API Asana](https://asana.com/developers/api-reference/users), który jest dostępny dla asany.

## <a name="assign-users-to-asana"></a>Przypisywanie użytkowników do Asany

Usługa Azure AD używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy przypisani do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy w usłudze Azure AD potrzebują dostępu do aplikacji Asana. Następnie możesz przypisać tych użytkowników do aplikacji Asana, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Asany

Zaleca się przypisanie jednego użytkownika usługi Azure AD do asany, aby przetestować konfigurację inicjowania obsługi administracyjnej. Dodatkowych użytkowników można przypisać później.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurowanie inicjowania obsługi administracyjnej użytkownika w asanie

W tej sekcji przewodnik po połączeniu usługi Azure AD z interfejsem API obsługi administracyjnej konta użytkownika usługi Asana. Usługę inicjowania obsługi administracyjnej można również skonfigurować w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w asanie na podstawie przypisań użytkowników w usłudze Azure AD.

> [!TIP]
> Aby włączyć logowanie jednokrotne oparte na saml dla asany, postępuj zgodnie z instrukcjami podanymi w [witrynie Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Aby skonfigurować automatyczne udostępnianie administracyjne konta użytkownika asanie w usłudze Azure AD

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji Wszystkie**aplikacje** **usługi Azure Active Directory** > **Enterprise Apps.** > 

1. Jeśli asana została już skonfigurowana do logowania jednokrotnego, wyszukaj wystąpienie Asany za pomocą pola wyszukiwania. W przeciwnym razie wybierz **pozycję Dodaj** i wyszukaj **Asanę** w galerii aplikacji. Wybierz **Asana** z wyników wyszukiwania i dodaj ją do listy aplikacji.

1. Wybierz wystąpienie asany, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

1. Ustaw **tryb inicjowania obsługi administracyjnej** na **automatyczny**.

    ![Inicjowanie obsługi administracyjnej asany](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. W sekcji **Poświadczenia administratora postępuj** zgodnie z tymi instrukcjami, aby wygenerować token i wprowadzić go w **tokenie tajnym:**

    a. Zaloguj się do [Asany,](https://app.asana.com) używając swojego konta administratora.

    b. Wybierz zdjęcie profilowe z górnego paska i wybierz bieżące ustawienia nazwy organizacji.

    d. Przejdź do karty **Konta usług.**

    d. Wybierz **pozycję Dodaj konto usługi**.

    e. W razie potrzeby zaktualizuj **nazwę** i **informacje** oraz zdjęcie profilowe. Skopiuj token w **tokenie**i wybierz go w **zapisz zmiany**.

1. W witrynie Azure portal wybierz pozycję **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją Asana. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Asana ma uprawnienia administratora, i spróbuj ponownie wykonać krok **Testuj połączenie.**

1. Wprowadź adres e-mail osoby lub grupy, które mają być wyświetlane w wiadomości **e-mail z powiadomieniem.** Zaznacz pole wyboru pod spodem.

1. Wybierz **pozycję Zapisz**.

1. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z asaną**.

1. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które mają być synchronizowane z usługi Azure AD do Asana. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Asana do operacji aktualizacji. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany. Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowań atrybutów aprowidywu użytkownika](../app-provisioning/customize-application-attributes.md).

1. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla asany, w sekcji **Ustawienia** zmień **stan inicjowania obsługi administracyjnej** **na Włączone**.

1. Wybierz **pozycję Zapisz**.

Teraz rozpoczyna się początkowa synchronizacja dla wszystkich użytkowników przypisanych do Asany w sekcji **Użytkownicy.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Użyj sekcji **Szczegóły synchronizacji,** aby monitorować postęp i śledzić łącza do dzienników działań inicjowania obsługi administracyjnej. Dzienniki inspekcji opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji Asana.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raport dotyczący automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](asana-tutorial.md)
