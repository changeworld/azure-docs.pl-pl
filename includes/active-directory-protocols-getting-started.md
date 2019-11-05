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
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523450"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi AD
Najpierw Zarejestruj swoją aplikację za pomocą dzierżawy usługi Azure Active Directory (Azure AD). Spowoduje to nadanie aplikacji identyfikatora oraz umożliwi jej otrzymywanie tokenów.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
   
1. Wybierz dzierżawę usługi Azure AD, wybierając swoje konto w prawym górnym rogu strony, a następnie wybierając pozycję **Przełącz katalog** nawigacji, a następnie wybierając odpowiednią dzierżawę. 
   - Pomiń ten krok, jeśli masz tylko jedną dzierżawę usługi Azure AD w ramach Twojego konta lub wybrano już odpowiednią dzierżawę usługi Azure AD.
   
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.
   
1. W menu **Azure Active Directory** po lewej stronie wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
   
1. Postępuj zgodnie z monitami i utwórz nową aplikację. Nie ma znaczenia, czy jest to aplikacja sieci Web lub aplikacja klienta publicznego (Mobile & Desktop) dla tego samouczka, ale jeśli chcesz znaleźć konkretne przykłady dla aplikacji sieci Web lub publicznych aplikacji klienckich, zapoznaj się z [przewodnikami szybki start](../articles/active-directory/develop/v1-overview.md).
   
   - **Nazwa** to nazwa aplikacji; opisuje aplikację innym użytkownikom.
   - W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
   - Podaj **Identyfikator URI przekierowania**. W przypadku aplikacji sieci Web jest to podstawowy adres URL aplikacji, w której użytkownicy mogą się logować.  Na przykład `http://localhost:12345`. W przypadku klienta publicznego (Mobile & Desktop) usługa Azure AD używa go do zwracania odpowiedzi tokenów. Wprowadź wartość specyficzną dla Twojej aplikacji.  Na przykład `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Po zakończeniu rejestracji usługa Azure AD przypisze aplikację unikatowy identyfikator klienta ( **Identyfikator aplikacji**). Ta wartość będzie potrzebna w następnych sekcjach, więc Skopiuj ją ze strony aplikacji.
   
1. Aby znaleźć aplikację w Azure Portal, wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Wyświetl wszystkie aplikacje**.
