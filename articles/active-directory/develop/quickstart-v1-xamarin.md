---
title: Usługa Azure AD i platforma Xamarin — wprowadzenie | Microsoft Docs
description: Twórz aplikacje platformy Xamarin, które integrują się z usługą Azure AD na potrzeby logowania i wywołują chronione przez usługę Azure AD interfejsy API przy użyciu protokołu OAuth.
services: active-directory
documentationcenter: xamarin
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36e354894700ec92a444f288b3e09f5d7af88329
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321039"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>Szybki start: Tworzenie aplikacji Xamarin integrującej logowanie firmy Microsoft

[Platforma tożsamości firmy Microsoft](v2-overview.md) to ewolucja platformy deweloperów Azure Active Directory (Azure AD). Dzięki niej deweloperzy mogą tworzyć aplikacje, które logują użytkowników przy użyciu wszystkich tożsamości firmy Microsoft i uzyskują tokeny do wywoływania interfejsów API firmy Microsoft, takich jak Microsoft Graph, lub interfejsów API opracowanych przez deweloperów.

[Biblioteka Microsoft Authentication Library (MSAL)](msal-overview.md) umożliwia deweloperom uzyskanie tokenów z punktu końcowego platformy tożsamości firmy Microsoft w celu uzyskania dostępu do zabezpieczonych interfejsów API sieci Web. Active Directory Authentication Library (ADAL) integruje się z punktem końcowym usługi Azure AD dla deweloperów (v 1.0), gdzie MSAL integruje się z punktem końcowym Microsoft Identity platform (v 2.0).

W przypadku nowych aplikacji platformy Xamarin zalecamy korzystanie z usługi Microsoft Identity platform (v 2.0) i MSAL w celu uzyskiwania tokenów i uzyskiwania dostępu do zabezpieczonych interfejsów API sieci Web: [Integruj tożsamość firmy Microsoft i Microsoft Graph w aplikacji interfejsu Xamarin Forms przy użyciu MSAL](https://github.com/azure-samples/active-directory-xamarin-native-v2#integrate-microsoft-identity-and-the-microsoft-graph-into-a-xamarin-forms-app-using-msal) (bez dodatkowych kroków).

