---
title: Problemy przy logowaniu do aplikacji utworzonych niestandardowych | Dokumentacja firmy Microsoft
description: Typowe rrors, który może być przyczyną nie będą mogli zalogować się do aplikacji utworzonych w usłudze Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 57b620f45d1985351064020e122c088584bcdcf5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26614142"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Problemy przy logowaniu do aplikacji utworzonych niestandardowych

Istnieje kilka błędów, które mogą być przyczyną nie będą mogli zalogować się do aplikacji. Jest to problem podczas potyczki osób Przyczyna największych błędnie skonfigurowane aplikacji.

## <a name="errors-related-to--misconfigured-apps"></a>Błędy związane z nieprawidłowej konfiguracji aplikacji

* Sprawdź, czy konfiguracje w portalu odpowiada mieć w Twojej aplikacji. W szczególności porównaj identyfikator klienta/aplikacji, adresy URL odpowiedzi, klucze kluczy tajnych klienta i identyfikator URI aplikacji.

* Porównywanie zasobów jest żądanie dostępu do kodu przy użyciu uprawnień skonfigurowanych w **wymagane zasoby** kartę, aby upewnić się, że tylko żądania zasobów skonfigurowano.

* Zobacz [Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory) dla podobnych jakichkolwiek problemów.

## <a name="next-steps"></a>Następne kroki

[Przewodnik dewelopera usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Integrowanie aplikacji z usługą Azure AD i zgody](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Permissioning dla usługi Azure AD w wersji 2.0 i zgody zbieżność aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory>)
