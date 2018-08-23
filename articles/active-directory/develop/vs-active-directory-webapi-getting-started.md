---
title: Rozpoczynanie pracy z usługą Azure AD w projektach programu Visual Studio WebApi
description: Jak rozpocząć pracę, przy użyciu usługi Azure Active Directory w projektach WebApi po nawiązywania połączenia lub tworzenia usługi Azure AD przy użyciu programu Visual Studio podłączone usługi
services: active-directory
author: ghogen
manager: douge
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 69f17b366b2480b34873d8fede223715619d0e66
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057014"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Rozpoczynanie pracy z usługą Azure Active Directory (projektów WebApi)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-webapi-getting-started.md)
> - [Co się stało](vs-active-directory-webapi-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki, po dodaniu usługi Active Directory do projektu interfejsu WebAPI ASP.NET za pomocą **Projekt > usług połączonych** polecenia programu Visual Studio. Jeśli jeszcze nie dodano usługę do projektu, możesz to zrobić w dowolnym momencie.

Zobacz [co się stało z moim projektem WebAPI?](vs-active-directory-webapi-what-happened.md) zmian wprowadzonych do projektu, podczas dodawania usługi połączonej.

## <a name="requiring-authentication-to-access-controllers"></a>Wymaganie uwierzytelniania do dostępu do kontrolerów

Wszystkie kontrolery w projekcie zostały powiązany z `[Authorize]` atrybutu. Ten atrybut wymaga od użytkownika uwierzytelniali się przed uzyskaniem dostępu do interfejsów API zdefiniowany przez tych kontrolerów. Aby umożliwić kontrolerowi można uzyskiwać dostęp anonimowo, Usuń ten atrybut z kontrolera. Jeśli chcesz ustawić uprawnienia na bardziej szczegółowym poziomie, należy zastosować atrybut do każdej metody, która wymaga autoryzacji zamiast zastosowanie go do klasy kontrolera.

## <a name="next-steps"></a>Kolejne kroki

- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](authentication-scenarios.md)
- [Dodawanie logowania z firmą Microsoft do aplikacji sieci web platformy ASP.NET](quickstart-v1-aspnet-webapp.md)
