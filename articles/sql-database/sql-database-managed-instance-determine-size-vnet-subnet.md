---
title: Azure SQL Database Określanie rozmiaru sieci wirtualnej/podsieci przez wystąpienie zarządzane
description: W tym temacie opisano sposób obliczania rozmiaru podsieci, w której zostaną wdrożone Azure SQL Database wystąpienia zarządzane.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: fb0d79a82553349d5dad547a2d78a460301cbd6d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688120"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Określ rozmiar podsieci sieci wirtualnej dla Azure SQL Database wystąpienia zarządzanego

Azure SQL Database wystąpienie zarządzane musi być wdrożone w ramach [sieci wirtualnej](../virtual-network/virtual-networks-overview.md)platformy Azure.

Liczba wystąpień zarządzanych, które można wdrożyć w podsieci sieci wirtualnej, zależy od rozmiaru podsieci (zakresu podsieci).

Podczas tworzenia wystąpienia zarządzanego platforma Azure przydziela wiele maszyn wirtualnych w zależności od wybranej warstwy podczas aprowizacji. Ponieważ te maszyny wirtualne są skojarzone z podsiecią, wymagają one adresów IP. Aby zapewnić wysoką dostępność podczas regularnych operacji i konserwacji usług, platforma Azure może przydzielić dodatkowe maszyny wirtualne. W związku z tym liczba wymaganych adresów IP w podsieci jest większa niż liczba wystąpień zarządzanych w tej podsieci.

Zgodnie z projektem wystąpienie zarządzane musi mieć co najmniej 16 adresów IP w podsieci i może korzystać z maksymalnie 256 adresów IP. W związku z tym podczas definiowania zakresów adresów IP podsieci można użyć masek podsieci między/28 i/24. Bit maski sieci wynoszący/28 (14 hostów na sieć) to dobry rozmiar jednego ogólnego celu lub krytycznego dla firmy. Bit maski z/27 (30 hostów na sieć) jest idealnym rozwiązaniem dla wielu wdrożeń wystąpienia zarządzanego w ramach tej samej sieci wirtualnej. Ustawienia bitów maski/26 (62 hosty) i/24 (hosty 254) umożliwiają dalsze skalowanie w sieci wirtualnej w celu obsługi dodatkowych wystąpień zarządzanych.

> [!IMPORTANT]
> Rozmiar podsieci z 16 adresami IP jest minimalny od zera z ograniczonym potencjalną operacją skalowania, taką jak rdzeń wirtualny zmiana rozmiaru. Wybór podsieci z prefiksem/27 lub najdłuższym prefiksem jest zdecydowanie zalecane.

## <a name="determine-subnet-size"></a>Określanie rozmiaru podsieci

Jeśli planujesz wdrożyć wiele wystąpień zarządzanych wewnątrz podsieci i chcesz zoptymalizować rozmiar podsieci, Użyj tych parametrów do tworzenia obliczeń:

- Platforma Azure używa pięciu adresów IP w podsieci do własnych potrzeb
- Każde wystąpienie Ogólnego przeznaczenia wymaga dwóch adresów
- Każde wystąpienie Krytyczne dla działania firmy potrzebuje czterech adresów

**Przykład**: planujesz trzy ogólnego przeznaczenia i dwa krytyczne dla działania firmy wystąpienia zarządzane. Oznacza to, że potrzebujesz 5 + 3 * 2 + 2 * 4 = 19 adresów IP. Ponieważ zakresy adresów IP są zdefiniowane w mocy 2, wymagany jest zakres adresów IP 32 (2 ^ 5). W związku z tym należy zarezerwować podsieć z maską podsieci/27.

> [!IMPORTANT]
> Obliczenia wyświetlane powyżej staną się przestarzałe z dalszych ulepszeń.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md).
- Dowiedz się więcej o [architekturze łączności dla wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
- Zobacz jak [utworzyć sieć wirtualną, w której będą wdrażane wystąpienia zarządzane](sql-database-managed-instance-create-vnet-subnet.md)
- W przypadku problemów z usługą DNS zobacz [Konfigurowanie niestandardowego systemu DNS](sql-database-managed-instance-custom-dns.md)
