---
title: Czyszczenie zadania usługi Azure Stream Analytics
description: W tym artykule przedstawiono różne metody usuwania zadań usługi Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 093ada2b2751540b986154be3a2f333784c1259f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173290"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Zatrzymywanie lub usuwanie zadania Azure Stream Analytics

Zadania Azure Stream Analytics można łatwo zatrzymać lub usunąć za pomocą Azure Portal, Azure PowerShell, zestawu Azure SDK dla platformy .NET lub interfejsu API REST. Nie można odzyskać zadania Stream Analytics po jego usunięciu.

>[!NOTE] 
>Po zatrzymaniu zadania usługi Stream Analytics, dane są utrwalane tylko w magazynie danych wejściowych i wyjściowych, takich jak Event Hubs lub usługi Azure SQL Database. Jeśli są wymagane, aby usunąć dane z platformy Azure, koniecznie wykonaj proces usuwania zasobów wejściowe i wyjściowe zadania usługi Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Zatrzymaj zadanie w witrynie Azure portal

Po zatrzymaniu zadania zasoby są cofane i zatrzymują przetwarzanie zdarzeń. Opłaty związane z tym zadaniem również są zatrzymane. Jednak cała konfiguracja jest zachowywana, a zadanie można uruchomić ponownie później 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Znajdź uruchamianie zadania usługi Stream Analytics i zaznacz je.

3. Na stronie zadania usługi Stream Analytics wybierz **zatrzymać** zatrzymania zadania. 

   ![Zatrzymaj zadanie usługi Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Usuwanie zadania w witrynie Azure portal

>[!WARNING] 
>Nie można odzyskać zadania Stream Analytics po jego usunięciu.

1. Zaloguj się do Portalu Azure. 

2. Znajdź istniejące zadanie usługi Stream Analytics i zaznacz je.

3. Na stronie zadania usługi Stream Analytics wybierz **Usuń** można usunąć zadania. 

   ![Usuń zadanie usługi Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zatrzymać lub usunąć zadania przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby zatrzymać zadanie przy użyciu programu PowerShell, użyj polecenia cmdlet [stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) . Aby usunąć zadanie przy użyciu programu PowerShell, użyj polecenia cmdlet [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zatrzymać lub usunąć zadania przy użyciu zestawu Azure SDK dla platformy .NET

Aby zatrzymać zadanie przy użyciu zestawu Azure SDK dla platformy .NET, użyj [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metody. Usuwanie zadania przy użyciu zestawu Azure SDK dla platformy .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metody.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zatrzymać lub usunąć zadania za pomocą interfejsu API REST

Aby zatrzymać zadania za pomocą interfejsu API REST, zobacz [zatrzymać](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metody. Aby usunąć zadanie, używając interfejsu API REST, zobacz [Usuń](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metody.
