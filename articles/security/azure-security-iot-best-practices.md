---
title: Internet of Things Security Best Practices | Dokumentacja firmy Microsoft
description: Artykuł zawiera listę nadzorowaną dotyczącą programu Microsoft Internet of Things Security Best Practices and ogólne zalecenia.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 9413c0503c1b78550776d1c2f6ab8239205a788b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121628"
---
# <a name="internet-of-things-security-best-practices"></a>Internet rzeczy najlepsze rozwiązania w zakresie zabezpieczeń

Zabezpieczanie infrastruktury Internetu rzeczy (IoT) jest krytyczna przedsiębiorstwa dla każdego, kto jest związane z rozwiązań IoT. Ze względu na liczbę urządzeń i Rozproszony charakter tych urządzeń wpływ zdarzenie związane z zabezpieczeniami dotyczą naruszenia milionami urządzeń IoT jest trywialny i może mieć wpływ powszechne.

Z tego powodu podejście zabezpieczeń zabezpieczeń w zakresie zabezpieczeń IoT. Dane muszą być bezpieczne w chmurze i kiedy przesuwa się on w sieciach prywatnych i publicznych. Metody muszą być spełnione, aby bezpiecznie aprowizacja urządzeń IoT, samodzielnie. Każda warstwa z urządzenia do sieci z chmurą zaplecza wymaga zapewnienia silne zabezpieczenia.

Najlepsze rozwiązania IoT mogą zostać podzielone w następujący sposób:

* Producent sprzętu IoT lub integrator
* Dla deweloperów rozwiązań IoT
* Narzędzie wdrażania rozwiązania IoT
* Operator rozwiązania IoT

Ten artykuł zawiera podsumowanie [Internet z rzeczy najlepszych rozwiązań dotyczących zabezpieczeń](../iot-suite/iot-security-best-practices.md). Zapoznaj się z tego artykułu, aby uzyskać szczegółowe informacje.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Producent sprzętu IoT lub integrator

Wykonaj poniższe najlepsze rozwiązania, jeśli są produkcji sprzętu IoT lub integrator sprzętu:

* **Zakres sprzętu minimalne wymagania**: projekt sprzętu powinna zawierać minimalne funkcje wymagane dla operacji sprzęt i nic więcej. 
* **Uczynienia sprzętu naruszanie dowód**: dołączanie mechanizmów naruszeniem fizycznego sprzętu, takich jak otwieranie pokrycia urządzenia, usuwając część urządzeń itp. 
* **Tworzenie wokół bezpieczny sprzęt**: Jeśli [KWS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) zezwolić, tworzyć funkcje zabezpieczeń, takie jak magazynowanie bezpieczna i szyfrowana i opartych na Trusted Platform Module TPM funkcje.
* **Zwiększyć bezpieczeństwo uaktualnień**: uaktualnienie oprogramowania układowego w okresie istnienia urządzenia jest nieuniknione.

## <a name="iot-solution-developer"></a>Dla deweloperów rozwiązań IoT

Jeśli jesteś deweloperem rozwiązań IoT, postępuj zgodnie z najlepszymi poniżej:

* **Postępuj zgodnie z metodologii rozwoju oprogramowania bezpiecznego**: tworzenie bezpiecznego oprogramowania wymaga podstaw myśleć o zabezpieczeniach od powstania projektu aż do jej implementacji, testowania i wdrażania.
* **Wybierz oprogramowanie typu open source z rozwagą**: oprogramowanie typu open source umożliwia szybkie opracowywanie rozwiązań.
* **Integracja z rozwagą**: istnieje wiele luki w zabezpieczeniach oprogramowania na granicy bibliotek i interfejsów API. 

## <a name="iot-solution-deployer"></a>Narzędzie wdrażania rozwiązania IoT

Wykonaj poniższe najlepsze rozwiązania, jeśli są wdrażania rozwiązania IoT:

* **Bezpieczne wdrażanie sprzętu**: Wdrożenia usługi IoT, mogą wymagać sprzętu, który ma zostać wdrożony w niezabezpieczonych lokalizacjach, takich jak publicznego miejsca do magazynowania lub ustawień nienadzorowanych.
* **Zabezpieczanie klucze uwierzytelniania**: podczas wdrażania, każde urządzenie wymaga identyfikatorów urządzeń i skojarzone klucze uwierzytelniania, generowane przez usługę w chmurze. Zabezpieczenie tych kluczy fizycznie nawet po wdrożeniu. Dowolny klawisz, którego bezpieczeństwo zostało naruszone, może służyć przez złośliwych urządzenie poczynania jako istniejące urządzenie.

## <a name="iot-solution-operator"></a>Operator rozwiązania IoT

Wykonaj poniższe najlepsze rozwiązania, jeśli uprawnienia operatora rozwiązania IoT:

* **Aktualizuj systemy**: Upewnij się, systemów operacyjnych urządzeń i wszystkie sterowniki urządzeń, które zostały zaktualizowane do najnowszej wersji. 
* **Ochrona przed złośliwymi działaniami**: pozwala na system operacyjny, umieść najnowszych funkcji ochrony antywirusowej i chroniące przed złośliwym kodem na każdy system operacyjny urządzenia. 
* **Inspekcja często**: inspekcja początek infrastruktury IoT zabezpieczeń związanych z problemów jest klucz, w przypadku reagowania na zdarzenia związane z bezpieczeństwem.
* **Fizycznie ochrona infrastruktury IoT**: najgorsze ataków bezpieczeństwa infrastruktury IoT uruchamianych przy użyciu fizyczny dostęp do urządzeń.
* **Ochrona poświadczeń w chmurze**: chmura poświadczenia uwierzytelniania używany do konfigurowania i obsługi wdrożenia usługi IoT są prawdopodobnie Najprostszym sposobem na uzyskanie dostępu i naruszenia bezpieczeństwa systemu IoT. 

