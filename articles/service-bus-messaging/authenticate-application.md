---
title: Uwierzytelnij aplikację, aby uzyskać dostęp do encji usługi Azure Service Bus
description: Ten artykuł zawiera informacje dotyczące uwierzytelniania aplikacji za pomocą usługi Azure Active Directory w celu uzyskania dostępu do encji usługi Azure Service Bus (kolejki, tematy itp.)
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259294"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Uwierzytelnij i autoryzuj aplikację za pomocą usługi Azure Active Directory w celu uzyskania dostępu do encji usługi Azure Service Bus
Usługa Azure Service Bus obsługuje korzystanie z usługi Azure Active Directory (Azure AD) w celu autoryzowania żądań do jednostek usługi Service Bus (kolejek, tematów, subskrypcji lub filtrów). Za pomocą usługi Azure AD można użyć kontroli dostępu opartej na rolach (RBAC) do udzielania uprawnień podmiotowi zabezpieczeń, który może być podmiotem usługi użytkownika, grupy lub usługi aplikacji. Aby dowiedzieć się więcej o rolach i przypisaniach ról, zobacz [Opis różnych ról](../role-based-access-control/overview.md).

## <a name="overview"></a>Omówienie
Gdy podmiot zabezpieczeń (użytkownik, grupa lub aplikacja) próbuje uzyskać dostęp do jednostki usługi Service Bus, żądanie musi być autoryzowane. Dzięki usłudze Azure AD dostęp do zasobu jest procesem dwuetapowym. 

 1. Po pierwsze tożsamość podmiotu zabezpieczeń jest uwierzytelniona i zwracany jest token OAuth 2.0. Nazwa zasobu, aby `https://servicebus.azure.net`zażądać tokenu jest .
 1. Następnie token jest przekazywany jako część żądania do usługi Service Bus, aby autoryzować dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawiera token dostępu OAuth 2.0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w jednostce platformy Azure, takiej jak maszyna wirtualna Azure, zestaw skalowania maszyny wirtualnej lub aplikacja funkcji platformy Azure, może użyć tożsamości zarządzanej, aby uzyskać dostęp do zasobów. Aby dowiedzieć się, jak uwierzytelniać żądania złożone przez zarządzaną tożsamość w usłudze Service Bus, zobacz [Uwierzytelnij dostęp do zasobów usługi Azure Service Bus za pomocą usługi Azure Active Directory i tożsamości zarządzanych dla zasobów platformy Azure.](service-bus-managed-service-identity.md) 

