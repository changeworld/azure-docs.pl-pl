---
title: Problemy z logowaniem się do aplikacji opracowanej na zamówienie | Dokumenty firmy Microsoft
description: Typowe błędy, które mogą powodować, że nie można zalogować się do aplikacji opracowanej za pomocą usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ad2499aea8bf4e41ca00d6c78d76e112f0493e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825237"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problemy z logowaniem się do aplikacji opracowanej na zamówienie

Istnieje kilka błędów, które mogą powodować, że nie można zalogować się do aplikacji. Największym powodem, dla którego ludzie napotykają ten problem, są nieprawidłowo skonfigurowane aplikacje.

## <a name="errors-related-to--misconfigured-apps"></a>Błędy związane z nieprawidłowo skonfigurowane aplikacje

* Sprawdź, czy obie konfiguracje w portalu są zgodne z tym, co masz w aplikacji. W szczególności porównaj identyfikator klienta/aplikacji, adresy URL odpowiedzi, wpisy tajne/klucze klienta i identyfikator URI identyfikatora aplikacji.

* Porównaj zasób, do którego żądasz dostępu w kodzie, ze skonfigurowanymi uprawnieniami na karcie **Wymagane zasoby,** aby upewnić się, że żądasz tylko skonfigurowanych zasobów.

* Zobacz [Usługi Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) dla podobnych błędów lub problemów.

## <a name="next-steps"></a>Następne kroki

[Przewodnik dla deweloperów usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Zgoda i integrowanie aplikacji z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Zgoda i uprawnienia dla aplikacji konwergentnych usługi Azure AD w wersji 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Przepływ stosu usługi Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory>)
