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
ms.openlocfilehash: cb31bb91c80e4d5dd032b009b40d8e3fc435e0c8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39359463"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi AD
Najpierw musisz zarejestrować aplikację w dzierżawie usługi Azure Active Directory (Azure AD). Spowoduje to nadanie aplikacji identyfikatora oraz umożliwi jej otrzymywanie tokenów.

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
* Wybierz dzierżawę usługi Azure AD, klikając swoje konto w prawym górnym rogu strony, a następnie klikając **Przełącz katalog** nawigacji, a następnie wybierz odpowiednią dzierżawy. 
  * Pomiń ten krok, jeśli masz tylko jedną dzierżawą usługi Azure AD w ramach konta usługi lub został już wybrany odpowiedni dzierżawy usługi Azure AD.
* W okienku nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**.
* Kliknij pozycję **rejestracje aplikacji** i kliknij pozycję **rejestrowanie nowej aplikacji**.
* Postępuj zgodnie z monitami i utwórz nową aplikację. Na potrzeby tego samouczka nie ma znaczenia, czy jest to aplikacja internetowa, czy aplikacja natywna, ale jeśli chcesz zapoznać się z konkretnymi przykładami dla aplikacji internetowych lub aplikacji natywnych, zobacz nasze [przewodniki szybkiego startu](../articles/active-directory/develop/active-directory-developers-guide.md).
  * W przypadku aplikacji sieci Web podaj **adres URL logowania**, czyli podstawowy adres URL aplikacji, w którym użytkownicy mogą się logować, np `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Dla aplikacji natywnych podaj **identyfikator URI przekierowania**, którego usługa Azure AD będzie używać do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla Twojej aplikacji, np. `http://MyFirstAADApp`
* Po zakończeniu rejestracji usługi Azure AD przypisze aplikacji identyfikatorem unikatowych klientów **identyfikator aplikacji**. Ta wartość będzie potrzebna w kolejnych sekcjach, więc skopiuj ją ze strony aplikacji.
* Aby znaleźć aplikację w witrynie Azure portal, kliknij **rejestracje aplikacji**, a następnie kliknij przycisk **Wyświetl wszystkie aplikacje**.
