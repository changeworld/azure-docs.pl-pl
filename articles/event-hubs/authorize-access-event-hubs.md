---
title: Autoryzacja dostępu do usługi Azure Event Hubs
description: Ten artykuł zawiera informacje o różnych opcjach autoryzowania dostępu do zasobów usługi Azure Event Hubs.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: f44be4e1d3d1186f0122bd4669ae800ab42e31d6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521308"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autoryzacja dostępu do usługi Azure Event Hubs
Za każdym razem, gdy publikujesz lub zużywasz zdarzenia/dane z centrum zdarzeń, klient próbuje uzyskać dostęp do zasobów centrum zdarzeń. Każde żądanie do bezpiecznego zasobu musi być autoryzowane, aby usługa mogła zapewnić, że klient ma wymagane uprawnienia do publikowania/korzystania z danych. 

Usługa Azure Event Hubs oferuje następujące opcje autoryzowania dostępu do bezpiecznych zasobów:

- Usługa Azure Active Directory
- Sygnatura dostępu współdzielonego

> [!NOTE]
> Ten artykuł dotyczy zarówno centrów zdarzeń, jak i scenariuszy [platformy Apache Kafka.](event-hubs-for-kafka-ecosystem-overview.md) 

## <a name="azure-active-directory"></a>Usługa Azure Active Directory
Integracja usługi Azure Active Directory (Azure AD) dla zasobów usługi Event Hubs zapewnia kontrolę dostępu opartą na rolach (RBAC) dla szczegółowej kontroli nad dostępem klienta do zasobów. Kontroli dostępu opartej na rolach (RBAC) można użyć do udzielania uprawnień podmiotowi zabezpieczeń, który może być użytkownikiem, grupą lub jednostką usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniony przez usługę Azure AD w celu zwrócenia tokenu OAuth 2.0. Token może służyć do autoryzowania żądania dostępu do zasobu Usługi Event Hubs.

Aby uzyskać więcej informacji na temat uwierzytelniania za pomocą usługi Azure AD, zobacz następujące artykuły:

- [Uwierzytelnij żądania w centrach zdarzeń platformy Azure przy użyciu usługi Azure Active Directory](authenticate-application.md)
- [Autoryzuj dostęp do zasobów Centrum zdarzeń przy użyciu usługi Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Podpisy dostępu współdzielonego 
Sygnatury dostępu współdzielonego (SAS) dla zasobów centrum zdarzeń zapewniają ograniczony dostęp delegowany do zasobów centrum zdarzeń. Dodawanie ograniczeń dotyczących przedziału czasu, dla którego podpis jest prawidłowy lub uprawnień, które przyznaje, zapewnia elastyczność w zarządzaniu zasobami. Aby uzyskać więcej informacji, zobacz [Uwierzytelnij przy użyciu sygnatur dostępu współdzielonego (SAS).](authenticate-shared-access-signature.md) 

Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2.0 zwróconego przez usługę Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia za pośrednictwem sygnatur dostępu współdzielonego (SAS). Dzięki usłudze Azure AD nie ma potrzeby przechowywania tokenów dostępu z kodem i potencjalnymi lukami w zabezpieczeniach. Chociaż można nadal używać podpisów dostępu współdzielonego (SAS) do udzielania szczegółowego dostępu do zasobów usługi Event Hubs, usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokenami sygnatury dostępu Współdzielonego lub martwić się o odwołanie naruszonego sygnatury dostępu Współdzielonego. 

Domyślnie wszystkie zasoby usługi Event Hubs są zabezpieczone i są dostępne tylko dla właściciela konta. Chociaż można użyć dowolnej ze strategii autoryzacji opisanych powyżej, aby udzielić klientom dostępu do zasobów usługi Event Hub. Firma Microsoft zaleca korzystanie z usługi Azure AD, jeśli to możliwe, aby zapewnić maksymalne bezpieczeństwo i łatwość użycia.

Aby uzyskać więcej informacji na temat autoryzacji przy użyciu sygnatury dostępu współdzielonego, zobacz [Autoryzowanie dostępu do zasobów Centrum zdarzeń przy użyciu podpisów dostępu współdzielonego](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Następne kroki
- Przejrzyj [przykłady RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) opublikowane w naszym repozytorium GitHub. 
- Zobacz następujące artykuły:
    - [Uwierzytelnij żądania do usługi Azure Event Hubs z aplikacji przy użyciu usługi Azure Active Directory](authenticate-application.md)
    - [Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Event Hubs](authenticate-managed-identity.md)
    - [Uwierzytelnij żądania w centrach zdarzeń platformy Azure przy użyciu podpisów dostępu współdzielonego](authenticate-shared-access-signature.md)
    - [Autoryzowanie dostępu do zasobów usługi Event Hubs przy użyciu usługi Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autoryzowanie dostępu do zasobów Centrum zdarzeń przy użyciu podpisów dostępu współdzielonego](authorize-access-shared-access-signature.md)

