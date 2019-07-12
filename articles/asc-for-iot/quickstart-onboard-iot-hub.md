---
title: Usługa Azure Security Center dla usługi IoT w wersji zapoznawczej Centrum IoT | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć usługę Azure Security Center dla usługi IoT w usłudze IoT Hub.
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
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: f81fb7aeed1b704ebdd82c1f5b83c33a4b05e9ca
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618003"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Szybki start: Włącz usługę w usłudze IoT Hub

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten artykuł zawiera wyjaśnienie, jak włączyć usługi Azure Security Center (ASC) usługi IoT (wersja zapoznawcza) w usłudze IoT Hub.  

> [!NOTE]
> Centrum zabezpieczeń Azure dla IoT aktualnie obsługuje tylko centrów iot Hub w warstwie standardowa.
> Centrum zabezpieczeń Azure dla IoT jest rozwiązaniem jednym Centrum. Jeśli potrzebujesz wielu centrach, wiele rozwiązań są wymagane. 

## <a name="prerequisites-for-enabling-the-service"></a>Wymagania wstępne dotyczące włączania usługi

- Obszar roboczy usługi log Analytics
  - Dwa typy informacji są domyślnie przechowywane w obszarze roboczym usługi Log Analytics przez usługę ASC dla IoT; **alerty zabezpieczeń** i **zalecenia**. 
  - Można wybrać dodać magazyn typu dodatkowe informacje, **nieprzetworzonych zdarzeń**. Należy pamiętać, że przechowywanie **nieprzetworzonych zdarzeń** w usłudze Log Analytics niesie ze sobą dodatkowe koszty. 
- Usługa IoT Hub (warstwa standardowa)
- Spełnisz [usługi wymagań wstępnych](service-prerequisites.md) 
- Usługi obsługiwane regiony
  - Środkowe stany USA
  - Europa Północna
  - Azja Południowo-Wschodnia

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Włączanie usługi ASC dla Internetu rzeczy na Twoim Centrum IoT Hub 

Aby włączyć zabezpieczenia w usłudze IoT Hub, wykonaj następujące czynności: 

1. Otwórz swoje **usługi IoT Hub** w witrynie Azure portal. 
2. W obszarze **zabezpieczeń** menu, kliknij przycisk **Przegląd**, następnie kliknij przycisk **Start (wersja zapoznawcza)** . 
3. Wybierz **Włącz zabezpieczenia IoT**. 
4. Podaj szczegóły obszaru roboczego usługi Log Analytics. 
   - Zdecydować się na przechowywanie **nieprzetworzonych zdarzeń** oprócz domyślne typy informacji magazynu, pozostawiając **nieprzetworzone zdarzenia** Przełącz **na**. 
   - Wybiera umożliwiające **bliźniaczej reprezentacji kolekcji** , pozostawiając **bliźniaczej reprezentacji kolekcji** Przełącz **na**. 
5. Kliknij polecenie **Zapisz**. 

Gratulacje! Ukończono włączanie ASC IoT w usłudze IoT Hub. 

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak skonfigurować rozwiązanie...

> [!div class="nextstepaction"]
> [Konfiguruj rozwiązanie](quickstart-configure-your-solution.md)
