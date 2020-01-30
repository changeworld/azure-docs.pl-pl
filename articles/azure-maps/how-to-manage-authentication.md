---
title: Zarządzanie uwierzytelnianiem | Mapy Microsoft Azure
description: Za pomocą Azure Portal można zarządzać uwierzytelnianiem w usłudze Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1f7f128898089292a8ccd92686af5d68fe328f3c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766061"
---
# <a name="manage-authentication-in-azure-maps"></a>Zarządzanie uwierzytelnianiem w Azure Maps

Po utworzeniu konta Azure Maps, identyfikator klienta i klucze są tworzone w celu obsługi Azure Active Directory (Azure AD) i uwierzytelniania klucza współużytkowanego.

## <a name="view-authentication-details"></a>Wyświetlanie szczegółów uwierzytelniania

Po utworzeniu konta Azure Maps są generowane klucze podstawowe i pomocnicze. Użyj klucza podstawowego jako klucza subskrypcji, czasami te nazwy są używane zamiennie. Klucz pomocniczy może być używany w scenariuszach, takich jak stopniowe zmiany klawiszy. W obu przypadkach klucz jest wymagany do wywołania Azure Maps. Ten proces jest nazywany [uwierzytelnianiem klucza wspólnego](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Aby dowiedzieć się więcej o kluczu udostępnionym i uwierzytelnianiu usługi Azure AD, zobacz temat [uwierzytelnianie za pomocą Azure Maps](https://aka.ms/amauth) .

Szczegóły uwierzytelniania można wyświetlić na Azure Portal. Przejdź do swojego konta i wybierz pozycję **uwierzytelnianie** w menu **Ustawienia** .

![Szczegóły uwierzytelniania](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="set-up-azure-ad-app-registration"></a>Konfigurowanie rejestracji aplikacji usługi Azure AD

Po utworzeniu konta Azure Maps należy nawiązać połączenie między dzierżawą usługi Azure AD i zasobem Azure Maps.

1. Wybierz **Azure Active Directory** z menu Portal. Podaj nazwę rejestracji. Kliknij **rejestracje aplikacji** następnie kliknij pozycję **Nowa rejestracja**. W polu **Identyfikator URI przekierowania** Podaj stronę główną aplikacji sieci Web. Na przykład https://localhost/. Jeśli masz już zarejestrowaną aplikację, przejdź do kroku 2.

    ![Rejestracja aplikacji](./media/how-to-manage-authentication/app-registration.png)

    ![Szczegóły rejestracji aplikacji](./media/how-to-manage-authentication/app-create.png)

2. Aby przypisać delegowane uprawnienia interfejsu API do Azure Maps, przejdź do aplikacji w obszarze **rejestracje aplikacji**, a następnie wybierz pozycję **uprawnienia interfejsu API**. Wybierz pozycję **Dodaj uprawnienie**. Wyszukaj i wybierz **Azure Maps** w obszarze **Wybierz interfejs API**.

    ![Uprawnienia interfejsu API aplikacji](./media/how-to-manage-authentication/app-permissions.png)

3. W obszarze **Wybierz uprawnienia zaznacz**pole wyboru **personifikacja użytkownika**, a następnie kliknij przycisk **Wybierz** u dołu.

    ![Wybieranie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/select-app-permissions.png)

4. Ukończ krok a lub b, w zależności od metody uwierzytelniania.

    1. Jeśli aplikacja używa uwierzytelniania tokenów użytkownika w Azure Maps Web SDK, Włącz `oauth2AllowImplicitFlow`, ustawiając dla niego wartość true w sekcji manifestu rejestracji aplikacji.
    
       ![Manifest aplikacji](./media/how-to-manage-authentication/app-manifest.png)

    2. Jeśli aplikacja używa uwierzytelniania serwera/aplikacji, przejdź do bloku **certyfikaty & wpisy tajne** w obszarze Rejestracja aplikacji, a następnie utwórz hasło lub Przekaż certyfikat klucza publicznego do rejestracji aplikacji. Jeśli utworzysz hasło, Zapisz je bezpiecznie do późniejszego użycia. To hasło będzie używane do uzyskiwania tokenów z usługi Azure AD.

       ![Klucze aplikacji](./media/how-to-manage-authentication/app-keys.png)


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
