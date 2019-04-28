---
title: Azure Service Bus Role-Based kontroli dostępu (RBAC) w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Kontroli dostępu opartej na rolach w usłudze Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: aschhab
ms.openlocfilehash: 7ef152b130e77e833e19c51ff97d0cea577216c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472254"
---
# <a name="active-directory-role-based-access-control-preview"></a>Kontrola dostępu w usłudze Active Directory Role-Based (wersja zapoznawcza)

Microsoft Azure oferuje zarządzanie kontrolą dostępu zintegrowanej dla zasobów i aplikacji opartych na usłudze Azure Active Directory (Azure AD). Za pomocą usługi Azure AD, albo można zarządzać kontami użytkowników i aplikacji opartych na aplikacji dla platformy Azure lub może tworzyć federacje istniejącą infrastrukturę usługi Active Directory z usługą Azure AD dla całej firmy, logowanie jednokrotne, obejmującej zasobów platformy Azure a aplikacje hostowane w systemie Azure. Następnie można przypisać te tożsamości użytkowników i aplikacji usługi Azure AD do globalnych i specyficznych dla usługi ról w celu udzielenia dostępu do zasobów platformy Azure.

Dla usługi Azure Service Bus, zarządzanie przestrzenie nazw i wszystkie pokrewne zasoby za pośrednictwem witryny Azure portal i zarządzanie zasobami platformy Azure, interfejsu API jest już chroniony przy użyciu *kontroli dostępu opartej na rolach* modelu (RBAC). RBAC dla operacji środowiska uruchomieniowego jest funkcją teraz w publicznej wersji zapoznawczej.

Aplikacja, która korzysta z usługi Azure AD RBAC nie trzeba obsługiwać zasady sygnatury dostępu Współdzielonego i klucze lub innych tokenów dostępu, specyficzne dla usługi Service Bus. Aplikacja kliencka wchodzi w interakcję z usługą Azure AD, aby ustanowić kontekstu uwierzytelniania, a następnie uzyskuje token dostępu dla usługi Service Bus. Przy użyciu kont użytkowników domeny, które wymagają logowania interakcyjnego aplikacja nigdy nie obsługuje żadnych poświadczeń bezpośrednio.

## <a name="service-bus-roles-and-permissions"></a>Uprawnienia i role usługi Service Bus

Początkowej publicznej wersji zapoznawczej można tylko dodać konta usługi Azure AD i nazwy główne usług do ról "Właściciel" lub "Współautor" w przestrzeni nazw komunikatów usługi Service Bus. Ta operacja spowoduje przydzielenie tożsamości pełną kontrolę wszystkich jednostek w przestrzeni nazw. Operacje zarządzania, które zmienić topologii przestrzeni nazw są początkowo tylko obsługiwane do usługi Azure resource management i nie przy użyciu natywnego interfejsu zarządzania interfejsu REST usługi Service Bus. Ta obsługa również oznacza, że klienta .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) obiektu nie można używać z kontem usługi Azure AD.

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Usługa Service Bus za pomocą konta użytkownika domeny usługi Azure AD

W poniższej sekcji opisano kroki wymagane do tworzenia i uruchamiania przykładowej aplikacji, która wyświetla monit dotyczący interaktywnego usługi Azure AD użytkownik był zalogowany, jak udzielić dostępu usługi Service Bus z tym kontem użytkownika i jak dostęp do usługi Event Hubs za pomocą tej tożsamości.

