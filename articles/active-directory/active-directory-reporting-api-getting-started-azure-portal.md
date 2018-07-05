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
ms.openlocfilehash: 93532f4b0b2d527a4d5c79e2ee1b2810394b2f11
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442087"
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

Możesz użyć [MSGraph explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) w celu sprawdzenia logowania i inspekcji danych interfejsu API. Upewnij się, że Zaloguj się do swojego konta przy użyciu zarówno przycisków logowania w Interfejsie użytkownika programu Graph Explorer i ustaw **Tasks.ReadWrite** i **Directory.ReadAll** uprawnienia dla swojej dzierżawy, jak pokazano.   

![Graph Explorer](./media/active-directory-reporting-api-getting-started-azure-portal/graph-explorer.png)

![Modyfikowanie uprawnień interfejsu użytkownika](./media/active-directory-reporting-api-getting-started-azure-portal/modify-permissions.png)

## <a name="recommendation"></a>Zalecenie 

Jeśli planowane jest na pobieranie danych raportowania, bez interwencji użytkownika, należy rozważyć użycie usługi Azure AD interfejsu API raportowania za pomocą certyfikatów.

Aby uzyskać szczegółowe instrukcje, zobacz [pobieranie danych przy użyciu usługi Azure API raportowania usługi AD z certyfikatami](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Eksploruj

Pobierz pierwsze wrażenie interfejsy API raportowania:
   
   - [Korzystanie z przykładów dla inspekcji interfejsu API](active-directory-reporting-api-audit-samples.md) 
 
   - [Korzystanie z przykładów w raporcie logowań interfejsu API](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Dostosowywanie  

Tworzenie własnego rozwiązania: 
   
   - [Za pomocą inspekcji, dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [Za pomocą odwołania raportu interfejsu API logowań](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



