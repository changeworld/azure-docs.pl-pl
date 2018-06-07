---
title: Włącz podczas tworzenia kopii zapasowej maszyny Wirtualnej Azure
description: Zobacz kroki, aby włączyć w trakcie procesu tworzenia kopii zapasowej maszyny wirtualnej platformy Azure.
services: backup, virtual-machines
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 928481f07875286a21f68dae6556f04eb2b6ae5c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606124"
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Włączenia kopii zapasowej podczas tworzenia maszyny wirtualnej platformy Azure 

Usługa Kopia zapasowa Azure udostępnia interfejs, aby utworzyć i skonfigurować tworzenie kopii zapasowych w chmurze. Ochrona danych za wykonywania kopii zapasowych, nazywany punktów odzyskiwania w regularnych odstępach czasu. Usługa Azure Backup tworzy punkty odzyskiwania, które można przechowywać w geograficznie nadmiarowych magazynach odzyskiwania. Ten artykuł zawiera szczegóły dotyczące sposobu włączenia kopii zapasowej podczas tworzenia maszyny wirtualnej (VM) w portalu Azure.  

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Jeśli nie jesteś już w zalogowany do konta, zaloguj się do [portalu Azure](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Utwórz maszynę wirtualną przy użyciu kopii zapasowej skonfigurowane 

1. W lewym górnym rogu portalu Azure kliknij **nowy**. 

2. Wybierz **obliczeniowe**, a następnie wybierz obraz maszyny wirtualnej.   

3. Wprowadź informacje o maszynie wirtualnej. Nazwa użytkownika i hasło podane służy do logowania się na maszynie wirtualnej. Po zakończeniu kliknij przycisk **OK**. 

4. Wybierz rozmiar maszyny wirtualnej.  

5. W obszarze **Ustawienia > kopii zapasowej**, kliknij przycisk **włączone** można wyświetlić ustawienia konfiguracji kopii zapasowej. Można zaakceptować wartości domyślne i kliknij przycisk **OK** na stronie Ustawienia, aby przejść do strony Podsumowanie, aby utworzyć maszynę Wirtualną. Jeśli chcesz zmienić wartości, wykonaj kolejne kroki.  

6. Utwórz lub wybierz magazyn usług odzyskiwania, w którym są przechowywane kopie zapasowe maszyny wirtualnej. W przypadku tworzenia magazynu usług odzyskiwania, można wybrać grupę zasobów magazynu.  

    ![Tworzenie konfiguracji kopii zapasowej na maszynie wirtualnej strony](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Grupy zasobów dla magazynu usług odzyskiwania może różnić się od grupy zasobów dla maszyny wirtualnej.  
    > 
    > 

7. Zasady tworzenia kopii zapasowej jest zaznaczone domyślnie, można szybko zapewnić ochronę maszyny Wirtualnej. Zasady tworzenia kopii zapasowych określa, jak często migawek kopii zapasowych i jak długo, aby zachować te kopie zapasowe. Możesz zaakceptować domyślną zasadę, lub można utworzyć lub wybrać inne zasady tworzenia kopii zapasowej. Aby edytować zasady tworzenia kopii zapasowej, wybierz **zasad tworzenia kopii zapasowej** i zmień wartości zasady.  

8. Po wszystkiego o wartości konfiguracji kopii zapasowej, na stronie Ustawienia, kliknij przycisk **OK**.  

9. Na stronie Podsumowanie, po upływie sprawdzania poprawności, kliknij **Utwórz** do utworzenia maszyny wirtualnej, która używa skonfigurowane ustawienia kopii zapasowej. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>Inicjowanie kopii zapasowej po utworzeniu maszyny Wirtualnej 

Chociaż zasady tworzenia kopii zapasowej został utworzony, jest dobrym rozwiązaniem, aby utworzyć początkową kopię zapasową. Aby wyświetlić szczegóły kopii zapasowej dla wirtualnego komputera po zakończeniu szablonu tworzenia maszyny Wirtualnej, z **operacji** ustawienie w menu po lewej stronie, kliknij przycisk **kopii zapasowej**. Umożliwia to Wyzwól kopię zapasową na żądanie, Przywróć pełną maszyny Wirtualnej lub wszystkie dyski, przywracania plików z kopii zapasowej maszyny Wirtualnej lub zmienić zasady tworzenia kopii zapasowej skojarzonego z maszyną wirtualną.  

## <a name="using-a-resource-manager-template-to-deploy-a-protected-vm"></a>Aby wdrożyć Maszynę wirtualną chronionych przy użyciu szablonu usługi Resource Manager

Poprzednie kroki wyjaśniono, jak korzystać z portalu Azure do utworzenia maszyny wirtualnej i chronić go do magazynu usług odzyskiwania. Jeśli chcesz szybko wdrożyć co najmniej jednej maszyny wirtualnej i chronić je w magazynie usług odzyskiwania, zobacz szablonu, [wdrożyć maszynę Wirtualną systemu Windows i włączenia kopii zapasowej](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Często zadawane pytania 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Obrazów maszyn wirtualnych, które włączenia kopii zapasowej w czasie tworzenia maszyny Wirtualnej? 

Z poniższej listy obrazów core opublikowane przez firmę Microsoft są obsługiwane w przypadku włączenia kopii zapasowej podczas tworzenia maszyny Wirtualnej. Dla innych maszyn wirtualnych można włączyć kopii zapasowej, po utworzeniu maszyny Wirtualnej. Dowiedz się więcej [włączenie wykonywania kopii zapasowych po utworzeniu maszyny Wirtualnej](quick-backup-vm-portal.md) 

- **Windows** -centrum danych programu Windows Server 2016, podstawowe centrum danych systemu Windows Server 2016, Windows Server 2012 DataCenter, Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 z dodatkiem SP1 
- **Ubuntu** -Ubuntu Server 1710, Ubuntu Server 1704, 1604(LTS) serwera UUbuntu 1404(LTS) Ubuntu Server 
- **RedHat** -RHEL 6.7, 6.8, 6,9, 7.2, 7.3, 7.4 
- **SUSE** -SUSE Linux Enterprise Server 11 z dodatkiem SP4, 12 z dodatkiem SP2, 12 z dodatkiem SP3 
- **Debian** -Debian 8, Debian 9 
- **CentOS** -CentOS 6,9, CentOS 7.3 
- **Oracle Linux** — Oracle Linux 6.7, 6.8, 6,9, 7.2, 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>Jest koszt kopii zapasowych objętych koszt maszyny Wirtualnej? 

Nie, koszty kopii zapasowej są oddzielne lub różne od kosztów maszyn wirtualnych. Aby uzyskać więcej informacji o cenach kopii zapasowych, zobacz [cennika kopii zapasowej lokacji](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Uprawnienia, które są wymagane do włączenia kopii zapasowej na maszynie Wirtualnej? 

Jeśli użytkownik jest współautorem maszyny wirtualnej, możesz włączyć kopii zapasowej na maszynie Wirtualnej. Jeśli używasz niestandardowej roli zabezpieczeń, należy następujące uprawnienia do pomyślnie włączenia kopii zapasowej na maszynie Wirtualnej. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Jeśli maszyn wirtualnych i magazynu usług odzyskiwania różnych grup zasobów, upewnij się, że masz uprawnienia do zapisu w grupie zasobów magazynu usług odzyskiwania.  

## <a name="next-steps"></a>Kolejne kroki 

Teraz, ochrony maszyny Wirtualnej, zobacz następujące artykuły, aby dowiedzieć się więcej o zadaniach zarządzania maszyny Wirtualnej i przywracanie maszyn wirtualnych. 

- [Monitorowanie maszyn wirtualnych i zarządzanie nimi](backup-azure-manage-vms.md) 
- [Przywracanie maszyn wirtualnych](backup-azure-arm-restore-vms.md) 
