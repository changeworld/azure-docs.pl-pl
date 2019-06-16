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
ms.openlocfilehash: 71a95e1974cbcec9afcc3337eb37275532e1b527
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60505392"
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

Aby uzyskać więcej informacji, zobacz [platformy ASP.NET 5](https://www.asp.net/vnext).

