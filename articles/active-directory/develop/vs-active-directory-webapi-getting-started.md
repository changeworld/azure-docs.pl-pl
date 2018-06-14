---
title: Rozpoczynanie pracy z usługą Azure AD w projektach Visual Studio WebApi
description: Jak rozpocząć pracę przy użyciu usługi Azure Active Directory w projektach WebApi po nawiązywania połączenia lub utworzenie usługi Azure AD przy użyciu programu Visual Studio połączenia usługi
services: active-directory
author: ghogen
manager: douge
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 109de9fb78ae3abfc09a37c6b8cb38c554f7613c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31784520"
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
