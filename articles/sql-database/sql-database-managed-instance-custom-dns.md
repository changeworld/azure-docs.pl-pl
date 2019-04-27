---
title: Usługa Azure SQL Database Managed wystąpienia niestandardowy system DNS | Dokumentacja firmy Microsoft
description: W tym temacie opisano opcje konfiguracji dla niestandardowego systemu DNS przy użyciu bazy danych wystąpienia zarządzanego Azure SQL.
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
ms.date: 12/13/2018
ms.openlocfilehash: bb5890b883b6062d834b928bff28a26a3664fb64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60700427"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurowanie niestandardowego DNS dla usługi Azure SQL Database wystąpienie zarządzane

Wystąpienia usługi Azure SQL Database Managed musi zostać wdrożony w ramach platformy Azure [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md). Istnieje kilka scenariuszy (na przykład poczty bazy danych, połączonych serwerów do innych wystąpień programu SQL w Twoim środowisku w chmurze czy hybrydowa), które wymagają nazwy hosta prywatnego do być rozwiązane wyłącznie z wystąpienia zarządzanego. W takim przypadku należy skonfigurować niestandardowe DNS wewnątrz platformy Azure. Ponieważ wystąpienia zarządzanego używa tego samego DNS dla działanie jego wewnętrznych mechanizmów, konfiguracji DNS sieci wirtualnej musi być zgodny z wystąpieniem zarządzanym.

   > [!IMPORTANT]
   > Zawsze należy używać w pełni kwalifikowanych nazw domen (FQDN) dla serwerów poczty, serwerami programu SQL Server i innych usług, nawet jeśli leżą one w sieci prywatnej strefy DNS. Na przykład użyć `smtp.contoso.com` serwera poczty ponieważ proste `smtp` nie będzie można poprawnie rozpoznać.

Niestandardową konfiguracją DNS jest niezgodny z wystąpieniem zarządzanym, należy:

- Konfigurowanie niestandardowego serwera DNS, dlatego jest w stanie rozpoznawać nazwy w domenie publicznej
- Umieść adresów IP usług platformy Azure cyklicznego programu rozpoznawania nazw DNS adresu 168.63.129.16 na końcu listy DNS sieci wirtualnej

## <a name="setting-up-custom-dns-servers-configuration"></a>Ustawianie niestandardowych konfiguracji serwerów DNS

1. W witrynie Azure portal Znajdź opcję niestandardową DNS dla sieci wirtualnej.

   ![Opcja niestandardowe dns](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png)

2. Przełącz się do niestandardowego, a następnie wprowadź niestandardowy adres IP serwera DNS, a także adres IP mechanizmów rozpoznawania cyklicznego platformy Azure 168.63.129.16.

   ![Opcja niestandardowe dns](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png)

   > [!IMPORTANT]
   > To ustawienie nie zostanie Azure cyklicznego programu rozpoznawania nazw na liście DNS może spowodować wystąpienie zarządzane do wprowadzania niestandardowych serwerów DNS jest niedostępny z jakiegoś powodu stanie awaryjnym. Odzyskanie stanu może być konieczna utworzyć nowego wystąpienia w sieci wirtualnej przy użyciu zgodnymi zasadami sieci, tworzenia danych na poziomie wystąpienia i przywracania baz danych. Ustawienia Azure cyklicznego programu rozpoznawania nazw, ponieważ zapewnia ostatni wpis na liście DNS, nawet wtedy, gdy nie wszystkie niestandardowe serwery DNS, nazwy publicznej nadal można rozpoznać.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Aby uzyskać samouczek omawiający Tworzenie nowego wystąpienia zarządzanego, zobacz [tworzenia wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej do wystąpienia zarządzanego, zobacz [konfiguracja sieci wirtualnej dla wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md)
