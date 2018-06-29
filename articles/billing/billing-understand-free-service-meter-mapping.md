---
title: Usługi w celu mapowania licznika dla Azure bezpłatne konto | Dokumentacja firmy Microsoft
description: Zrozumienie usługi do mapowania licznika dla usługi uwzględnione w bezpłatne konto.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 3019fe5f8b44eaa32d4ab309f296d6b4574df1d3
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061374"
---
# <a name="understand-free-service-to-meter-mapping"></a>Zrozumienie bezpłatnej usługi do pomiaru mapowania

Każda usługa Azure emituje użycia przed liczniki, które korzysta z platformy Azure systemów rozliczeniowych do obciążania użytkowników dla usług. Aby lepiej zrozumieć użycie bezpłatnych usług, Przyjrzyjmy się usługi do pomiaru mapowania dla tych usług. Aby dowiedzieć się, jak utworzyć bezpłatnych usług, zobacz [tworzenie bezpłatnych usług z bezpłatne konto platformy Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Usługi do pomiaru mapowania bezpłatne konto kwalifikujących się usług 

|    Usługa   | Nazwa licznika w portalu Azure | Nazwa licznika w użycia/interfejs API plików | Identyfikator miernika |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S maszyny Wirtualnej systemu Linux | Godziny - obliczeniowe Standard_B1 maszyny Wirtualnej | Godziny - wolnego obliczeniowe | 8260cba2-4437-47d1-a31e-2561cd370f50
| Windows B1S maszyny Wirtualnej | Godziny - obliczeniowe Standard_B1 maszyna wirtualna (system Windows) | Godziny - wolnego obliczeniowe | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S maszyny Wirtualnej — publiczny adres IP  | Godziny korzystania z adresu IP — publiczny adres IP | Godziny adres IP — bezpłatne | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Magazyn (GB) — rozwiązania Cosmos bazy danych | Magazyn (GB) — bezpłatne | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 jednostek żądania (godziny) - DB rozwiązania Cosmos | 100 jednostek żądania (godziny) — w warstwie bezpłatna | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | Standardowe we/wy — pliki (GB) — Magazyn lokalnie nadmiarowy | Standardowe we/wy — pliki (GB) — bezpłatne | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standardowe We/Wy — jednostki operacji odczytu dla plików (w 10 000) | Standardowe we/wy — plik jednostki operacji odczytu (w 10 000) — bezpłatne | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standardowe We/Wy — jednostki operacji zapisu dla plików (w 10 000) | Standardowe we/wy — plik jednostki operacji zapisu (w 10 000) — bezpłatne | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standardowe We/Wy — jednostki operacji na protokołach plików (w 10 000) | Standardowe we/wy — operacja jednostki na protokołach plików (w 10 000) — bezpłatne | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standardowe We/Wy — jednostki operacji generowania listy dla plików (w 10 000) | Standardowe we/wy — plik jednostki operacji generowania listy (w 10 000) — bezpłatne | e8ae79ad-c2ab-4D82-b226-dd3c33dfd40c
| Gorący blokowy obiekt Blob magazynu | Standardowe we/wy — operacje odczytu dla gorących blokowych obiektów Blob (w 10 000) | Standardowe we/wy — operacje (w 10 000) - wolnego odczytu dla gorących blokowych obiektów Blob |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Gorący blokowy obiekt Blob magazynu | Standardowe we/wy — gorący blokowy obiekt Blob (GB) — Magazyn lokalnie nadmiarowy | Standardowe we/wy — gorący blokowy obiekt Blob (GB) — bezpłatne | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Gorący blokowy obiekt Blob magazynu | Standardowe we/wy — operacje zapisu dla gorących blokowych obiektów Blob (w 10 000) | Standardowe we/wy — gorący blokowy obiekt Blob operacje zapisu (w 10 000) — bezpłatne | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Gorący blokowy obiekt Blob magazynu  | Standardowe we/wy — operacje zapisu/wyświetlenia listy gorących blokowych obiektów Blob (w 10 000) | Standardowe we/wy — gorący blokowy obiekt Blob zapisu/wyświetlenia listy operacje (w 10 000) — bezpłatne | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Zarządzane dysku *  | Standardowe dyski zarządzane/migawki (GB) — Magazyn lokalnie nadmiarowy | Standardowe dyski zarządzane/migawki (GB) — bezpłatne | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Zarządzane dysku *  | Operacje dysków zarządzanych w warstwie standardowa (w 10 000) | Operacje dysków zarządzanych w warstwie standardowa (w 10 000) — w warstwie bezpłatna | 82cc6ea4-0abd-43ac-ACC0-ec34edf0f14c
| Zarządzane dysku *  | Magazyn w warstwie Premium — stronicowy obiekt Blob/P6 (jednostki) — Magazyn lokalnie nadmiarowy | Magazyn w warstwie Premium — stronicowy obiekt Blob/P6 (jednostki) — bezpłatne | 2b98c168-27CA-4cc1-b509-e887dec87657
| SQL Database | Dni korzystania z bazy danych standardowa S0 — baza danych SQL | Dni korzystania z bazy danych standardowa S0 — w warstwie bezpłatna | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Shared - przepustowości ** | Wychodzący transfer danych (GB) | Transfer danych wychodzących (GB) — w warstwie bezpłatna | 0fc067a1-65d2-46DA-b24b-7a9cbe2c69bd

\* Po utworzeniu maszyny wirtualnej systemu Windows i wybierz dysków zarządzanych, będą korzystać z licznika dysków zarządzanych w ramach maszyny wirtualnej.

\** Udostępniony liczniki mogą być używane przez wiele usług. Na przykład zarówno maszyn wirtualnych i magazynu Emituj użycia przed Out(GB) transferu danych miernika.





## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
