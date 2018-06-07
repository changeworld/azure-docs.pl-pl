---
title: Oczyszczanie zadania usługi analiza strumienia Azure
description: W tym artykule jest przewodnikiem podczas sposób usuwania zadania usługi analiza strumienia Azure.
services: stream-analytics
author: mamccrea
manager: kfile
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 4773f542f12ae1773e881106bc8948c663bfd1e3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660924"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Oczyszczanie zadania usługi analiza strumienia Azure

Zadania usługi analiza strumienia Azure można łatwo usunąć za pomocą portalu Azure, programu Azure PowerShell zestawu Azure SDK dla platformy .net lub interfejsu API REST.

>[!NOTE] 
>Po zatrzymaniu zadania usługi analiza strumienia danych będzie nadal występować tylko w magazynie wejścia i wyjścia, takich jak usługi Event Hubs lub baza danych SQL Azure. Jeśli są wymagane do usunięcia danych z platformy Azure, należy postępować zgodnie proces usuwania zasobów wejściowych i wyjściowych zadania usługi analiza strumienia.

## <a name="stop-a-job-in-azure-portal"></a>Zatrzymanie zadania w portalu Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Znajdź uruchomione zadania usługi analiza strumienia i zaznacz je.

3. Na stronie zadania usługi analiza strumienia wybierz **zatrzymać** zatrzymania zadania. 

   ![Zatrzymaj zadanie](./media/stream-analytics-clean-up-your-job/stop-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Usuwanie zadania w portalu Azure

1. Zaloguj się do Portalu Azure. 

2. Znajdź istniejące zadania usługi analiza strumienia i zaznacz je.

3. Na stronie zadania usługi analiza strumienia wybierz **usunąć** można usunąć zadania. 

   ![Usuń zadanie](./media/stream-analytics-clean-up-your-job/delete-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zatrzymać lub usunąć zadania przy użyciu programu PowerShell

Aby zatrzymać zadania przy użyciu programu PowerShell, użyj [Stop AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0) polecenia cmdlet. Aby usunąć zadania przy użyciu programu PowerShell, użyj [AzureRmStreamAnalyticsJob Usuń](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0) polecenia cmdlet.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zatrzymać lub usunąć zadania przy użyciu zestawu Azure SDK dla platformy .NET

Aby zatrzymać zadania przy użyciu zestawu Azure SDK dla platformy .NET, należy użyć [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metody. Usuwanie zadania przy użyciu zestawu Azure SDK dla platformy .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metody.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zatrzymać lub usunąć zadania przy użyciu interfejsu API REST

Aby zatrzymać zadania przy użyciu interfejsu API REST, należy zapoznać się [zatrzymać](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#stop) metody. Aby usunąć zadania przy użyciu interfejsu API REST, należy zapoznać się [usunąć](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#delete) metody.