Krok autoryzacji wymaga przypisania co najmniej jednej roli RBAC do podmiotu zabezpieczeń. Usługa Azure Service Bus udostępnia role RBAC, które obejmują zestawy uprawnień dla zasobów usługi Service Bus. Role, które są przypisane do podmiotu zabezpieczeń określić uprawnienia, które podmiot zabezpieczeń będzie mieć. Aby dowiedzieć się więcej na temat przypisywania ról RBAC do usługi Azure Service Bus, zobacz [Wbudowane role RBAC dla usługi Azure Service Bus](#built-in-rbac-roles-for-azure-service-bus). 

Aplikacje natywne i aplikacje sieci web, które żądają usługi Service Bus, mogą również autoryzować za pomocą usługi Azure AD. W tym artykule pokazano, jak zażądać tokenu dostępu i używać go do autoryzowania żądań dotyczących zasobów usługi Service Bus. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC dla praw dostępu
Usługa Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC).](../role-based-access-control/overview.md) Usługa Azure Service Bus definiuje zestaw wbudowanych ról RBAC, które obejmują typowe zestawy uprawnień używanych do uzyskiwania dostępu do jednostek usługi Service Bus i można również zdefiniować niestandardowe role dostępu do danych.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure udziela dostępu do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp może być ograniczony do poziomu subskrypcji, grupy zasobów lub obszaru nazw usługi Service Bus. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, jednostką usługi aplikacji lub [tożsamością zarządzaną dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Wbudowane role RBAC dla usługi Azure Service Bus
W przypadku usługi Azure Service Bus zarządzanie obszarami nazw i wszystkimi powiązanymi zasobami za pośrednictwem witryny Azure portal i interfejsu API zarządzania zasobami platformy Azure jest już chronione przy użyciu modelu *kontroli dostępu opartego na rolach* (RBAC). Platforma Azure udostępnia poniższe wbudowane role RBAC do autoryzowania dostępu do obszaru nazw usługi Service Bus:

- [Właściciel danych usługi Azure Service Bus:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)umożliwia dostęp do danych do obszaru nazw usługi Service Bus i jej jednostek (kolejki, tematy, subskrypcje i filtry)
- [Nadawca danych usługi Azure Service Bus:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)użyj tej roli, aby dać dostęp do usługi Service Bus i jej jednostek.
- [Odbiorca danych usługi Azure Service Bus:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)Użyj tej roli, aby dać dostęp do obszaru nazw usługi Service Bus i jego jednostek. 

## <a name="resource-scope"></a>Zakres zasobu 
Przed przypisaniem roli RBAC do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, na których można mieć dostęp do zasobów usługi Service Bus, zaczynając od najwęższego zakresu:

- **Kolejka,** **temat**lub **subskrypcja**: Przypisanie roli ma zastosowanie do określonej encji usługi Service Bus. Obecnie portal Azure portal nie obsługuje przypisywania użytkowników/grup/tożsamości zarządzanych do ról RBAC usługi Service Bus na poziomie subskrypcji. 
- **Obszar nazw usługi Service Bus:** Przypisanie roli obejmuje całą topologię usługi Service Bus w obszarze nazw i do skojarzonej z nią grupy odbiorców.
- **Grupa zasobów:** Przypisanie roli ma zastosowanie do wszystkich zasobów usługi Service Bus w grupie zasobów.
- **Subskrypcja:** Przypisanie roli ma zastosowanie do wszystkich zasobów usługi Service Bus we wszystkich grupach zasobów w ramach subskrypcji.

> [!NOTE]
> Należy pamiętać, że przydziały ról RBAC może potrwać do pięciu minut do propagacji. 

Aby uzyskać więcej informacji na temat definiowania ról wbudowanych, zobacz [Rozumienie definicji ról](../role-based-access-control/role-definitions.md#management-and-data-operations). Aby uzyskać informacje dotyczące tworzenia niestandardowych ról RBAC, zobacz [Tworzenie ról niestandardowych dla kontroli dostępu opartej na rolach platformy Azure](../role-based-access-control/custom-roles.md).


## <a name="assign-rbac-roles-using-the-azure-portal"></a>Przypisywanie ról RBAC za pomocą witryny Azure portal  
Aby dowiedzieć się więcej na temat zarządzania dostępem do zasobów platformy Azure przy użyciu funkcji RBAC i witryny Azure portal, zobacz [ten artykuł](..//role-based-access-control/role-assignments-portal.md). 

Po określiniu odpowiedniego zakresu przypisania roli przejdź do tego zasobu w witrynie Azure Portal. Wyświetl ustawienia kontroli dostępu (IAM) zasobu i postępuj zgodnie z tymi instrukcjami, aby zarządzać przypisaniami ról:

> [!NOTE]
> Opisane poniżej kroki przypisują rolę do obszaru nazw usługi Service Bus. Można wykonać te same kroki, aby przypisać rolę do innych obsługiwanych zakresów (grupa zasobów, subskrypcja itp.).

1. W [witrynie Azure portal](https://portal.azure.com/)przejdź do obszaru nazw usługi Service Bus. Wybierz **pozycję Kontrola dostępu (IAM)** w lewym menu, aby wyświetlić ustawienia kontroli dostępu dla obszaru nazw. Jeśli chcesz utworzyć obszar nazw usługi Service Bus, postępuj zgodnie z instrukcjami z tego artykułu: [Tworzenie obszaru nazw obsługi komunikatów usługi Bus](service-bus-create-namespace-portal.md).

    ![Wybierz kontrolę dostępu w menu po lewej stronie](./media/authenticate-application/select-access-control-menu.png)
1. Wybierz kartę **Przypisania ról,** aby wyświetlić listę przypisań ról. Wybierz przycisk **Dodaj** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przypisanie roli**. 

    ![Przycisk Dodaj na pasku narzędzi](./media/authenticate-application/role-assignments-add-button.png)
1. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności:
    1. Wybierz **rolę usługi Service Bus,** którą chcesz przypisać. 
    1. Wyszukaj **podmiot zabezpieczeń** (użytkownik, grupa, podmiot usługi), do którego chcesz przypisać rolę.
    1. Wybierz **pozycję Zapisz,** aby zapisać przypisanie roli. 

        ![Przypisywanie roli do użytkownika](./media/authenticate-application/assign-role-to-user.png)
    4. Tożsamość, do której przypisano rolę, jest wyświetlana na liście w ramach tej roli. Na przykład na poniższej ilustracji pokazano, że użytkownicy platformy Azure znajduje się w roli właściciela danych usługi Azure Service Bus. 
        
        ![Użytkownik na liście](./media/authenticate-application/user-in-list.png)

Można wykonać podobne kroki, aby przypisać rolę o zakresie do grupy zasobów lub subskrypcji. Po zdefiniowaniu roli i jej zakresu można przetestować to zachowanie za pomocą [przykładów w usłudze GitHub.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)


## <a name="authenticate-from-an-application"></a>Uwierzytelnianie przy użyciu aplikacji
Kluczową zaletą korzystania z usługi Azure AD z usługą Service Bus jest to, że poświadczenia nie muszą być już przechowywane w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2.0 z platformy tożsamości firmy Microsoft. Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupy lub jednostki usługi) z uruchomieniem aplikacji. Jeśli uwierzytelnianie zakończy się pomyślnie, usługa Azure AD zwraca token dostępu do aplikacji, a aplikacja może następnie użyć tokenu dostępu do autoryzowania żądań usługi Azure Service Bus.

W poniższych sekcjach pokazano, jak skonfigurować aplikację macierzystą lub aplikację sieci web do uwierzytelniania za pomocą platformy tożsamości firmy Microsoft 2.0. Aby uzyskać więcej informacji na temat platformy tożsamości firmy Microsoft 2.0, zobacz [omówienie platformy tożsamości firmy Microsoft (w wersji 2.0).](../active-directory/develop/v2-overview.md)

Aby zapoznać się z omówieniem przepływu grantów kodu OAuth 2.0, zobacz [Autoryzowanie dostępu do aplikacji sieci Web usługi Azure Active Directory przy użyciu przepływu dotacji kodu OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi Azure AD
Pierwszym krokiem przy użyciu usługi Azure AD do autoryzowania jednostek usługi Service Bus jest rejestrowanie aplikacji klienckiej za pomocą dzierżawy usługi Azure AD z [witryny Azure portal.](https://portal.azure.com/) Podczas rejestrowania aplikacji klienckiej, należy podać informacje o aplikacji do usługi AD. Usługa Azure AD udostępnia następnie identyfikator klienta (nazywany również identyfikatorem aplikacji), którego można użyć do skojarzenia aplikacji ze środowiska uruchomieniowego usługi Azure AD. Aby dowiedzieć się więcej o identyfikatorze klienta, zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Na poniższych obrazach przedstawiono kroki rejestrowania aplikacji sieci web:

![Rejestrowanie aplikacji](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Jeśli zarejestrujesz aplikację jako aplikację macierzystą, można określić dowolny prawidłowy identyfikator URI dla identyfikatora URI przekierowania. W przypadku aplikacji natywnych ta wartość nie musi być rzeczywistym adresem URL. W przypadku aplikacji sieci web identyfikator URI przekierowania musi być prawidłowym identyfikatorem URI, ponieważ określa adres URL, do którego są dostarczane tokeny.

Po zarejestrowaniu aplikacji w obszarze **Ustawienia**zostanie wyświetlony **identyfikator aplikacji (klienta):**

![Identyfikator wniosku zarejestrowanego](./media/authenticate-application/application-id.png)

Aby uzyskać więcej informacji na temat rejestrowania aplikacji za pomocą usługi Azure AD, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).

> [!IMPORTANT]
> Zanotuj **identyfikatora dzierżawcy** i **identyfikatora aplikacji**. Te wartości będą potrzebne do uruchomienia aplikacji.

### <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta   
Aplikacja potrzebuje klucza tajnego klienta, aby udowodnić swoją tożsamość podczas żądania tokenu. Aby dodać klucz tajny klienta, wykonaj następujące kroki.

1. Przejdź do rejestracji aplikacji w witrynie Azure portal, jeśli nie jesteś jeszcze na stronie.
1. Wybierz **pozycję Certyfikaty & wpisów tajnych** w menu po lewej stronie.
1. W obszarze **Wpisy tajne klienta**wybierz **pozycję Nowy klucz tajny klienta,** aby utworzyć nowy klucz tajny.

    ![Nowy klucz tajny klienta - przycisk](./media/authenticate-application/new-client-secret-button.png)
1. Podaj opis klucza tajnego i wybierz wybrany interwał wygaśnięcia, a następnie wybierz pozycję **Dodaj**.

    ![Dodawanie tajnej strony klienta](./media/authenticate-application/add-client-secret-page.png)
1. Natychmiast skopiuj wartość nowego klucza tajnego do bezpiecznej lokalizacji. Wartość wypełnienia jest wyświetlana tylko raz.

    ![Klucz tajny klienta](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Uprawnienia do interfejsu API usługi Service Bus
Jeśli aplikacja jest aplikacją konsoli, należy zarejestrować aplikację macierzystą i dodać uprawnienia interfejsu API dla **microsoft.ServiceBus** do **zestawu wymaganych uprawnień.** Aplikacje natywne również potrzebują **identyfikatora URI przekierowania** w usłudze Azure AD, który służy jako identyfikator; identyfikator URI nie musi być miejscem docelowym sieci. Użyj `https://servicebus.microsoft.com` w tym przykładzie, ponieważ przykładowy kod już używa tego identyfikatora URI.

### <a name="client-libraries-for-token-acquisition"></a>Biblioteki klientów do pozyskiwania tokenów  
Po zarejestrowaniu aplikacji i przyznaniu jej uprawnień do wysyłania/odbierania danych w usłudze Azure Service Bus, można dodać kod do aplikacji, aby uwierzytelnić podmiot zabezpieczeń i uzyskać token OAuth 2.0. Aby uwierzytelnić i uzyskać token, można użyć jednej z [bibliotek uwierzytelniania platformy tożsamości firmy Microsoft](../active-directory/develop/reference-v2-libraries.md) lub innej biblioteki open source obsługującej OpenID lub Connect 1.0. Aplikacja może następnie użyć tokenu dostępu do autoryzacji żądania względem usługi Azure Service Bus.

Aby uzyskać listę scenariuszy, dla których tokeny pozyskiwania jest obsługiwany, zobacz [scenariusze](https://aka.ms/msal-net-scenarios) sekcji [Biblioteki uwierzytelniania firmy Microsoft (MSAL) dla repozytorium .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub.

## <a name="sample-on-github"></a>Przykład w usłudze GitHub
Zobacz poniższy przykład w usłudze GitHub: [Kontrola dostępu bazy ról dla usługi Service Bus.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl) 

Użyj opcji **Logowanie tajne klienta,** a nie opcji **Interaktywne logowanie użytkownika.** Gdy używasz opcji klucza tajnego klienta, nie jest widoczne okno podręczne. Aplikacja wykorzystuje identyfikator dzierżawy i identyfikator aplikacji do uwierzytelniania. 

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Przed uruchomieniem próbki należy edytować plik **app.config** i, w zależności od scenariusza, ustawić następujące wartości:

- `tenantId`: Ustaw wartość **TenantId.**
- `clientId`: Ustaw wartość **Identyfikator aplikacji.**
- `clientSecret`: Jeśli chcesz zalogować się przy użyciu klucza tajnego klienta, utwórz go w usłudze Azure AD. Ponadto użyj aplikacji sieci web lub interfejsu API zamiast aplikacji natywnej. Ponadto dodaj aplikację w obszarze **Kontrola dostępu (IAM)** w wcześniej utworzonej przestrzeni nazw.
- `serviceBusNamespaceFQDN`: ustawić pełną nazwę DNS nowo utworzonego obszaru nazw usługi Service Bus; na przykład `example.servicebus.windows.net`.
- `queueName`: Ustaw nazwę utworzonej kolejki.
- Identyfikator URI przekierowania określony w aplikacji w poprzednich krokach.

Po uruchomieniu aplikacji konsoli zostanie wyświetlony monit o wybranie scenariusza. Wybierz **interaktywne logowanie użytkownika,** wpisując jego numer i naciskając klawisz ENTER. Aplikacja wyświetla okno logowania, prosi o zgodę na dostęp do usługi Service Bus, a następnie używa usługi do uruchamiania za pośrednictwem scenariusza wysyłania/odbierania przy użyciu tożsamości logowania.


## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej o RBAC, zobacz [Co to jest kontrola dostępu oparta na rolach (RBAC)?](../role-based-access-control/overview.md)
- Aby dowiedzieć się, jak przypisywać przypisania ról RBAC i zarządzać nimi za pomocą programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST, zobacz następujące artykuły:
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu API REST](../role-based-access-control/role-assignments-rest.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą szablonów usługi Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy.

- [Przykłady RBAC usługi Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
- [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
- [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
