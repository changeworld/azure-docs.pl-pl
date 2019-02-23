---
title: Włącz kopię zapasową maszyny Wirtualnej platformy Azure podczas tworzenia
description: Zobacz kroki, aby włączyć kopie zapasowe maszyn wirtualnych platformy Azure w trakcie procesu tworzenia.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 0cb1d925d4f9c67439b3c96a85088102c7d0e05c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737199"
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Włącz wykonywanie kopii zapasowej podczas tworzenia maszyny wirtualnej platformy Azure 

Usługa Azure Backup udostępnia interfejs do tworzenia i konfigurowania kopii zapasowych w chmurze. Chroń swoje dane, tworząc kopie zapasowe, nazywane punktami odzyskiwania, w regularnych odstępach czasu. Usługa Azure Backup tworzy punkty odzyskiwania, które można przechowywać w geograficznie nadmiarowych magazynach odzyskiwania. Ten artykuł szczegółowo opisuje sposób włączania kopii zapasowej podczas tworzenia maszyny wirtualnej (VM) w witrynie Azure portal.  

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Jeśli nie jesteś już w zalogowano się do swojego konta, zaloguj się do [witryny Azure portal](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Utwórz maszynę wirtualną z kopii zapasowej skonfigurowane 

1. W lewym górnym rogu witryny Azure portal kliknij **New**. 

2. Wybierz **obliczenia**, a następnie wybierz obraz maszyny wirtualnej.   

3. Wprowadź informacje o maszynie wirtualnej. Nazwę użytkownika i hasło podane jest używana do logowania do maszyny wirtualnej. Po zakończeniu kliknij przycisk **OK**. 

4. Wybierz rozmiar maszyny wirtualnej.  

5. W obszarze **Ustawienia > kopii zapasowej**, kliknij przycisk **włączone** Aby wyświetlić ustawienia konfiguracji kopii zapasowej. Możesz zaakceptować wartości domyślne i kliknij **OK** na stronie Ustawienia, aby przejść do strony podsumowania, aby utworzyć maszynę Wirtualną. Jeśli chcesz zmienić wartości, wykonaj kolejne kroki.  

6. Utwórz lub wybierz magazyn usługi Recovery Services, który przechowuje kopie zapasowe maszyny wirtualnej. W przypadku tworzenia magazynu usługi recovery services można wybrać grupę zasobów magazynu.  

    ![Konfiguracja kopii zapasowej na maszynie wirtualnej, Utwórz stronę](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Grupa zasobów dla magazynu usługi Recovery Services może być inna niż grupa zasobów dla maszyny wirtualnej.  
    > 
    > 

7. Domyślnie zasad tworzenia kopii zapasowej jest zaznaczone, umożliwia szybkie chronienie maszyny Wirtualnej. Zasady tworzenia kopii zapasowych określa, jak często migawek kopii zapasowych oraz jak długo należy zachować te kopie zapasowe. Możesz zaakceptować domyślne zasady lub można utworzyć lub wybrać inne zasady kopii zapasowych. Aby edytować zasady kopii zapasowych, wybierz **zasad tworzenia kopii zapasowej** i zmień wartości zasad.  

8. Gdy jesteś zadowolony z wartości konfiguracji kopii zapasowej, na stronie ustawień, kliknij przycisk **OK**.  

9. Na stronie podsumowania, kliknij przycisk po upływie weryfikacji **Utwórz** do utworzenia maszyny wirtualnej, który używa skonfigurowanych ustawień kopii zapasowej. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>Zainicjuj tworzenie kopii zapasowej po utworzeniu maszyny Wirtualnej 

Chociaż zasady tworzenia kopii zapasowych został utworzony, jest dobrym rozwiązaniem, aby utworzyć początkową kopię zapasową. Aby wyświetlić szczegóły kopii zapasowej dla maszynę wirtualną po zakończeniu szablonu tworzenia maszyny Wirtualnej, z **operacji** w menu po lewej stronie kliknij pozycję **kopii zapasowej**. Umożliwia to wyzwalanie tworzenia kopii zapasowej na żądanie, przywrócić pełną maszynę Wirtualną lub wszystkie dyski, przywrócić pliki z kopii zapasowej maszyny Wirtualnej lub zmienić zasady tworzenia kopii zapasowej skojarzonego z maszyną wirtualną.  

## <a name="using-a-resource-manager-template-to-deploy-a-protected-vm"></a>Aby wdrożyć chronioną maszyną Wirtualną przy użyciu szablonu usługi Resource Manager

Poprzednie kroki wyjaśniają jak używać witryny Azure portal do utworzenia maszyny wirtualnej oraz chronienia ich w magazynie usługi Recovery Services. Jeśli chcesz szybko wdrożyć co najmniej jednej maszyny wirtualnej i chronić je w magazynie usługi Recovery Services, zobacz szablonu, [wdrażanie maszyny Wirtualnej z systemem Windows i włącz wykonywanie kopii zapasowej](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Często zadawane pytania 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Obrazy maszyn wirtualnych, które Włącz kopię zapasową w czasie tworzenia maszyny Wirtualnej? 

Poniższa lista obrazów podstawowych opublikowane przez firmę Microsoft są obsługiwane w przypadku włączania kopii zapasowych podczas tworzenia maszyny Wirtualnej. Dla innych maszyn wirtualnych aby umożliwić kopii zapasowej po utworzeniu maszyny Wirtualnej. Dowiedz się więcej [Włącz kopię zapasową, po utworzeniu maszyny Wirtualnej](quick-backup-vm-portal.md) 

- **Windows** — centrum danych 2016 serwera systemu Windows, podstawowe centrum danych systemu Windows Server 2016, Windows Server 2012 DataCenter, Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 z dodatkiem SP1 
- **Ubuntu** — Ubuntu Server 1710, Ubuntu Server 1704, 1604(LTS) serwera UUbuntu 1404(LTS) serwer Ubuntu 
- **Red Hat** -RHEL 6.7, 6.8 6,9, 7.2, 7.3, wersji 7.4 
- **SUSE** — SUSE Linux Enterprise Server 11 z dodatkiem SP4, 12 z dodatkiem SP2, 12 z dodatkiem SP3 
- **Debian** — Debian 8, Debian 9 
- **CentOS** -CentOS 6,9, CentOS 7.3 
- **Oracle Linux** — Oracle Linux 6.7, 6.8, 6,9, 7.2, 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>Jest wliczany w koszt maszyny Wirtualnej koszt kopii zapasowej? 

Nie ma kopii zapasowej koszty są oddzielne lub znacznie, od kosztów maszyn wirtualnych. Aby uzyskać więcej informacji o cenach kopii zapasowych, zobacz [cennikiem kopii zapasowych lokacji](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Jakie uprawnienia są wymagane do włączenia kopii zapasowej na maszynie Wirtualnej? 

Jeśli Współautor maszyny wirtualnej, aby umożliwić kopii zapasowej na maszynie Wirtualnej. Jeśli używasz roli niestandardowej należy następujących uprawnień, aby pomyślnie umożliwić kopii zapasowej na maszynie Wirtualnej. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Jeśli maszyn wirtualnych i magazynu usługi recovery services różnych grupach zasobów, upewnij się, że masz uprawnienia do zapisu w grupie zasobów magazynu usług odzyskiwania.  

## <a name="next-steps"></a>Kolejne kroki 

Teraz, po zabezpieczeniu maszyny Wirtualnej, zobacz następujące artykuły, aby dowiedzieć się więcej o zadań zarządzania maszyny Wirtualnej i przywracanie maszyn wirtualnych. 

- [Monitorowanie maszyn wirtualnych i zarządzanie nimi](backup-azure-manage-vms.md) 
- [Przywracanie maszyn wirtualnych](backup-azure-arm-restore-vms.md) 
