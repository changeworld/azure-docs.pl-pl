---
title: Zagadnienia dotyczące zabezpieczeń dla programu SQL Server na platformie Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera ogólne wskazówki dotyczące zabezpieczania programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d5d10562a70b7d37908bc272bf555fd967831009
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076927"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Zagadnienia dotyczące zabezpieczeń programu SQL Server na maszynach wirtualnych platformy Azure

Ten temat zawiera ogólne wytyczne dotyczące zabezpieczeń, ułatwiających ustanowienia bezpiecznego dostępu do wystąpienia programu SQL Server na maszynie wirtualnej (VM) platformy Azure.

Azure jest zgodna z kilku branży regulacjami i standardami, które umożliwiają utworzenie rozwiązania do zgodnych z programem SQL Server działa na maszynie wirtualnej. Aby uzyskać informacje o zgodności z przepisami dzięki platformie Azure, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Kontrola dostępu do maszyny Wirtualnej SQL

Podczas tworzenia maszyny wirtualnej programu SQL Server, zastanów się, jak dokładnie kontrolować, kto ma dostęp do maszyny i programu SQL Server. Ogólnie rzecz biorąc należy wykonać następujące czynności:

- Należy ograniczyć dostęp do programu SQL Server, tylko do aplikacji i klientów, którzy muszą ją.
- Stosuj najlepsze rozwiązania dotyczące zarządzania konta użytkownika i hasła.

Poniższe sekcje zawierają sugestie na myślenie za pośrednictwem tych punktów.

## <a name="secure-connections"></a>Bezpieczne połączenia

Podczas tworzenia maszyny wirtualnej programu SQL Server z obrazem galerii **łączność z serwerem SQL** opcja zapewnia wybór **lokalnego (wewnątrz maszyny Wirtualnej)** , **prywatnie (wewnątrz sieci wirtualnej)** , lub **publiczne (Internet)** .

