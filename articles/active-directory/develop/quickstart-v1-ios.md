---
title: Logowanie i Microsoft Graph Wywołaj z systemu iOS | Azure
description: Dowiedz się, jak logować użytkowników i wywołać interfejs API programu Microsoft Graph z aplikacji systemu iOS.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: brandwe
ms.openlocfilehash: 0f612a6f59455162d0acdffc8e05201be0a730d9
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703783"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app-v10"></a>Szybki Start: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph z aplikacji dla systemu iOS (v 1.0)

[Platforma tożsamości firmy Microsoft](v2-overview.md) to ewolucja platformy deweloperów Azure Active Directory (Azure AD). Dzięki niej deweloperzy mogą tworzyć aplikacje, które logują użytkowników przy użyciu wszystkich tożsamości firmy Microsoft i uzyskują tokeny do wywoływania interfejsów API firmy Microsoft, takich jak Microsoft Graph, lub interfejsów API opracowanych przez deweloperów.

[Biblioteka Microsoft Authentication Library (MSAL)](msal-overview.md) umożliwia deweloperom uzyskanie tokenów z punktu końcowego platformy tożsamości firmy Microsoft w celu uzyskania dostępu do zabezpieczonych interfejsów API sieci Web. Active Directory Authentication Library (ADAL) integruje się z punktem końcowym usługi Azure AD dla deweloperów (v 1.0), gdzie MSAL integruje się z punktem końcowym Microsoft Identity platform (v 2.0).

W przypadku nowych aplikacji macOS i iOS zalecamy korzystanie z usługi Microsoft Identity platform (v 2.0) i MSAL do uzyskiwania tokenów i uzyskiwania dostępu do zabezpieczonych interfejsów API sieci Web: [Szybki Start: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph z aplikacji dla systemu iOS lub macOS](quickstart-v2-ios.md).
