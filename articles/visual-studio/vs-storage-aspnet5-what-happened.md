---
title: Co się stało z moim projektem ASP.NET 5 (usługi połączone Visual Studio) | Microsoft Docs
description: Opisuje, co się stanie po nawiązaniu połączenia z kontem usługi Azure Storage w projekcie programu Visual Studio ASP.NET 5 przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: e29c0302ecd703cb02199df95892e24917baf8e8
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510759"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Co się stało z moim projektem ASP.NET 5 (usługa połączone usługi Visual Studio Azure Storage)?
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

Ponadto został dodany pakiet NuGet **Microsoft. Framework. Configuration. JSON** .

## <a name="connection-string-for-azure-storage-added"></a>Dodano parametry połączenia dla usługi Azure Storage
W pliku config. JSON projektu, element został utworzony przy użyciu wybranych parametrów i kluczy połączenia dla konta magazynu.

Aby uzyskać więcej informacji, zobacz [ASP.NET 5](https://www.asp.net/vnext).

