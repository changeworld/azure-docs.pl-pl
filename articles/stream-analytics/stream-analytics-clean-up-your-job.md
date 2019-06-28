---
title: Czyszczenie zadania usługi Azure Stream Analytics
description: W tym artykule przedstawiono różne metody usuwania zadań usługi Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: cb81c73f7946a10bae0470a55dcf1c0d55c2b847
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330048"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Zatrzymać lub usunąć zadania usługi Azure Stream Analytics

Zadania usługi Azure Stream Analytics można łatwo zatrzymać lub usunąć za pomocą witryny Azure portal, programu Azure PowerShell, zestawu Azure SDK dla platformy .net lub interfejsu API REST. Nie można odzyskać zadania usługi Stream Analytics, gdy został on usunięty.

>[!NOTE] 
>Po zatrzymaniu zadania usługi Stream Analytics, dane są utrwalane tylko w magazynie danych wejściowych i wyjściowych, takich jak Event Hubs lub usługi Azure SQL Database. Jeśli są wymagane, aby usunąć dane z platformy Azure, koniecznie wykonaj proces usuwania zasobów wejściowe i wyjściowe zadania usługi Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Zatrzymaj zadanie w witrynie Azure portal

Po zatrzymaniu zadania zasoby są deprovisionned i zatrzymuje się przetwarzanie zdarzeń. Opłaty związane z tym zadanie również zostaną zatrzymane. Jednakże wszystkich konfiguracji są zachowywane i można ponownie uruchomić zadanie później 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Znajdź uruchamianie zadania usługi Stream Analytics i zaznacz je.

3. Na stronie zadania usługi Stream Analytics wybierz **zatrzymać** zatrzymania zadania. 

   ![Zatrzymaj zadanie usługi Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Usuwanie zadania w witrynie Azure portal

>[!WARNING] 
>Nie można odzyskać zadania usługi Stream Analytics, gdy został on usunięty.

1. Zaloguj się do Portalu Azure. 

2. Znajdź istniejące zadanie usługi Stream Analytics i zaznacz je.

3. Na stronie zadania usługi Stream Analytics wybierz **Usuń** można usunąć zadania. 

   ![Usuń zadanie usługi Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zatrzymać lub usunąć zadania przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby zatrzymać zadania przy użyciu programu PowerShell, użyj [Stop AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) polecenia cmdlet. Aby usunąć zadanie przy użyciu programu PowerShell, użyj [AzStreamAnalyticsJob Usuń](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) polecenia cmdlet.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zatrzymać lub usunąć zadania przy użyciu zestawu Azure SDK dla platformy .NET

Aby zatrzymać zadanie przy użyciu zestawu Azure SDK dla platformy .NET, użyj [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metody. Usuwanie zadania przy użyciu zestawu Azure SDK dla platformy .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metody.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zatrzymać lub usunąć zadania za pomocą interfejsu API REST

Aby zatrzymać zadania za pomocą interfejsu API REST, zobacz [zatrzymać](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metody. Aby usunąć zadanie, używając interfejsu API REST, zobacz [Usuń](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metody.
