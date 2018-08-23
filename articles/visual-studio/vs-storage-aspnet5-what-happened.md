---
title: Co się stało z moim projektem platformy ASP.NET 5 (usług połączonych programu Visual Studio) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, co się dzieje po łączenie się z kontem usługi Azure storage w projekcie programu Visual Studio ASP.NET 5 za pomocą programu Visual Studio podłączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 0e38e570b5ecb2a19af943e0dde96e2ed66f3507
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057076"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Co się stało z moim projektem platformy ASP.NET 5 (usług połączonych programu Visual Studio usługi Azure Storage)?
## <a name="references-added"></a>Odwołania dodane
Pakiet NuGet usługi Azure Storage został dodany do projektu programu Visual Studio.  
Ten pakiet dodaje następujące odwołania do platformy .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

Ponadto pakiet NuGet **Microsoft.Framework.Configuration.Json** został dodany.

## <a name="connection-string-for-azure-storage-added"></a>Parametry połączenia dla usługi Azure Storage dodane
W pliku config.json projektu element został utworzony przy użyciu parametrów połączenia i klucz konta wybrany magazyn.

Aby uzyskać więcej informacji, zobacz [platformy ASP.NET 5](http://www.asp.net/vnext).

