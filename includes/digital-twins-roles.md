---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 864d693f2919922b5035f963103b2ce98600a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044944"
---
W poniższej tabeli opisano role, które są dostępne w usłudze Azure Digital Twins:

| **Roli** | **Opis** | **Identyfikator** |
| --- | --- | --- |
| Administrator miejsca | *Uprawnienie CREATE*, *READ*, *UPDATE*i *DELETE* dla określonego miejsca i wszystkich węzłów pod spodem. Globalne uprawnienia. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Administrator użytkowników| *Uprawnienie CREATE*, *READ*, *UPDATE*i *DELETE* dla użytkowników i obiektów związanych z użytkownikiem. *Uprawnienie DO ODCZYTU* dla przestrzeni. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Administrator urządzenia | *Uprawnienie CREATE*, *READ*, *UPDATE*i *DELETE* dla urządzeń i obiektów związanych z urządzeniem. *Uprawnienie DO ODCZYTU* dla przestrzeni. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Administrator kluczy | *Uprawnienie CREATE*, *READ*, *UPDATE*i *DELETE* dla kluczy dostępu. *Uprawnienie DO ODCZYTU* dla przestrzeni. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Administrator tokenu |  Uprawnienia *READ* i *UPDATE* dla kluczy dostępu. *Uprawnienie DO ODCZYTU* dla przestrzeni. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Użytkownik |  *Uprawnienie READ* dla przestrzeni, czujników i użytkowników, które obejmuje odpowiadające im powiązane obiekty. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Specjalista ds. Wsparcia |  *Uprawnienie READ* dla wszystkiego, z wyjątkiem kluczy dostępu. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Instalator urządzeń | *Uprawnienie READ* i *UPDATE* dla urządzeń i czujników, które obejmują odpowiadające im powiązane obiekty. *Uprawnienie DO ODCZYTU* dla przestrzeni. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Urządzenie bramy | *Uprawnienie CREATE* dla czujników. *Uprawnienie READ* dla urządzeń i czujników, które obejmuje ich odpowiednie obiekty pokrewne. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |