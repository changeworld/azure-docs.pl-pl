---
title: Architektura wdrożenia - sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej | Dokumentacja firmy Microsoft
description: Podstawowe zagadnienia, korzystając z platformy Azure w wersji dedykowanej w module HSM w ramach architektury aplikacji
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: barclayn
ms.openlocfilehash: f078df7677e771d131f15056ac4a54a58a3134bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60912293"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Architektura wdrożenia na platformie Azure w wersji dedykowanej przez moduł HSM

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń udostępnia kryptograficznych magazynu kluczy na platformie Azure. Spełnia wymagania dotyczące bezpieczeństwa. Klienci będą mogli korzystać z przy użyciu sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej, jeśli one:

* Musi spełniać FIPS 140-2 Level 3 certyfikacji
* Wymagają wyłącznego dostępu do modułu HSM
* powinien mieć pełną kontrolę nad swoimi urządzeniami

Moduły HSM są dystrybuowane w obrębie centrów danych firmy Microsoft i mogą łatwo udostępnić jako pary urządzeń jako podstawę rozwiązania o wysokiej dostępności. Mogą one również wdrożony kilku regionach, odporne na błędy rozwiązanie po awarii. Regiony z dedykowanego sprzętowego modułu zabezpieczeń dostępne obecnie są:

* Wschodnie stany USA
* Wschodnie stany USA 2
* Zachodnie stany USA
* Środkowo-południowe stany USA
* Azja Południowo-Wschodnia
* Azja Wschodnia
* Europa Północna
* Europa Zachodnia
* Południowe Zjednoczone Królestwo
* Zachodnie Zjednoczone Królestwo
* Kanada Środkowa
* Kanada Wschodnia
* Australia Wschodnia
* Australia Południowo-Wschodnia

Każda z tych regionów ma stojakami HSM wdrożone w dwóch niezależnych centrów danych lub przynajmniej dwóch strefach dostępności niezależne. Azja południowo-wschodnia ma trzy strefy dostępności i wschodnie stany USA 2 ma dwa. Europa, Azja Wschodnia i USA, które oferuje usługi w wersji dedykowanej przez moduł HSM jest daje w sumie ośmiu regionach. Aby uzyskać więcej informacji na temat regionów platformy Azure, można znaleźć w oficjalnej [regionów świadczenia usługi Azure information](https://azure.microsoft.com/global-infrastructure/regions/).
Pewne czynniki projektowania dla dowolnego rozwiązania oparte na dedykowany przez moduł HSM są lokalizacji/opóźnienia i wysokiej dostępności i obsługi innych aplikacji rozproszonych.

## <a name="device-location"></a>Lokalizacja urządzenia

Optymalne lokalizacji urządzenia sprzętowego modułu zabezpieczeń zbliża się najbliższy do aplikacji, wykonywanie operacji kryptograficznych. Opóźnienia w regionie powinien być oznaczony jedną cyfrą milisekund. Opóźnienie między regionami, może być 5 do 10 razy wyższa niż to.

## <a name="high-availability"></a>Wysoka dostępność

Osiągaj wysoką wydajność, klient korzystać tylko dwa urządzenia HSM w regionie, skonfigurowanych za pomocą oprogramowania firmy Gemalto jako parze o wysokiej dostępności. Ten typ wdrożenia zapewnia dostępność kluczy, jeśli pojedyncze urządzenie napotka problem uniemożliwia klucza operacji przetwarzania. Ponadto znacząco zmniejsza ryzyko podczas przeprowadzania konserwacji w zakresie usuwania awarii, takie jak zastąpienie dostaw energii. Jest ważne w przypadku projekt, aby uwzględnić regionalnym poziomie awarii. Regionalnymi awariami poziomu może się zdarzyć w przypadku klęski żywiołowe, takie jak hurricanes, powodzie lub trzęsienia ziemi. Te typy zdarzeń powinny być ograniczona przez inicjowania obsługi urządzeń przez moduł HSM w innym regionie. Urządzenia wdrożone w innym regionie mogą łączyć się ze sobą za pośrednictwem firmy Gemalto konfiguracji oprogramowania. Oznacza to, że minimalna wdrożenia o wysokiej dostępności i odzyskiwanie po awarii odporne na błędy rozwiązanie jest cztery urządzenia HSM w dwóch regionach. Lokalne nadmiarowość i nadmiarowości w regionach może służyć jako punkt odniesienia można dodać kolejne sprzętowego modułu zabezpieczeń urządzenia wdrożeń w celu zapewnienia obsługi opóźnienia, pojemności lub on spełniać inne wymagania specyficzne dla aplikacji.

## <a name="distributed-application-support"></a>Obsługa aplikacji rozproszonych

Dedykowane urządzenia sprzętowego modułu zabezpieczeń są zazwyczaj wdrożone w odniesieniu do aplikacji, które trzeba wykonać magazynu kluczy i operacjami pobierania klucza. Dedykowane urządzenia sprzętowego modułu zabezpieczeń znajduje się 10 partycji dla pomocy technicznej niezależnie od aplikacji. Lokalizacji urządzenia powinna być oparta na holistycznego widoku wszystkie aplikacje, które muszą korzystać z niej.

## <a name="next-steps"></a>Kolejne kroki

Po określeniu architektura wdrożenia większość działań konfiguracyjnych do wdrożenia tej architektury będzie świadczona przez firmy Gemalto. Obejmuje to konfigurację urządzenia, a także aplikacji scenariuszy integracji. Aby uzyskać więcej informacji, użyj [dział obsługi klienta firmy Gemalto](https://supportportal.gemalto.com/csm/) portal i Pobierz przewodniki administracji i konfiguracji. Witryna partner firmy Microsoft zawiera różne przewodnikach dotyczących integracji.
Zaleca się, że wszystkie kluczowe pojęcia usługi, takie jak wysoka dostępność i bezpieczeństwo na przykład są dobrze zrozumiałe przed device provisioning lub projekt aplikacji i wdrożenia.
Dodatkowe tematy poziomu pojęcia:

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowanie](monitoring.md)
