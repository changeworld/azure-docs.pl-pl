---
title: Omówienie protokołu .NET usługi Azure AD | Microsoft Docs
description: Jak używać wiadomości HTTP do autoryzacji dostępu do aplikacji internetowych i internetowych interfejsów API w dzierżawie za pomocą usługi Azure AD.
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
ms.openlocfilehash: 5a4f08718175dd7ef369d801807e6c8e1ff28fd9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39485844"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi AD
Najpierw musisz zarejestrować aplikację w dzierżawie usługi Azure Active Directory (Azure AD). Spowoduje to nadanie aplikacji identyfikatora oraz umożliwi jej otrzymywanie tokenów.

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
* Wybierz dzierżawę usługi Azure AD, klikając swoje konto w prawym górnym rogu strony, a następnie klikając **Przełącz katalog** nawigacji, a następnie wybierz odpowiednią dzierżawy. 
  * Pomiń ten krok, jeśli masz tylko jedną dzierżawą usługi Azure AD w ramach konta usługi lub został już wybrany odpowiedni dzierżawy usługi Azure AD.
* W okienku nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**.
* Kliknij pozycję **rejestracje aplikacji** i kliknij pozycję **rejestrowanie nowej aplikacji**.
* Postępuj zgodnie z monitami i utwórz nową aplikację. Na potrzeby tego samouczka nie ma znaczenia, czy jest to aplikacja internetowa, czy aplikacja natywna, ale jeśli chcesz zapoznać się z konkretnymi przykładami dla aplikacji internetowych lub aplikacji natywnych, zobacz nasze [przewodniki szybkiego startu](../articles/active-directory/develop/azure-ad-developers-guide.md).
  * W przypadku aplikacji sieci Web podaj **adres URL logowania**, czyli podstawowy adres URL aplikacji, w którym użytkownicy mogą się logować, np `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Dla aplikacji natywnych podaj **identyfikator URI przekierowania**, którego usługa Azure AD będzie używać do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla Twojej aplikacji, np. `http://MyFirstAADApp`
* Po zakończeniu rejestracji usługi Azure AD przypisze aplikacji identyfikatorem unikatowych klientów **identyfikator aplikacji**. Ta wartość będzie potrzebna w kolejnych sekcjach, więc skopiuj ją ze strony aplikacji.
* Aby znaleźć aplikację w witrynie Azure portal, kliknij **rejestracje aplikacji**, a następnie kliknij przycisk **Wyświetl wszystkie aplikacje**.
