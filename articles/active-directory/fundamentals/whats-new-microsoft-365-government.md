---
title: What's new for Azure Active Directory w programie Microsoft 365 dla instytucji rządowych — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat pewne zmiany do usługi Azure Active Directory (Azure AD) w przypadku chmury Microsoft 365 dla instytucji rządowych mogą mieć wpływ na możesz.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258897"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>What's new for Azure Active Directory w programie Microsoft 365 dla instytucji rządowych

Wprowadziliśmy pewne zmiany do usługi Azure Active Directory (Azure AD) w przypadku chmury Microsoft 365 dla instytucji rządowych jest stosowana do klientów przy użyciu następujących usług:

- Microsoft Azure Government

- Rozwiązania Microsoft 365 dla instytucji rządowych — GCC wysoka

- Rozwiązania Microsoft 365 dla instytucji rządowych — DoD

W tym artykule nie ma zastosowania do firmy Microsoft 365 dla instytucji rządowych — klienci GCC.

## <a name="changes-to-the-initial-domain-name"></a>Zmiany nazwy domeny początkowej

Podczas tworzenia konta usługi online firmy Microsoft 365 dla instytucji rządowych początkowego Twojej organizacji, możesz zostały monit o wybranie nazwy domeny Twojej organizacji, `<your-domain-name>.onmicrosoft.com`. Jeśli masz już nazwę domeny z sufiksem .com, nic nie zmieni się.

Jednakże, jeśli logujesz do nowej usługi Microsoft 365 dla instytucji rządowych, użytkownik zostanie zapytany wybrać nazwę domeny przy użyciu `.us` sufiks. Tak, będzie on `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Ta zmiana nie dotyczy żadnych klientów, którzy są zarządzane przez dostawców usług w chmurze (CSP).

## <a name="changes-to-portal-access"></a>Zmiany dostępu do portalu

Zaktualizowaliśmy portal punktów końcowych dla platformy Microsoft Azure Government, Microsoft 365 dla instytucji rządowych — GCC wysoki i Microsoft 365 dla instytucji rządowych — DoD, jak pokazano na [tabeli mapowania punktów końcowych](#endpoint-mapping).

Wcześniej klienci mogą Zaloguj się przy użyciu Azure na całym świecie (portal.azure.com) i portale usługi Office 365 (portal.office.com). Dzięki tej aktualizacji klienci muszą teraz Zaloguj się przy użyciu określonej platformy Microsoft Azure Government, Microsoft 365 dla instytucji rządowych — GCC wysokiej i Microsoft 365 dla instytucji rządowych — DoD portali.

## <a name="endpoint-mapping"></a>Mapowanie punktu końcowego

W poniższej tabeli przedstawiono punkty końcowe dla wszystkich klientów:

| Name (Nazwa) | szczegóły punktu końcowego |
|------|------------------|
| Portale |Microsoft Azure dla instytucji publicznych: https://portal.azure.us<p>Rozwiązania Microsoft 365 dla instytucji rządowych — GCC wysokiej: https://portal.office365.us<p>Rozwiązania Microsoft 365 Government — DoD: https://portal.apps.mil |
| Punkt końcowy urzędu usługi Azure Active Directory | https://login.microsoftonline.us |
| Interfejs API programu Graph usługi Azure Active Directory | https://graph.windows.net |
| Microsoft interfejsu API programu Graph dla rozwiązania Microsoft 365 dla instytucji rządowych — GCC wysoka | https://graph.microsoft.us |
| Microsoft interfejsu API programu Graph dla rozwiązania Microsoft 365 dla instytucji rządowych — DoD | https://dod-graph.microsoft.us |
| Platforma Azure Government usług punktów końcowych | Aby uzyskać więcej informacji, zobacz [Azure dla instytucji rządowych — przewodnik dewelopera](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Rozwiązania Microsoft 365 dla instytucji rządowych — GCC wysokiej punktów końcowych | Aby uzyskać więcej informacji, zobacz [Office 365 w Stanach Zjednoczonych Wysoka GCC punktów końcowych dla instytucji rządowych](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Rozwiązania Microsoft 365 dla instytucji rządowych — DoD | Aby uzyskać więcej informacji, zobacz [Office 365 w Stanach Zjednoczonych Punkty końcowe DoD dla instytucji rządowych](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji można znaleźć w tych artykułach:

- [Co to jest platforma Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Aktualizacja punktu końcowego urzędu usługi AAD dla platformy Azure dla instytucji rządowych](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Punkty końcowe programu Microsoft Graph, w chmurze dla instytucji rządowych USA](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Usługi Office 365 dla instytucji rządowych USA GCC wysoki i DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)