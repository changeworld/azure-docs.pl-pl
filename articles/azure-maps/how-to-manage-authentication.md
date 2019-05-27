---
title: Zarządzaj uwierzytelnianiem w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Można użyć witryny Azure portal, aby zarządzać uwierzytelnianiem w usługi Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 617adbcda70799aa07248945bbc27f9d95aa77a3
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952572"
---
# <a name="manage-authentication-in-azure-maps"></a>Zarządzaj uwierzytelnianiem w usługi Azure Maps

Po utworzeniu konta usługi Azure Maps, identyfikator klienta i klucze są tworzone do obsługi usługi Azure Active Directory (Azure AD) lub uwierzytelniania klucza wspólnego.

## <a name="view-authentication-details"></a>Wyświetl szczegóły uwierzytelniania

Informacje dotyczące uwierzytelniania można wyświetlić w witrynie Azure portal. Przejdź do swojego konta i wybierz **uwierzytelniania** na **ustawienia** menu.

![Szczegóły uwierzytelniania](./media/how-to-manage-authentication/how-to-view-auth.png)

 Aby dowiedzieć się więcej, zobacz [uwierzytelniania za pomocą usługi Azure Maps](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Konfigurowanie rejestracji aplikacji usługi Azure AD

Po utworzeniu konta usługi Azure Maps, należy ustanowić połączenia między dzierżawą usługi Azure AD i zasobów usługi Azure Maps.

1. Przejdź do bloku usługi Azure AD i Utwórz rejestracji aplikacji. Podaj nazwę dla rejestracji. W **adres URL logowania** Podaj strony głównej aplikacji internetowej / interfejsu API (np. https:\//localhost/). Jeśli masz już zarejestrowana aplikacja, przejdź do kroku 2.

    ![Rejestracja aplikacji](./media/how-to-manage-authentication/app-registration.png)

    ![Szczegóły rejestracji aplikacji](./media/how-to-manage-authentication/app-create.png)

2. Aby przypisać uprawnienia delegowane do interfejsu API usługi Azure Maps, przejdź do aplikacji w ramach **rejestracje aplikacji**, a następnie wybierz pozycję **ustawienia**.  Wybierz **wymagane uprawnienia**, a następnie wybierz pozycję **Dodaj**. Wyszukaj i wybierz pozycję **usługi Azure Maps** w obszarze **wybierz interfejs API**, a następnie wybierz pozycję **wybierz** przycisku.

    ![Uprawnienia aplikacji interfejsu API](./media/how-to-manage-authentication/app-permissions.png)

3. W obszarze **wybierz uprawnienia**, wybierz opcję **dostępu do usługi Azure Maps**, a następnie wybierz pozycję **wybierz** przycisku.

    ![Wybierz uprawnienia aplikacji interfejsu API](./media/how-to-manage-authentication/select-app-permissions.png)

4. Wykonaj krok lub b, w zależności od metody uwierzytelniania.

    1. Jeśli aplikacja używa uwierzytelniania tokenu użytkownika przy użyciu zestawu SDK usługi sieci Web Azure Maps, Włącz `oauthEnableImplicitFlow` ustawiając dla niej wartość true w sekcji manifestu strony szczegółów rejestracji aplikacji.
    
       ![Manifest aplikacji](./media/how-to-manage-authentication/app-manifest.png)

    2. Jeśli aplikacja używa uwierzytelniania serwera i aplikacji, przejdź do strony **klucze** bloku w rejestracji aplikacji i Utwórz hasło lub przekazywanie certyfikatu klucza publicznego do rejestracji aplikacji. Jeśli tworzysz hasła, po wybraniu **Zapisz**, skopiuj hasło do późniejszego i przechowuj w bezpiecznym miejscu. To hasło będzie używane do uzyskania tokenów z usługi Azure AD.

       ![Klucze aplikacji](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Udziel RBAC do usługi Azure Maps

Po skojarzeniu konta usługi Azure Maps z dzierżawą usługi Azure AD, można przyznać kontroli dostępu, przydzielając użytkownikowi lub aplikacji do co najmniej jedną rolę kontroli dostępu do usługi Azure Maps.

1. Przejdź do **kontrola dostępu (IAM)**, wybierz opcję **przypisań ról**, a następnie wybierz pozycję **Dodaj przypisanie roli**.

    ![Udziel kontroli RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. W **Dodaj przypisanie roli** okna, w obszarze **roli**, wybierz opcję **czytnika Data mapy platformy Azure (wersja zapoznawcza)**. W obszarze **Przypisz dostęp do**, wybierz opcję **użytkownika, grupy lub jednostki usługi Azure AD**. W obszarze **wybierz**, wybierz użytkownika lub aplikacji. Wybierz pozycję **Zapisz**.

    ![Dodaj przypisanie roli](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Wyświetlanie dostępnych ról RBAC mapy platformy Azure

Aby wyświetlić ról kontroli dostępu opartej na rolach, które są dostępne dla usługi Azure Maps, przejdź do **kontrola dostępu (IAM)**, wybierz opcję **role**, a następnie wyszukaj role począwszy od **usługi Azure Maps**. Są to role, które można udzielić dostępu do.

![Wyświetlanie dostępnych ról.](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Wyświetlanie usługi Azure Maps RBAC

RBAC zapewnia pełną kontrolę dostępu.

Aby wyświetlić użytkowników i aplikacje, które zostały udzielone RBAC dla usługi Azure Maps, przejdź do **kontrola dostępu (IAM)**, wybierz opcję **przypisań ról**, a następnie filtrować dane według **usługi Azure Maps**.

![Wyświetl użytkowników i aplikacje przyznawane RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Żądania tokenów dla usługi Azure Maps

Po zarejestrowaniu aplikacji i skojarzonego z nią za pomocą usługi Azure Maps, może żądać tokenów dostępu.

* Jeśli aplikacja używa uwierzytelniania tokenu użytkownika przy użyciu zestawu SDK usługi sieci Web Azure Maps, należy skonfigurować stronę HTML z Identyfikatorem klienta usługi Azure Maps i identyfikator aplikacji usługi Azure AD.

* Jeśli aplikacja używa uwierzytelniania serwera i aplikacji, musisz żądania tokenu z punktu końcowego logowania usługi Azure AD `https://login.microsoftonline.com` o identyfikatorze zasobu usługi Azure AD `https://atlas.microsoft.com/`, identyfikator klienta usługi Azure Maps, identyfikator aplikacji usługi Azure AD i rejestrowanie aplikacji usługi Azure AD hasło lub certyfikat.

Aby uzyskać więcej informacji na temat żądania tokenów dostępu z usługi Azure AD dla użytkowników i nazwy główne usług, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat uwierzytelniania usługi Azure AD i zestawu SDK usługi sieci Web Azure Maps, zobacz [usługi Azure AD i zestawu SDK sieci Web usługi Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Dowiedz się, jak wyświetlać metryki użycia interfejsu API dla swojego konta usługi Azure Maps:
> [!div class="nextstepaction"] 
> [Wyświetl metryki użycia](how-to-view-api-usage.md)