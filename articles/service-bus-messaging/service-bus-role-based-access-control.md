---
title: Azure w wersji zapoznawczej Service Bus Role-Based kontroli dostępu (RBAC) | Dokumentacja firmy Microsoft
description: Kontroli dostępu opartej na rolach w usłudze Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 729d6db6b2fc6495ffb0f4fbe4d545d7ad953cef
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783452"
---
# <a name="active-directory-role-based-access-control-preview"></a>Kontrola dostępu w usłudze Active Directory Role-Based (wersja zapoznawcza)

Microsoft Azure zapewnia zarządzanie kontrolą zintegrowanego dostępu do zasobów i aplikacje oparte na usłudze Azure Active Directory (Azure AD). Z usługą Azure AD, albo można zarządzać kontami aplikacji opartych na aplikacji specjalnie dla platformy Azure i można było wykonać Federację istniejącą infrastrukturę usługi Active Directory z usługą Azure AD dla całej firmy single-sign-on, obejmującej zasobów platformy Azure i Azure obsługiwanych aplikacji. Następnie można przypisać tych tożsamości użytkownika i aplikacji usługi Azure AD do globalnej i specyficzne dla usługi ról w celu udzielenia dostępu do zasobów platformy Azure.

Dla usługi Azure Service Bus, zarządzania obszary nazw i wszystkich powiązanych zasobów za pośrednictwem portalu Azure i zarządzania zasobami Azure interfejsu API jest już chroniona za pomocą *kontroli dostępu opartej na rolach* modelu (RBAC). RBAC dla operacji obsługi jest funkcją w publicznej wersji zapoznawczej. 

Aplikację, która używa usługi Azure AD RBAC nie trzeba samodzielnie zasady sygnatury dostępu Współdzielonego i klucze lub innych tokenów dostępu, specyficzne dla usługi Service Bus. Aplikacja kliencka wchodzi w interakcję z usługą Azure AD, aby ustanowić kontekst uwierzytelniania i uzyskuje token dostępu dla usługi Service Bus. Z konta użytkownika domeny, które wymagają logowania interakcyjnego aplikacja nigdy nie obsługuje wszystkie poświadczenia bezpośrednio.

## <a name="service-bus-roles-and-permissions"></a>Magistrala usług ról i uprawnień

Początkowej publicznej wersji zapoznawczej można tylko dodawać konta usługi Azure AD i nazwy główne usług do ról "Właściciela" lub "Współautora" w przestrzeni nazw usługi Service Bus do obsługi komunikatów. Ta operacja przyznaje tożsamości pełną kontrolę nad wszystkich jednostek w przestrzeni nazw. Operacje zarządzania, które zmienić topologii przestrzeni nazw są początkowo tylko obsługiwane zarządzanie zasobami jednak Azure, a nie za pomocą natywnego interfejsu zarządzania REST magistrali usługi. Ta obsługa również oznacza, że klient .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nie można użyć obiektu przy użyciu konta usługi Azure AD.  

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Używanie usługi Service Bus przy użyciu konta użytkownika domeny usługi Azure AD

W poniższej sekcji opisano kroki wymagane do tworzenia i uruchamiania przykładowej aplikacji, która wyświetla monit dotyczący interakcyjne Azure do zalogowania użytkownika AD, sposób przyznania dostępu do usługi Service Bus do tego konta użytkownika i sposobu używania tej tożsamości można uzyskać dostępu do usługi Event Hubs. 

