---
title: Zabezpieczenia fizycznego przez moduł HSM — sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej | Dokumentacja firmy Microsoft
description: Informacje o bezpieczeństwo fizyczne urządzenia sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej w centrach danych
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 6fe0557aa90ec3345d4ca0c3d9292cab3435bb2f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118064"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Fizyczne zabezpieczeń platformy Azure w wersji dedykowanej przez moduł HSM

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń pomoże Ci spełnić wymagania zaawansowanych zabezpieczeń dla magazynu kluczy. Jest zarządzane następujące rygorystyczne rozwiązania w zakresie zabezpieczeń przez cały cykl życia pełną, aby zaspokoić potrzeby klientów.

## <a name="security-through-procurement"></a>Zabezpieczenia dzięki zaopatrzenia

Microsoft poniżej proces bezpiecznych zakupów. Możemy zarządzać łańcuch nadzoru i upewnij się, że określone urządzenie uporządkowana i będzie dostarczany urządzenia odbieranych z naszych centrów danych. Urządzenia są w zdarzeń odporne tworzywa tworzy kopię. Są one przechowywane w obszarze bezpiecznego magazynu, do momentu upoważnione w galerii danych w centrum danych.  Stojaki, zawierające urządzenia sprzętowego modułu zabezpieczeń są traktowane jako impact(HBI) biznesowe wysoki. Urządzenia są zablokowane i w obszarze monitorowania wideo przez cały czas przodu i do tyłu.

## <a name="security-through-deployment"></a>Zabezpieczenia dzięki wdrożeniu

Moduły HSM są instalowane w stojakach wraz z skojarzone składniki sieci. Po zakończeniu instalacji należy skonfigurować je przed stają się one dostępne w ramach usługi platformy Azure w wersji dedykowanej sprzętowego modułu zabezpieczeń. To działanie konfiguracji jest wykonywane przez pracowników firmy Microsoft, które zostały poddane wyboru tła. "Dokładnie na czas" (JIT) Administracja służy do ograniczania dostępu do tylko pracowników i tylko czas, który wymagany jest dostęp. Procedury składowane i systemy używane upewnij się również, że wszystkie działania związane z urządzeniami przez moduł HSM jest zarejestrowany.

## <a name="security-in-operations"></a>Zabezpieczenia w operacjach

Moduły HSM są sprzęt urządzeń (rzeczywistych HSM jest karta PCI w ramach urządzenia), dzięki czemu jest możliwe, że mogą wystąpić problemy z poziomu składnika. Potencjalne problemy obejmują, ale nie są ograniczone do awarii zasilania wentylator i zasilania. Tego typu zdarzenia wymagają konserwacji lub działania, aby zamienić wszystkie składniki swappable w zakresie usuwania awarii.

### <a name="component-replacement"></a>Wymiana składników

Po aprowizacji urządzenia i w obszarze zarządzania klientami, wyłączania zasilania jest tylko składniki, które mogłyby zostać zastąpione. Ten składnik jest poza granicą zabezpieczeń i nie powoduje to zdarzenie odporne. Systemu obsługi biletów służy do autoryzowania inżynier z firmy Microsoft z tyłu stojak o dużym znaczeniu Biznesowym dostęp do. Po przetworzeniu--ticket, wystawiany jest kluczem tymczasowym fizycznych. Ten klucz zapewnia inżynier ds. dostęp do urządzenia i umożliwia ich wymiany składnik. Inne dostępu (naruszanie oznacza to, co powoduje zdarzenie) będą wykonywane, gdy urządzenie nie jest przydzielony do klienta, w związku z tym minimalizując ryzyko bezpieczeństwa i dostępności.  

### <a name="device-replacement"></a>Zastąpienie urządzenia

W przypadku awarii łączna liczba urządzeń to proces podobny do tego, który został użyty podczas awarii składnika jest zakończony. Klient nie będzie mógł zeroize urządzenia, czy urządzenie jest w nieznanym stanie, danych, mając urządzenia zostaną usunięte i umieszczone w pojemniku w stojaku zniszczenia. Urządzenia umieszczone w pojemniku zostaną zniszczone w kontrolowany i bezpieczny sposób. Żadne dane nie mając urządzeń z o dużym znaczeniu Biznesowym stojak spowoduje, że w centrum danych firmy Microsoft.

### <a name="other-rack-access-activities"></a>Inne działania, stojak dostępu

Jeśli inżynier z firmy Microsoft musi uzyskać dostęp do stojak, używany przez urządzenia sprzętowego modułu zabezpieczeń (na przykład sieci obsługa urządzenia), procedury standardowych zabezpieczeń będzie służyć do uzyskania dostępu do o dużym znaczeniu Biznesowym bezpieczne stojaka. Wszelki dostęp będzie pod nadzorem wideo. Urządzenia HSM są zweryfikowane do [FIPS 140-2 poziom 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) więc wszelkie nieautoryzowanego dostępu do urządzenia sprzętowego modułu zabezpieczeń są sygnalizowane dla klienta i dane będą zeroized.

## <a name="logical-level-security-considerations"></a>Zagadnienia dotyczące zabezpieczeń na poziomie logiczne

Moduły HSM są aprowizowane z siecią wirtualną, które zostały utworzone przez klienta. Jest to prywatnej przestrzeni adresowej IUP klienta.  Ta konfiguracja zapewnia izolację poziomu cenne sieć logiczną i zapewnia dostęp tylko przez klienta. Oznacza to, czy wszystkie kontrolki zabezpieczeń na poziomie logiczne są odpowiedzialne za klienta.

## <a name="next-steps"></a>Kolejne kroki

Zaleca się, że wszystkie najważniejsze pojęcia związane z usług, takich jak wysokiej dostępności i zabezpieczeń i obsługi na przykład są dobrze zrozumiałe przed Inicjowanie obsługi administracyjnej urządzeń, projekt aplikacji lub wdrożenia.

* [Wysoka dostępność](high-availability.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowanie](monitoring.md)
* [Architektura wdrożenia](deployment-architecture.md)