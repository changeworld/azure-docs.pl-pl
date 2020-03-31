---
title: 'Szybki start: konfigurowanie usługi Azure Security Center dla rozwiązania IoT'
description: W tym przewodniku Szybki start dowiedz się, jak skonfigurować kompleksowe rozwiązanie IoT przy użyciu usługi Azure Security Center dla IoT.
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
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: e670df359cc33c9eaca089d0ed8f9614ef8c0468
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73904151"
---
# <a name="quickstart-configure-your-iot-solution"></a>Szybki start: konfigurowanie rozwiązania IoT

Ten artykuł zawiera wyjaśnienie, jak wykonać początkową konfigurację rozwiązania zabezpieczeń IoT przy użyciu usługi Azure Security Center dla IoT. 

## <a name="azure-security-center-for-iot"></a>Azure Security Center dla IoT

Usługa Azure Security Center for IoT zapewnia kompleksowe zabezpieczenia kompleksowych rozwiązań IoT opartych na platformie Azure.

Dzięki usłudze Azure Security Center dla IoT możesz monitorować całe rozwiązanie IoT na jednym pulpicie nawigacyjnym, napawając wszystkie urządzenia IoT, platformy IoT i zasoby zaplecza na platformie Azure.

Po włączeniu usługi IoT Hub usługa Azure Security Center for IoT automatycznie identyfikuje inne usługi platformy Azure, również połączone z centrum IoT Hub i powiązane z rozwiązaniem IoT.

Oprócz automatycznego wykrywania relacji można również wybrać i wybrać inne grupy zasobów platformy Azure do oznaczenia jako część rozwiązania IoT. 

Wybrane opcje umożliwiają dodawanie całych subskrypcji, grup zasobów lub pojedynczych zasobów. 

Po zdefiniowaniu wszystkich relacji zasobów usługa Azure Security Center for IoT korzysta z usługi Azure Security Center w celu zapewnienia zaleceń dotyczących zabezpieczeń i alertów dotyczących tych zasobów.

## <a name="add-azure-resources-to-your-iot-solution"></a>Dodawanie zasobów platformy Azure do rozwiązania IoT

Aby dodać nowy zasób do rozwiązania IoT, wykonaj następujące czynności: 

1. Otwórz centrum **IoT Hub** w witrynie Azure portal. 
1. Wybierz i otwórz **zasoby** z obszarze **Zabezpieczenia** w menu po lewej stronie. 
1. Wybierz **pozycję Edytuj** i wybierz grupy zasobów, które należą do rozwiązania IoT.
1. Kliknij przycisk **Dodaj**. 

Gratulacje! Dodano nową grupę zasobów do rozwiązania IoT.

Usługa Azure Security Center dla IoT monitoruje teraz nowo dodane grupy zasobów i na powierzchniach odpowiednie zalecenia dotyczące zabezpieczeń i alerty w ramach rozwiązania IoT.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak tworzyć moduły bezpieczeństwa...

> [!div class="nextstepaction"]
> [Tworzenie modułów zabezpieczeń](quickstart-create-security-twin.md)