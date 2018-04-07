---
title: Zaloguj się do klasycznej maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Użyj portalu Azure do logowania się do systemu Windows maszyny wirtualnej utworzonej z klasycznym modelu wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: bcbebc216dbd63acfb33cf72ba774d088149a3a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Logowanie do maszyny wirtualnej systemu Windows przy użyciu witryny Azure Portal
W portalu Azure, użyj **Connect** przycisk, aby rozpocząć sesję pulpitu zdalnego i zaloguj się do maszyny Wirtualnej systemu Windows.

Czy chcesz połączyć z maszyną wirtualną systemu Linux Zobacz [jak zalogować się do maszyny wirtualnej z systemem Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby uzyskać informacje o tym, jak zalogować się do maszyny Wirtualnej przy użyciu modelu Resource Manager, zobacz [tutaj](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną
1. Zaloguj się do Portalu Azure.
2. Kliknij maszynę wirtualną, którą chcesz uzyskać dostęp. Nazwa jest wyświetlana w **wszystkie zasoby** okienka.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

3. Kliknij przycisk **Connect** na pasku poleceń nad pulpitu nawigacyjnego maszyny wirtualnej.

    ![Połączenia dla maszyny wirtualnej](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>Logowanie do maszyny wirtualnej
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Kolejne kroki
* Jeśli **Connect** przycisk jest nieaktywny lub występują inne problemy z połączeniem pulpitu zdalnego, spróbuj zresetować konfigurację. Kliknij przycisk **Zresetuj dostęp zdalny** z poziomu pulpitu nawigacyjnego maszyny wirtualnej.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Problemy z hasłem spróbuj zresetować urządzenie. Kliknij przycisk **resetowania hasła** wzdłuż lewej krawędzi wirtualnej maszyny pulpitu nawigacyjnego, w obszarze **pomocy technicznej i rozwiązywania problemów**.

    ![Zresetuj hasło](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Te porady nie działa lub nie są potrzebne, zobacz [połączeń Rozwiązywanie problemów z pulpitu zdalnego do systemu Windows Azure maszyny wirtualnej](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). W tym artykule przedstawiono sposób diagnozowania i rozwiązywania typowych problemów.
