---
title: Autoryzuj dostęp za pomocą Azure Active Directory
description: Ten artykuł zawiera informacje dotyczące autoryzowania dostępu do zasobów Event Hubs przy użyciu Azure Active Directory.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1a42843cc81070cc284863b3736549576e32cb17
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011875"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Autoryzuj dostęp do zasobów Event Hubs przy użyciu Azure Active Directory
Usługa Azure Event Hubs obsługuje używanie Azure Active Directory (Azure AD) do autoryzacji żądań Event Hubs zasobów. Za pomocą usługi Azure AD można używać kontroli dostępu opartej na rolach (RBAC) do udzielania uprawnień podmiotowi zabezpieczeń, który może być użytkownikiem, grupą lub jednostką usługi aplikacji. Aby dowiedzieć się więcej o rolach i przypisaniach ról, zobacz [opis różnych ról](../role-based-access-control/overview.md).

## <a name="overview"></a>Omówienie
Gdy podmiot zabezpieczeń (użytkownik, Grupa lub aplikacja) próbuje uzyskać dostęp do zasobu Event Hubs, żądanie musi być autoryzowane. W przypadku usługi Azure AD dostęp do zasobu jest procesem dwuetapowym. 

 1. Najpierw jest uwierzytelniana tożsamość podmiotu zabezpieczeń i zwracany jest token OAuth 2,0. 
 1. Następnie token jest przesyłany w ramach żądania do usługi Event Hubs, aby autoryzować dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak maszyna wirtualna platformy Azure, zestaw skalowania maszyn wirtualnych lub aplikacja funkcji platformy Azure, może używać tożsamości zarządzanej do uzyskiwania dostępu do zasobów. Aby dowiedzieć się, jak uwierzytelniać żądania wysyłane przez zarządzaną tożsamość do usługi Event Hubs, zobacz temat [uwierzytelnianie dostępu do zasobów usługi azure Event Hubs za pomocą Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](authenticate-managed-identity.md). 

Krok autoryzacji wymaga, aby co najmniej jedna rola RBAC była przypisana do podmiotu zabezpieczeń. Usługa Azure Event Hubs oferuje role RBAC, które obejmują zestawy uprawnień dla Event Hubs zasobów. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia, które będą miały. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [wbudowane role RBAC dla usługi Azure Event Hubs](#built-in-rbac-roles-for-azure-event-hubs). 

Aplikacje natywne i aplikacje sieci Web, które wysyłają żądania do Event Hubs mogą również autoryzować się z usługą Azure AD. Aby dowiedzieć się, jak zażądać tokenu dostępu i używać go do autoryzacji żądań dotyczących zasobów Event Hubs, zobacz temat [uwierzytelnianie dostępu do usługi azure Event Hubs za pomocą usługi Azure AD z poziomu aplikacji](authenticate-application.md). 

## <a name="assign-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC na potrzeby praw dostępu
Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md). Usługa Azure Event Hubs definiuje zestaw wbudowanych ról RBAC, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do danych centrum zdarzeń, a także definiuje role niestandardowe na potrzeby uzyskiwania dostępu do danych.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów, przestrzeni nazw Event Hubs lub dowolnego zasobu w ramach tego elementu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, główną usługą aplikacji lub zarządzaną tożsamością [dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Wbudowane role RBAC dla usługi Azure Event Hubs
Platforma Azure udostępnia następujące wbudowane role RBAC do autoryzowania dostępu do danych Event Hubs przy użyciu usługi Azure AD i uwierzytelniania OAuth:

- [Właściciel danych Event Hubs platformy Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Ta rola umożliwia pełen dostęp do zasobów Event Hubs.
- [Nadawca danych Event Hubs platformy Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Ta rola służy do nadawania dostępu Event Hubs do zasobów.
- [Usługa Azure Event Hubs Data Receiver](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Ta rola umożliwia nadanie dostępu do zasobów Event Hubs lub otrzymywanie do nich.

## <a name="resource-scope"></a>Zakres zasobów 
Przed przypisaniem roli RBAC do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, w których można określić zakres dostępu do zasobów Event Hubs, rozpoczynając od najwęższego zakresu:

- **Grupy użytkowników**: W tym zakresie przypisanie roli ma zastosowanie tylko do tej jednostki. Obecnie Azure Portal nie obsługuje przypisywania roli RBAC do podmiotu zabezpieczeń na tym poziomie. 
- **Centrum zdarzeń**: Przypisanie roli dotyczy jednostki centrum zdarzeń i grupy konsumentów.
- **Przestrzeń nazw**: Przypisanie roli obejmuje całą topologię Event Hubs w przestrzeni nazw oraz do skojarzonej z nią grupy odbiorców.
- **Grupa zasobów**: Przypisanie roli dotyczy wszystkich zasobów Event Hubs w grupie zasobów.
- **Subskrypcja**: Przypisanie roli dotyczy wszystkich zasobów Event Hubs we wszystkich grupach zasobów w subskrypcji.

> [!NOTE]
> Należy pamiętać, że propagacja ról RBAC może potrwać do 5 minut. 

Aby uzyskać więcej informacji na temat sposobu definiowania wbudowanych ról, zobacz [Omówienie definicji ról](../role-based-access-control/role-definitions.md#management-and-data-operations). Aby uzyskać informacje na temat tworzenia niestandardowych ról RBAC, zobacz [Tworzenie ról niestandardowych dla Access Control opartej na rolach](../role-based-access-control/custom-roles.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak przypisać wbudowaną rolę RBAC do podmiotu zabezpieczeń, zobacz temat [uwierzytelnianie dostępu do zasobów Event Hubs przy użyciu Azure Active Directory](authenticate-application.md).
- Dowiedz się [, jak tworzyć role niestandardowe przy użyciu RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Dowiedz się [, jak używać Azure Active Directory z usługą EH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Zapoznaj się z następującymi artykułami:

- [Uwierzytelniaj żądania do Event Hubs platformy Azure z aplikacji przy użyciu Azure Active Directory](authenticate-application.md)
- [Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-managed-identity.md)
- [Uwierzytelnianie żądań na platformie Azure Event Hubs przy użyciu sygnatur dostępu współdzielonego](authenticate-shared-access-signature.md)
- [Autoryzuj dostęp do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego](authorize-access-shared-access-signature.md)