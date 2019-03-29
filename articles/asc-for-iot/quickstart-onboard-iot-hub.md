---
title: Włącz ASC dla usługi IoT w wersji zapoznawczej Centrum IoT | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć ASC dla usługi IoT w usłudze IoT Hub.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: cea7517a99358d41a8ba60a78b4e2bfdbdeaf0e8
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576228"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Szybki start: Włącz usługę w usłudze IoT Hub

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten artykuł zawiera wyjaśnienie, jak włączyć ASC usługi IoT (wersja zapoznawcza) w usłudze IoT Hub.  

> [!NOTE]
> ASC IoT aktualnie obsługuje tylko w warstwie standardowa i wyższych centra IoT Hub.
>ASC IoT jest rozwiązaniem jednym Centrum. Jeśli potrzebujesz wielu centrach, wiele rozwiązań są wymagane. 

## <a name="prerequisites-for-enabling-the-service"></a>Wymagania wstępne dotyczące włączania usługi

- Obszar roboczy usługi Log Analytics
  - Dwa typy informacji są domyślnie przechowywane w obszarze roboczym usługi Log Analytics przez usługę ASC dla IoT; **alerty zabezpieczeń** i **zalecenia**. 
  - Można wybrać dodać magazyn typu dodatkowe informacje, **nieprzetworzonych zdarzeń**. Należy pamiętać, że przechowywanie **nieprzetworzonych zdarzeń** w usłudze Log Analytics niesie ze sobą dodatkowe koszty. 
- Usługa IoT Hub (w warstwie standardowa lub wyższa)

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Włączanie usługi ASC dla Internetu rzeczy na Twoim Centrum IoT Hub 

Aby włączyć zabezpieczenia w usłudze IoT Hub, wykonaj następujące czynności: 

1. Otwórz swoje **usługi IoT Hub** w witrynie Azure portal. 
2. Wybierz i Otwórz **zabezpieczeń** menu po lewej stronie. 
3. Wybierz **Włącz zabezpieczenia IoT**. 
4. Podaj szczegóły obszaru roboczego usługi Log Analytics. 
   - Zdecydować się na przechowywanie **nieprzetworzonych zdarzeń** oprócz domyślne typy informacji magazynu, pozostawiając **nieprzetworzone zdarzenia** Przełącz **na**. 
   - Wybiera umożliwiające **bliźniaczej reprezentacji kolekcji** , pozostawiając **bliźniaczej reprezentacji kolekcji** Przełącz **na**. 
5. Kliknij przycisk **OK**. 
6. Kliknij pozycję **Zapisz**. 

Gratulacje! Ukończono włączanie ASC IoT w usłudze IoT Hub. 

## <a name="next-steps"></a>Kolejne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak skonfigurować rozwiązanie...

> [!div class="nextstepaction"]
> [Konfiguruj rozwiązanie](quickstart-configure-your-solution.md)