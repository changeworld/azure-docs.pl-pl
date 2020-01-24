---
title: Uzyskiwanie Microsoft Graph & wywołań tokenu (konsola .NET Core) (v 1.0) | Azure
description: Tworzenie aplikacji demona .NET, która integruje się z usługą Azure AD & wywołuje chronione interfejsy API usługi Azure AD przy użyciu protokołu OAuth 2,0
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.openlocfilehash: bdcf0632110df23cbe91040f41535f51fb06ba44
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703800"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-using-console-apps-identity-v10"></a>Szybki Start: uzyskiwanie tokenu i Wywołaj Microsoft Graph przy użyciu tożsamości aplikacji konsolowej (v 1.0)

[Platforma tożsamości firmy Microsoft](v2-overview.md) to ewolucja platformy deweloperów Azure Active Directory (Azure AD). Dzięki niej deweloperzy mogą tworzyć aplikacje, które logują użytkowników przy użyciu wszystkich tożsamości firmy Microsoft i uzyskują tokeny do wywoływania interfejsów API firmy Microsoft, takich jak Microsoft Graph, lub interfejsów API opracowanych przez deweloperów.

[Biblioteka Microsoft Authentication Library (MSAL)](msal-overview.md) umożliwia deweloperom uzyskanie tokenów z punktu końcowego platformy tożsamości firmy Microsoft w celu uzyskania dostępu do zabezpieczonych interfejsów API sieci Web. Active Directory Authentication Library (ADAL) integruje się z punktem końcowym usługi Azure AD dla deweloperów (v 1.0), gdzie MSAL integruje się z punktem końcowym Microsoft Identity platform (v 2.0).

## <a name="next-steps"></a>Następne kroki

W przypadku nowych aplikacji programu .NET DAEMON zalecamy korzystanie z usługi Microsoft Identity platform (v 2.0) i MSAL w celu uzyskiwania tokenów i uzyskiwania dostępu do zabezpieczonych interfejsów API sieci Web: [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z aplikacji konsolowej przy użyciu tożsamości aplikacji](quickstart-v2-netcore-daemon.md).
