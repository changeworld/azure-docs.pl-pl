---
title: Oczyszczanie zadania usługi Azure Stream Analytics
description: W tym artykule przedstawiono różne metody usuwania zadań usługi Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426482"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Zatrzymywać lub usuwać zadanie usługi Azure Stream Analytics

Zadania usługi Azure Stream Analytics można łatwo zatrzymać lub usunąć za pośrednictwem witryny Azure portal, Azure PowerShell, Azure SDK dla platformy .Net lub interfejsu API REST. Nie można odzyskać zadania usługi Stream Analytics po jego usunięciu.

>[!NOTE] 
>Po zatrzymaniu zadania usługi Stream Analytics dane będą się powtarzać tylko w magazynie danych wejściowych i wyjściowych, takich jak Centra zdarzeń lub baza danych SQL Azure. Jeśli wymagane jest usunięcie danych z platformy Azure, należy wykonać proces usuwania zasobów wejściowych i wyjściowych zadania usługi Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Zatrzymywać pracę w witrynie Azure portal

Po zatrzymaniu zadania zasoby są usuwane z obsługi administracyjnej i zatrzymuje przetwarzanie zdarzeń. Opłaty związane z tym zadaniem również są zatrzymane. Jednak cała konfiguracja jest zachowana i można ponownie uruchomić zadanie później 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 

2. Znajdź uruchomione zadanie usługi Stream Analytics i wybierz je.

3. Na stronie zadania usługi Stream Analytics wybierz pozycję **Zatrzymaj,** aby zatrzymać zadanie. 

   ![Zatrzymywać zadanie usługi Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Usuwanie zadania w witrynie Azure portal

>[!WARNING] 
>Nie można odzyskać zadania usługi Stream Analytics po jego usunięciu.

1. Zaloguj się do Portalu Azure. 

2. Znajdź istniejące zadanie usługi Stream Analytics i wybierz je.

3. Na stronie zadania usługi Stream Analytics wybierz pozycję **Usuń,** aby usunąć zadanie. 

   ![Usuwanie zadania usługi Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Zatrzymywać lub usuwać zadanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby zatrzymać zadanie przy użyciu programu PowerShell, należy użyć polecenia cmdlet [Stop-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) Aby usunąć zadanie przy użyciu programu PowerShell, należy użyć polecenia cmdlet [Remove-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob)

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Zatrzymywać lub usuwać zadanie przy użyciu narzędzia Azure SDK dla platformy .NET

Aby zatrzymać zadanie przy użyciu narzędzia Azure SDK dla platformy .NET, należy użyć [metody StreamingJobsOperationsExtensions.BeginStop.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) Aby usunąć zadanie przy użyciu narzędzia Azure SDK dla platformy .NET, [Metoda StreamingJobsOperationsExtensions.BeginDelete.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet)

## <a name="stop-or-delete-a-job-using-rest-api"></a>Zatrzymywać lub usuwać zadanie przy użyciu interfejsu REST API

Aby zatrzymać zadanie przy użyciu interfejsu API REST, zapoznaj się [z Stop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metody. Aby usunąć zadanie przy użyciu interfejsu API REST, zapoznaj się [z delete](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metody.
