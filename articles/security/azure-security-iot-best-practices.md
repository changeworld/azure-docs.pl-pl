---
title: Internet rzeczy najlepsze rozwiązania w zakresie zabezpieczeń | Dokumentacja firmy Microsoft
description: Artykuł zawiera listę nadzorowaną dotyczącą programu Microsoft Internet rzeczy najlepsze rozwiązania i ogólne zalecenia.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 804dd6828931ca2801360ab18be938c8bacf2bfa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185344"
---
# <a name="internet-of-things-security-best-practices"></a>Internet rzeczy najlepsze rozwiązania w zakresie zabezpieczeń

Zabezpieczanie infrastruktury Internetu rzeczy (IoT) jest krytyczne przedsiębiorstwa dla każdego związanego z rozwiązania IoT. Ze względu na liczbę urządzeń i Rozproszony charakter tych urządzeń wpływ zdarzeń zabezpieczeń związane z naruszenia miliony urządzenia IoT jest nieuproszczony i może mieć wpływ powszechnie.

Z tego powodu podejście zabezpieczeń zabezpieczeń w zakresie zabezpieczeń IoT. Dane muszą być bezpieczne w chmurze i przesyłane za pośrednictwem sieci prywatnej i publicznej. Metody muszą być spełnione, aby bezpiecznie udostępnić z samymi urządzeniami IoT. Każda warstwa z urządzenia do sieci wewnętrznej w chmurze musi silne zabezpieczenie gwarancji.

Najlepsze rozwiązania IoT mogą zostać podzielone w następujący sposób:

* IoT producenta sprzętu lub integrator
* Deweloper rozwiązania IoT
* Narzędzie wdrażania rozwiązania IoT
* Operator rozwiązania IoT

Ten artykuł zawiera podsumowanie [Internet z innymi najlepszych rozwiązań dotyczących zabezpieczeń](../iot-suite/iot-security-best-practices.md). Można znaleźć w tym artykule, aby uzyskać szczegółowe informacje.

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT producenta sprzętu lub integrator

Wykonaj poniższe najlepsze rozwiązania IoT produkcji sprzętu lub integrator sprzętu:

* **Zakres sprzętu do minimalnych wymagań**: projekt sprzętu powinna zawierać minimalne funkcje wymagane dla operacji sprzętu i nic więcej. 
* **Należy sprzętu manipulację dowód**: kompilacji w mechanizmów przed naruszeniem fizycznego sprzętu, takich jak otwieranie pokrywy urządzenia, usunięcie części urządzenia itd. 
* **Tworzenie wokół bezpiecznych składników sprzętowych**: Jeśli [kst](https://en.wikipedia.org/wiki/Cost_of_goods_sold) zezwolić, tworzenia funkcji zabezpieczeń, takich jak bezpieczna i szyfrowana magazynowania i funkcji rozruchowego opartego na modułu TPM.
* **Zabezpieczyć uaktualnień**: uaktualnienie oprogramowania układowego okres istnienia urządzenia są nieuniknione.

## <a name="iot-solution-developer"></a>Deweloper rozwiązania IoT

Jeśli jesteś deweloperem rozwiązania IoT, wykonaj poniższe najlepsze rozwiązania:

* **Postępuj zgodnie z metodologii rozwoju oprogramowania bezpiecznego**: tworzenie bezpiecznej oprogramowania wymaga podstaw planowania zabezpieczeń od momentu rozpoczęcia projektu aż do jej wdrożenia, testowania i wdrażania.
* **Wybierz oprogramowanie typu open source z rozwagą**: oprogramowanie typu open source zapewnia możliwość szybkiego opracowywania rozwiązań.
* **Integracja z rozwagą**: istnieje wiele luk w zabezpieczeniach oprogramowania na granicy bibliotek i interfejsów API. 

## <a name="iot-solution-deployer"></a>Narzędzie wdrażania rozwiązania IoT

Wykonaj poniższe najlepsze rozwiązania w przypadku wdrażania rozwiązania IoT:

* **Wdrażanie sprzętu bezpiecznie**: IoT wdrożenia może wymagać sprzętu, który ma zostać wdrożony w lokalizacjach niezabezpieczone, taki jak publiczny spacji lub ustawień nienadzorowanych.
* **Bezpieczeństwa kluczy uwierzytelniania**: podczas wdrażania, każde urządzenie wymaga identyfikatory urządzeń i skojarzonych kluczy uwierzytelniania generowane przez usługę w chmurze. Zachowaj te klucze fizycznie bezpieczne nawet po wdrożeniu. Dowolny klawisz, którego bezpieczeństwo zostało naruszone można za pomocą złośliwych urządzenia udają istniejące urządzenie.

## <a name="iot-solution-operator"></a>Operator rozwiązania IoT

Wykonaj poniższe najlepsze rozwiązania w przypadku operator rozwiązania IoT:

* **Aktualności systemów**: Upewnij się, systemów operacyjnych urządzeń i wszystkie sterowniki zostały zaktualizowane do najnowszej wersji. 
* **Ochrona przed złośliwych działań**: pozwala na system operacyjny, umieść najnowszych funkcji oprogramowania antywirusowego i przed złośliwym oprogramowaniem w każdym systemie operacyjnym urządzenia. 
* **Inspekcji, często**: inspekcja IoT infrastruktury zabezpieczeń związanych z problemów jest kluczem podczas reagowania na przypadki naruszenia zabezpieczeń.
* **Fizycznie ochrona infrastruktury IoT**: najgorszy ataki zabezpieczeń infrastruktury IoT jest uruchomiony przy użyciu fizyczny dostęp do urządzeń.
* **Ochrona poświadczeń w chmurze**: chmury uwierzytelniania poświadczenia używane do konfigurowania i obsługi wdrażania IoT są prawdopodobnie najłatwiejszym sposobem na uzyskanie dostępu i złamanie IoT. 

