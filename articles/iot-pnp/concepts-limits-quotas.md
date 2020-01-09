---
title: Limity i przydziały IoT Plug and Play w wersji zapoznawczej | Microsoft Docs
description: Zapoznaj się z limitami, przydziałami i ograniczeniami dotyczącymi korzystania z programu IoT Plug and Play w wersji zapoznawczej.
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531381"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limity, przydziały i ograniczenia dotyczące Plug and Play IoT

W tym artykule wyjaśniono limity, przydziały i ograniczenia dotyczące Plug and Play IoT, które są stosowane w publicznej wersji zapoznawczej. Istnieją także [przydziały IoT Hub i ograniczanie przepustowości](../iot-hub/iot-hub-devguide-quotas-throttling.md) .

## <a name="iot-hub"></a>IoT Hub

W publicznej wersji zapoznawczej do centrum IoT są stosowane następujące limity i przydziały:

| Limity, ograniczenia i ograniczenia | Wartość | Uwagi |
|-----|-----|-----|
| Liczba modeli możliwości urządzeń (DCMs) lub interfejsów, które mogą być zarejestrowane na Hub | 1500 ||
| Maksymalna liczba interfejsów, które mogą być zarejestrowane na urządzenie | 40 ||
| Maksymalna liczba DCMs, które można zarejestrować na urządzenie | 1 ||
| Maksymalny rozmiar pliku interfejsu/DCM | 512 znaków ||
| Maksymalny rozmiar nazwy interfejsu | 256 znaków ||
| Maksymalny rozmiar nazwy właściwości  | 64 bajtów, 7 poziomów na głębokości (a pierwszy poziom jest zastrzeżony dla `$iotin`) | Dozwolone znaki: a-z, A-Z, 0-9 (nie jako pierwszy znak) i podkreślenia. |
| Maksymalny rozmiar wartości właściwości | 512 bajtów ||
| Maksymalny rozmiar nazwy polecenia | 100 B ||
| Rozmiar przędzy urządzenia | Analogicznie jak [limity IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Rozpoznawanie wywołań interfejsu API między jednostkami SKU (niezależnie od jednostek) | 100 żądań na sekundę ||

## <a name="model-repository"></a>Repozytorium modelu

W publicznej wersji zapoznawczej następujące limity i przydziały mają zastosowanie do repozytorium modelu:

| Limity, ograniczenia i ograniczenia| Wartość |
|-----|-----|
| Liczba repozytoriów modelu firmy dla dzierżawy Azure Active Directory | 1 |
| Liczba kluczy autoryzacji na repozytorium modelu | 10  |
| Liczba modeli (DCMs lub interfejsy) na repozytorium modelu firmy| 1500  |
| Liczba modeli (DCMs lub Interfaces) w repozytorium modelu publicznego dla dzierżawy Azure Active Directory| 1500  |
| Liczba DCMs lub interfejsów usuniętych w repozytorium modelu firmy | 10 zapytań na sekundę (zapytań)|
| Liczba repozytoriów modelu tworzonych/aktualizowanych przez dzierżawcę| 1 ZAPYTAŃ |
| Liczba kluczy autoryzacji tworzonych/aktualizowanych/usuniętych w repozytorium modelu | 1 ZAPYTAŃ|
| Liczba tworzonych DCMs w repozytorium modelu firmy | 10 ZAPYTAŃ |
| Liczba tworzonych interfejsów w repozytorium modelu firmy | 10 ZAPYTAŃ|
| Liczba tworzonych DCMs w repozytorium modelu publicznego | 10 ZAPYTAŃ|
| Liczba tworzonych interfejsów w repozytorium modelu publicznego | 10 ZAPYTAŃ|

## <a name="parser-library"></a>Biblioteka analizatora

Biblioteka analizatorów stosuje się do ograniczeń, które mają zastosowanie do [języka definicji cyfrowej sznurka](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Następne kroki

Sugerowany następny krok to Dowiedz się [, jak nawiązać połączenie z urządzeniem IoT Plug and Play i korzystać z niego](./howto-develop-solution.md).
