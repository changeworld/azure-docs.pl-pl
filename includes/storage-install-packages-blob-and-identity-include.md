---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806590"
---
## <a name="install-client-library-packages"></a>Instalowanie pakietów biblioteki klienta

> [!NOTE]
> Przykłady pokazane w tym miejscu użyć biblioteki klienta usługi Azure Storage w wersji 12. Biblioteka klienta w wersji 12 jest częścią narzędzia Azure SDK. Aby uzyskać więcej informacji na temat zestawu SDK platformy Azure, zobacz repozytorium SDK platformy Azure w [usłudze GitHub.](https://github.com/Azure/azure-sdk)

Aby zainstalować pakiet magazynu obiektów Blob, uruchom następujące polecenie z konsoli Menedżera pakietów NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

Przykłady pokazane tutaj również użyć najnowszej wersji [biblioteki klienta usługi Azure Identity dla platformy .NET](https://www.nuget.org/packages/Azure.Identity/) do uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Aby zainstalować pakiet, uruchom następujące polecenie z konsoli menedżera pakietów NuGet:

```powershell
Install-Package Azure.Identity
```
