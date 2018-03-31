---
title: Rozpoczynanie pracy z usługą Azure AD w projektach Visual Studio WebApi | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę przy użyciu usługi Azure Active Directory w projektach WebApi po nawiązywania połączenia lub utworzenie usługi Azure AD przy użyciu programu Visual Studio połączenia usługi
services: active-directory
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: f44c60cc02ef93fb8bd08954d8b059889eb82df1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Rozpoczynanie pracy z usługą Azure Active Directory (WebApi projekty)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-webapi-getting-started.md)
> - [Co się stało](vs-active-directory-webapi-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki po dodaniu usługi Active Directory do projektu programu ASP.NET WebAPI za pośrednictwem **projektu > usług połączonych** polecenia programu Visual Studio. Jeśli usługa nie jest już zostały dodane do projektu, możesz to zrobić w dowolnym momencie.

Zobacz [co się stało z projektu WebAPI?](vs-active-directory-webapi-what-happened.md) , aby zmiany wprowadzone do projektu podczas dodawania podłączonej usługi.

## <a name="requiring-authentication-to-access-controllers"></a>Wymaganie uwierzytelniania do kontrolerów dostępu

Wszystkie kontrolery w projekcie zostały adorned z `[Authorize]` atrybutu. Ten atrybut wymaga od użytkownika mają być uwierzytelniani przed uzyskaniem dostępu do interfejsów API zdefiniowany przez te kontrolery. Aby umożliwić kontrolerowi można uzyskać dostępu do anonimowo, Usuń ten atrybut z kontrolera. Aby ustawić uprawnienia na poziomie bardziej szczegółowego, należy zastosować atrybut do każdej metody, która wymaga uwierzytelnienia, zamiast stosować go do klasy kontrolera.

## <a name="next-steps"></a>Kolejne kroki

- [Scenariusze uwierzytelniania usługi Azure Active Directory](active-directory-authentication-scenarios.md)
- [Dodaj logowanie z firmy Microsoft do aplikacji sieci web platformy ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)
