---
title: Co się stało z moim projektem ASP.NET? | Microsoft Docs
description: Informacje o tym, co się stanie po dodaniu usługi Azure Storage do projektu ASP.NET przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 74db2e1e15c282f4393715c67561b2ed61bbcdfb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512071"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Co się stało z moim projektem ASP.NET (usługa połączona usługi Visual Studio Azure Storage)?
## <a name="references-added"></a>Dodane odwołania
Pakiet NuGet usługi Azure Storage został dodany do projektu programu Visual Studio.  
Ten pakiet dodaje następujące odwołania platformy .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Dodano parametry połączenia dla usługi Azure Storage
W pliku Web. config projektu, element został utworzony z parametrami i kluczami połączenia wybranego konta magazynu.

Aby uzyskać więcej informacji, zobacz [ASP.NET](https://www.asp.net).

