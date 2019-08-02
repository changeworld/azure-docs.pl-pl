---
title: Najlepsze rozwiązania w zakresie zabezpieczeń Internet rzeczy | Microsoft Docs
description: Artykuł zawiera zanadzorowana listę najlepszych rozwiązań w zakresie zabezpieczeń firmy Microsoft Internet rzeczy i ogólnych zaleceń.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 14f1905b1acd60acdf7b3dad0686e3fa34629953
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727054"
---
# <a name="internet-of-things-security-best-practices"></a>Najlepsze rozwiązania w zakresie zabezpieczeń Internet rzeczy

Zabezpieczanie infrastruktury Internet rzeczy (IoT) stanowi krytyczne zobowiązanie dla wszystkich osób korzystających z rozwiązań IoT. Ze względu na liczbę urządzeń i rozproszonego charakteru tych urządzeń, wpływ zdarzenia zabezpieczeń związanego z naruszeniem milionów urządzeń IoT nie jest uproszczony i może mieć szeroki wpływ.

Z tego powodu zabezpieczenia IoT wymagają podejścia w zakresie zabezpieczeń. Dane muszą być bezpieczne w chmurze i przenoszone przez sieci prywatne i publiczne. Metody muszą być stosowane w celu bezpiecznego aprowizacji urządzeń IoT. Każda warstwa, z urządzenia, do sieci, do zaplecza w chmurze musi mieć silne gwarancje bezpieczeństwa.

Najlepsze rozwiązania IoT można podzielić na kategorie w następujący sposób:

* Producent sprzętu IoT lub Integrator
* Deweloper rozwiązań IoT
* Wdrażanie rozwiązań IoT
* Operator rozwiązania IoT

W tym artykule przedstawiono podsumowanie najlepszych rozwiązań dotyczących [zabezpieczeń Internet rzeczy](/azure/iot-fundamentals/iot-security-best-practices). Zapoznaj się z tym artykułem, aby uzyskać bardziej szczegółowe informacje.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Producent sprzętu IoT lub Integrator

Postępuj zgodnie z najlepszymi rozwiązaniami, jeśli jesteś wytwarzanym sprzętem IoT lub integratorem sprzętowym:

* **Zakres sprzętu do minimalnych wymagań**: projekt sprzętu powinien zawierać minimalne funkcje wymagane do działania sprzętu i nic więcej. 
* **Wykonaj próbę naruszenia sprzętowego**: Kompiluj w mechanizmach wykrywania fizycznego manipulowania sprzętem, takich jak otwieranie okładki urządzenia, usuwanie części urządzenia itp. 
* **Kompiluj na bezpiecznym sprzęcie**: w przypadku zezwolenia na [KWS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) , twórz funkcje zabezpieczeń, takie jak bezpieczne i zaszyfrowane magazyny oraz funkcje rozruchowe oparte na moduł TPM (TPM).
* **Zabezpieczanie uaktualnień**: uaktualnianie oprogramowania układowego w trakcie okresu istnienia urządzenia jest nieuniknione.

## <a name="iot-solution-developer"></a>Deweloper rozwiązań IoT

Postępuj zgodnie z najlepszymi rozwiązaniami, jeśli jesteś deweloperem rozwiązań IoT:

* **Postępuj zgodnie z metodologią opracowywania oprogramowania zabezpieczającego**: Programowanie bezpiecznego oprogramowania wymaga założenia bezpieczeństwa od rozpoczęcia projektu do jego implementacji, testowania i wdrażania.
* **Wybierz oprogramowanie Open Source z opieką**: oprogramowanie Open Source zapewnia możliwość szybkiego tworzenia rozwiązań.
* **Integracja z opieką**: wiele wad zabezpieczeń oprogramowania istnieje na granicy bibliotek i interfejsów API. 

## <a name="iot-solution-deployer"></a>Wdrażanie rozwiązań IoT

Postępuj zgodnie z najlepszymi rozwiązaniami, jeśli jesteś rozwiązaniem do wdrażania rozwiązań IoT:

* **Bezpieczne wdrażanie sprzętu**: Wdrożenia IoT mogą wymagać wdrożenia sprzętu w niezabezpieczonych lokalizacjach, takich jak publiczne miejsca lub nienadzorowane ustawienia regionalne.
* **Zabezpieczanie kluczy uwierzytelniania**: podczas wdrażania każde urządzenie wymaga identyfikatorów urządzeń i skojarzonych kluczy uwierzytelniania generowanych przez usługę w chmurze. Przechowuj te klucze fizycznie bezpiecznie, nawet po wdrożeniu. Każdy złamany klucz może być używany przez złośliwe urządzenie do zamaskowanego jako istniejące urządzenie.

## <a name="iot-solution-operator"></a>Operator rozwiązania IoT

Wykonaj poniższe najlepsze rozwiązania, jeśli jesteś operatorem rozwiązania IoT:

* **Dbaj o**aktualność systemów: Upewnij się, że systemy operacyjne urządzeń i wszystkie sterowniki urządzeń zostały zaktualizowane do najnowszych wersji. 
* **Ochrona przed złośliwymi działaniami**: w przypadku zezwolenia na system operacyjny należy umieścić najnowsze funkcje oprogramowania antywirusowego i chroniącego przed złośliwym oprogramowaniem w poszczególnych systemach operacyjnych urządzeń. 
* **Inspekcja często**: Inspekcja infrastruktury IoT pod kątem problemów związanych z zabezpieczeniami polega na kluczu odpowiedzi na zdarzenia związane z bezpieczeństwem.
* **Fizyczna ochrona infrastruktury IoT**: najgorsze ataki zabezpieczeń dotyczące infrastruktury IoT są uruchamiane przy użyciu fizycznego dostępu do urządzeń.
* **Ochrona poświadczeń w chmurze**: poświadczenia uwierzytelniania w chmurze używane do konfigurowania i obsługi wdrożenia IoT są prawdopodobnie Najprostszym sposobem uzyskania dostępu i naruszenia bezpieczeństwa systemu IoT. 

