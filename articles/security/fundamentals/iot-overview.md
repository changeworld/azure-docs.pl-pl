---
title: Zabezpiecz swoje Internet rzeczy (IoT) na platformie Azure | Microsoft Docs
description: " Usługi Internet rzeczy (IoT) platformy Azure oferują szeroką gamę możliwości. Ten artykuł pomaga zrozumieć sposób zabezpieczania rozwiązań IoT na platformie Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 670fcc5e92cdb9937026d91ee85b7e8e856a3025
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727032"
---
# <a name="internet-of-things-security-overview"></a>Omówienie zabezpieczeń Internet rzeczy
Usługi Internet rzeczy (IoT) platformy Azure oferują szeroką gamę możliwości. Są to usługi klasy korporacyjnej, które pozwalają wykonywać następujące operacje:

* Zbieranie danych z urządzeń
* Analizowanie strumieni danych w ruchu
* Przechowywanie dużych zestawów danych i tworzenie dotyczących ich zapytań
* Wizualizowanie danych w czasie rzeczywistym i danych historycznych
* Integrowanie z systemami zaplecza biura

Aby zapewnić te możliwości, pakiet akceleratorów rozwiązań usługi Azure IoT udostępnia wiele usług platformy Azure z rozszerzeniami niestandardowymi jako wstępnie skonfigurowanymi rozwiązaniami. Te wstępnie skonfigurowane rozwiązania stanowią podstawowe implementacje typowych wzorców rozwiązań IoT i pomagają skrócić czas dostarczania własnych rozwiązań IoT. Korzystając z zestawów IoT Software Development Kit, można dostosować i rozłożyć te rozwiązania w celu spełnienia własnych wymagań. Można ich także użyć jako przykładów lub szablonów podczas tworzenia nowych rozwiązań IoT.

Akceleratory rozwiązań usługi Azure IoT to zaawansowane rozwiązania dla potrzeb IoT. Jednak jest to najbardziej ważne, aby Twoje rozwiązania IoT zostały zaprojektowane z myślą o bezpieczeństwie od samego początku. Ze względu na liczbę zawiera urządzeń IoT każde zdarzenie związane z bezpieczeństwem może szybko stać się rozległym wydarzeniem z istotnymi skutkami.

Aby ułatwić zrozumienie sposobu zabezpieczania rozwiązań IoT, mamy następujące informacje.

## <a name="security-architecture"></a>Architektura zabezpieczeń
Podczas projektowania systemu ważne jest, aby zrozumieć potencjalne zagrożenia dla tego systemu i odpowiednio dodać odpowiednie zabezpieczenia, gdy system został zaprojektowany i opracowany. Ważne jest, aby zaprojektować produkt od samego początku z myślą o bezpieczeństwie, ponieważ zrozumienie, jak osoba atakująca może naruszyć bezpieczeństwo systemu, pozwala upewnić się, że na początku są stosowane odpowiednie środki zaradcze.

Informacje o architekturze zabezpieczeń IoT można znaleźć, odczytując [Internet rzeczy architekturę zabezpieczeń](/azure/iot-fundamentals/iot-security-architecture).

W tym artykule omówiono następujące tematy:

* [Zabezpieczenia zaczynają się od modelu zagrożeń](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Zabezpieczenia w IoT](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Modelowanie zagrożeń architektura referencyjna usługi Azure IoT](/azure/iot-fundamentals/iot-security-architecture)

## <a name="security-from-the-ground-up"></a>Zabezpieczenia od podstaw
IoT stanowi unikatowe wyzwania w zakresie zabezpieczeń, prywatności i zgodności dla firm na całym świecie. W przeciwieństwie do tradycyjnej technologii cybernetycznymi, w której te problemy są nanoszone wokół oprogramowania i sposobu ich implementacji, IoT ma wpływ na to, co się dzieje w przypadku zbieżności cybernetycznymi i fizycznych rozwiązań. Ochrona rozwiązań IoT wymaga zapewnienia bezpiecznego aprowizacji urządzeń, bezpiecznej łączności między tymi urządzeniami i chmurą oraz zapewnienia bezpiecznej ochrony danych w chmurze podczas przetwarzania i przechowywania. Korzystanie z tych funkcji jest jednak urządzeniami z ograniczeniami zasobów, dystrybucją geograficzną wdrożeń i wieloma urządzeniami w ramach rozwiązania.

Aby dowiedzieć się, jak obsługiwać zabezpieczenia w tych obszarach, należy przeczytać [Internet rzeczy zabezpieczenia od podstaw](/azure/iot-fundamentals/iot-security-ground-up).

W tym artykule omówiono następujące tematy:

* [Bezpieczna infrastruktura od podstaw](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure — Bezpieczna infrastruktura IoT dla Twojej firmy](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Najlepsze rozwiązania
Zabezpieczenie infrastruktury IoT wymaga rygorystycznej strategii dotyczącej zabezpieczeń. Z zabezpieczania danych w chmurze, chroniąc integralność danych podczas przesyłania ich przez publiczny Internet, w celu bezpiecznego aprowizacji urządzeń, każda warstwa kompiluje większą gwarancję bezpieczeństwa w ogólnej infrastrukturze.

Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących zabezpieczeń Internet rzeczy, Przeczytaj [Internet rzeczy najlepszych](/azure/iot-fundamentals/iot-security-best-practices)rozwiązań w zakresie zabezpieczeń.

W tym artykule omówiono następujące tematy:

* [Producent/Integrator sprzętu IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [Deweloper rozwiązań IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [Wdrażanie rozwiązań IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [Operator rozwiązania IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
