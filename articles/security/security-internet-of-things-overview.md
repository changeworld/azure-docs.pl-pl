---
title: Zabezpieczanie Internet of Things (IoT) na platformie Azure | Dokumentacja firmy Microsoft
description: " Internetu rzeczy (IoT), usług platformy Azure oferują szeroką gamę możliwości. Ten artykuł pomoże Ci zrozumieć, jak zabezpieczyć swoje rozwiązania IoT na platformie Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: db1c2b9c852479b9af3674f6c5e1f1135ee289f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62107444"
---
# <a name="internet-of-things-security-overview"></a>Omówienie zabezpieczeń Internet of Things
Internetu rzeczy (IoT), usług platformy Azure oferują szeroką gamę możliwości. Są to usługi klasy korporacyjnej, które pozwalają wykonywać następujące operacje:

* Zbieranie danych z urządzeń
* Analizowanie strumieni danych w ruchu
* Przechowywanie dużych zestawów danych i tworzenie dotyczących ich zapytań
* Wizualizowanie danych w czasie rzeczywistym i danych historycznych
* Integrowanie z systemami zaplecza biura

Do udostępniania tych możliwości, pakietu akceleratorów rozwiązań Azure IoT ze sobą wiele usług platformy Azure z rozszerzeniami niestandardowymi jako wstępnie skonfigurowanych rozwiązań. Te wstępnie skonfigurowane rozwiązania stanowią podstawowe implementacje typowych wzorców rozwiązań IoT i pomagają skrócić czas dostarczania własnych rozwiązań IoT. Za pomocą zestawów SDK IoT, można dostosować i rozszerzyć pod kątem własnych wymagań tych rozwiązań. Można ich także użyć jako przykładów lub szablonów podczas tworzenia nowych rozwiązań IoT.

Akceleratory rozwiązań IoT platformy Azure są zaawansowane rozwiązania dotyczące aplikacji potrzebom IoT. Będzie jednak znaczenie upmost że rozwiązań IoT zostały zaprojektowane z myślą o bezpieczeństwie od samego początku. Ze względu na znaczne zmniejszenie liczby urządzeń IoT wszystkimi zdarzeniami zabezpieczeń może szybko stać się powszechne zdarzeń za pomocą poważne konsekwencje.

Aby pomóc Ci zrozumieć, jak zabezpieczyć swoje rozwiązania IoT, mamy następujące informacje.

## <a name="security-architecture"></a>Architektura zabezpieczeń
Podczas projektowania systemu, ważne jest zrozumienie potencjalnych zagrożeń dla tego systemu i dodanie odpowiednich mechanizmów obronnych w związku z tym, ponieważ system został zaprojektowany i zaprojektowana. Należy zaprojektować produktu od samego początku z myślą o bezpieczeństwie, ponieważ zrozumienie, jak atakujący może być w stanie naruszyć bezpieczeństwo systemu, pomaga upewnić się, że odpowiednie środki zaradcze są stosowane od samego początku.

Informacje na temat architektury zabezpieczeń IoT, czytając [Internet rzeczy architekturę zabezpieczeń](/azure/iot-fundamentals/iot-security-architecture).

W tym artykule omówiono następujące tematy:

* [Rozpoczyna się Model zagrożeń bezpieczeństwa](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Zabezpieczenia w IoT](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Architektura referencyjna IoT platformy Azure do modelowania zagrożeń](/azure/iot-fundamentals/iot-security-architecture)

## <a name="security-from-the-ground-up"></a>Zabezpieczenia od podstaw
IoT stwarza wyzwania zabezpieczeń, ochrony prywatności i zgodności dla firm na całym świecie. W przeciwieństwie do tradycyjnych cybernetycznymi technologii gdzie tych problemów koncentrują się wokół oprogramowania oraz sposób implementacji IoT dotyczy, co się stanie po zbiegają się tym i cech fizycznych. Ochrona rozwiązań IoT wymaga zapewnienia bezpieczne Inicjowanie obsługi urządzeń, bezpieczną łączność między tymi urządzeniami i chmurą i ochronę danych w chmurze podczas przetwarzania i przechowywania. Działa z takich funkcji, są jednak ograniczonych zasobach urządzeń, rozkład geograficzny wdrożeń i wiele urządzeń w ramach rozwiązania.

Możesz dowiedzieć się, jak obsługiwać zabezpieczeń w tych obszarach, czytając [zabezpieczeń Internetu rzeczy od podstaw](/azure/iot-fundamentals/iot-security-ground-up).

W tym artykule omówiono następujące tematy:

* [Bezpieczną infrastrukturę od podstaw](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure — bezpiecznej infrastruktury IoT dla swojej firmy](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Najlepsze rozwiązania
Zabezpieczanie infrastruktury IoT wymaga rygorystyczne strategii zabezpieczeń w głębi. Z zabezpieczania danych w chmurze, ochrona integralności danych przesyłanych za pośrednictwem publicznej sieci internet, aby bezpiecznie inicjowania obsługi urządzeń, każda warstwa kompilacje większa kontrola zabezpieczeń w całej infrastruktury.

Informacje na temat zabezpieczeń Internet of Things najlepszych rozwiązań, czytając [najlepszych rozwiązań dotyczących zabezpieczeń Internet of Things](/azure/iot-fundamentals/iot-security-best-practices).

W tym artykule omówiono następujące tematy:

* [IoT, producent sprzętu/integrator](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [Dla deweloperów rozwiązań IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [Narzędzie wdrażania rozwiązania IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [Operator rozwiązania IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
