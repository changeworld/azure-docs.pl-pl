---
title: Limity i przydziały IoT Plug and Play Preview | Dokumenty firmy Microsoft
description: Opis limitów, przydziałów i ograniczania przepustowości, które mają zastosowanie podczas korzystania z wersji Zapoznawczej i odtworzania IoT.
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531381"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limity podglądu i przepustnice IoT Plug and Play, przydziały i ograniczenia

W tym artykule wyjaśniono limity specyficzne dla i odtworzania IoT, przydziały i ograniczanie przepustowości, które mają zastosowanie w publicznej wersji zapoznawczej. Istnieją istniejące [przydziały IoT Hub i ograniczania przepustowości,](../iot-hub/iot-hub-devguide-quotas-throttling.md) które również mają zastosowanie.

## <a name="iot-hub"></a>Usługa IoT Hub

W przypadku publicznej wersji zapoznawczej do centrum IoT hub mają zastosowanie następujące limity i przydziały:

| Limity, ograniczenia i ograniczenia | Wartość | Uwagi |
|-----|-----|-----|
| Liczba modeli możliwości urządzenia (DCM) lub interfejsów, które można zarejestrować na koncentrator | 1500 ||
| Maksymalna liczba interfejsów, które można zarejestrować na urządzenie | 40 ||
| Maksymalna liczba dcmów, które mogą być rejestrowane na urządzenie | 1 ||
| Maksymalny rozmiar pliku interfejsu/kontrolera domeny | 512 znaków ||
| Maksymalny rozmiar nazwy interfejsu | 256 znaków ||
| Maksymalny rozmiar nazwy właściwości  | 64 bajty, 7 poziomów głębokości (a pierwszy `$iotin`poziom jest zarezerwowany dla) | Dozwolone znaki: a-z, A-Z, 0-9 (nie jako pierwszy znak) i podkreślenia. |
| Maksymalny rozmiar wartości właściwości | 512 bajtów ||
| Maksymalny rozmiar nazwy polecenia | 100 bajtów ||
| Podwójny rozmiar urządzenia | Tak samo jak [limity koncentratora IoT](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Rozpoznawanie wywołań interfejsu API w jednostce SKU (niezależnie od jednostek) | 100 żądań na sekundę ||

## <a name="model-repository"></a>Repozytorium modeli

W przypadku publicznej wersji zapoznawczej do repozytorium modelu mają zastosowanie następujące limity i przydziały:

| Limity, ograniczenia i ograniczenia| Wartość |
|-----|-----|
| Liczba repozytoriów modeli firmy na dzierżawę usługi Azure Active Directory | 1 |
| Liczba kluczy autoryzacji na repozytorium modelu | 10  |
| Liczba modeli (DCM lub interfejsów) na repozytorium modelu firmy| 1500  |
| Liczba modeli (DCM lub interfejsów) w publicznym repozytorium modeli na dzierżawę usługi Azure Active Directory| 1500  |
| Liczba kontrolerów DOMENY lub interfejsów usuwanych w repozytorium modeli firmy | 10 zapytań na sekundę (QPS)|
| Liczba repozytoriów modelu tworzonych/aktualizowanych przez dzierżawę| 1 k/k |
| Liczba tworzonych/aktualizowanych/usuwanych kluczy autoryzacji w repozytorium modelu | 1 k/k|
| Liczba dcm tworzonych w repozytorium modelu firmy | 10 k.p.m. |
| Liczba interfejsów tworzonych w repozytorium modeli firmy | 10 k.p.m.|
| Liczba dcm tworzonych w publicznym repozytorium modelu | 10 k.p.m.|
| Liczba interfejsów tworzonych w publicznym repozytorium modelu | 10 k.p.m.|

## <a name="parser-library"></a>Biblioteka analizatora

Biblioteka analizatora jest zgodna z ograniczeniami, które mają zastosowanie do [języka cyfrowej podwójnej definicji](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Następne kroki

Sugerowanym następnym krokiem jest nauczenie się, jak [połączyć się z urządzeniem IoT Plug and Play i wchodzić w nią w interakcje.](./howto-develop-solution.md)
