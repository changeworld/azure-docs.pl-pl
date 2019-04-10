---
title: Wystąpienie usługi Azure SQL Database Managed Określanie rozmiaru sieci wirtualnej/podsieci | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób obliczania rozmiar podsieci, w której zostaną wdrożone usługi Azure SQL wystąpieniach zarządzanych bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 02/22/2019
ms.openlocfilehash: 2a10876bc3c9558de29caf9fee2ae0b06ee87f28
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358923"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Określ rozmiar podsieci sieci wirtualnej dla wystąpienia zarządzanego Azure SQL Database

Wystąpienie usługi Azure SQL Database Managed musi zostać wdrożony w ramach platformy Azure [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md).

Liczba wystąpień zarządzanych, które mogą być wdrażane w podsieci sieci wirtualnej zależy od rozmiaru podsieci (zakres podsieci).

Podczas tworzenia wystąpienia zarządzanego Azure przydziela liczbę maszyn wirtualnych, w zależności od warstwy wybranej podczas inicjowania obsługi. Ponieważ te maszyny wirtualne są skojarzone z podsieci, wymagają one adresów IP. Aby zapewnić wysoką dostępność podczas regularnych operacjach i obsłudze usługi, platformy Azure może przydzielić dodatkowe maszyny wirtualne. W wyniku liczbę wymaganych adresów IP w podsieci jest większa niż liczba wystąpień zarządzanych w tej podsieci.

Zgodnie z projektem wystąpienie zarządzane wymaga co najmniej 16 adresów IP w podsieci i może używać maksymalnie 256 adresów IP. W rezultacie można użyć maski podsieci, między /28 i prefiksie/24, podczas definiowania zakresów IP podsieci. Bit maski sieci/28 (14 hostów) jest dobry rozmiar dla jednego ogólnego przeznaczenia lub krytyczne dla prowadzonej działalności wdrożenia. Bit maski/27 (30 hostów) są idealne dla wielu wdrożeń do wystąpienia zarządzanego, w ramach tej samej sieci wirtualnej. Maska bitowa ustawienia /26 (62 hostów) i prefiksie/24 (254 hostów) umożliwia dalsze skalowanie spoza sieci wirtualnej, która obsługuje dodatkowe wystąpienia zarządzanego.

> [!IMPORTANT]
> Rozmiar podsieci, z 16 adresów IP jest absolutnego minimum przy użyciu ograniczonych możliwości dalsze skalowanie wystąpienia zarządzanego w poziomie. Zdecydowanie zaleca się wybranie pozycji podsieci z prefiksem wartość/27 lub poniżej.

## <a name="determine-subnet-size"></a>Należy określić rozmiar podsieci

Jeśli planujesz wdrożyć wiele wystąpień zarządzanych w tej podsieci i należy zoptymalizować rozmiar podsieci, użyj tych parametrów w celu utworzenia obliczeń:

- Platforma Azure używa pięciu adresów IP w podsieci dla własnych potrzeb
- Każde wystąpienie ogólnego przeznaczenia należy dwa adresy
- Każde wystąpienie krytyczne dla działania firmy wymaga czterech adresów

**Przykład**: Ma trzy ogólnego przeznaczenia i dwa biznesowe krytyczne wystąpienia zarządzane przez usługę. Czy potrzebujesz 5 + 3 * 2 + 2 * 4 = 19 oznacza, że adresy IP. Zakresy adresów IP określonych w potęgą liczby 2 należy zakresu adresów IP 32 (2 ^ 5) adresy IP. W związku z tym należy zarezerwować podsieć z maską podsieci/27.

> [!IMPORTANT]
> Obliczenie wyświetlane powyżej staną się nieaktualne z dalsze ulepszenia.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md).
- Dowiedz się więcej o [architektura łączności dla wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
- Zobacz jak [Utwórz sieć wirtualną, w którym będą wdrażane wystąpienia zarządzane przez usługę](sql-database-managed-instance-create-vnet-subnet.md)
- W przypadku problemów DNS, zobacz [Konfigurowanie niestandardowych serwerów DNS](sql-database-managed-instance-custom-dns.md)
