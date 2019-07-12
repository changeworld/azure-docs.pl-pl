---
title: Usługa Azure AD v2 Windows Desktop wprowadzenie pracę - Config | Dokumentacja firmy Microsoft
description: Jak aplikacji .NET Desktop Windows (XAML) można uzyskać token dostępu i wywołać interfejs API chroniony przez punkt końcowy w wersji 2 usługi Azure Active Directory.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fce464c2351de7d3ef26882d0ab56f11743ea3f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702952"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Dodaj informacje o rejestracji aplikacji do aplikacji
W tym kroku należy dodać identyfikator aplikacji do projektu.

1.  Otwórz `App.xaml.cs` i Zastąp wiersz zawierający `ClientId` za pomocą:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Co to jest dalej

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
