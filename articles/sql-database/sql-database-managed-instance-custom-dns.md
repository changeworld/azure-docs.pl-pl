---
title: Usługa Azure SQL Database Managed wystąpienia niestandardowy system DNS | Dokumentacja firmy Microsoft
description: W tym temacie opisano opcje konfiguracji dla niestandardowego systemu DNS przy użyciu bazy danych wystąpienia zarządzanego Azure SQL.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: d5bb2f2f4b79c4b03e631fc844a712f76fc69109
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258171"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurowanie niestandardowego DNS dla usługi Azure SQL Database wystąpienie zarządzane

Baza danych wystąpienia zarządzanego Azure SQL (wersja zapoznawcza) musi zostać wdrożony w ramach platformy Azure [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md). Istnieje kilka scenariuszy, połączone serwery do innych wystąpień programu SQL w Twoim środowisku w chmurze czy hybrydowa, które wymagają nazwy hosta prywatnego do być rozwiązane wyłącznie z wystąpienia zarządzanego. W takim przypadku należy skonfigurować niestandardowe DNS wewnątrz platformy Azure. Ponieważ wystąpienia zarządzanego używa tego samego DNS dla działanie jego wewnętrznych mechanizmów, konfiguracji DNS sieci wirtualnej musi być zgodny z wystąpieniem zarządzanym. 

Aby zapewnić niestandardową konfiguracją DNS zgodność z wystąpieniem zarządzanym, musisz wykonaj następujące czynności: 
- Konfigurowanie niestandardowych serwerów DNS do przekazywania żądań do usługi Azure DNS 
- Konfigurowanie niestandardowych serwerów DNS jako podstawowy i usługi Azure DNS jako pomocniczy dla sieci wirtualnej 
- Rejestrowanie niestandardowych serwerów DNS jako podstawowy i usługi Azure DNS jako pomocniczego

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Konfigurowanie niestandardowych serwerów DNS do przekazywania żądań do usługi Azure DNS 

Aby skonfigurować przekazywanie DNS w systemie Windows Server 2016, wykonaj następujące kroki: 

1. W **Menedżera serwera**, kliknij przycisk **narzędzia**, a następnie kliknij przycisk **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Kliknij dwukrotnie **usług przesyłania dalej**.

   ![Usługi przesyłania dalej](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Kliknij pozycję **Edytuj**. 

   ![Forwarders-list](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Wprowadź platformy Azure rekursywnego rozpoznawania nazw adres IP, takich jak 168.63.129.16.

   ![Adres ip mechanizmów rozpoznawania cyklicznego](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Konfigurowanie niestandardowych serwerów DNS jako podstawowy i usługi Azure DNS jako pomocniczego 
 
Konfiguracji serwera DNS na sieć wirtualną platformy Azure wymaga wprowadź adresy IP, więc Konfigurowanie maszyny Wirtualnej platformy Azure, który jest hostem serwera DNS ze statycznym adresem IP przy użyciu następujących czynności: 

1. W witrynie Azure portal Otwórz niestandardowe DNS interfejsu sieciowego maszyny Wirtualnej.

   ![Interfejs sieciowy](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. W sekcji konfiguracji adresu IP. Wybierz konfigurację adresu IP 

   ![konfiguracja adresu IP](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Ustaw prywatny adres IP jako statycznego. Wpisz adres IP (10.0.1.5 na tym zrzucie ekranu) 

   ![statyczny](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Rejestrowanie niestandardowych serwerów DNS jako podstawowy i usługi Azure DNS jako pomocniczy 

1. W witrynie Azure portal Znajdź opcję niestandardową DNS dla sieci wirtualnej.

   ![Opcja niestandardowe dns](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Przełącz się do niestandardowego, a następnie wprowadź niestandardowy adres IP serwera DNS, a także platformy Azure rekursywnego rozpoznawania nazw adres IP, takich jak 168.63.129.16. 

   ![Opcja niestandardowe dns](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > To ustawienie nie zostanie Azure cyklicznego programu rozpoznawania nazw DNS listy powoduje, że wystąpienie zarządzane wejść w stan uszkodzony. Odzyskanie stanu może być konieczna utworzyć nowego wystąpienia w sieci wirtualnej przy użyciu zgodnymi zasadami sieci, tworzenia danych na poziomie wystąpienia i przywracania baz danych. Zobacz [konfiguracja sieci wirtualnej](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Aby uzyskać samouczek omawiający Tworzenie nowego wystąpienia zarządzanego, zobacz [tworzenia wystąpienia zarządzanego](sql-database-managed-instance-create-tutorial-portal.md).
- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej do wystąpienia zarządzanego, zobacz [konfiguracja sieci wirtualnej dla wystąpienia zarządzanego](sql-database-managed-instance-vnet-configuration.md)
