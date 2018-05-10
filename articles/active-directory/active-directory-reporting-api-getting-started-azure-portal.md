---
title: Wprowadzenie do raportowania interfejsu API usługi Azure AD | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z usługą Active Directory Azure, interfejsu API raportowania
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a818c9b0e2c9097f45d2fdd39676ef6807d06ca5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Wprowadzenie do usługi Azure Active Directory raportowania interfejsu API

Usługa Azure Active Directory zapewnia szereg [raporty](active-directory-reporting-azure-portal.md). Dane z tych raportów mogą być bardzo przydatne w aplikacjach, takich jak systemy SIEM oraz narzędzia do inspekcji i analizy biznesowej. 

Korzystając z funkcji raportowania interfejsu API usługi Azure AD, można uzyskać programowy dostęp do danych za pomocą zestawu opartego na interfejsie REST API. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania.

Ten artykuł zawiera przewodnik do uzyskiwania dostępu do danych raportowania przy użyciu interfejsu API powiązane.

Jeśli wystąpiły problemy, zobacz [jak uzyskać pomoc techniczną dotyczącą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do interfejsu API raportowania, nawet wtedy, gdy planujesz na uzyskiwanie dostępu do interfejsu API za pomocą skryptu, musisz:

1. Przypisz role (Administrator globalny czytnika zabezpieczeń, administrator zabezpieczeń)
2. Rejestrowanie aplikacji
3. Udziel uprawnień
4. Zbierz ustawienia konfiguracji


 
Aby uzyskać szczegółowe instrukcje, zobacz [wymagania wstępne dotyczące dostęp do usługi Azure Active Directory interfejsem API raportowania](active-directory-reporting-api-prerequisites-azure-portal.md).


## <a name="recommendation"></a>Zalecenie 

Jeśli planujesz na pobieranie danych raportowania bez interwencji użytkownika, należy rozważyć przy użyciu usługi Azure AD API raportowania z certyfikatami.

Aby uzyskać szczegółowe instrukcje, zobacz [Pobierz dane przy użyciu usługi Azure AD API raportowania z certyfikatami](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Eksploruj

Pobierz pierwsze wrażenia raportowania interfejsów API:
   
   - [Korzystanie z przykładów dla inspekcji interfejsu API](active-directory-reporting-api-audit-samples.md) 
 
   - [Korzystanie z przykładów dla raport aktywności logowania interfejsu API](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Dostosowywanie  

Tworzenie własnych rozwiązań: 
   
   - [Przy użyciu audytu dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [Przy użyciu działań logowania odwołania raportu interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



