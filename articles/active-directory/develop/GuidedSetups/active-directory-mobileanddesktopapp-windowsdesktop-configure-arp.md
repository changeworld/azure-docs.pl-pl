---
title: Wprowadzenie do pulpitu usługi Azure AD w wersji 2 — konfigura | Dokumenty firmy Microsoft
description: Jak aplikacja Windows Desktop .NET (XAML) może uzyskać token dostępu i wywołać interfejs API chroniony przez punkt końcowy usługi Azure Active Directory w wersji 2.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4c757b52e7378bffe4a4954022b131b772a337cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897686"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Dodawanie informacji rejestracyjnych aplikacji do aplikacji
W tym kroku należy dodać identyfikator aplikacji do projektu.

1.  Otwórz `App.xaml.cs` i wymień `ClientId` linię zawierającą:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Co dalej

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
