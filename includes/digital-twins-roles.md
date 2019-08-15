---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 10161d6b1d944857aa45d5152c77e107a208d98d
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012223"
---
W poniższej tabeli opisano role, które są dostępne w usłudze Azure Digital bliźniaczych reprezentacji:

| **Rola** | **Opis** | **Identyfikatora** |
| --- | --- | --- |
| Administrator miejsca | Uprawnienia do *tworzenia*, *odczytu*, *aktualizacji*i *usuwania* dla określonego obszaru i wszystkich węzłów poniżej. Uprawnienie globalne. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Administrator użytkowników| Uprawnienia do *tworzenia*, *odczytu*, *aktualizacji*i *usuwania* dla użytkowników i obiektów związanych z użytkownikiem. Uprawnienie *Odczyt* dla spacji. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Administrator urządzenia | Uprawnienia do *tworzenia*, *odczytu*, *aktualizacji*i *usuwania* dla urządzeń i obiektów związanych z urządzeniami. Uprawnienie *Odczyt* dla spacji. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Administrator kluczy | Uprawnienia do *tworzenia*, *odczytu*, *aktualizowania*i *usuwania* kluczy dostępu. Uprawnienie *Odczyt* dla spacji. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Administrator tokenu |  Uprawnienia do *odczytu* i *aktualizacji* kluczy dostępu. Uprawnienie *Odczyt* dla spacji. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Użytkownik |  Uprawnienie *Odczyt* dotyczące spacji, czujników i użytkowników, które obejmują odpowiednie obiekty pokrewne. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Specjalista ds. pomocy technicznej |  Uprawnienie *Odczyt* dla wszystkich elementów oprócz kluczy dostępu. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Instalator urządzenia | Uprawnienie *Odczyt* i *Aktualizacja* dla urządzeń i czujników, które obejmują odpowiednie obiekty pokrewne. Uprawnienie *Odczyt* dla spacji. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Urządzenie bramy | *Utwórz* uprawnienie dla czujników. Uprawnienie *Odczyt* dla urządzeń i czujników, które obejmują odpowiednie obiekty pokrewne. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |