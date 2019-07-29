---
title: Skonfiguruj Azure Security Center rozwiązania IoT | Microsoft Docs
description: Dowiedz się, jak skonfigurować kompleksowe rozwiązanie IoT przy użyciu Azure Security Center dla IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 15c8aabbcb19d009bb202d111dc7f80da4cebff1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597032"
---
# <a name="quickstart-configure-your-iot-solution"></a>Szybki start: Skonfiguruj rozwiązanie IoT

Ten artykuł zawiera informacje na temat przeprowadzania początkowej konfiguracji rozwiązania do ochrony IoT przy użyciu Azure Security Center dla IoT. 

## <a name="azure-security-center-for-iot"></a>Azure Security Center dla IoT

Azure Security Center dla IoT oferuje kompleksowe zabezpieczenia kompleksowych rozwiązań IoT opartych na platformie Azure.

Dzięki Azure Security Center dla IoT możesz monitorować całe rozwiązanie IoT na jednym pulpicie nawigacyjnym, obsłużyć wszystkie urządzenia IoT, platformy IoT i zasoby zaplecza na platformie Azure.

Po włączeniu IoT Hub usługa IoT Azure Security Center automatycznie identyfikuje inne usługi platformy Azure, połączyły się z Centrum IoT i powiązane z Twoim rozwiązaniem IoT.

Oprócz automatycznego wykrywania relacji można także wybierać i wybierać inne grupy zasobów platformy Azure, które mają być używane w ramach rozwiązania IoT.

Wybrane opcje umożliwiają dodawanie całych subskrypcji, grup zasobów lub pojedynczych zasobów.

Po zdefiniowaniu wszystkich relacji zasobów Azure Security Center dla usługi IoT wykorzystuje Azure Security Center, aby udostępnić zalecenia dotyczące zabezpieczeń i alerty dotyczące tych zasobów.

## <a name="add-azure-resources-to-your-iot-solution"></a>Dodawanie zasobów platformy Azure do rozwiązania IoT

Aby dodać nowy zasób do rozwiązania IoT, wykonaj następujące czynności: 

1. Otwórz **IoT Hub** w Azure Portal. 
2. Wybierz i Otwórz **zasoby** w obszarze **zabezpieczenia** z menu po lewej stronie. 
3. Wybierz pozycję **Edytuj** i wybierz grupy zasobów należące do rozwiązania IoT.
5. Kliknij przycisk **Dodaj**. 

Gratulacje! Dodano nową grupę zasobów do rozwiązania IoT.

Azure Security Center usługi IoT teraz monitoruje nowo dodane grupy zasobów i prezentuje odpowiednie zalecenia dotyczące zabezpieczeń i alerty w ramach rozwiązania IoT.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć moduły zabezpieczeń...

> [!div class="nextstepaction"]
> [Tworzenie modułów zabezpieczeń](quickstart-create-security-twin.md)