---
title: Azure AD v2 Windows Desktop Wprowadzenie-config | Microsoft Docs
description: Jak aplikacja Windows Desktop .NET (XAML) może uzyskać token dostępu i wywołać interfejs API chroniony przez punkt końcowy Azure Active Directory v2.
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897686"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Dodawanie informacji rejestracyjnych aplikacji do aplikacji
W tym kroku należy dodać identyfikator aplikacji do projektu.

1.  Otwórz `App.xaml.cs` i Zastąp wiersz zawierający `ClientId`:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Co dalej

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
