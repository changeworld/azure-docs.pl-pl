---
title: Czyszczenie zadania usługi Azure Stream Analytics
description: Ten artykuł to podręcznik sposobu usuwania zadań usługi Azure Stream Analytics.
services: stream-analytics
author: mamccrea
manager: kfile
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 580d05909ff3c94c982be5353b3b5e86a78fc43f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969344"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Czyszczenie zadania usługi Azure Stream Analytics

Zadania usługi Azure Stream Analytics można łatwo usunąć za pomocą witryny Azure portal, programu Azure PowerShell, zestawu Azure SDK dla platformy .net lub interfejsu API REST.

>[!NOTE] 
>Po zatrzymaniu zadania usługi Stream Analytics, dane są utrwalane tylko w magazynie danych wejściowych i wyjściowych, takich jak Event Hubs lub usługi Azure SQL Database. Jeśli są wymagane, aby usunąć dane z platformy Azure, koniecznie wykonaj proces usuwania zasobów wejściowe i wyjściowe zadania usługi Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Zatrzymaj zadanie w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Znajdź uruchamianie zadania usługi Stream Analytics i zaznacz je.

3. Na stronie zadania usługi Stream Analytics wybierz **zatrzymać** zatrzymania zadania. 

   ![Zatrzymaj zadanie](./media/stream-analytics-clean-up-your-job/stop-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Usuwanie zadania w witrynie Azure portal

1. Zaloguj się do Portalu Azure. 

2. Znajdź istniejące zadanie usługi Stream Analytics i zaznacz je.

3. Na stronie zadania usługi Stream Analytics wybierz **Usuń** można usunąć zadania. 

   ![Usuń zadanie](./media/stream-analytics-clean-up-your-job/delete-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zatrzymać lub usunąć zadania przy użyciu programu PowerShell

Aby zatrzymać zadania przy użyciu programu PowerShell, użyj [Stop-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0) polecenia cmdlet. Aby usunąć zadanie przy użyciu programu PowerShell, użyj [Remove-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0) polecenia cmdlet.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zatrzymać lub usunąć zadania przy użyciu zestawu Azure SDK dla platformy .NET

Aby zatrzymać zadanie przy użyciu zestawu Azure SDK dla platformy .NET, użyj [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metody. Usuwanie zadania przy użyciu zestawu Azure SDK dla platformy .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metody.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zatrzymać lub usunąć zadania za pomocą interfejsu API REST

Aby zatrzymać zadania za pomocą interfejsu API REST, zobacz [zatrzymać](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metody. Aby usunąć zadanie, używając interfejsu API REST, zobacz [Usuń](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metody.