---
title: Dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET | Microsoft Docs
description: Dowiedz się, jak dodać firmę Microsoft w ramach rozwiązania ASP.NET z tradycyjną aplikacją opartą na przeglądarce sieci Web przy użyciu usługi OpenID Connect Connect Standard.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 240b64bcaa242e39140f39faff31418f96b34193
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149918"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Szybki start: dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET

[Platforma tożsamości firmy Microsoft](v2-overview.md) to ewolucja platformy deweloperów Azure Active Directory (Azure AD). Dzięki niej deweloperzy mogą tworzyć aplikacje, które logują użytkowników przy użyciu wszystkich tożsamości firmy Microsoft i uzyskują tokeny do wywoływania interfejsów API firmy Microsoft, takich jak Microsoft Graph, lub interfejsów API opracowanych przez deweloperów.

[Biblioteka Microsoft Authentication Library (MSAL)](msal-overview.md) umożliwia deweloperom uzyskanie tokenów z punktu końcowego platformy tożsamości firmy Microsoft w celu uzyskania dostępu do zabezpieczonych interfejsów API sieci Web. Active Directory Authentication Library (ADAL) integruje się z punktem końcowym usługi Azure AD dla deweloperów (v 1.0), gdzie MSAL integruje się z punktem końcowym Microsoft Identity platform (v 2.0).

## <a name="next-steps"></a>Następne kroki

W przypadku nowych aplikacji sieci Web zalecamy korzystanie z usługi Microsoft Identity platform (v 2.0) i MSAL w celu uzyskiwania tokenów i uzyskiwania dostępu do zabezpieczonych interfejsów API sieci Web. Aby rozpocząć pracę, zobacz [Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji sieci web ASP.NET](quickstart-v2-aspnet-webapp.md) .
