---
title: Zarządzanie uwierzytelnianiem | Mapy Microsoft Azure
description: Za pomocą Azure Portal można zarządzać uwierzytelnianiem w usłudze Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1a38c970f3c9fa5b90032f5816f8e541b305531c
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911550"
---
# <a name="manage-authentication-in-azure-maps"></a>Zarządzanie uwierzytelnianiem w Azure Maps

Po utworzeniu konta Azure Maps, identyfikator klienta i klucze są tworzone w celu obsługi uwierzytelniania przy użyciu usługi Azure Active Directory (Azure AD) lub klucza współużytkowanego.

## <a name="view-authentication-details"></a>Wyświetlanie szczegółów uwierzytelniania

Po utworzeniu konta Azure Maps są generowane klucze podstawowe i pomocnicze. Zaleca się używanie klucza podstawowego jako klucza subskrypcji podczas wywoływania Azure Maps przy użyciu uwierzytelniania za pomocą [klucza współużytkowanego](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Klucz pomocniczy może być używany w scenariuszach, takich jak stopniowe zmiany klawiszy. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie przy użyciu Azure Maps](https://aka.ms/amauth).

Szczegóły uwierzytelniania można wyświetlić na Azure Portal. Przejdź do swojego konta i wybierz pozycję **uwierzytelnianie** w menu **Ustawienia** .

![Szczegóły uwierzytelniania](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="set-up-azure-ad-app-registration"></a>Konfigurowanie rejestracji aplikacji usługi Azure AD

Po utworzeniu konta Azure Maps należy nawiązać połączenie między dzierżawą usługi Azure AD i zasobem Azure Maps.

1. Przejdź do bloku Azure AD i Utwórz rejestrację aplikacji. Podaj nazwę rejestracji. W polu **adres URL logowania** Podaj stronę główną aplikacji/interfejsu API sieci Web (na przykład https:\//localhost/). Jeśli masz już zarejestrowaną aplikację, przejdź do kroku 2.

    ![Rejestracja aplikacji](./media/how-to-manage-authentication/app-registration.png)

    ![Szczegóły rejestracji aplikacji](./media/how-to-manage-authentication/app-create.png)

2. Aby przypisać delegowane uprawnienia interfejsu API do Azure Maps, przejdź do aplikacji w obszarze **rejestracje aplikacji**, a następnie wybierz pozycję **Ustawienia**.  Wybierz pozycję **wymagane uprawnienia**, a następnie wybierz pozycję **Dodaj**. Wyszukaj i wybierz pozycję **Azure Maps** w obszarze **Wybierz interfejs API**, a następnie wybierz przycisk **Wybierz** .

    ![Uprawnienia interfejsu API aplikacji](./media/how-to-manage-authentication/app-permissions.png)

3. W obszarze **Wybierz uprawnienia**wybierz pozycję **dostęp Azure Maps**, a następnie wybierz przycisk **Wybierz** .

    ![Wybieranie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/select-app-permissions.png)

4. Ukończ krok a lub b, w zależności od metody uwierzytelniania.

    1. Jeśli aplikacja używa uwierzytelniania przy użyciu tokenu użytkownika w Azure Maps Web SDK, Włącz `oauthEnableImplicitFlow`, ustawiając dla niego wartość true w sekcji manifestu na stronie szczegółów rejestracji aplikacji.
    
       ![Manifest aplikacji](./media/how-to-manage-authentication/app-manifest.png)

    2. Jeśli aplikacja używa uwierzytelniania serwera/aplikacji, przejdź do bloku **klucze** w obszarze Rejestracja aplikacji, a następnie utwórz hasło lub Przekaż certyfikat klucza publicznego do rejestracji aplikacji. Jeśli utworzysz hasło, po wybraniu opcji **Zapisz**Skopiuj hasło do niego w przyszłości i Zapisz je w bezpiecznym miejscu. To hasło będzie używane do uzyskiwania tokenów z usługi Azure AD.

       ![Klucze aplikacji](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Przyznaj RBAC Azure Maps

Po skojarzeniu konta Azure Maps z dzierżawą usługi Azure AD można udzielić kontroli dostępu, przypisując użytkownika, grupę lub aplikację do co najmniej jednej roli kontroli dostępu Azure Maps.

1. Przejdź do pozycji **Kontrola dostępu (IAM)** , wybierz pozycję **przypisania ról**, a następnie wybierz pozycję **Dodaj przypisanie roli**.

    ![Przyznaj RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. W oknie **Dodawanie przypisania roli** w obszarze **rola**wybierz pozycję **Azure Maps Data Reader (wersja zapoznawcza)** . W obszarze **Przypisywanie dostępu do**wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**. W obszarze **Wybierz**wybierz użytkownika lub aplikację. Wybierz pozycję **Zapisz**.

    ![Dodawanie przypisania roli](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Wyświetl dostępne Azure Maps ról RBAC

Aby wyświetlić role kontroli dostępu opartej na rolach (RBAC), które są dostępne dla Azure Maps, przejdź do obszaru **Kontrola dostępu (IAM)** , wybierz pozycję **role**, a następnie wyszukaj role zaczynające się od **Azure Maps**. Są to role, do których można udzielić dostępu.

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