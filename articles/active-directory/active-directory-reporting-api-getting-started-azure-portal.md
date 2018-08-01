---
title: Rozpoczynanie pracy z usługą Azure AD, interfejsu API raportowania | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z usługą Azure Active Directory, interfejsu API raportowania
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 89562e6a2bfb977585cec1925a5f306c69c3d1e8
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390674"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Rozpoczynanie pracy z usługą Azure Active Directory, interfejsu API raportowania

Usługa Azure Active Directory oferuje szereg [raporty](active-directory-reporting-azure-portal.md). Dane z tych raportów mogą być bardzo przydatne w aplikacjach, takich jak systemy SIEM oraz narzędzia do inspekcji i analizy biznesowej. 

Za pomocą interfejsu API raportowania usługi Azure AD, możesz uzyskać programowy dostęp do danych za pomocą zestawu interfejsów API, oparte na protokole REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania.

Ten artykuł zawiera plan działania do uzyskiwania dostępu do danych raportowania przy użyciu powiązanych interfejsu API.

Jeśli napotkasz problemy, zobacz [jak uzyskać pomoc techniczną dotyczącą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do interfejsu API raportowania, nawet jeśli planujesz uzyskiwanie dostępu do interfejsu API za pomocą skryptu, należy:

1. Przypisz role (Administrator globalny Czytelnik zabezpieczeń, administratora zabezpieczeń)
2. Rejestrowanie aplikacji
3. Udzielenie uprawnień
4. Zbieranie ustawień konfiguracji

Aby uzyskać szczegółowe instrukcje, zobacz [wymagania wstępne dotyczące dostępu do usługi Azure Active Directory reporting API](active-directory-reporting-api-prerequisites-azure-portal.md).

## <a name="apis-with-graph-explorer"></a>Interfejsy API za pomocą Eksploratora programu Graph

Możesz użyć [MSGraph explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) w celu sprawdzenia logowania i inspekcji danych interfejsu API. Upewnij się, że Zaloguj się do swojego konta przy użyciu zarówno przycisków logowania w Interfejsie użytkownika programu Graph Explorer i ustaw **AuditLog.Read.All** i **Directory.Read.All** uprawnienia dla swojej dzierżawy, jak pokazano.   

![Graph Explorer](./media/active-directory-reporting-api-getting-started-azure-portal/graph-explorer.png)

![Modyfikowanie uprawnień interfejsu użytkownika](./media/active-directory-reporting-api-getting-started-azure-portal/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Dostęp do interfejsu API raportowania usługi Azure AD za pomocą przystawki Certyfikaty 

Należy rozważyć użycie usługi Azure AD interfejsu API raportowania za pomocą certyfikatów, jeśli planowane jest pobrać dane raportów bez interwencji użytkownika.

Aby uzyskać szczegółowe instrukcje, zobacz [pobieranie danych przy użyciu usługi Azure API raportowania usługi AD z certyfikatami](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Eksploruj

Pobierz pierwsze wrażenie interfejsy API raportowania:
   
   - [Korzystanie z przykładów dla inspekcji interfejsu API](active-directory-reporting-api-audit-samples.md) 
   - [Korzystanie z przykładów w raporcie logowań interfejsu API](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="next-steps"></a>Kolejne kroki

 * [Dokumentacja interfejsu API inspekcji](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 * [Raport aktywności logowania, dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
 * [Rozwiązywanie problemów z błędami w usłudze Azure AD, interfejsu API raportowania](active-directory-reporting-troubleshoot-graph-api.md)


