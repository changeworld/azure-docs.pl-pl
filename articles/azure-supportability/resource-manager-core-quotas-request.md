---
title: Limit przydziału usługi Azure Resource Manager procesorów vCPU zwiększyć żądań | Dokumentacja firmy Microsoft
description: Żądań zwiększenia limitu przydziału usługi Azure Resource Manager procesorów wirtualnych.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9a997af984b92ea59cc02d99fbd66d8967ca31bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076805"
---
# <a name="quota-increase-requests"></a>Żądania zwiększenia limitu przydziału

Limity przydziału procesorów wirtualnych Menedżera zasobów, dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są wymuszane w dwóch warstwach dla każdej subskrypcji, w każdym regionie. 

Pierwsza warstwa jest całkowita regionalny limit procesorów wirtualnych Vcpu (we wszystkich maszyn wirtualnych serii), a druga warstwa na serię maszyn wirtualnych limit procesorów wirtualnych (np. wirtualnych procesorów CPU serii D). Zawsze, gdy nowa maszyna wirtualna ma zostać wdrożony, Suma użytkowania nowych i istniejących wirtualnych procesorów CPU w tej serii maszyny Wirtualnej nie może przekraczać limit przydziału procesorów wirtualnych, zatwierdzone dla tego konkretnego serię maszyn wirtualnych. Co więcej liczbę łączna liczba procesorów wirtualnych vCPU nowych i istniejących, wdrożone we wszystkich maszyn wirtualnych serii nie powinna przekraczać łączny regionalnego limitu przydziału procesorów wirtualnych Vcpu zatwierdzone dla subskrypcji. Jeśli jedno z tych limitów przydziału zostaną przekroczone, wdrażanie maszyny Wirtualnej będzie niemożliwe.
Można zażądać zwiększenia limitu przydziału procesorów wirtualnych na maszyny Wirtualne serii z witryny Azure portal. Zwiększenie limitu przydziału maszyny Wirtualnej serii automatycznie zwiększa łączny regionalny limit procesorów wirtualnych Vcpu jednakową kwotę. 

Po utworzeniu nowej subskrypcji domyślnej łączna liczba procesorów wirtualnych Vcpu nie może być równa sumie domyślne limity przydziału procesorów wirtualnych w przypadku wszystkich poszczególnych serii maszyny Wirtualnej. Może to spowodować, że subskrypcja z wystarczający przydział dla każdej poszczególnych serii maszyny Wirtualnej, który chcesz wdrożyć, ale nie ma wystarczającej ilości przydział łączna liczba procesorów wirtualnych Vcpu dla wszystkich wdrożeń. W takim przypadku należy przesłać żądanie zwiększyć całkowity regionalny limit procesorów wirtualnych Vcpu jawnie. Łączna liczba procesorów wirtualnych Vcpu limit nie może przekraczać sumę zatwierdzonych przydziału we wszystkich serię maszyn wirtualnych dla regionu.

Więcej informacji na temat limitów przydziału na [stronie limity przydziału procesorów wirtualnych maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) i [limity usług i subskrypcji platformy Azure](https://aka.ms/quotalimits) strony. 

