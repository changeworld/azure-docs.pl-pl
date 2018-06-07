---
title: Zabezpieczanie Internetu rzeczy (IoT) na platformie Azure | Dokumentacja firmy Microsoft
description: " Azure Internetu rzeczy (IoT) usług oferuje szeroką gamę możliwości. Ten artykuł pomaga zrozumieć, jak zabezpieczyć Twojego rozwiązania IoT na platformie Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: d5c1cb22fdfe59bd8409f9595b2fa4c3a0df771e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641241"
---
# <a name="internet-of-things-security-overview"></a>Przegląd zabezpieczeń Internetu rzeczy
Azure Internetu rzeczy (IoT) usług oferuje szeroką gamę możliwości. Są to usługi klasy korporacyjnej, które pozwalają wykonywać następujące operacje:

* Zbieranie danych z urządzeń
* Analizowanie strumieni danych w ruchu
* Przechowywanie dużych zestawów danych i tworzenie dotyczących ich zapytań
* Wizualizowanie danych w czasie rzeczywistym i danych historycznych
* Integrowanie z systemami zaplecza biura

Aby dostarczyć te możliwości, pakiet akceleratorów rozwiązania Azure IoT jednocześnie wiele usług platformy Azure z rozszerzeniami niestandardowymi jako wstępnie skonfigurowanych rozwiązań. Te wstępnie skonfigurowane rozwiązania stanowią podstawowe implementacje typowych wzorców rozwiązań IoT i pomagają skrócić czas dostarczania własnych rozwiązań IoT. Przy użyciu IoT software development kit, można dostosować i rozszerzyć tych rozwiązań, aby spełniały określone wymagania. Można ich także użyć jako przykładów lub szablonów podczas tworzenia nowych rozwiązań IoT.

Akceleratory rozwiązania Azure IoT to wydajne rozwiązania do potrzeb IoT. Warto jednak znaczenie upmost czy Twojego rozwiązania IoT zostały zaprojektowane z myślą od początku o bezpieczeństwie. Z powodu wielość urządzenia IoT wszystkie zdarzenia zabezpieczeń może szybko stać się powszechnie zdarzeń z znaczący wpływ.

Aby ułatwić zrozumienie, jak zabezpieczyć Twojego rozwiązania IoT, zostały następujące informacje.

## <a name="security-architecture"></a>Architektura zabezpieczeń
Podczas projektowania systemu, ważne jest zrozumienie potencjalnych zagrożeń dla tego systemu, a Dodaj odpowiednie zabezpieczenia w związku z tym system został zaprojektowany i zaprojektowana. Należy zaprojektować produktu od początku z myślą o bezpieczeństwie, ponieważ Opis ułatwia sposób atakujący może być w stanie naruszyć bezpieczeństwo systemu, upewnij się, że odpowiednie środki zaradcze są stosowane od początku.

Informacje na temat architektury IoT zabezpieczeń, odczytując [Internet rzeczy Architektura zabezpieczeń](../iot-accelerators/iot-security-architecture.md).

W tym artykule omówiono następujące zagadnienia:

* [Rozpoczyna się modelu zagrożeń zabezpieczeń](../iot-accelerators/iot-security-architecture.md#security-starts-with-a-threat-model)
* [Zabezpieczenia w IoT](../iot-accelerators/iot-security-architecture.md#security-in-iot)
* [Modelowanie architektury Azure IoT odwołanie zagrożeń](../iot-accelerators/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Zabezpieczenia od podstaw
IoT stanowi wyjątkowe wyzwanie zabezpieczeń, prywatności i zgodności dla firm na całym świecie. W przeciwieństwie do tradycyjnych przez technologii gdzie te problemy koncentrują się wokół oprogramowania i jak jest implementowane IoT dotyczy, co się stanie po zbieżne ataków i względem fizycznych. Ochrona rozwiązania IoT wymaga zapewnienia bezpiecznego inicjowania obsługi urządzeń i bezpieczna łączność między tymi urządzeniami i chmurą i ochronę danych w chmurze podczas przetwarzania i przechowywania. Praca z takich funkcji, są jednak ograniczone zasobów urządzeń, rozmieszczenie geograficzne wdrożeń i wiele urządzeń w ramach rozwiązania.

Znajdują się informacje dotyczące obsługi zabezpieczeń w następujących obszarach odczytując [zabezpieczeń Internetu rzeczy od podstaw](../iot-accelerators/securing-iot-ground-up.md).

W tym artykule omówiono następujące tematy:

* [Bezpiecznej infrastruktury od podstaw](../iot-accelerators/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure — bezpiecznej infrastruktury IoT dla biznesu](../iot-accelerators/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Najlepsze rozwiązania
Zabezpieczanie infrastruktury IoT wymaga rygorystyczne strategii zabezpieczeń zabezpieczeń. Z Zabezpieczanie danych w chmurze, ochrony integralności danych przesyłanych za pośrednictwem publicznego Internetu do bezpiecznego inicjowania obsługi urządzeń, każda warstwa kompilacje gwarancję zabezpieczeń w całej infrastruktury.

Informacje na temat zabezpieczeń Internetu rzeczy najlepsze rozwiązania w zakresie odczytując [najlepszych rozwiązań dotyczących zabezpieczeń Internetu rzeczy](../iot-accelerators/iot-security-best-practices.md).

W tym artykule omówiono następujące tematy:

* [Producenta sprzętu IoT/integrator](../iot-accelerators/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
* [Deweloper rozwiązania IoT](../iot-accelerators/iot-security-best-practices.md#iot-solution-developer)
* [Narzędzie wdrażania rozwiązania IoT](../iot-accelerators/iot-security-best-practices.md#iot-solution-deployer)
* [Operator rozwiązania IoT](../iot-accelerators/iot-security-best-practices.md#iot-solution-operator)
