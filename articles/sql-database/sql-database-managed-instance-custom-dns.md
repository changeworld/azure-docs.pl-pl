---
title: Baza danych Azure SQL zarządzane wystąpienia niestandardowe DNS | Dokumentacja firmy Microsoft
description: W tym temacie opisano opcje konfiguracji dla niestandardowego serwera DNS z wystąpieniem zarządzane bazy danych SQL Azure.
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 05a7b600ae8672447126b79cda10ca94c6d0fb48
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649333"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurowanie niestandardowego DNS dla bazy danych Azure SQL zarządzane wystąpienia

Azure wystąpienia bazy danych SQL zarządzane (wersja zapoznawcza) musi być wdrażana w obrębie platformy Azure [sieć wirtualną (VNet)](../virtual-network/virtual-networks-overview.md). Istnieje kilka scenariuszy, połączone serwery do innych wystąpień programu SQL w środowisku w chmurze czy hybrydowa, które wymagają nazwy hostów prywatnej zostać rozpoznane z wystąpienia zarządzane. W takim przypadku należy skonfigurować niestandardowe DNS wewnątrz Azure. Ponieważ zarządzane wystąpienia używa tego samego serwera DNS dla jego przebiega, konfiguracji DNS sieci wirtualnej musi być zgodny z zarządzanego wystąpienia. 

Aby niestandardowej konfiguracji DNS zgodny z wystąpieniem zarządzane, należy wykonać następujące czynności: 
- Skonfiguruj niestandardowe DNS do przekazywania żądań do usługi Azure DNS 
- Skonfiguruj niestandardowe DNS jako podstawowa i usługi Azure DNS jako dodatkowej sieci wirtualnej 
- Rejestrowanie niestandardowe DNS jako podstawowa i usługi Azure DNS jako dodatkowej

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Skonfiguruj niestandardowe DNS do przekazywania żądań do usługi Azure DNS 

Aby skonfigurować przekierowywanie DNS w systemie Windows Server 2016, wykonaj następujące kroki: 

1. W **Menedżera serwera**, kliknij przycisk **narzędzia**, a następnie kliknij przycisk **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Kliknij dwukrotnie **usług przesyłania dalej**.

   ![Usługi przesyłania dalej](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Kliknij pozycję **Edytuj**. 

   ![Forwarders-list](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Wprowadź Azure cykliczne mechanizmów rozpoznawania adresu IP, takich jak 168.63.129.16.

   ![Adres ip rozwiązujący cykliczne](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Skonfiguruj niestandardowe DNS jako podstawowa i usługi Azure DNS jako dodatkowej 
 
Konfiguracji serwera DNS na sieć wirtualną platformy Azure wymaga wprowadź adresy IP, aby skonfigurować maszyny Wirtualnej platformy Azure, który jest hostem serwera DNS za pomocą statycznego adresu IP za pomocą następujących czynności: 

1. W portalu Azure Otwórz niestandardowe DNS interfejsu sieciowego maszyny Wirtualnej.

   ![Interfejs sieciowy](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. W sekcji konfiguracji adresów IP. Wybierz konfigurację adresu IP 

   ![konfiguracja adresu IP](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Ustaw prywatnego adresu IP jako Static. Wpisz adres IP (10.0.1.5 na tym zrzucie ekranu) 

   ![statyczny](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Rejestrowanie niestandardowe DNS jako podstawowa i usługi Azure DNS jako pomocniczy 

1. W portalu Azure Znajdź opcja niestandardowa DNS dla sieci wirtualnej.

   ![Opcja niestandardowe dns](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Przełącz się do niestandardowego, a następnie wprowadź niestandardowy adres IP serwera DNS, a także Azure cykliczne mechanizmów rozpoznawania adresu IP, takich jak 168.63.129.16. 

   ![Opcja niestandardowe dns](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > To ustawienie nie zostanie Azure cyklicznego programu rozpoznawania nazw DNS listy powoduje wystąpienie zarządzane na przejściu w stan uszkodzony. Odzyskiwanie ze stanu może być konieczna utworzenia nowego wystąpienia w sieci wirtualnej z zasadami zgodności sieci, Utwórz danych na poziomie wystąpienia i przywracanie baz danych. Zobacz [konfigurację sieci wirtualnej](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać ogólne informacje, zobacz [co to jest wystąpieniem zarządzane](sql-database-managed-instance.md)
- Samouczek pokazuje sposób tworzenia nowego wystąpienia zarządzane, zobacz [Tworzenie wystąpienia zarządzane](sql-database-managed-instance-create-tutorial-portal.md).
- Informacji o konfigurowaniu sieci wirtualnej w przypadku zarządzanych, zobacz [konfigurację sieci wirtualnej dla zarządzanych wystąpień](sql-database-managed-instance-vnet-configuration.md)
