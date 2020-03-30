---
title: Zagadnienia dotyczące zabezpieczeń dla programu SQL Server na platformie Azure | Dokumenty firmy Microsoft
description: Ten temat zawiera ogólne wskazówki dotyczące zabezpieczania programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f5ea0ddff38532b119d8d984f2dabd6d898b44a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031360"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Zagadnienia dotyczące zabezpieczeń programu SQL Server w usłudze Azure Virtual Machines

Ten temat zawiera ogólne wskazówki dotyczące zabezpieczeń, które pomagają ustanowić bezpieczny dostęp do wystąpień programu SQL Server na maszynie wirtualnej platformy Azure (VM).

Platforma Azure jest zgodna z kilkoma przepisami branżowymi i standardami, które umożliwiają tworzenie zgodnego rozwiązania z programem SQL Server uruchomionym na maszynie wirtualnej. Aby uzyskać informacje na temat zgodności z przepisami platformy Azure, zobacz [Centrum zaufania platformy Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Kontrola dostępu do maszyny Wirtualnej SQL

Podczas tworzenia maszyny wirtualnej programu SQL Server należy rozważyć sposób dokładnego kontrolowania, kto ma dostęp do komputera i programu SQL Server. Ogólnie rzecz biorąc, należy wykonać następujące czynności:

- Ogranicz dostęp do programu SQL Server tylko do aplikacji i klientów, którzy jej potrzebują.
- Postępuj zgodnie z najlepszymi rozwiązaniami w zakresie zarządzania kontami użytkowników i hasłami.

W poniższych sekcjach przedstawiono sugestie dotyczące przemyślenia tych punktów.

## <a name="secure-connections"></a>Bezpieczne połączenia

Podczas tworzenia maszyny wirtualnej programu SQL Server z obrazem galerii opcja **Łączność programu SQL Server** umożliwia wybór **opcji Local (inside VM),** **Private (within Virtual Network)** lub **Public (Internet).**

![Łączność z programem SQL Server](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Aby uzyskać najlepsze zabezpieczenia, wybierz najbardziej restrykcyjną opcję dla twojego scenariusza. Na przykład jeśli korzystasz z aplikacji, która uzyskuje dostęp do programu SQL Server na tej samej maszynie wirtualnej, **local** jest najbezpieczniejszym wyborem. Jeśli używasz aplikacji platformy Azure, która wymaga dostępu do programu SQL Server, **private** zabezpiecza komunikację z programem SQL Server tylko w ramach określonej [sieci wirtualnej platformy Azure.](../../../virtual-network/virtual-networks-overview.md) Jeśli potrzebujesz **publicznego** dostępu (internet) do maszyny Wirtualnej programu SQL Server, upewnij się, że należy postępować zgodnie z innymi najlepszymi rozwiązaniami w tym temacie, aby zmniejszyć obszar obszaru ataku.

Wybrane opcje w portalu używają reguł zabezpieczeń przychodzących w [sieciowej grupie zabezpieczeń](../../../virtual-network/security-overview.md) maszyny Wirtualnej(NSG), aby zezwolić lub odmówić ruchu sieciowego na maszynie wirtualnej. Można zmodyfikować lub utworzyć nowe przychodzące reguły sieciowej sieciowej sieciowej, aby zezwolić na ruch do portu programu SQL Server (domyślnie 1433). Można również określić określone adresy IP, które mogą komunikować się za jego podaniem za jego korzystać za ok.

![Reguły sieciowych grup zabezpieczeń](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Oprócz reguł sieciowej grup sieciowych w celu ograniczenia ruchu sieciowego można również użyć Zapory systemu Windows na maszynie wirtualnej.

Jeśli używasz punktów końcowych z klasycznym modelem wdrażania, usuń wszystkie punkty końcowe na maszynie wirtualnej, jeśli ich nie używasz. Aby uzyskać instrukcje dotyczące używania list ACL z punktami końcowymi, zobacz [Zarządzanie listą ACL w punkcie końcowym](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Nie jest to konieczne dla maszyn wirtualnych korzystających z Menedżera zasobów.

Na koniec należy rozważyć włączenie połączeń szyfrowanych dla wystąpienia aparatu bazy danych programu SQL Server na maszynie wirtualnej platformy Azure. Konfigurowanie wystąpienia serwera SQL przy obliczu podpisanego certyfikatu. Aby uzyskać więcej informacji, zobacz [Włączanie połączeń szyfrowanych z aparatem bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) i [składnią ciągu połączenia](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="encryption"></a>Szyfrowanie

Dyski zarządzane oferują szyfrowanie po stronie serwera i szyfrowanie dysków platformy Azure. [Szyfrowanie po stronie serwera](/azure/virtual-machines/windows/disk-encryption) zapewnia szyfrowanie w spoczynku i chroni dane, aby spełnić zobowiązania dotyczące bezpieczeństwa i zgodności organizacji. [Usługa Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) używa technologii Bitlocker lub DM-Crypt i integruje się z usługą Azure Key Vault w celu szyfrowania dysków systemu operacyjnego i danych. 

## <a name="use-a-non-default-port"></a>Używanie portu nieobjętego domyślnym

Domyślnie program SQL Server nasłuchuje na dobrze znanym porcie 1433. Aby zwiększyć bezpieczeństwo, należy skonfigurować program SQL Server do nasłuchiwać na porcie nienawidowym, takim jak 1401. Jeśli aprowizujesz obraz galerii programu SQL Server w portalu Azure portal, możesz określić ten port w bloku **ustawień programu SQL Server.**

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Aby skonfigurować to po zainicjowaniu obsługi administracyjnej, dostępne są dwie opcje:

- W przypadku maszyn wirtualnych Menedżera zasobów można wybrać pozycję **Zabezpieczenia** z [zasobu maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource). Umożliwia to zmianę portu.

  ![Zmiana portu TCP w portalu](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- W przypadku klasycznych maszyn wirtualnych lub maszyn wirtualnych programu SQL Server, które nie zostały zainicjowane za pomocą portalu, można ręcznie skonfigurować port, łącząc się zdalnie z maszyną wirtualną. Aby zapoznać się z instrukcjami konfiguracji, zobacz [Konfigurowanie serwera do nasłuchiwać na określonym porcie TCP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Jeśli używasz tej techniki ręcznej, należy również dodać regułę Zapory systemu Windows, aby zezwolić na ruch przychodzący na tym porcie TCP.

> [!IMPORTANT]
> Określenie portu nieniewiążącego jest dobrym pomysłem, jeśli port programu SQL Server jest otwarty dla publicznych połączeń internetowych.

Gdy sql server nasłuchuje na porcie nie-domyślnym, należy określić port podczas nawiązywania połączenia. Rozważmy na przykład scenariusz, w którym adres IP serwera jest 13.55.255.255 i SQL Server nasłuchuje na porcie 1401. Aby połączyć się z `13.55.255.255,1401` programem SQL Server, należy określić w ciągu połączenia.

## <a name="manage-accounts"></a>Zarządzanie kontami

Nie chcesz, aby osoby atakujące łatwo odgadły nazwy kont lub hasła. Skorzystaj z następujących wskazówek, aby pomóc:

- Utwórz unikatowe konto administratora lokalnego, które nie nosi nazwy **Administrator**.

- Używaj złożonych silnych haseł dla wszystkich swoich kont. Aby uzyskać więcej informacji na temat tworzenia silnego hasła, zobacz Tworzenie artykułu [o silnym haśle.](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password)

- Domyślnie platforma Azure wybiera uwierzytelnianie systemu Windows podczas konfigurowania maszyny wirtualnej programu SQL Server. W związku z tym logowanie **SA** jest wyłączone, a hasło jest przypisywane przez instalatora. Zaleca się, aby nie używać ani włączać logowania do **sa.** Jeśli musisz mieć login SQL, użyj jednej z następujących strategii:

  - Utwórz konto SQL o unikatowej nazwie, która ma członkostwo **sysadmin.** Można to zrobić z portalu, włączając **uwierzytelnianie SQL** podczas inicjowania obsługi administracyjnej.

    > [!TIP] 
    > Jeśli uwierzytelnianie SQL nie zostanie włączona podczas inicjowania obsługi administracyjnej, należy ręcznie zmienić tryb uwierzytelniania na **SQL Server i Tryb uwierzytelniania systemu Windows**. Aby uzyskać więcej informacji, zobacz [Zmienianie trybu uwierzytelniania serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Jeśli musisz użyć logowania **SA,** włącz logowanie po inicjowaniu obsługi administracyjnej i przypisz nowe silne hasło.

## <a name="additional-best-practices"></a>Dodatkowe najlepsze rozwiązania

Oprócz rozwiązań opisanych w tym temacie zaleca się przejrzenie i wdrożenie najlepszych rozwiązań w zakresie zabezpieczeń zarówno z tradycyjnych lokalnych rozwiązań w zakresie zabezpieczeń, jak i najlepszych rozwiązań dotyczących zabezpieczeń maszyn wirtualnych. 

Aby uzyskać więcej informacji na temat lokalnych praktyk w zakresie zabezpieczeń, zobacz [Zagadnienia dotyczące zabezpieczeń dotyczące instalacji programu SQL Server](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) i [Centrum zabezpieczeń](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Aby uzyskać więcej informacji na temat zabezpieczeń maszyn [wirtualnych, zobacz omówienie zabezpieczeń maszyn wirtualnych](/azure/security/fundamentals/virtual-machines-overview).


## <a name="next-steps"></a>Następne kroki

Jeśli jesteś również zainteresowany najlepszymi rozwiązaniami w zakresie wydajności, zobacz [Najważniejsze wskazówki dotyczące wydajności programu SQL Server w platformie Azure Virtual Machines.](virtual-machines-windows-sql-performance.md)

Aby zapoznać się z innymi tematami związanymi z uruchamianiem programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [omówienie programu SQL Server na maszynach wirtualnych platformy Azure.](virtual-machines-windows-sql-server-iaas-overview.md) Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

