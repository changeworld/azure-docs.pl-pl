---
title: Mapowanie usług do mierników na bezpłatnym koncie platformy Azure
description: Omówienie mapowania usług dostępnych w ramach bezpłatnego konta do mierników.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 00f0db0107cee4a7f52dbf4e04a586c5e7920d61
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709398"
---
# <a name="understand-free-service-to-meter-mapping"></a>Omówienie mapowania bezpłatnych usług do mierników

Każda usługa platformy Azure generuje użycie rejestrowane przez mierniki, na podstawie których w systemie rozliczeń platformy Azure są naliczane opłaty za usługi używane przez użytkowników. Aby lepiej zrozumieć użycie bezpłatnych usług, przyjrzyjmy się mapowaniu usług do mierników. Aby dowiedzieć się, jak tworzyć bezpłatne usługi, zobacz [Tworzenie bezpłatnych usług przy użyciu bezpłatnego konta platformy Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-eligible-services"></a>Mapowanie kwalifikujących się usług do mierników

|    Usługa   | Nazwa miernika w witrynie Azure Portal | Nazwa miernika w pliku użycia/interfejsie API | Identyfikator miernika |
| ------------ | -------------------------- | -------------------------| -------- |
| Maszyna wirtualna B1S z systemem Linux | Godziny obliczeniowe — Maszyna wirtualna Standard_B1 | Godziny obliczeniowe — Bezpłatne | 8260cba2-4437-47d1-a31e-2561cd370f50
| Maszyna wirtualna B1S z systemem Windows | Godziny obliczeniowe — Maszyna wirtualna Standard_B1 (Windows) | Godziny obliczeniowe — Bezpłatne | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| Maszyna wirtualna B1S — Publiczne adresy IP  | Godziny korzystania z adresu IP — Publiczne adresy IP | Godziny korzystania z adresu IP — Bezpłatne | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Storage (GB) — Cosmos DB | Storage (GB) — Bezpłatne | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 jednostek żądania (godziny) — Cosmos DB | 100 jednostek żądania (godziny) — Bezpłatne | 5d638a6f-e221-41cf-ae3f-0f81d368cef6
| File Storage | Standardowe We/Wy — pliki (GB) — Lokalnie nadmiarowe | Standardowe We/Wy — pliki (GB) — Bezpłatne | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standardowe We/Wy — jednostki operacji odczytu dla plików (w 10 000) | Standardowe We/Wy — jednostki operacji odczytu dla plików (w 10 000) — Bezpłatne | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standardowe We/Wy — jednostki operacji zapisu dla plików (w 10 000) | Standardowe We/Wy — jednostki operacji zapisu dla plików (w 10 000) — Bezpłatne | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standardowe We/Wy — jednostki operacji na protokołach plików (w 10 000) | Standardowe We/Wy — jednostki operacji na protokołach plików (w 10 000) — Bezpłatne | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standardowe We/Wy — jednostki operacji generowania listy dla plików (w 10 000) | Standardowe We/Wy — jednostki operacji generowania listy dla plików (w 10 000) — Bezpłatne | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Blob Storage — gorące blokowe obiekty blob | Standardowe We/Wy — operacje odczytu gorących blokowych obiektów blob (w 10 000) | Standardowe We/Wy — operacje odczytu gorących blokowych obiektów blob (w 10 000) — bezpłatne |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Blob Storage — gorące blokowe obiekty blob | Standardowe We/Wy — gorące blokowe obiekty blob (GB) — Lokalnie nadmiarowe | Standardowe We/Wy — gorące blokowe obiekty blob (GB) — Bezpłatne | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Blob Storage — gorące blokowe obiekty blob | Standardowe We/Wy — operacje zapisu gorących blokowych obiektów blob (w 10 000) | Standardowe We/Wy — operacje zapisu gorących blokowych obiektów blob (w 10 000) — Bezpłatne | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Blob Storage — gorące blokowe obiekty blob  | Standardowe We/Wy — operacje zapisu/generowania list gorących blokowych obiektów blob (w 10 000) | Standardowe We/Wy — operacje zapisu/generowania list gorących blokowych obiektów blob (w 10 000) — Bezpłatne | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Dysk zarządzany <sup>1</sup>  | Dyski zarządzane/migawki w warstwie Standardowa (GB) — Lokalnie nadmiarowe | Dyski zarządzane/migawki w warstwie Standardowa (GB) — Lokalnie nadmiarowe — Bezpłatne | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Dysk zarządzany <sup>1</sup>  | Operacje na dyskach zarządzanych w warstwie Standardowa (w 10 000) | Operacje na dyskach zarządzanych w warstwie Standardowa (w 10 000) — Bezpłatne | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Dysk zarządzany <sup>1</sup>  | Premium Storage — stronicowy obiekt blob/P6 (jednostki) — Lokalnie nadmiarowe | Premium Storage — stronicowy obiekt Blob/P6 (jednostki) — Bezpłatne | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Liczba dni korzystania z bazy danych w warstwie Standardowa S0 — SQL Database | Liczba dni korzystania z bazy danych w warstwie Standardowa S0 — Bezpłatne | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Współdzielone — Bandwidth <sup>2</sup> | Transfer danych wychodzących (GB) | Transfer danych wychodzących (GB) — Bezpłatne | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

<sup>1</sup> W przypadku utworzenia maszyny wirtualnej z systemem Windows i wybrania dysku zarządzanego użycie będzie rejestrowane przez miernik dysku zarządzanego w ramach maszyny wirtualnej.

<sup>2</sup> Współdzielone liczniki mogą rejestrować użycie w ramach wielu usług. Na przykład usługi Virtual Machines i Storage generują użycie rejestrowane przez licznik Transfer danych wychodzących (GB).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- [Podwyższenie poziomu subskrypcji](billing-upgrade-azure-subscription.md)
