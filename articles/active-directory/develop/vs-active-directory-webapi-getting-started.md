---
title: Rozpoczynanie pracy z usługą Azure AD w projektach programu Visual Studio WebApi
description: Jak rozpocząć korzystanie z Azure Active Directory w projektach WebApi po nawiązaniu połączenia z usługą Azure AD lub utworzeniu jej przy użyciu usług połączonych programu Visual Studio
services: active-directory
ms.service: active-directory
ms.subservice: develop
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 662e0a3e85684287c636b8d0b512b072c978dd3f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320730"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Wprowadzenie do Azure Active Directory (projekty WebApi)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-webapi-getting-started.md)
> - [Co się stało](vs-active-directory-webapi-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki po dodaniu Active Directory do projektu ASP.NET WebAPI za pomocą polecenia **project > Connect Services** w programie Visual Studio. Jeśli usługa nie została jeszcze dodana do projektu, możesz to zrobić w dowolnym momencie.

Zobacz, [co się stało z moim projektem WebAPI?](vs-active-directory-webapi-what-happened.md) dla zmian wprowadzonych w projekcie podczas dodawania połączonej usługi.

## <a name="requiring-authentication-to-access-controllers"></a>Wymaganie uwierzytelniania do kontrolerów dostępu

Wszystkie kontrolery w projekcie zostały wbudowane przy użyciu `[Authorize]` atrybutu. Ten atrybut wymaga uwierzytelnienia użytkownika przed uzyskaniem dostępu do interfejsów API zdefiniowanych przez te kontrolery. Aby umożliwić dostęp do kontrolera anonimowo, Usuń ten atrybut z kontrolera. Jeśli chcesz ustawić uprawnienia na bardziej szczegółowym poziomie, zastosuj atrybut do każdej metody wymagającej autoryzacji zamiast zastosowania jej do klasy kontrolera.

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla Azure Active Directory](authentication-scenarios.md)
- [Dodawanie logowania z firmą Microsoft do aplikacji sieci Web ASP.NET](quickstart-v1-aspnet-webapp.md)
