---
title: Wprowadzenie do interfejsu API raportowania usługi Azure AD | Dokumenty firmy Microsoft
description: Jak rozpocząć pracę z interfejsem API raportowania usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56f240a5191dd483f89889f3ffe13b1819ca1e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399327"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Wprowadzenie do interfejsu API raportowania usługi Azure Active Directory

Usługa Azure Active Directory udostępnia wiele [raportów](overview-reports.md)zawierających przydatne informacje dotyczące aplikacji, takich jak systemy SIEM, inspekcja i narzędzia analizy biznesowej. 

Korzystając z interfejsu API programu Microsoft Graph dla raportów usługi Azure AD, można uzyskać programowy dostęp do danych za pośrednictwem zestawu interfejsów API opartych na rest. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania.

Ten artykuł zawiera omówienie interfejsu API raportowania, w tym sposobów dostępu do niego.

Jeśli napotkasz problemy, [zobacz, jak uzyskać pomoc techniczną dla usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do interfejsu API raportowania, z interwencją użytkownika lub bez niej, należy:

1. Przypisywanie ról (czytnik zabezpieczeń, administrator zabezpieczeń, administrator globalny)
2. Rejestrowanie aplikacji
3. Udzielenie uprawnień
4. Zbieranie ustawień konfiguracji

Aby uzyskać szczegółowe instrukcje, zobacz [wymagania wstępne dostępu do interfejsu API raportowania usługi Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>Punkty końcowe interfejsu API 

Punktem końcowym interfejsu API programu Microsoft `https://graph.microsoft.com/beta/auditLogs/directoryAudits` Graph dla dzienników inspekcji jest punkt `https://graph.microsoft.com/beta/auditLogs/signIns`końcowy interfejsu API programu Microsoft Graph dla logowań. Aby uzyskać więcej informacji, zobacz [odwołanie do interfejsu API inspekcji](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) i odwołanie do interfejsu API [logowania](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

Ponadto można użyć [interfejsu API wykrywania ryzyka ochrony tożsamości,](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) aby uzyskać programowy dostęp do wykrywania zabezpieczeń za pomocą programu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Active Directory Identity Protection i Microsoft Graph](../identity-protection/graph-get-started.md). 
  
## <a name="apis-with-microsoft-graph-explorer"></a>Interfejsy API z Eksploratorem wykresów firmy Microsoft

Za pomocą [Eksploratora programu Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) można zweryfikować dane interfejsu API logowania i inspekcji. Upewnij się, że zalogujesz się do swojego konta przy użyciu obu przycisków logowania w interfejsie użytkownika Graph Explorer i ustaw **uprawnienia AuditLog.Read.All** i **Directory.Read.All** dla dzierżawy, jak pokazano.   

![Eksplorator programu Graph](./media/concept-reporting-api/graph-explorer.png)

![Modyfikowanie interfejsu użytkownika uprawnień](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Uzyskiwanie dostępu do interfejsu API raportowania usługi Azure AD za pomocą certyfikatów 

Użyj interfejsu API raportowania usługi Azure AD z certyfikatami, jeśli planujesz pobrać dane raportowania bez interwencji użytkownika.

Aby uzyskać szczegółowe instrukcje, zobacz [Pobierz dane przy użyciu interfejsu API raportowania usługi Azure AD reporting z certyfikatami](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Następne kroki

 * [Wymagania wstępne dostępu do interfejsu API raportowania](howto-configure-prerequisites-for-reporting-api.md) 
 * [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure AD z certyfikatami](tutorial-access-api-with-certificates.md)
 * [Rozwiązywanie problemów z błędami w interfejsie API raportowania usługi Azure AD](troubleshoot-graph-api.md)


