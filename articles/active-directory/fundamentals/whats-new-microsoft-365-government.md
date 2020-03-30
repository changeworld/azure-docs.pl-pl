---
title: Co nowego w usłudze Azure AD w usłudze Microsoft 365 dla instytucji rządowych? | Microsoft Docs
description: Dowiedz się więcej o niektórych zmianach w usłudze Azure Active Directory (Azure AD) w wystąpieniu chmury dla instytucji rządowych usługi Microsoft 365, które mogą mieć wpływ na Ciebie.
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
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425548"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Co nowego w usłudze Azure Active Directory w usłudze Microsoft 365 dla instytucji rządowych

Wprowadzono pewne zmiany w usłudze Azure Active Directory (Azure AD) w wystąpieniu chmury usługi Microsoft 365 dla instytucji rządowych, które ma zastosowanie do klientów korzystających z następujących usług:

- Platforma Microsoft Azure dla instytucji rządowych

- Microsoft 365 Rząd – GCC High

- Microsoft 365 rząd — DoD

Ten artykuł nie dotyczy klientów usługi Microsoft 365 government — GCC.

## <a name="changes-to-the-initial-domain-name"></a>Zmiany w początkowej nazwie domeny

Podczas początkowej rejestracji w organizacji do usługi online dla instytucji rządowych usługi Microsoft 365, `<your-domain-name>.onmicrosoft.com`zostałeś poproszony o wybranie nazwy domeny organizacji, . Jeśli masz już nazwę domeny z sufiksem .com, nic się nie zmieni.

Jeśli jednak rejestrujesz się w nowej usłudze microsoft 365 dla instytucji rządowych, zostaniesz poproszony o wybranie nazwy domeny przy użyciu sufiksu. `.us` Tak, to `<your-domain-name>.onmicrosoft.us`będzie .

>[!Note]
>Ta zmiana nie ma zastosowania do klientów zarządzanych przez dostawców usług w chmurze (CSP).

## <a name="changes-to-portal-access"></a>Zmiany w dostępie do portalu

Zaktualizowaliśmy punkty końcowe portalu dla platformy Microsoft Azure Dla instytucji rządowych, microsoft 365 government — GCC High i Microsoft 365 Government — DoD, jak pokazano w [tabeli mapowania punktów końcowych.](#endpoint-mapping)

Wcześniej klienci mogli logował się przy użyciu portali platformy Azure (portal.azure.com) i usługi Office 365 (portal.office.com). Dzięki tej aktualizacji klienci muszą teraz zalogować się przy użyciu określonych portali Microsoft Azure Government, Microsoft 365 Government — GCC High i Microsoft 365 Government — DoD portali.

## <a name="endpoint-mapping"></a>Mapowanie punktu końcowego

W poniższej tabeli przedstawiono punkty końcowe dla wszystkich klientów:

| Nazwa | Szczegóły punktu końcowego |
|------|------------------|
| Portale |Platforma Microsoft Azure dla instytucji rządowych:https://portal.azure.us<p>Microsoft 365 rząd — GCC High:https://portal.office365.us<p>Microsoft 365 rząd — DoD:https://portal.apps.mil |
| Punkt końcowy urzędu usługi Azure Active Directory | https://login.microsoftonline.us |
| Microsoft Graph API dla microsoft 365 rząd - GCC High | https://graph.microsoft.us |
| Interfejs API programu Microsoft Graph dla usługi Microsoft 365 Government — DoD | https://dod-graph.microsoft.us |
| Punkty końcowe usług azure dla instytucji rządowych | Aby uzyskać szczegółowe informacje, zobacz [Przewodnik dla deweloperów platformy Azure dla instytucji rządowych](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government - GCC High endpoints | Aby uzyskać szczegółowe informacje, zobacz [U.S. Government U.S. GCC High endpoints](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government - DoD | Aby uzyskać szczegółowe informacje, zobacz [Punkty końcowe usługi Office 365 dla instytucji rządowych](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w tych artykułach:

- [Co to jest platforma Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Aktualizacja punktu końcowego urzędu instytucji rządowych platformy Azure](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Punkty końcowe programu Microsoft Graph w chmurze instytucji rządowych w Stanach Zjednoczonych](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 Us Government GCC High i DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
