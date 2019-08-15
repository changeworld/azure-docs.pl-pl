---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011994"
---
Biblioteka klienta [uwierzytelniania aplikacji Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) dla platformy .NET (wersja zapoznawcza) upraszcza proces uzyskiwania i odnawiania tokenu z kodu. Biblioteka klienta uwierzytelniania aplikacji zarządza uwierzytelnianiem automatycznie. Biblioteka używa poświadczeń dewelopera do uwierzytelniania podczas lokalnego tworzenia oprogramowania. Korzystanie z poświadczeń dewelopera podczas lokalnego tworzenia jest bezpieczniejsze, ponieważ nie ma potrzeby tworzenia poświadczeń usługi Azure AD ani udostępniania poświadczeń między deweloperami. Gdy rozwiązanie zostanie wdrożone później na platformie Azure, biblioteka automatycznie przełączy się do korzystania z poświadczeń aplikacji.

Aby użyć biblioteki uwierzytelniania aplikacji w aplikacji usługi Azure Storage, zainstaluj najnowszy pakiet wersji zapoznawczej z programu [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), a także najnowszą wersję [biblioteki wspólnych klientów usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) i [biblioteki klienckiej usługi Azure Blob Storage dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Dodaj następujące instrukcje **using** do kodu:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
