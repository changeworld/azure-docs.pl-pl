---
title: Wprowadzenie do pulpitu usługi Azure AD w wersji 2 — konfiguracja
description: Jak aplikacja Windows Desktop .NET (XAML) może uzyskać token dostępu i wywołać interfejs API chroniony przez punkt końcowy usługi Azure Active Directory w wersji 2.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885773"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Dodawanie informacji rejestracyjnych aplikacji do aplikacji
W tym kroku należy dodać identyfikator aplikacji do projektu.

1.  Otwórz `App.xaml.cs` i wymień `ClientId` linię zawierającą:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Co dalej

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
