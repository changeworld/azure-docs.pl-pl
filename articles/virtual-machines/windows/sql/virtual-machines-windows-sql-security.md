---
title: Zagadnienia dotyczące zabezpieczeń dla programu SQL Server na platformie Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera ogólne wskazówki dotyczące zabezpieczania programu SQL Server uruchomionego w maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: rothja
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
ms.author: jroth
ms.openlocfilehash: b91638b6b76675711150323bee4aa9ad9d9a73d1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Zagadnienia dotyczące zabezpieczeń programu SQL Server na maszynach wirtualnych platformy Azure

Ten temat zawiera ogólne wskazówki dotyczące zabezpieczeń, które pomaga ustalić bezpieczny dostęp do wystąpień programu SQL Server w maszynie wirtualnej platformy Azure (VM).

Azure jest zgodna z kilku przepisów branżowych i standardy, które umożliwiają utworzenie rozwiązania do zgodnych z programem SQL Server działa na maszynie wirtualnej. Aby uzyskać informacje o zgodności z przepisami z platformy Azure, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Kontrola dostępu do maszyny Wirtualnej SQL

Podczas tworzenia maszyny wirtualnej programu SQL Server, należy wziąć pod uwagę sposób dokładnie kontrolować, kto ma dostęp do komputera i SQL Server. Ogólnie rzecz biorąc należy wykonać następujące czynności:

- Ograniczanie dostępu do programu SQL Server tylko do aplikacji i klientów, którzy go potrzebują.
- Należy stosować najlepsze rozwiązania dotyczące zarządzania konta użytkownika i hasła.

Poniższe sekcje zawierają sugestie dotyczące planowania przy użyciu tych punktów.

## <a name="secure-connections"></a>Bezpieczne połączenia

Podczas tworzenia maszyny wirtualnej programu SQL Server z obrazem galerii **łączności z serwerem SQL** opcja umożliwia wybór **lokalnego (wewnątrz maszyny Wirtualnej)**, **prywatne (wewnątrz sieci wirtualnej)**, lub **publiczne (Internet)**.

