---
title: Wystąpienie zarządzane określa rozmiar sieci wirtualnej/podsieci
description: W tym temacie opisano sposób obliczania rozmiaru podsieci, w której zostaną wdrożone wystąpienia zarządzane bazy danych Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 7f0ef26343284b7b668e71676114586f4bec8b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825758"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Określanie rozmiaru podsieci sieci wirtualnej dla wystąpienia zarządzanego usługi Azure SQL Database

Wystąpienie zarządzanego bazy danych SQL platformy Azure musi zostać wdrożone w [sieci wirtualnej platformy](../virtual-network/virtual-networks-overview.md)Azure .

Liczba wystąpień zarządzanych, które można wdrożyć w podsieci sieci wirtualnej, zależy od rozmiaru podsieci (zakresu podsieci).

Podczas tworzenia wystąpienia zarządzanego platforma Azure przydziela liczbę maszyn wirtualnych w zależności od warstwy wybranej podczas inicjowania obsługi administracyjnej. Ponieważ te maszyny wirtualne są skojarzone z podsiecią, wymagają adresów IP. Aby zapewnić wysoką dostępność podczas regularnych operacji i konserwacji usług, platforma Azure może przydzielić dodatkowe maszyny wirtualne. W rezultacie liczba wymaganych adresów IP w podsieci jest większa niż liczba wystąpień zarządzanych w tej podsieci.

Zgodnie z projektem wystąpienie zarządzane wymaga co najmniej 16 adresów IP w podsieci i może używać maksymalnie 256 adresów IP. W rezultacie podczas definiowania zakresów adresów IP podsieci można użyć masek podsieci z zakresu od /28 do /24. Bit maski sieciowej /28 (14 hostów na sieć) jest dobrym rozmiarem dla pojedynczego wdrożenia ogólnego przeznaczenia lub o krytycznym znaczeniu dla firmy. Nieco maski /27 (30 hostów na sieć) jest idealny dla wielu wdrożeń wystąpienia zarządzanego w tej samej sieci wirtualnej. Ustawienia bitowe maski /26 (62 hostów) i /24 (254 hostów) umożliwiają dalsze skalowanie sieci wirtualnej w celu obsługi dodatkowych wystąpień zarządzanych.

> [!IMPORTANT]
> Rozmiar podsieci z 16 adresami IP jest minimum nienajmnym o ograniczonym potencjale, w którym operacja skalowania, taka jak zmiana rozmiaru vCore, nie jest obsługiwana. Wybór podsieci z prefiksem /27 lub najdłuższym prefiksem jest wysoce zalecany.

## <a name="determine-subnet-size"></a>Określanie rozmiaru podsieci

Jeśli planujesz wdrożyć wiele wystąpień zarządzanych wewnątrz podsieci i chcesz zoptymalizować rozmiar podsieci, użyj tych parametrów, aby utworzyć obliczenia:

- Platforma Azure używa pięciu adresów IP w podsieci dla własnych potrzeb
- Każde wystąpienie ogólnego przeznaczenia wymaga dwóch adresów
- Każde wystąpienie krytyczne dla firmy wymaga czterech adresów

**Przykład:** Planujesz mieć trzy ogólnego przeznaczenia i dwa wystąpienia zarządzane krytyczne dla firmy. Oznacza to, że potrzebujesz 5 + 3 * 2 + 2 * 4 = 19 adresów IP. Ponieważ zakresy adresów IP są zdefiniowane w mocy 2, potrzebny jest zakres IP 32 (2^5) adresów IP. W związku z tym należy zarezerwować podsieć z maską podsieci /27.

> [!IMPORTANT]
> Obliczenia wyświetlane powyżej staną się przestarzałe wraz z dalszymi ulepszeniami.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie, zobacz [Co to jest wystąpienie zarządzane](sql-database-managed-instance.md).
- Dowiedz się więcej o [architekturze łączności dla wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
- Zobacz, jak [utworzyć sieć wirtualną, w której będzie wdrażać wystąpienia zarządzane](sql-database-managed-instance-create-vnet-subnet.md)
- W przypadku problemów z systemem DNS zobacz [Konfigurowanie niestandardowego systemu DNS](sql-database-managed-instance-custom-dns.md)
