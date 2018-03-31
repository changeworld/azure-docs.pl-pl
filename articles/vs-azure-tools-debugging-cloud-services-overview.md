---
title: Usługi w chmurze opcji debugowania Azure | Dokumentacja firmy Microsoft
description: Debugowanie usług w chmurze Azure
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: mikejo
ms.openlocfilehash: 3d559bea8043ebcde9ffc655b617f711bfe0ea7f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Informacje dotyczące różnych sposobów, aby debugować usługi w chmurze Azure
Ten artykuł zawiera łącza do różnych sposobów debugowania usługi w chmurze Azure. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Debugowanie usługi w chmurze platformy Azure w programie Visual Studio
Można oszczędzić czas i pieniądze przy użyciu platformy Azure obliczeniowe emulatora debugowania usługi w chmurze na komputerze lokalnym. Przez lokalnie debugowania usługi przed wdrożeniem, można zwiększyć niezawodność i wydajność bez płatności dla czasu obliczeniowego. Jednak niektóre błędy mogą wystąpić tylko w przypadku uruchamiania usługi w chmurze na platformie Azure. Błędy występujące tylko w przypadku uruchamiania usługi w chmurze na platformie Azure było możliwe, należy włączyć debugowanie zdalne po opublikowaniu usługi, a następnie dołączanie debugera do wystąpienia roli. Aby uzyskać więcej informacji, zobacz [debugowania usługi w chmurze na komputerze lokalnym](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

## <a name="using-intellitrace"></a>Używanie funkcji IntelliTrace 
Jeśli używasz programu Visual Studio Enterprise do zapisu ról docelową programu .NET Framework 4.5, można włączyć funkcji IntelliTrace w tym czasie wdrożenie usługi w chmurze platformy Azure w programie Visual Studio. IntelliTrace zawiera dziennik, który z programem Visual Studio umożliwia Debuguj aplikację tak, jakby były uruchomione w systemie Azure. Aby uzyskać więcej informacji, zobacz [debugowania usługi opublikowana chmura za pomocą funkcji IntelliTrace i Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).

## <a name="remote-debugging"></a>Debugowanie zdalne 
Umożliwia zdalne debugowanie usług w chmurze w momencie podczas wdrażania usługi w chmurze w programie Visual Studio. Jeśli zdecydujesz się na włączanie debugowania zdalnego we wdrożeniu, usług debugowania zdalnego są instalowane na maszynach wirtualnych, które Uruchom każde wystąpienie roli. Te usługi — takie jak `msvsmon.exe` — nie wpływają na wydajność lub spowodować dodatkowych kosztów. Aby uzyskać więcej informacji, zobacz [debugowania usługi w chmurze na platformie Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).

## <a name="next-steps"></a>Kolejne kroki
- [Debugowanie usługi w chmurze Azure lub maszyny Wirtualnej w programie Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md) — Dowiedz się szczegóły dotyczące debugowania usług w chmurze Azure.