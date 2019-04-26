---
title: Problemy z logowaniem do aplikacji niestandardowej | Dokumentacja firmy Microsoft
description: Typowe rrors, który może być przyczyną nie będą mogli zalogować się do aplikacji opracowanych w z usługą Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4365c87f0028fe3a9c4ba35f40599359eb1455f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60438782"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Problemy z logowaniem do aplikacji niestandardowej

Istnieje kilka błędów, które mogą być przyczyną nie będą mogli zalogować się do aplikacji. Największe encounter osób przyczyny tego problemu jest błędnie skonfigurowane aplikacje.

## <a name="errors-related-to--misconfigured-apps"></a>Błędy związane z nieprawidłowej konfiguracji aplikacji

* Sprawdź, czy konfiguracje w portalu dopasowania, co znajduje się w aplikacji. W szczególności porównaj identyfikator klienta/aplikacji, adresy URL odpowiedzi, kluczy/wpisów tajnych klienta i identyfikator URI Identyfikatora aplikacji.

* Porównaj zasobów jest żądanie dostępu do kodu przy użyciu skonfigurowanych uprawnień w **wymagane zasoby** kartę, aby upewnić się, że tylko żądania zasobów, które zostały skonfigurowane.

* Zobacz [usługi Azure AD w witrynie StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) występują podobne błędy lub problemy.

## <a name="next-steps"></a>Kolejne kroki

[Przewodnik dewelopera usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Integrowanie aplikacji z usługą Azure AD i zgody](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Wyrażania zgody i udzielania do nich uprawnień dla usługi Azure AD v2.0 zbieżne aplikacje](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
