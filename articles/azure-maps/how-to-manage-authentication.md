---
title: Zarządzaj uwierzytelnianiem w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Można użyć witryny Azure portal, aby zarządzać uwierzytelnianiem w usługi Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: db0450ab8b765ec158201c6db149a7de7d135b98
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56143847"
---
# <a name="manage-authentication-in-azure-maps"></a>Zarządzaj uwierzytelnianiem w usługi Azure Maps

Po utworzeniu konta usługi Azure Maps, trzy identyfikatory GUID są tworzone do obsługi usługi Azure Active Directory (Azure AD) lub uwierzytelniania klucza wspólnego.

Informacje dotyczące uwierzytelniania można znaleźć, przechodząc do **uwierzytelniania** strony w obszarze **ustawienia** w witrynie Azure portal. Przejdź do swojego konta. Następnie wybierz pozycję **uwierzytelniania** z menu.


## <a name="view-authentication-details"></a>Wyświetl szczegóły uwierzytelniania

Aby wyświetlić informacje dotyczące uwierzytelniania, przejdź do **uwierzytelniania** opcji w menu Ustawienia.

![Wyświetl uwierzytelniania](./media/how-to-manage-authentication/how-to-view-auth.png)

 Aby dowiedzieć się więcej o uwierzytelnianiu i usługi Azure Maps, zobacz [uwierzytelniania za pomocą usługi Azure Maps](https://aka.ms/amauth).


## <a name="configure-azure-ad-app-registration"></a>Konfigurowanie rejestracji aplikacji w usłudze Azure AD

Po utworzeniu konta usługi Azure Map połączenia między dzierżawą usługi Azure AD i zasobów platformy Azure mapy platformy Azure jest wymagana. 

1. Przejdź do bloku usługi AAD i Utwórz rejestrację aplikacji z nazwą i znak w adresie URL jako strony głównej aplikacji internetowej / interfejsu API, takie jak "https://localhost/". Jeśli masz już zarejestrowana aplikacja, przejdź do kroku 2.

    ![Rejestracja aplikacji](./media/how-to-manage-authentication/app-registration.png)

    ![Rejestracja aplikacji](./media/how-to-manage-authentication/app-create.png)

2. Przypisz uprawnienia delegowane do interfejsu API do usługi Azure Maps, przechodząc do aplikacji w ramach rejestracji aplikacji i kliknij pozycję **ustawienia**.  Wybierz **wymagane uprawnienia** i wybierz **Dodaj**. Wyszukaj, a następnie wybierz usługi Azure Maps w obszarze **wybierz interfejs API** i kliknij przycisk **wybierz**.

    ![Uprawnienia do aplikacji interfejsu api](./media/how-to-manage-authentication/app-permissions.png)

3. Na koniec w obszarze wybierz uprawnienia wybierz dostępu do usługi Azure Maps i kliknij pozycję Wybierz.

    ![Wybierz uprawnienia do aplikacji interfejsu api](./media/how-to-manage-authentication/select-app-permissions.png)

4. Wykonaj krok lub b, w zależności od implementacji uwierzytelniania.

    1. Jeśli aplikacja jest pomyślny przebieg operacji tokenu uwierzytelniania użytkowników za pomocą naszego zestawu SDK sieci Web mapy usługi Azure, musisz włączyć `oauthEnableImplicitFlow` ustawiając dla niej wartość true w sekcji manifestu strony szczegółów rejestracji aplikacji. 
    
       ![Manifest aplikacji](./media/how-to-manage-authentication/app-manifest.png)

    2. Jeśli Twoja aplikacja korzysta z uwierzytelniania serwera i aplikacji, przejdź do **klucze** bloku w rejestracji aplikacji, a następnie utwórz hasło lub przekazywanie certyfikatu klucza publicznego do rejestracji aplikacji. Jeśli tworzysz hasła, po **Zapisz**, skopiuj hasło do późniejszego i zapisz go w bezpiecznym miejscu, zostanie ona użyta do uzyskania tokenów z usługi AAD. 

       ![Klucze aplikacji](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Udziel RBAC do usługi Azure Maps

Gdy konto usługi Azure Maps został skojarzony z dzierżawą usługi Azure AD, kontroli dostępu mogą być udzielane przez przypisanie użytkownika lub aplikacji do dostępnych ról kontroli dostępu do usługi Azure Maps.

1. Przejdź do **kontroli dostępu** kliknij przycisk **przypisań ról**, i **Dodaj przypisanie roli**.

    ![Udziel kontroli RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Przypisanie roli wyskakiwania okna wybierz czytnika Data mapy platformy Azure (wersja zapoznawcza) **roli**, **Przypisz dostęp do**: Użytkownika usługi Azure AD, grupy lub jednostki usługi, **wybierz** użytkownikowi lub aplikacji z listy rozwijanej, a **Zapisz**.

    ![Dodaj przypisanie roli](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Wyświetlanie dostępnych ról RBAC mapy platformy Azure

Aby wyświetlić ról kontroli dostępu opartej na rolach dla usługi Azure Maps, który można udzielić dostępu do, przejdź do **kontrola dostępu (IAM)** opcji, kliknij pozycję **role**, i wyszukaj role począwszy od **Usługi azure Maps**.

![Wyświetlanie dostępnych ról.](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach usługi Azure Maps widoku (RBAC)

Usługa Azure AD umożliwia szczegółową kontrolę dostępu za pomocą kontroli dostępu opartej na rolach (RBAC). 

Aby wyświetlić użytkowników lub aplikacji, które zostały udzielone RBAC dla usługi Azure Maps, przejdź do **kontrola dostępu (IAM)** wybierz **przypisań ról**i Zastosuj filtr **usługi Azure Maps**. Wszystkie dostępne role pojawi się poniżej.

![Wyświetl RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Żądania tokenów dla usługi Azure Maps

Gdy Twoja aplikacja jest zarejestrowana i skojarzone z usługi Azure Maps, teraz możesz żądać tokenów dostępu.

* Jeśli aplikacja jest pomyślny przebieg operacji tokenu uwierzytelniania użytkowników za pomocą naszej usługi Azure Maps zestawu SDK sieci Web (Internet), należy skonfigurować stronę html z Identyfikatorem klienta mapy platformy Azure i identyfikatora usługi Azure AD aplikacji.


* Dla aplikacji przy użyciu uwierzytelniania serwera i aplikacji, żądania tokenu z punktu końcowego logowania usługi Azure AD https://login.microsoftonline.com przy użyciu Identyfikatora klienta usługi Azure Maps, identyfikator aplikacji w usłudze Azure AD i aplikacji usługi Azure AD hasło rejestracji lub certyfikat.

Aby uzyskać więcej informacji na temat żądania tokenów dostępu z usługi Azure AD dla użytkowników i nazwy główne usług, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat uwierzytelniania usługi Azure AD i zestawu SDK usługi sieci Web Azure Maps, zobacz [usługi Azure AD i zestawu SDK sieci Web usługi Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).