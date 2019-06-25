---
title: Omówienie protokołu .NET usługi Azure AD | Microsoft Docs
description: Jak używać wiadomości HTTP do autoryzacji dostępu do aplikacji internetowych i internetowych interfejsów API w dzierżawie za pomocą usługi Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183404"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi AD
Najpierw musisz zarejestrować aplikację w dzierżawie usługi Azure Active Directory (Azure AD). Spowoduje to nadanie aplikacji identyfikatora oraz umożliwi jej otrzymywanie tokenów.

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
* Wybierz dzierżawę usługi Azure AD, klikając swoje konto w prawym górnym rogu strony, a następnie klikając **Przełącz katalog** nawigacji, a następnie wybierz odpowiednią dzierżawy. 
  * Pomiń ten krok, jeśli masz tylko jedną dzierżawę usługi Azure AD w ramach konta usługi lub jeśli wybrano już odpowiednią dzierżawę usługi Azure AD.
* W okienku nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**.
* Kliknij pozycję **rejestracje aplikacji** i kliknij pozycję **nowej rejestracji**.
* Postępuj zgodnie z monitami i utwórz nową aplikację. Go nie ma znaczenia w przypadku aplikacji sieci web lub aplikacja kliencka publiczny (mobilnych i klasycznych), w tym samouczku, ale jeśli konkretne przykłady dla aplikacji sieci web lub aplikacji publicznych klienta, zapoznaj się z naszym [przewodników Szybki Start](../articles/active-directory/develop/v1-overview.md).
  * **Nazwa** to nazwa aplikacji; opisuje aplikację innym użytkownikom.
  * W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
  * Podaj **identyfikator URI przekierowania**. Dla aplikacji sieci Web jest to podstawowy adres URL aplikacji, w którym użytkownicy mogą się logować.  Na przykład `http://localhost:12345`. Dla klienta publicznego (mobilnych i klasycznych) usługa Azure AD używa go do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla twojej aplikacji.  Na przykład `http://MyFirstAADApp`.
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* Po zakończeniu rejestracji usługi Azure AD przypisze aplikacji unikatowego identyfikatora klienckiego ( **identyfikator aplikacji**). Ta wartość będzie potrzebna w kolejnych sekcjach, więc skopiuj ją ze strony aplikacji.
* Aby znaleźć aplikację w witrynie Azure portal, kliknij **rejestracje aplikacji**, a następnie kliknij przycisk **Wyświetl wszystkie aplikacje**.
