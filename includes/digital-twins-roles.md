---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 7e4760990229433b2ea40fadd0d17de0b52fcb36
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183866"
---
W poniższej tabeli opisano role, które są dostępne w reprezentacji urządzeń cyfrowych platformy Azure:

| **Rola** | **Opis** | **Identyfikator** |
| --- | --- | --- |
| Administrator miejsca | *Tworzenie*, *odczytu*, *aktualizacji*, i *Usuń* uprawnień do określonego miejsca i na wszystkich węzłach poniżej. Uprawnienie globalne. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Administrator użytkowników| *Tworzenie*, *odczytu*, *aktualizacji*, i *Usuń* uprawnień dla użytkowników i obiektów związanych z użytkownika. *Przeczytaj* uprawnienia dla miejsca do magazynowania. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Rola Administrator urządzenia | *Tworzenie*, *odczytu*, *aktualizacji*, i *Usuń* uprawnień dla urządzeń i obiektów związanych z urządzenia. *Przeczytaj* uprawnienia dla miejsca do magazynowania. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Klucz administratora | *Tworzenie*, *odczytu*, *aktualizacji*, i *Usuń* uprawnienia dostępu do kluczy. *Przeczytaj* uprawnienia dla miejsca do magazynowania. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Token administratora |  *Przeczytaj* i *aktualizacji* uprawnienia dostępu do kluczy. *Przeczytaj* uprawnienia dla miejsca do magazynowania. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Użytkownik |  *Przeczytaj* uprawnień miejsca do magazynowania, czujniki i użytkowników, która zawiera odpowiednie powiązane obiekty. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Specjalista ds. pomocy technicznej |  *Przeczytaj* uprawnienie wszystkim, z wyjątkiem klucze dostępu. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Instalator urządzenia | *Przeczytaj* i *aktualizacji* uprawnienia dla urządzeń i czujników, w tym odpowiadające im powiązanych obiektów. *Przeczytaj* uprawnienia dla miejsca do magazynowania. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Urządzenie bramy | *Utwórz* uprawnień dla czujników. *Przeczytaj* uprawnienia dla urządzeń i czujników, w tym odpowiadające im powiązanych obiektów. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |