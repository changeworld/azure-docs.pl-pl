---
title: Omówienie protokołu .NET usługi Azure AD | Microsoft Docs
description: Jak używać wiadomości HTTP do autoryzacji dostępu do aplikacji sieci Web i interfejsów API sieci Web w dzierżawie za pomocą usługi Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2018
ms.author: priyamo
ms.openlocfilehash: 0b78ed6cdb1209d70cf0d561f74cfcddc09b2391
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
## <a name="register-your-application-with-your-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi AD
Najpierw musisz zarejestrować aplikację z dzierżawą usługi Azure Active Directory (Azure AD). Spowoduje to nadanie aplikacji identyfikatora oraz umożliwi jej otrzymywanie tokenów.

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
* Wybierz dzierżawę usługi Azure AD, klikając swoje konto w prawym górnym rogu strony.
* W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory**.
* Polecenie **rejestracji aplikacji** i wybierz polecenie **nowej rejestracji aplikacji**.
* Postępuj zgodnie z monitami i utwórz nową aplikację. Na potrzeby tego samouczka nie ma znaczenia, czy jest to aplikacja sieci Web, czy aplikacja natywna, ale jeśli chcesz zapoznać się z konkretnymi przykładami dla aplikacji sieci Web lub aplikacji natywnych, zobacz nasze [przewodniki szybkiego startu](../articles/active-directory/develop/active-directory-developers-guide.md).
  * W przypadku aplikacji sieci Web, podaj **adres URL logowania**, który jest podstawowy adres URL aplikacji, w którym użytkownicy mogą rejestrować w np. `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Dla natywnych aplikacji zapewniają **identyfikator URI przekierowania**, który będzie używany w usłudze Azure AD do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla Twojej aplikacji, np. `http://MyFirstAADApp`
* Po zakończeniu rejestracji usługi Azure AD będą przypisywane aplikacji identyfikatorem unikatowych klientów **identyfikator aplikacji**. Potrzebujesz tej wartości w kolejnych sekcjach, dlatego skopiuj go ze strony aplikacji.
* Aby znaleźć aplikacji w portalu Azure, kliknij przycisk **rejestracji aplikacji**, a następnie kliknij przycisk **Wyświetl wszystkie aplikacje**.