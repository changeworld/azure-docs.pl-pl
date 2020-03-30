---
title: Architektura wdrażania — dedykowany moduł HSM platformy Azure | Dokumenty firmy Microsoft
description: Podstawowe zagadnienia dotyczące projektowania podczas korzystania z dedykowanego modułu HSM platformy Azure jako części architektury aplikacji
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 89e3bf95a6b048e5e97cfb151ef9302b70eac1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048568"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Architektura wdrożenia usługi Azure Dedicated HSM

Dedykowany moduł HSM platformy Azure udostępnia kryptograficzne magazyny kluczy na platformie Azure. Spełnia rygorystyczne wymagania dotyczące zabezpieczeń. Klienci będą korzystać z korzystania z usługi Azure Dedykowany moduł HSM, jeśli:

* Musi spełniać wymagania certyfikatu FIPS 140-2 Level 3
* Wymagaj, aby miały wyłączny dostęp do modułu HSM
* powinny mieć pełną kontrolę nad swoimi urządzeniami

Moduły HSM są rozproszone w centrach danych firmy Microsoft i mogą być łatwo udostępniane jako para urządzeń jako podstawa rozwiązania o wysokiej dostępności. Mogą one również być wdrażane w różnych regionach w celu rozwiązania odporne na awarie. Regiony z dedykowanym modułem HSM są obecnie dostępne:

* Wschodnie stany USA
* Wschodnie stany USA 2
* Zachodnie stany USA
* Południowo-środkowe stany USA
* Azja Południowo-Wschodnia
* Azja Wschodnia
* Indie Środkowe
* Indie Południowe
* Japonia Wschodnia
* Japonia Zachodnia
* Europa Północna
* Europa Zachodnia
* Południowe Zjednoczone Królestwo
* Zachodnie Zjednoczone Królestwo
* Kanada Środkowa
* Kanada Wschodnia
* Australia Wschodnia
* Australia Południowo-Wschodnia

Każdy z tych regionów ma stojaki HSM wdrożone w dwóch niezależnych centrach danych lub co najmniej dwóch niezależnych strefach dostępności. Azja Południowo-Wschodnia ma trzy strefy dostępności, a wschodnie STANY USA 2 dwa. Istnieje w sumie osiem regionów w Europie, Azji i USA, które oferują dedykowaną usługę HSM. Aby uzyskać więcej informacji na temat regionów platformy Azure, zobacz oficjalne [informacje o regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
Niektóre czynniki projektowe dla dowolnego dedykowanego rozwiązania opartego na modułach HSM to lokalizacja/opóźnienie, wysoka dostępność i obsługa innych aplikacji rozproszonych.

## <a name="device-location"></a>Lokalizacja urządzenia

Optymalna lokalizacja urządzenia HSM znajduje się w najbliższej odległości od aplikacji wykonujących operacje kryptograficzne. Oczekuje się, że opóźnienie w regionie będzie jednocyfrowe milisekundy. Opóźnienie między regionami może być 5 do 10 razy wyższe niż to.

## <a name="high-availability"></a>Wysoka dostępność

Aby osiągnąć wysoką dostępność, klient musi używać dwóch urządzeń HSM w regionie, które są skonfigurowane przy użyciu oprogramowania Gemalto jako pary wysokiej dostępności. Ten typ wdrożenia zapewnia dostępność kluczy, jeśli na jednym urządzeniu wystąpi problem uniemożliwiający mu przetwarzanie operacji klucza. To również znacznie zmniejsza ryzyko podczas wykonywania przerwy / naprawy konserwacji, takich jak wymiana zasilania. Ważne jest, aby projekt uwzględniał wszelkiego rodzaju awarie na poziomie regionalnym. Awarie na poziomie regionalnym mogą wystąpić, gdy występują klęski żywiołowe, takie jak huragany, powodzie lub trzęsienia ziemi. Te typy zdarzeń powinny być złagodzone przez inicjowanie obsługi administracyjnej urządzeń HSM w innym regionie. Urządzenia wdrożone w innym regionie mogą być sparowane ze sobą za pomocą konfiguracji oprogramowania Gemalto. Oznacza to, że minimalne wdrożenie dla rozwiązania o wysokiej dostępności i odporności na awarie to cztery urządzenia HSM w dwóch regionach. Nadmiarowość i nadmiarowość lokalna w różnych regionach może służyć jako punkt odniesienia, aby dodać wszelkie dalsze wdrożenia urządzeń HSM do obsługi opóźnienia, pojemności lub w celu spełnienia innych wymagań specyficznych dla aplikacji.

## <a name="distributed-application-support"></a>Obsługa aplikacji rozproszonych

Dedykowane urządzenia HSM są zazwyczaj wdrażane w celu obsługi aplikacji, które muszą wykonywać operacje przechowywania kluczy i pobierania kluczy. Dedykowane urządzenia HSM mają 10 partycji do obsługi niezależnych aplikacji. Lokalizacja urządzenia powinna opierać się na całościowym widoku wszystkich aplikacji, które muszą korzystać z usługi.

## <a name="next-steps"></a>Następne kroki

Po określeniu architektury wdrażania większość działań konfiguracyjnych w celu zaimplementowania tej architektury zostanie dostarczona przez Gemalto. Obejmuje to konfigurację urządzenia, a także scenariusze integracji aplikacji. Aby uzyskać więcej informacji, skorzystaj z portalu [obsługi klienta Gemalto](https://supportportal.gemalto.com/csm/) i przewodników administracyjnych i konfiguracyjnych pobierania. Witryna partnera firmy Microsoft zawiera wiele przewodników integracji.
Zaleca się, aby wszystkie kluczowe pojęcia usługi, takie jak wysoka dostępność i zabezpieczenia, na przykład były dobrze rozumiane przed inicjowania obsługi administracyjnej urządzenia lub projektowania aplikacji i wdrażania.
Dalsze tematy na poziomie koncepcji:

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Obsługa sieci](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowania](monitoring.md)
