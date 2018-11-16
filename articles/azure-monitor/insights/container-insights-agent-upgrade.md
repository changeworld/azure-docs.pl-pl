---
title: Jak uaktualnić usługi Azure Monitor dla agenta kontenery (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak uaktualnić agenta usługi Log Analytics, używane przez usługi Azure Monitor dla kontenerów.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 57bfa47d60ffd8aa7c4240ab77f788773e426bd8
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51715547"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Jak uaktualnić usługi Azure Monitor dla agenta kontenery (wersja zapoznawcza)
Usługa Azure Monitor dla kontenerów używa konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux. Po wydaniu nowej wersji agenta agent nie zostanie automatycznie uaktualniona na zarządzanych klastrów Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS).

W tym artykule opisano proces uaktualniania agenta.

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Uaktualnienie agenta na monitorowanych klastra Kubernetes
Proces uaktualniania agenta składa się z dwa proste kroki. Pierwszym krokiem jest wyłączyć monitorowanie za pomocą usługi Azure Monitor dla kontenerów za pomocą narzędzia wiersza polecenia platformy Azure.  Wykonaj kroki opisane w [Wyłącz monitorowanie](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli) artykułu. Przy użyciu wiersza polecenia platformy Azure pozwala usunąć agenta z węzłów w klastrze bez wywierania wpływu na rozwiązanie i odpowiadające im dane, które są przechowywane w obszarze roboczym. 

>[!NOTE]
>Podczas wykonywania związanych z konserwacją, węzły w klastrze nie przekazuje dalej zebranych danych i widokach wydajności nie będą wyświetlane dane w czasie między usunąć agenta i zainstalowanie nowej wersji. 
>

Aby zainstalować nową wersję agenta, wykonaj czynności opisane w [dołączanie monitorowania](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) artykuł przy użyciu wiersza polecenia platformy Azure, aby ukończyć ten proces.  

Po ponownym włączeniu, monitorowania, może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji zaktualizowane dla klastra. 

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy podczas uaktualniania agenta, zapoznaj się z [przewodnik rozwiązywania problemów z](container-insights-troubleshoot.md) pomocy technicznej.