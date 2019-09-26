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
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: a546d153c6fe4f14ccc8c21308bd4a33385870c3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299687"
---
# <a name="quickstart-configure-your-iot-solution"></a>Szybki start: Skonfiguruj rozwiązanie IoT

Ten artykuł zawiera informacje na temat przeprowadzania początkowej konfiguracji rozwiązania do ochrony IoT przy użyciu Azure Security Center dla IoT. 

## <a name="azure-security-center-for-iot"></a>Azure Security Center dla IoT

Azure Security Center dla IoT oferuje kompleksowe zabezpieczenia kompleksowych rozwiązań IoT opartych na platformie Azure.

Dzięki Azure Security Center dla IoT możesz monitorować całe rozwiązanie IoT na jednym pulpicie nawigacyjnym, obsłużyć wszystkie urządzenia IoT, platformy IoT i zasoby zaplecza na platformie Azure.

Po włączeniu usługi IoT w IoT Hub Azure Security Center w celu automatycznego zidentyfikowania innych usług platformy Azure, również połączona z IoT Hub i związanych z Twoim rozwiązaniem IoT.

Oprócz automatycznego wykrywania relacji można także wybierać i wybierać inne grupy zasobów platformy Azure, które mają być używane w ramach rozwiązania IoT. 

Wybrane opcje umożliwiają dodawanie całych subskrypcji, grup zasobów lub pojedynczych zasobów. 

Po zdefiniowaniu wszystkich relacji zasobów Azure Security Center dla usługi IoT wykorzystuje Azure Security Center, aby udostępnić zalecenia dotyczące zabezpieczeń i alerty dotyczące tych zasobów.

## <a name="add-azure-resources-to-your-iot-solution"></a>Dodawanie zasobów platformy Azure do rozwiązania IoT

Aby dodać nowy zasób do rozwiązania IoT, wykonaj następujące czynności: 

1. Otwórz **IoT Hub** w Azure Portal. 
1. Wybierz i Otwórz **zasoby** z opcji **zabezpieczenia** w menu po lewej stronie. 
1. Wybierz pozycję **Edytuj** i wybierz grupy zasobów należące do rozwiązania IoT.
1. Kliknij pozycję **Dodaj**. 

Gratulacje! Dodano nową grupę zasobów do rozwiązania IoT.

Azure Security Center usługi IoT teraz monitoruje nowo dodane grupy zasobów i prezentuje odpowiednie zalecenia dotyczące zabezpieczeń i alerty w ramach rozwiązania IoT.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć moduły zabezpieczeń...

> [!div class="nextstepaction"]
> [Tworzenie modułów zabezpieczeń](quickstart-create-security-twin.md)