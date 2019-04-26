---
title: Usługi w celu mapowania miernika, aby uzyskać bezpłatne konto platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie usług do mapowania liczników dla usług przy użyciu bezpłatnego konta.
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
ms.author: banders
ms.openlocfilehash: 2468f61c187d9b10ed9fe55ccf76e5d2561d0505
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370734"
---
# <a name="understand-free-service-to-meter-mapping"></a>Omówienie mapowania miernika bezpłatnej usługi

Do każdej usługi platformy Azure emituje użycie względem liczniki, które korzysta z systemu rozliczeń platformy Azure do obciążenia użytkownicy usługi. Aby lepiej zrozumieć użycie w przypadku usług bezpłatnych, Przyjrzyjmy się usługi do mapowania liczników dla tych usług. Aby dowiedzieć się, jak utworzyć bezpłatnych usług, zobacz [tworzyć bezpłatne usługi przy użyciu bezpłatnego konta platformy Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Usługa do licznika mapowanie bezpłatnego konta kwalifikujących się usług. 

|    Usługa   | Nazwa licznika w witrynie Azure portal | Nazwa licznika w pliku/interfejs API użycia | Identyfikator miernika |
| ------------ | -------------------------- | -------------------------| -------- |
| Maszyny Wirtualnej systemu Linux B1S | Godziny obliczeniowe — Standard_B1 maszyny Wirtualnej | Godziny obliczeniowe — bezpłatnie | 8260cba2-4437-47d1-a31e-2561cd370f50
| Windows B1S maszyny Wirtualnej | Godziny obliczeniowe — Standard_B1 maszyny Wirtualnej (Windows) | Godziny obliczeniowe — bezpłatnie | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S maszyny Wirtualnej — publiczne adresy IP  | Godziny korzystania z adresu IP — publiczne adresy IP | Godziny adres IP — bezpłatna | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Magazyn (GB) — usługi Cosmos DB | Wolna pamięć (GB) — | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 jednostek żądania (godziny) — usługi Cosmos DB | 100 jednostek żądania (godziny) — bezpłatnie | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | Standardowe we/wy — pliki (GB) — lokalnie nadmiarowy | Standardowe we/wy — pliki (GB) — bezpłatnie | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standardowe We/Wy — jednostki operacji odczytu dla plików (w 10 000) | Standardowe we/wy — jednostki operacji odczytu (w 10 000) — bezpłatna pliku | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standardowe We/Wy — jednostki operacji zapisu dla plików (w 10 000) | Standardowe we/wy — plik jednostki operacji zapisu (w 10 000) — bezpłatnie | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standardowe We/Wy — jednostki operacji na protokołach plików (w 10 000) | Standardowe we/wy — plik jednostki operacji na protokołach (w 10 000) — bezpłatnie | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standardowe We/Wy — jednostki operacji generowania listy dla plików (w 10 000) | Standardowe we/wy — plik jednostki operacji generowania listy (w 10 000) — bezpłatnie | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Gorących blokowych obiektów Blob magazynu | Standardowe we/wy — operacje odczytu gorących blokowych obiektów Blob (w 10 000) | Standardowe we/wy — operacje (w 10 000) — bezpłatna odczytu dla gorących blokowych obiektów Blob |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Gorących blokowych obiektów Blob magazynu | Standardowe we/wy — gorący blokowy obiekt Blob (GB) — lokalnie nadmiarowy | Standardowe we/wy — gorący blokowy obiekt Blob (GB) — bezpłatnie | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Gorących blokowych obiektów Blob magazynu | Standardowe we/wy — operacje zapisu programu gorących blokowych obiektów Blob (w 10 000) | Standardowe we/wy — gorący blokowy obiekt Blob operacje zapisu (w 10 000) — bezpłatnie | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Gorących blokowych obiektów Blob magazynu  | Standardowe we/wy — operacje zapisu/wyświetlenia listy obiektów Blob gorących blokowych (w 10 000) | Standardowe we/wy — gorący blokowy obiekt Blob zapisu/wyświetlenia listy operacje (w 10 000) — bezpłatnie | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Dysk zarządzany *  | Standardowy dysk zarządzany/migawki (GB) — lokalnie nadmiarowy | Standardowy dysk zarządzany/migawki (GB) — bezpłatnie | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Dysk zarządzany *  | Operacje dysku zarządzanego w warstwie standardowa (w 10 000) | Operacje dysku zarządzanego w warstwie standardowa (w 10 000) — bezpłatnie | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Dysk zarządzany *  | Magazyn w warstwie Premium — stronicowy obiekt Blob/P6 (jednostki) — lokalnie nadmiarowy | Magazyn w warstwie Premium — stronicowy obiekt Blob/P6 (jednostki) — bezpłatna | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Liczba dni bazy danych — warstwa standardowa S0 — bazy danych SQL | Liczba dni bazy danych — warstwa standardowa S0 — bezpłatnie | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| W warstwie współdzielona — przepustowości ** | Wychodzący transfer danych (GB) | Transfer danych wychodzących (GB) — bezpłatnie | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Utwórz maszynę wirtualną Windows i wybierz dysk zarządzany, będą wymagały miernika dysków zarządzanych w ramach maszyny wirtualnej.

\** Udostępnionego liczniki mogą być używane przez wiele usług. Na przykład zarówno maszyny wirtualne i Magazyn emituje użycie względem Out(GB) transferu danych miernika.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
