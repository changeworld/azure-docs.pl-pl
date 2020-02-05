---
title: Zarządzanie uwierzytelnianiem | Mapy Microsoft Azure
description: Za pomocą Azure Portal można zarządzać uwierzytelnianiem w usłudze Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: f856aebe5e3acaca142e460d18ec8c6498b18787
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989308"
---
# <a name="manage-authentication-in-azure-maps"></a>Zarządzanie uwierzytelnianiem w Azure Maps

Po utworzeniu konta Azure Maps, identyfikator klienta i klucze są tworzone w celu obsługi Azure Active Directory (Azure AD) i uwierzytelniania klucza współużytkowanego.

## <a name="view-authentication-details"></a>Wyświetlanie szczegółów uwierzytelniania

Po utworzeniu konta Azure Maps są generowane klucze podstawowe i pomocnicze. Zaleca się używanie klucza podstawowego jako klucza subskrypcji podczas wywoływania Azure Maps przy użyciu uwierzytelniania za pomocą [klucza współużytkowanego](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Klucz pomocniczy może być używany w scenariuszach, takich jak stopniowe zmiany klawiszy. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie przy użyciu Azure Maps](https://aka.ms/amauth).

Szczegóły uwierzytelniania można wyświetlić na Azure Portal. Przejdź do swojego konta i wybierz pozycję **uwierzytelnianie** w menu **Ustawienia** .

![Szczegóły uwierzytelniania](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="configure-azure-ad-app-registration"></a>Konfigurowanie rejestracji aplikacja usługi Azure AD

1. Wybierz **Azure Active Directory** z listy usług platformy Azure w Azure Portal.  Wybierz pozycję **rejestracje aplikacji** i kliknij pozycję **Nowa rejestracja**.  Ponown. Wprowadź **nazwę**, wybierz pozycję **Pomoc techniczna**, a następnie kliknij pozycję **zarejestruj**.  Jeśli masz już zarejestrowaną aplikację, przejdź do kroku 2. 

    ![Rejestracja aplikacji](./media/how-to-manage-authentication/app-registration.png)

    ![Szczegóły rejestracji aplikacji](./media/how-to-manage-authentication/app-create.png)

2. Aby przypisać delegowane uprawnienia interfejsu API do Azure Maps, przejdź do aplikacji w obszarze **rejestracje aplikacji**. Następnie wybierz pozycję **uprawnienia interfejsu API**, a następnie wybierz pozycję **Dodaj uprawnienie**. Wyszukaj i wybierz **Azure Maps** w obszarze **interfejsy API, które są wykorzystywane przez moją organizację**.

    ![Uprawnienia interfejsu API aplikacji](./media/how-to-manage-authentication/app-permissions.png)

3. Sprawdź **Azure Maps dostępu** , a następnie kliknij przycisk **Dodaj uprawnienia**.

    ![Wybieranie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/select-app-permissions.png)

4. Ukończ krok a lub b, w zależności od metody uwierzytelniania. 

    1. Jeśli aplikacja używa uwierzytelniania przy użyciu tokenu użytkownika w Azure Maps Web SDK, Włącz `oauth2AllowImplicitFlow`. Aby włączyć `oauth2AllowImplicitFlow`, ustaw dla niego wartość true w sekcji manifestu rejestracji aplikacji. 
    
       ![Manifest aplikacji](./media/how-to-manage-authentication/app-manifest.png)

    2. Jeśli aplikacja używa uwierzytelniania serwera/aplikacji, przejdź do bloku **certyfikaty & wpisy tajne** na stronie rejestracji aplikacji, a następnie utwórz hasło, klikając **Nowy wpis tajny klienta** lub Przekaż certyfikat klucza publicznego do rejestracji aplikacji. Jeśli utworzysz hasło, po kliknięciu przycisku **Dodaj**Skopiuj hasło do nowszej lokalizacji i Zapisz je w bezpiecznym miejscu. To hasło będzie używane do uzyskiwania tokenów z usługi Azure AD.

       ![Klucze aplikacji](./media/how-to-manage-authentication/app-keys.png)

       ![Dodaj klucz](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>Przyznaj kontroli dostępu opartej na rolach (RBAC) do Azure Maps

Po skojarzeniu konta Azure Maps z dzierżawą usługi Azure AD można udzielić kontroli dostępu. Kontrolę dostępu można udzielić, przypisując użytkownika, grupę lub aplikację do co najmniej jednej Azure Maps ról kontroli dostępu.

1. Przejdź do swojego **konta Azure Maps**. Wybierz pozycję **Kontrola dostępu (IAM)** , a następnie wybierz pozycję **przypisanie roli**.

    ![Przyznaj RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. W oknie **przypisanie roli** w obszarze **rola**wybierz pozycję **Azure Maps Data Reader (wersja zapoznawcza)** . W obszarze **Przypisz dostęp do** wybierz **regułę użytkownik, Grupa lub usługa Azure AD**. Wybierz użytkownika lub aplikację. Wybierz pozycję **Zapisz**.

    ![Dodaj przypisanie roli](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Wyświetl dostępne Azure Maps ról RBAC

Aby wyświetlić role kontroli dostępu opartej na rolach (RBAC), które są dostępne dla Azure Maps, przejdź do obszaru **Kontrola dostępu (IAM)** , wybierz pozycję **role**, a następnie wyszukaj role zaczynające się od **Azure Maps**. Role te są rolami, do których można udzielić dostępu.

![Wyświetl dostępne role](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Wyświetl Azure Maps RBAC

RBAC zapewnia szczegółową kontrolę dostępu.

Aby wyświetlić użytkowników i aplikacje, którym udzielono dostępu RBAC dla Azure Maps, przejdź do **Access Control (IAM)** , wybierz **przypisania ról**, a następnie Filtruj według **Azure Maps**.

![Wyświetlanie użytkowników i aplikacji, którym udzielono kontroli RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Tokeny żądania dla Azure Maps

Po zarejestrowaniu aplikacji i skojarzeniu jej z Azure Maps można zażądać tokenów dostępu.

* Jeśli aplikacja korzysta z uwierzytelniania za pomocą tokenu użytkownika w Azure Maps Web SDK, należy skonfigurować stronę HTML z IDENTYFIKATORem klienta Azure Maps i IDENTYFIKATORem aplikacji usługi Azure AD.

* Jeśli aplikacja używa uwierzytelniania serwera/aplikacji, musisz zażądać tokenu z punktu końcowego tokenu usługi Azure AD `https://login.microsoftonline.com` z IDENTYFIKATORem zasobu usługi Azure AD `https://atlas.microsoft.com/`, IDENTYFIKATORem klienta Azure Maps, IDENTYFIKATORem aplikacji usługi Azure AD oraz hasłem lub certyfikatem rejestracji aplikacji usługi Azure AD.

| Środowisko platformy Azure   | Punkt końcowy tokenu usługi Azure AD | Identyfikator zasobu platformy Azure |
| --------------------|-------------------------|-------------------|
| Azure Public        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Platforma Azure dla instytucji rządowych    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Aby uzyskać więcej informacji na temat żądania tokenów dostępu z usługi Azure AD dla użytkowników i podmiotów usługi, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o uwierzytelnianiu usługi Azure AD i Azure Maps Web SDK, zobacz [Azure AD i Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Dowiedz się, jak wyświetlić metryki użycia interfejsu API dla konta Azure Maps:
> [!div class="nextstepaction"] 
> [Wyświetl metryki użycia](how-to-view-api-usage.md)

Aby uzyskać listę przykładów pokazujących, jak zintegrować Azure Active Directory (AAD) z Azure Maps, zobacz:

> [!div class="nextstepaction"]
> [Przykłady uwierzytelniania usługi Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