Tego wprowadzenia opisano prostej aplikacji konsolowej, [kodu, dla którego znajduje się w witrynie GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Tworzenie konta użytkownika usługi Active Directory

Ten pierwszy krok jest opcjonalny. Każda subskrypcja platformy Azure jest automatycznie skojarzone z dzierżawą usługi Azure Active Directory, i jeśli użytkownik ma dostęp do subskrypcji platformy Azure, Twoje konto użytkownika jest już zarejestrowany. Oznacza to, że wystarczy użyć swojego konta.

Jeśli nadal chcesz utworzyć konto określone w tym scenariuszu [wykonaj następujące kroki](../automation/automation-create-aduser-account.md). Musi mieć uprawnienia do tworzenia kont w dzierżawie usługi Azure Active Directory, która nie może być w przypadku większych scenariuszach dla przedsiębiorstw.

### <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus

Następnie [tworzenie przestrzeni nazw komunikatów usługi Service Bus](service-bus-create-namespace-portal.md) w jednym z regionów świadczenia usługi Azure, które mają wersję zapoznawczą obsługi kontroli RBAC: **Wschodnie stany USA**, **wschodnie stany USA 2**, lub **Europa Zachodnia**.

Po utworzeniu przestrzeni nazw, przejdź do jej **kontrola dostępu (IAM)** strony w portalu, a następnie kliknij przycisk **Dodaj przypisanie roli** można dodać konto użytkownika usługi Azure AD do roli właściciel. Jeśli używasz konta użytkownika, a następnie utworzono przestrzeń nazw, użytkownik jest już w roli właściciela. Aby dodać innego konta do roli, wyszukaj nazwę aplikacji sieci web w **Dodaj uprawnienia** panelu **wybierz** pola, a następnie kliknij pozycję. Następnie kliknij przycisk **Save** (Zapisz).

Konto użytkownika ma teraz dostęp do przestrzeni nazw usługi Service Bus, a w kolejce został wcześniej utworzony.

### <a name="register-the-application"></a>Rejestrowanie aplikacji

Przed uruchomieniem przykładowej aplikacji, zarejestruj go w usłudze Azure AD i zatwierdź monit o wyrażenie zgody, która umożliwia aplikacji dostęp do usługi Azure Service Bus w jej imieniu.

Ponieważ Przykładowa aplikacja to aplikacja konsoli, musisz zarejestrować aplikacji natywnej i Dodaj uprawnienia do interfejsu API dla **elementu Microsoft.ServiceBus** do zestawu "required uprawnienia". Natywne aplikacje wymagają także **identyfikatora URI przekierowania** w usłudze Azure AD, która służy jako identyfikatora; identyfikator URI musi być miejsce docelowe w sieci. Użyj `https://servicebus.microsoft.com` w tym przykładzie ponieważ próbki kodu już używa tego identyfikatora URI.

Kroki szczegółowe rejestracji są wyjaśnione w [w tym samouczku](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Postępuj zgodnie z instrukcjami Aby zarejestrować **natywnych** aplikacji, a następnie postępuj zgodnie z instrukcjami aktualizacji, aby dodać **elementu Microsoft.ServiceBus** interfejsu API, aby wymagane uprawnienia. Kroki, zanotuj **TenantId** i **ApplicationId**, ponieważ będzie potrzebny tych wartości, aby uruchomić aplikację.

### <a name="run-the-app"></a>Uruchamianie aplikacji

Przed uruchomieniem próbki, Edytuj plik App.config i zależnie od scenariusza, ustaw następujące wartości:

- `tenantId`: Ustaw **TenantId** wartości.
- `clientId`: Ustaw **ApplicationId** wartość.
- `clientSecret`: Jeśli chcesz zalogować się przy użyciu klucza tajnego klienta, należy go utworzyć w usłudze Azure AD. Ponadto używane zamiast aplikacji natywnej aplikacji sieci web lub interfejsu API. Ponadto Dodaj aplikację w obszarze **kontrola dostępu (IAM)** w przestrzeni nazw utworzone wcześniej.
- `serviceBusNamespaceFQDN`: Ustaw na pełną nazwę DNS nowo utworzoną przestrzeń nazw usługi Service Bus; na przykład `example.servicebus.windows.net`.
- `queueName`: Ustaw nazwę kolejki, który został utworzony.
- Identyfikator URI przekierowania określone w aplikacji w poprzednich krokach.

Po uruchomieniu aplikacji konsoli, monit o wybór scenariusza; Kliknij przycisk **interakcyjnego logowania użytkownika** , wpisując jego numer, a następnie naciskając klawisz ENTER. Aplikacja wyświetli okno logowania, poprosi o podanie Twojej zgody na dostęp do usługi Service Bus i następnie używa usługi, aby uruchamiać funkcję wysyłania i odbierania scenariusza, przy użyciu tożsamości logowania.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy.

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