![Łączność serwera SQL](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Najlepiej wybierz opcję najbardziej restrykcyjne dla danego scenariusza. Na przykład jeśli używasz aplikacji, który uzyskuje dostęp do programu SQL Server na tej samej maszyny Wirtualnej, następnie **lokalnego** jest najbezpieczniejszy wybór. Jeśli używasz aplikacji Azure, która wymaga dostępu do programu SQL Server, następnie **prywatnej** zabezpiecza komunikację z programem SQL Server tylko w obrębie określonego [sieci wirtualnej Azure](../../../virtual-network/virtual-networks-overview.md). Jeśli potrzebujesz **publicznego** (internet) dostęp do maszyny Wirtualnej programu SQL Server, a następnie upewnij się, że należy stosować inne najlepsze rozwiązania w tym temacie w celu ograniczenia obszaru powierzchni ataku.

Wybrane opcje w portalu Użyj reguł zabezpieczeń dla ruchu przychodzącego na maszynach wirtualnych [sieciowej grupy zabezpieczeń](../../../virtual-network/virtual-networks-nsg.md) (NSG), aby zezwolić lub odmówić ruch sieciowy do maszyny wirtualnej. Można zmodyfikować lub utworzyć nowe reguły NSG dla ruchu przychodzącego zezwalająca na ruch do portu programu SQL Server (domyślnie 1433). Można również określić określonych adresów IP, które mogą komunikować się za pośrednictwem tego portu.

![Reguły sieciowych grup zabezpieczeń](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Oprócz reguły NSG, aby ograniczyć ruch sieciowy umożliwia także zapory systemu Windows na maszynie wirtualnej.

Jeśli korzystasz z punktów końcowych z klasycznym modelu wdrażania, Usuń wszystkie punkty końcowe na maszynie wirtualnej, jeśli nie należy używać. Aby uzyskać instrukcje dotyczące przy użyciu list kontroli dostępu z punktami końcowymi, zobacz [Zarządzanie listy ACL punktu końcowego](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint). To nie jest niezbędna dla maszyn wirtualnych, które używają Menedżera zasobów.

Zaleca się włączenie połączeń szyfrowanych wystąpienia aparatu bazy danych programu SQL Server w sieci maszyny wirtualnej platformy Azure. Skonfiguruj wystąpienie programu SQL server przy użyciu podpisanego certyfikatu. Aby uzyskać więcej informacji, zobacz [włączyć szyfrowane połączeń z aparatem bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) i [składnia ciągu połączenia](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Korzystanie z portu inny niż domyślny

Domyślnie program SQL Server nasłuchuje na dobrze znanego portu 1433. Aby zwiększyć bezpieczeństwo należy skonfigurować serwer SQL do nasłuchiwania na porcie innych niż domyślne, takie jak 1401. Jeśli dostarczasz obraz galerii programu SQL Server w portalu Azure, można określić tego portu w **ustawienia programu SQL Server** bloku.

Aby skonfigurować to po zainicjowaniu obsługi administracyjnej, masz dwie opcje:

- Dla maszyn wirtualnych Menedżera zasobów, można wybrać **konfigurację programu SQL Server** z bloku omówienie maszyny Wirtualnej. Zapewnia to opcję, aby zmienić port.

  ![Zmień TCP port w portalu](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Klasycznych maszyn wirtualnych lub maszyn wirtualnych serwera SQL, które nie zostały udostępnione przy użyciu portalu można ręcznie skonfigurować port, nawiązując połączenie zdalne z maszyną wirtualną. Kroki konfiguracji, zobacz [Konfigurowanie serwera do nasłuchiwania na konkretnym porcie TCP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Jeśli używasz tej techniki ręczne, należy dodać reguły zapory systemu Windows w celu zezwalania na ruch przychodzący na tym porcie TCP.

> [!IMPORTANT]
> Określenie z systemem innym niż domyślny port jest dobrym rozwiązaniem, jeśli port programu SQL Server jest otwarty do publicznego połączenia z Internetem.

Gdy serwer SQL nasłuchuje na porcie innych niż domyślne, należy określić numer portu podczas nawiązywania połączenia. Na przykład Rozważmy scenariusz, w którym 13.55.255.255 jest adres IP serwera i programu SQL Server nasłuchuje na porcie 1401. Aby połączyć się z programem SQL Server, należy określić `13.55.255.255,1401` w parametrach połączenia.

## <a name="manage-accounts"></a>Zarządzanie kontami

Osoby atakujące łatwo odgadnąć nazwy kont lub haseł, które nie mają. Poniższe wskazówki umożliwiają pomocy:

- Utwórz konto administratora lokalnego unikatowy, który nie ma nazwy **administratora**.

- Użyj złożonych silnych haseł dla wszystkich kont. Aby uzyskać więcej informacji na temat tworzenia silnych haseł, zobacz [Utwórz silne hasło](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) artykułu.

- Domyślnie program Azure wybiera uwierzytelniania systemu Windows podczas instalacji maszyny wirtualnej programu SQL Server. W związku z tym **SA** logowania jest wyłączona, a hasło jest przypisywany przez Instalatora. Zalecamy, aby **SA** logowania nie należy używać ani włączony. Jeśli identyfikator logowania SQL, użyj jednej z następujących strategii:

  - Utwórz konto SQL o unikatowej nazwie, która ma **sysadmin** członkostwa. Można to zrobić w portalu, należy włączyć **uwierzytelniania SQL** podczas inicjowania obsługi.

    > [!TIP] 
    > Jeśli nie zostanie włączone uwierzytelnianie SQL podczas inicjowania obsługi, należy ręcznie zmienić tryb uwierzytelniania **programu SQL Server i tryb uwierzytelniania systemu Windows**. Aby uzyskać więcej informacji, zobacz [Zmień tryb uwierzytelniania serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Jeśli musisz użyć **SA** logowania, Włącz logowanie po zainicjowaniu obsługi administracyjnej i przypisz nowe silne hasło.

## <a name="follow-on-premises-best-practices"></a>Należy stosować najlepsze rozwiązania lokalnego

Oprócz rozwiązania opisane w tym temacie zaleca się, możesz sprawdzić i wdrożenia rozwiązania w zakresie zabezpieczeń tradycyjnych, lokalnie, jeśli to możliwe. Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące zabezpieczeń dla instalacji serwera SQL](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Następne kroki

Jeśli interesuje Cię również najlepsze rozwiązania dotyczące wydajności, zobacz [wydajności najlepsze rozwiązania dotyczące programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Do innych tematów związanych z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