![Łączność z serwerem SQL](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Najlepiej wybrać restrykcyjny dla danego scenariusza. Na przykład, jeśli korzystasz z aplikacji, uzyskuje dostęp do programu SQL Server na tej samej maszyny Wirtualnej, następnie **lokalnego** to najbezpieczniejsza opcja wybór. Jeśli korzystasz z aplikacją platformy Azure, która wymaga dostępu do programu SQL Server, następnie **prywatnej** zabezpiecza komunikacji z serwerem SQL tylko w ramach określonych [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Jeśli potrzebujesz **publicznych** (internet) dostęp do maszyny Wirtualnej programu SQL Server, a następnie upewnij się, że inne najlepszych rozwiązań w tym temacie, aby zmniejszyć obszar powierzchni ataku.

Wybrane opcje w witrynie portal użyć reguły zabezpieczeń ruchu przychodzącego na maszynie Wirtualnej [sieciowej grupy zabezpieczeń](../../../virtual-network/security-overview.md) (NSG), aby udzielić lub odmówić ruch sieciowy do maszyny wirtualnej. Można zmodyfikować lub utworzyć nowe reguły dla ruchu przychodzącego sieciowej grupy zabezpieczeń zezwalającą na ruch do portu programu SQL Server (domyślnie 1433). Można również określić określone adresy IP, które mogą komunikować się za pośrednictwem tego portu.

![Reguły sieciowych grup zabezpieczeń](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Oprócz reguły sieciowej grupy zabezpieczeń umożliwiają ograniczanie ruchu sieciowego można również użyć Zapory Windows na maszynie wirtualnej.

Jeśli używasz punktów końcowych przy użyciu klasycznego modelu wdrażania, Usuń wszystkie punkty końcowe na maszynie wirtualnej, jeśli nie należy ich używać. Aby uzyskać instrukcje na temat korzystania z listy ACL z punktami końcowymi, zobacz [listy ACL punktu końcowego zarządzania](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Nie jest to konieczne w przypadku maszyn wirtualnych, które używają Menedżera zasobów.

Na koniec należy wziąć pod uwagę Włączanie połączeń szyfrowanych wystąpienia aparatu bazy danych programu SQL Server na maszynie wirtualnej platformy Azure. Wystąpienie programu SQL server należy skonfigurować przy użyciu certyfikatu z podpisem. Aby uzyskać więcej informacji, zobacz [Włącz zaszyfrowane połączenia z aparatem bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) i [składnia ciągu połączenia](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Korzystanie z portu innego niż domyślny

Domyślnie program SQL Server nasłuchuje na dobrze znanym porcie 1433. Aby zwiększyć bezpieczeństwo należy skonfigurować program SQL Server do nasłuchiwania na porcie inny niż domyślny, takim jak 1401. Zainicjowanie obsługi wyświetlania obrazu z galerii programu SQL Server w witrynie Azure portal, można określić ten port w **ustawień programu SQL Server** bloku.

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Aby to skonfigurować po zainicjowaniu obsługi administracyjnej, masz dwie opcje:

- W przypadku maszyn wirtualnych usługi Resource Manager możesz wybrać **zabezpieczeń** z [zasobów maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource). Zapewnia to możliwość zmiany portu.

  ![TCP port zmiany w portalu](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Dla klasycznych maszyn wirtualnych lub maszyn wirtualnych serwera SQL, które nie zostały aprowizowane za pomocą portalu można ręcznie skonfigurować port, nawiązując połączenie zdalne z maszyną wirtualną. Kroki konfiguracji opisane w artykule [skonfigurować serwer do nasłuchiwania na konkretnym porcie TCP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Jeśli korzystasz z tej techniki ręcznie, należy dodać reguły zapory Windows w celu zezwalania na ruch przychodzący na tym porcie TCP.

> [!IMPORTANT]
> Określanie portów innych niż domyślne jest dobrym rozwiązaniem, jeśli port programu SQL Server jest otwarty w celu połączenia przez publiczny internet.

Gdy program SQL Server nasłuchuje na porcie innych niż domyślne, należy określić port, po nawiązaniu połączenia. Na przykład, Rozważmy scenariusz, w których adres IP serwera jest 13.55.255.255 i programu SQL Server nasłuchuje na porcie 1401. Aby połączyć z programem SQL Server, należy określić `13.55.255.255,1401` w parametrach połączenia.

## <a name="manage-accounts"></a>Zarządzanie kontami

Nie chcesz, aby osoby atakujące do łatwego odgadnięcia nazw kont lub haseł. Skorzystaj z następujących porad ułatwiających:

- Utwórz konto unikatowy administratora lokalnego, którego nie ma nazwy **administratora**.

- Używaj złożonych silnych haseł dla wszystkich kont. Aby uzyskać więcej informacji na temat tworzenia silnych haseł, zobacz [Utwórz silne hasło](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) artykułu.

- Domyślnie platforma Azure wybiera uwierzytelniania Windows podczas instalacji maszyny wirtualnej programu SQL Server. W związku z tym **SA** logowania jest wyłączona, a hasło jest przypisywany przez Instalatora. Zaleca się **SA** logowania nie powinny być używane lub włączone. Jeśli identyfikator logowania SQL, użyj jednej z następujących strategii:

  - Utwórz konto SQL z unikatową nazwę, która ma **sysadmin** członkostwa. Można to zrobić z poziomu portalu, należy włączyć **uwierzytelniania SQL** podczas inicjowania obsługi.

    > [!TIP] 
    > Jeśli nie zostanie włączone uwierzytelnianie SQL podczas inicjowania obsługi, należy ręcznie zmienić tryb uwierzytelniania **programu SQL Server i tryb uwierzytelniania Windows**. Aby uzyskać więcej informacji, zobacz [Zmień tryb uwierzytelniania serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Jeśli musisz użyć **SA** logowania, Włącz logowanie po zainicjowaniu obsługi administracyjnej oraz Przypisz silne hasło.

## <a name="follow-on-premises-best-practices"></a>Stosuj najlepsze rozwiązania w środowisku lokalnym

Oprócz rozwiązania opisane w tym temacie firma Microsoft zaleca, możesz przejrzeć i zaimplementować rozwiązania w zakresie zabezpieczeń tradycyjnych lokalnych, jeśli ma to zastosowanie. Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące zabezpieczeń na potrzeby instalacji serwera SQL](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Następne kroki

Jeśli interesuje Cię również najlepsze rozwiązania dotyczące wydajności, zobacz [Performance Best Practices for SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Aby uzyskać inne tematy związane z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

