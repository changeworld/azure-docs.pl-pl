---
title: Włącz Azure Security Center dla usługi IoT w IoT Hub | Microsoft Docs
description: Dowiedz się, jak włączyć usługę Azure Security Center dla usługi IoT w IoT Hub.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3d9c5352a90d5bcacbaf27b7b62be61fc404e87a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299485"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Szybki start: Dołączanie Azure Security Center usługi IoT w programie IoT Hub

Ten artykuł zawiera informacje na temat sposobu włączania usługi IoT Azure Security Center dla istniejących IoT Hub. Jeśli obecnie nie masz IoT Hub, zobacz [tworzenie IoT Hub przy użyciu Azure Portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) , aby rozpocząć pracę. 

> [!NOTE]
> Azure Security Center IoT obecnie obsługuje tylko centra IoT w warstwie Standardowa.
> Azure Security Center IoT to pojedyncze rozwiązanie centrum. Jeśli potrzebujesz wielu centrów, wymagane jest wiele Azure Security Center dla rozwiązań IoT. 

## <a name="prerequisites-for-enabling-the-service"></a>Wymagania wstępne dotyczące włączania usługi

- Obszar roboczy usługi Log Analytics
  - Dwa typy informacji są domyślnie przechowywane w obszarze roboczym Log Analytics przez Azure Security Center dla IoT; **alerty** i **zalecenia dotyczące**zabezpieczeń. 
  - Możesz dodać magazyn o dodatkowym typie informacji, **zdarzenia pierwotne**. Należy pamiętać, że przechowywanie **nieprzetworzonych zdarzeń** w log Analytics obejmuje dodatkowe koszty magazynowania. 
- IoT Hub (warstwa standardowa)
- Spełnia wszystkie [wymagania wstępne dotyczące usługi](service-prerequisites.md) 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Włącz Azure Security Center IoT na IoT Hub 

Aby włączyć zabezpieczenia na IoT Hub, wykonaj następujące czynności: 

1. Otwórz **IoT Hub** w Azure Portal. 
1. W menu **zabezpieczenia** kliknij pozycję **Zabezpiecz swoje rozwiązanie IoT**
1. Pozostaw **wybrane** ustawienie domyślne. 
1. Wybierz obszar roboczy usługi log Analytics.
1. Podaj szczegóły obszaru roboczego Log Analytics. 
   - Wybierz, aby włączyć **zbieranie sznurów** , pozostawiając przełącznik kolekcjiz **przędzą** .
   - Wybierz opcję przechowywania **nieprzetworzonych zdarzeń** Oprócz domyślnych typów informacji magazynu, wybierając pozycję **przechowuj nieprzetworzone zdarzenia zabezpieczeń** w log Analytics. Pozostaw **nieprzetworzony** przełącznik zdarzeń. 
    
1. Kliknij pozycję **Zapisz**. 

Gratulacje! Ukończono Włączanie Azure Security Center IoT na IoT Hub. 

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby skonfigurować Twoje rozwiązanie...

> [!div class="nextstepaction"]
> [Skonfiguruj rozwiązanie](quickstart-configure-your-solution.md)


