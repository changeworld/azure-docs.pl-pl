---
title: Wprowadzenie do usługi Azure AD w projektach WebApi programu Visual Studio
description: Jak rozpocząć korzystanie z usługi Azure Active Directory w projektach webApi po nawiązaniu połączenia z usługą Azure AD lub utworzeniu usługi Azure AD przy użyciu połączonych usług programu Visual Studio
author: ghogen
manager: jillfra
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 33894e237144628634c3b63393130eb0af5b9877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159458"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Wprowadzenie do usługi Azure Active Directory (projekty WebApi)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-webapi-getting-started.md)
> - [Co się stało](vs-active-directory-webapi-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki po dodaniu usługi Active Directory do ASP.NET projektu WebAPI za pośrednictwem polecenia **Programu Project > Connected Services** programu Visual Studio. Jeśli usługa nie została jeszcze dodana do projektu, możesz to zrobić w dowolnym momencie.

Zobacz [Co się stało z moim projektem WebAPI?](vs-active-directory-webapi-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Wymaganie uwierzytelniania w celu uzyskania dostępu do kontrolerów

Wszystkie kontrolery w projekcie zostały ozdobione atrybutem. `[Authorize]` Ten atrybut wymaga, aby użytkownik został uwierzytelniony przed dostępem do interfejsów API zdefiniowanych przez te kontrolery. Aby umożliwić dostęp do kontrolera anonimowo, usuń ten atrybut z kontrolera. Jeśli chcesz ustawić uprawnienia na poziomie bardziej szczegółowy, zastosuj atrybut do każdej metody, która wymaga autoryzacji zamiast stosowania go do klasy kontrolera.

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](authentication-scenarios.md)
- [dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET](quickstart-v2-aspnet-webapp.md)
