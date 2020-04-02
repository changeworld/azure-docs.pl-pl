---
title: Uwierzytelnij aplikację, aby uzyskać dostęp do zasobów usługi Azure Event Hubs
description: Ten artykuł zawiera informacje dotyczące uwierzytelniania aplikacji za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Azure Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 4cef49f138b96848b8e59cb5b2d0b185d4568aa9
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520993"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Uwierzytelnij aplikację za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Event Hubs
Platforma Microsoft Azure zapewnia zintegrowane zarządzanie kontrolą dostępu dla zasobów i aplikacji opartych na usłudze Azure Active Directory (Azure AD). Kluczową zaletą korzystania z usługi Azure AD z usługi Azure Event Hubs jest to, że nie trzeba już przechowywać poświadczenia w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2.0 z platformy Microsoft Identity. Nazwa zasobu, aby `https://eventhubs.azure.net/` zażądać tokenu jest (Dla klientów `https://<namespace>.servicebus.windows.net`platformy Kafka, zasób, aby zażądać tokenu jest ). Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupy lub jednostki usługi) z uruchomieniem aplikacji. Jeśli uwierzytelnianie zakończy się pomyślnie, usługa Azure AD zwraca token dostępu do aplikacji, a aplikacja może następnie użyć tokenu dostępu do autoryzowania żądania do zasobów usługi Azure Event Hubs.

Gdy rola jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure udziela dostępu do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp może być ograniczony do poziomu subskrypcji, grupy zasobów, obszaru nazw Centrum zdarzeń lub dowolnego zasobu pod nim. Zabezpieczenia usługi Azure AD mogą przypisywać role użytkownikowi, grupie, podmiotowi usługi aplikacji lub [tożsamości zarządzanej dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md) 

