---
title: Monitorowanie usługi Azure Stream Analytics i zarządzanie nią za pomocą programu Visual Studio
description: W tym artykule opisano sposób używania programu Visual Studio do monitorowania zadań usługi Azure Stream Analytics i zarządzania nimi.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: e43a8d157baaf7e02ab7f8db4c777009d2a0abe9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431684"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Monitorowanie zadań usługi Stream Analytics i zarządzanie nimi za pomocą programu Visual Studio

W tym artykule pokazano, jak monitorować zadanie usługi Stream Analytics w programie Visual Studio. Narzędzia usługi Azure Stream Analytics dla programu Visual Studio zapewniają środowisko monitorowania podobne do witryny Azure portal bez konieczności opuszczania ide. Można rozpocząć monitorowanie zadania, jak tylko **przesłać do platformy Azure** z **Script.asaql**lub można monitorować istniejące zadania, niezależnie od tego, jak zostały utworzone. 

## <a name="job-summary"></a>Podsumowanie zadania

**Podsumowanie zadań** i **metryki zadań** zapewniają szybką migawkę zadania. Na pierwszy rzut oka można określić stan zadania i informacje o zdarzeniu.]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Metryki zadania

Podsumowanie **zadania** można zwinąć i kliknąć kartę **Metryki zadań,** aby wyświetlić wykres z ważnymi metrykami. Sprawdź i usuń zaznaczenie typów metryk, aby dodać je i usunąć z wykresu.

![Metryki usługi Stream Analytics w programie Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Monitorowanie błędów

Błędy można również monitorować, klikając kartę **Błędy.**

![Błędy usługi Stream Analytics w programie Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Tworzenie zadania usługi Azure Stream Analytics za pomocą programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Instalowanie narzędzi usługi Azure Stream Analytics dla programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 


