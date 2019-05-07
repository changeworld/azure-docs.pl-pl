---
title: Konfigurowanie usługi Azure Security Center dla rozwiązania IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować end-to-end rozwiązania IoT przy użyciu usługi Azure Security Center dla IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: c60b421e9b60c6a2191fe2be189d1abd1c328f24
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200797"
---
# <a name="quickstart-configure-your-iot-solution"></a>Szybki start: Konfiguruj rozwiązanie IoT

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten artykuł zawiera omówienie sposobu przeprowadzenia początkowej konfiguracji rozwiązania zabezpieczeń IoT za pomocą usługi ASC dla IoT. 

## <a name="azure-security-center-asc-for-iot"></a>Usługa Azure Security Center (ASC) dla IoT

ASC IoT zapewnia kompleksowe zabezpieczenia end-to-end rozwiązania IoT opartych na platformie Azure.

Za pomocą usługi ASC dla IoT można monitorować całego rozwiązania IoT na jednym pulpicie nawigacyjnym, dzięki czemu są ujawniane wszystkich urządzeń IoT, IoT platform i zasobów wewnętrznej bazy danych na platformie Azure.

Po włączeniu w usłudze IoT Hub ASC IoT automatycznie rozpoznaje innych usług platformy Azure, również podłączonych do Centrum IoT i związane z rozwiązania IoT.

Oprócz wykrywania automatycznego relacji można można również wybrać które innych zasobów platformy Azure do tagu jako część swojego rozwiązania IoT.
Opcje umożliwiają dodanie całej subskrypcji, grupy zasobów lub pojedynczych zasobów.

Po zdefiniowaniu wszystkich relacji między zasobami, ASC IoT korzysta z usługi Azure Security Center, aby zapewnić są zalecenia dotyczące zabezpieczeń i alerty dla tych zasobów.

## <a name="add-azure-resources-to-your-iot-solution"></a>Dodawanie zasobów platformy Azure do swojego rozwiązania IoT

Aby dodać nowy zasób do rozwiązania IoT, wykonaj następujące czynności: 

1. Otwórz swoje **usługi IoT Hub** w witrynie Azure portal. 
2. Wybierz i Otwórz **zasobów** w obszarze **zabezpieczeń** menu po lewej stronie. 
3. Wybierz **Dodaj zasoby**.
4. Wybierz zasoby, które należą do swojego rozwiązania IoT.
5. Kliknij pozycję **Add** (Dodaj). 

Gratulacje! Nowy zasób zostały dodane do rozwiązania IoT.

ASC IoT teraz monitory, które Cię nowo dodane zasobów oraz zalecenia dotyczące zabezpieczeń istotne powierzchnie i alerty w ramach swojego rozwiązania IoT.

## <a name="next-steps"></a>Kolejne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć modułów zabezpieczeń...

> [!div class="nextstepaction"]
> [Tworzenie modułów zabezpieczeń](quickstart-create-security-twin.md)