> [!NOTE]
> Definicja roli to kolekcja uprawnień. Kontrola dostępu oparta na rolach (RBAC) kontroluje sposób wymuszania tych uprawnień za pomocą przypisania roli. Przypisanie roli składa się z trzech elementów: podmiotu zabezpieczeń, definicji roli i zakresu. Aby uzyskać więcej informacji, zobacz [Opis różnych ról](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Wbudowane role w centrum zdarzeń platformy Azure
Platforma Azure udostępnia następujące wbudowane role RBAC do autoryzowania dostępu do danych usługi Event Hubs przy użyciu usługi Azure AD i OAuth:

- [Właściciel danych usługi Azure Event Hubs:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)Użyj tej roli, aby zapewnić pełny dostęp do zasobów centrum zdarzeń.
- [Nadawca danych usługi Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): użyj tej roli, aby udzielić dostępu do usług Event Hubs.
- [Odbiorca danych usługi Azure Event Hubs:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)Użyj tej roli, aby udzielić dostępu do zasobów usługi Event Hubs.   

> [!IMPORTANT]
> Nasza wersja zapoznawcza obsługiwała dodawanie uprawnień dostępu do danych usługi Event Hubs do roli Właściciela lub współautora. Jednak uprawnienia dostępu do danych dla roli właściciela i współautora nie są już honorowane. Jeśli używasz roli Właściciel lub Współautor, przełącz się do przy użyciu roli właściciela danych usługi Azure Event Hubs.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Przypisywanie ról RBAC za pomocą witryny Azure portal  
Aby dowiedzieć się więcej na temat zarządzania dostępem do zasobów platformy Azure przy użyciu funkcji RBAC i witryny Azure portal, zobacz [ten artykuł](..//role-based-access-control/role-assignments-portal.md). 

Po określiniu odpowiedniego zakresu przypisania roli przejdź do tego zasobu w witrynie Azure Portal. Wyświetl ustawienia kontroli dostępu (IAM) zasobu i postępuj zgodnie z tymi instrukcjami, aby zarządzać przypisaniami ról:

> [!NOTE]
> Kroki opisane poniżej przypisuje rolę do centrum zdarzeń w obszarze nazw Usługi Event Hubs, ale można wykonać te same kroki, aby przypisać rolę o zakresie do dowolnego zasobu Usługi Event Hubs.

1. W [witrynie Azure portal](https://portal.azure.com/)przejdź do obszaru nazw centrum zdarzeń.
2. Na stronie **Przegląd** wybierz Centrum zdarzeń, do którego chcesz przypisać rolę.

    ![Wybierz centrum zdarzeń](./media/authenticate-application/select-event-hub.png)
1. Wybierz **pozycję Kontrola dostępu (IAM),** aby wyświetlić ustawienia kontroli dostępu dla centrum zdarzeń. 
1. Wybierz kartę **Przypisania ról,** aby wyświetlić listę przypisań ról. Wybierz przycisk **Dodaj** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przypisanie roli**. 

    ![Przycisk Dodaj na pasku narzędzi](./media/authenticate-application/role-assignments-add-button.png)
1. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności:
    1. Wybierz **rolę Centrum zdarzeń,** którą chcesz przypisać. 
    1. Wyszukaj **podmiot zabezpieczeń** (użytkownik, grupa, podmiot usługi), do którego chcesz przypisać rolę.
    1. Wybierz **pozycję Zapisz,** aby zapisać przypisanie roli. 

        ![Przypisywanie roli do użytkownika](./media/authenticate-application/assign-role-to-user.png)
    4. Tożsamość, do której przypisano rolę, jest wyświetlana na liście w ramach tej roli. Na przykład na poniższej ilustracji pokazano, że użytkownicy platformy Azure znajduje się w roli właściciela danych usługi Azure Event Hubs. 
        
        ![Użytkownik na liście](./media/authenticate-application/user-in-list.png)

Można wykonać podobne kroki, aby przypisać rolę o zakresie do obszaru nazw Usługi event hubów, grupy zasobów lub subskrypcji. Po zdefiniowaniu roli i jej zakresu można przetestować to zachowanie za pomocą przykładów [w tej lokalizacji Usługi GitHub.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)


## <a name="authenticate-from-an-application"></a>Uwierzytelnianie przy użyciu aplikacji
Kluczową zaletą korzystania z usługi Azure AD z usługami Event Hubs jest to, że poświadczenia nie muszą być już przechowywane w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2.0 z platformy tożsamości firmy Microsoft. Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupy lub jednostki usługi) z uruchomieniem aplikacji. Jeśli uwierzytelnianie zakończy się pomyślnie, usługa Azure AD zwraca token dostępu do aplikacji, a aplikacja może następnie użyć tokenu dostępu do autoryzowania żądań do usługi Azure Event Hubs.

W poniższych sekcjach pokazano, jak skonfigurować aplikację macierzystą lub aplikację sieci web do uwierzytelniania za pomocą platformy tożsamości firmy Microsoft 2.0. Aby uzyskać więcej informacji na temat platformy tożsamości firmy Microsoft 2.0, zobacz [omówienie platformy tożsamości firmy Microsoft (w wersji 2.0).](../active-directory/develop/v2-overview.md)

Aby zapoznać się z omówieniem przepływu grantów kodu OAuth 2.0, zobacz [Autoryzowanie dostępu do aplikacji sieci Web usługi Azure Active Directory przy użyciu przepływu dotacji kodu OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi Azure AD
Pierwszym krokiem przy użyciu usługi Azure AD do autoryzowania zasobów usługi Event Hubs jest rejestrowanie aplikacji klienckiej za pomocą dzierżawy usługi Azure AD z [witryny Azure portal.](https://portal.azure.com/) Podczas rejestrowania aplikacji klienckiej, należy podać informacje o aplikacji do usługi AD. Usługa Azure AD udostępnia następnie identyfikator klienta (nazywany również identyfikatorem aplikacji), którego można użyć do skojarzenia aplikacji ze środowiska uruchomieniowego usługi Azure AD. Aby dowiedzieć się więcej o identyfikatorze klienta, zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Na poniższych obrazach przedstawiono kroki rejestrowania aplikacji sieci web:

![Rejestrowanie aplikacji](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Jeśli zarejestrujesz aplikację jako aplikację macierzystą, można określić dowolny prawidłowy identyfikator URI dla identyfikatora URI przekierowania. W przypadku aplikacji natywnych ta wartość nie musi być rzeczywistym adresem URL. W przypadku aplikacji sieci web identyfikator URI przekierowania musi być prawidłowym identyfikatorem URI, ponieważ określa adres URL, do którego są dostarczane tokeny.

Po zarejestrowaniu aplikacji w obszarze **Ustawienia**zostanie wyświetlony **identyfikator aplikacji (klienta):**

![Identyfikator wniosku zarejestrowanego](./media/authenticate-application/application-id.png)

Aby uzyskać więcej informacji na temat rejestrowania aplikacji za pomocą usługi Azure AD, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).


### <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta   
Aplikacja potrzebuje klucza tajnego klienta, aby udowodnić swoją tożsamość podczas żądania tokenu. Aby dodać klucz tajny klienta, wykonaj następujące kroki.

1. Przejdź do rejestracji aplikacji w witrynie Azure portal.
1. Wybierz ustawienie **Certyfikaty & wpisy tajne.**
1. W obszarze **Wpisy tajne klienta**wybierz **pozycję Nowy klucz tajny klienta,** aby utworzyć nowy klucz tajny.
1. Podaj opis klucza tajnego i wybierz pożądany interwał wygaśnięcia.
1. Natychmiast skopiuj wartość nowego klucza tajnego do bezpiecznej lokalizacji. Wartość wypełnienia jest wyświetlana tylko raz.

    ![Klucz tajny klienta](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Biblioteki klientów do pozyskiwania tokenów  
Po zarejestrowaniu aplikacji i przyznaniu jej uprawnień do wysyłania/odbierania danych w usłudze Azure Event Hubs można dodać kod do aplikacji, aby uwierzytelnić podmiot zabezpieczeń i uzyskać token OAuth 2.0. Aby uwierzytelnić i uzyskać token, można użyć jednej z [bibliotek uwierzytelniania platformy tożsamości firmy Microsoft](../active-directory/develop/reference-v2-libraries.md) lub innej biblioteki open source obsługującej OpenID lub Connect 1.0. Aplikacja może następnie użyć tokenu dostępu do autoryzowania żądania względem usługi Azure Event Hubs.

Aby uzyskać listę scenariuszy, dla których tokeny pozyskiwania jest obsługiwany, zobacz [scenariusze](https://aka.ms/msal-net-scenarios) sekcji [Biblioteki uwierzytelniania firmy Microsoft (MSAL) dla repozytorium .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub.

## <a name="samples"></a>Samples
- [Przykłady witryn Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Te przykłady używają starej biblioteki **Microsoft.Azure.EventHubs,** ale można łatwo zaktualizować go do korzystania z najnowszej biblioteki **Azure.Messaging.EventHubs.** Aby przenieść przykład ze starej biblioteki do nowej, zobacz [Przewodnik do migracji z witryny Microsoft.Azure.EventHubs do usługi Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Przykłady witryn Azure.Messaging.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Ten przykład został zaktualizowany, aby użyć najnowszej biblioteki **Azure.Messaging.EventHubs.**

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej o RBAC, zobacz [Co to jest kontrola dostępu oparta na rolach (RBAC)?](../role-based-access-control/overview.md)
- Aby dowiedzieć się, jak przypisywać przypisania ról RBAC i zarządzać nimi za pomocą programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST, zobacz następujące artykuły:
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu API REST](../role-based-access-control/role-assignments-rest.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą szablonów usługi Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Zobacz następujące artykuły pokrewne:
- [Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Event Hubs](authenticate-managed-identity.md)
- [Uwierzytelnij żądania w centrach zdarzeń platformy Azure przy użyciu podpisów dostępu współdzielonego](authenticate-shared-access-signature.md)
- [Autoryzowanie dostępu do zasobów usługi Event Hubs przy użyciu usługi Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autoryzowanie dostępu do zasobów Centrum zdarzeń przy użyciu podpisów dostępu współdzielonego](authorize-access-shared-access-signature.md)

