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
ms.openlocfilehash: 0adf1280fa50e9ee594f3025dff70786f5ba2199
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666170"
---
## <a name="install-client-library-packages"></a>Zainstaluj pakiety biblioteki klienta

> [!NOTE]
> W przykładach pokazano, jak używać biblioteki klienckiej usługi Azure Storage w wersji 12. Biblioteka klienta w wersji 12 jest częścią zestawu Azure SDK. Aby uzyskać więcej informacji na temat zestawu Azure SDK, zobacz repozytorium zestawu Azure SDK w witrynie [GitHub](https://github.com/Azure/azure-sdk).

Aby zainstalować pakiet usługi BLOB Storage, uruchom następujące polecenie w konsoli Menedżera pakietów NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

W przykładach pokazano również, jak używać najnowszej wersji [biblioteki klienta tożsamości platformy Azure dla platformy .NET](https://www.nuget.org/packages/Azure.Identity/) do uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Aby zainstalować pakiet, uruchom następujące polecenie w konsoli Menedżera pakietów NuGet:

```powershell
Install-Package Azure.Identity
```

Aby dowiedzieć się więcej o sposobie uwierzytelniania przy użyciu biblioteki klienta tożsamości platformy Azure z usługi Azure Storage, zapoznaj się z sekcją **uwierzytelnianie przy użyciu biblioteki tożsamości platformy Azure** w artykule [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure](/azure/storage/common/storage-auth-aad-msi?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).