To wprowadzenie opisuje prostej aplikacji konsolowej, [kod jest w serwisie Github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Tworzenie konta użytkownika usługi Active Directory

Ten pierwszy krok jest opcjonalny. Każda subskrypcja platformy Azure automatycznie łączyć się z dzierżawy usługi Azure Active Directory i czy masz dostęp do subskrypcji platformy Azure, konto użytkownika jest już zarejestrowany. Oznacza to, że można użyć Twojego konta. 

Jeśli nadal chcesz utworzyć konto określone w tym scenariuszu [wykonaj następujące kroki](../automation/automation-create-aduser-account.md). Musi mieć uprawnienia do tworzenia konta w dzierżawie usługi Azure Active Directory, które nie mogą być w przypadku większych scenariuszach dla przedsiębiorstwa.

### <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus

Następnie [tworzenie przestrzeni nazw usługi magistrali komunikatów](service-bus-create-namespace-portal.md) w jednym z regiony platformy Azure, które obsługują podglądu RBAC: **nam wschodnie**, **nam wschodnie 2**, lub **Europa Zachodnia** . 

Po utworzeniu przestrzeni nazw, przejdź do jego **kontroli dostępu (IAM)** w portalu, a następnie kliknij przycisk **Dodaj** Aby dodać konto użytkownika usługi Azure AD do roli właściciela. Jeśli używasz konta użytkownika i tworzenia przestrzeni nazw, masz już rolę właściciela. Aby dodać innego konta do roli, wyszukaj nazwę aplikacji sieci web w **dodać uprawnienia** panelu **wybierz** pola, a następnie kliknij polecenie wpisu. Następnie kliknij przycisk **Save** (Zapisz).

![](./media/service-bus-role-based-access-control/rbac1.PNG)

Konto użytkownika ma teraz dostęp do obszaru nazw usługi Service Bus, a do kolejki wcześniej została utworzona.
 
### <a name="register-the-application"></a>Zarejestrować aplikację

Przed uruchomieniem aplikacji przykładowej, zarejestruj go w usłudze Azure AD i zatwierdzić monit o zgodę, który umożliwia aplikacjom dostęp do usługi Azure Service Bus w jej imieniu. 

Ponieważ do aplikacji przykładowej aplikacji konsoli, musisz zarejestrować aplikacji natywnej i Dodaj uprawnienia interfejsu API dla **Microsoft.ServiceBus** do zestawu "wymaga uprawnień". Natywnych aplikacji może też być konieczne **identyfikatora URI przekierowania** w usłudze Azure AD, która służy jako identyfikator; identyfikator URI musi być miejsce docelowe w sieci. Użyj `http://servicebus.microsoft.com` w tym przykładzie, ponieważ próbki kodu już używa tego identyfikatora URI.

Kroki szczegółowe rejestracji opisano szczegółowo w [w tym samouczku](../active-directory/develop/active-directory-integrating-applications.md). Wykonaj kroki, aby zarejestrować **natywnego** aplikacji, a następnie postępuj zgodnie z instrukcjami aktualizacji, aby dodać **Microsoft.ServiceBus** interfejsu API do wymaganych uprawnień. Kroki zostaną wykonane, zwróć uwagę na **TenantId** i **ApplicationId**, ponieważ będzie potrzebny tych wartości, aby uruchomić aplikację.

### <a name="run-the-app"></a>Uruchomienie aplikacji

Przed uruchomieniem próbki, Edytuj plik App.config i w zależności od scenariusza, ustaw następujące wartości:

- `tenantId`: Ustawioną **TenantId** wartości.
- `clientId`: Ustawioną **ApplicationId** wartość. 
- `clientSecret`: Jeśli chcesz zalogować się przy użyciu klucza tajnego klienta, należy go utworzyć w usłudze Azure AD. Należy także użyć zamiast aplikacji natywnej aplikacji sieci web lub interfejsu API. Ponadto Dodaj aplikację w obszarze **kontroli dostępu (IAM)** w przestrzeni nazw została wcześniej utworzona.
- `serviceBusNamespaceFQDN`: Ustaw na pełną nazwę DNS nowo utworzonego obszaru nazw usługi Service Bus; na przykład `example.servicebus.windows.net`.
- `queueName`: Ustaw nazwę kolejki, który został utworzony.
- Identyfikator URI przekierowania określony w aplikacji w poprzednich krokach.
 
Po uruchomieniu aplikacji konsoli zostanie wyświetlony monit o wybranie scenariusza; Kliknij przycisk **interakcyjnego logowania użytkownika** wpisując jego numeru i naciskając klawisz ENTER. Aplikacja wyświetla okno logowania, pyta o Twojej zgody na dostęp do usługi Service Bus, a następnie używa usługi ma być uruchamiana w scenariuszu wysyłania i odbierania za pomocą tożsamości logowania.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy.

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)