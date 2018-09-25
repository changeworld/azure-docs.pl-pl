---
title: Usługa Azure SQL Database Managed wystąpienia niestandardowy system DNS | Dokumentacja firmy Microsoft
description: W tym temacie opisano opcje konfiguracji dla niestandardowego systemu DNS przy użyciu bazy danych wystąpienia zarządzanego Azure SQL.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 09/23/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 2d1bb7e8522da32dd33933261ea41b578f8afac1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949489"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurowanie niestandardowego DNS dla usługi Azure SQL Database wystąpienie zarządzane

Wystąpienia usługi Azure SQL Database Managed musi zostać wdrożony w ramach platformy Azure [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md). Istnieje kilka scenariuszy (tj. połączone serwery do innych wystąpień programu SQL w Twoim środowisku w chmurze czy hybrydowa), które wymagają nazwy hosta prywatnego do być rozwiązane wyłącznie z wystąpienia zarządzanego. W takim przypadku należy skonfigurować niestandardowe DNS wewnątrz platformy Azure. Ponieważ wystąpienia zarządzanego używa tego samego DNS dla działanie jego wewnętrznych mechanizmów, konfiguracji DNS sieci wirtualnej musi być zgodny z wystąpieniem zarządzanym. 

Niestandardową konfiguracją DNS jest niezgodny z wystąpieniem zarządzanym, należy: 
- Konfigurowanie niestandardowego serwera DNS, dlatego jest w stanie rozpoznawać nazwy w domenie publicznej 
- Umieść adresów IP usług platformy Azure cyklicznego programu rozpoznawania nazw DNS adresu 168.63.129.16 na końcu listy DNS sieci wirtualnej 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>Ustawianie niestandardowych konfiguracji serwerów DNS

1. W witrynie Azure portal Znajdź opcję niestandardową DNS dla sieci wirtualnej.

   ![Opcja niestandardowe dns](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Przełącz się do niestandardowego, a następnie wprowadź niestandardowy adres IP serwera DNS, a także adres IP mechanizmów rozpoznawania cyklicznego platformy Azure 168.63.129.16. 

   ![Opcja niestandardowe dns](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > To ustawienie nie zostanie Azure cyklicznego programu rozpoznawania nazw DNS listy powoduje, że wystąpienie zarządzane wejść w stan uszkodzony. Odzyskanie stanu może być konieczna utworzyć nowego wystąpienia w sieci wirtualnej przy użyciu zgodnymi zasadami sieci, tworzenia danych na poziomie wystąpienia i przywracania baz danych. Zobacz [konfiguracja sieci wirtualnej](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Aby uzyskać samouczek omawiający Tworzenie nowego wystąpienia zarządzanego, zobacz [tworzenia wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej do wystąpienia zarządzanego, zobacz [konfiguracja sieci wirtualnej dla wystąpienia zarządzanego](sql-database-managed-instance-vnet-configuration.md)
