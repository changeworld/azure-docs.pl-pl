---
title: Rozwiązywanie problemów z błędami rozruchu na maszynach wirtualnych systemu Azure Linux | Dokumenty firmy Microsoft
description: Ten artykuł zawiera łącza do artykułów w celu rozwiązywania problemów z błędami rozruchu na maszynach wirtualnych systemu Azure Linux.
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74408744"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Rozwiązywanie problemów z błędami rozruchu maszyn wirtualnych systemu Azure Linux

W tym artykule wymieniono typowe błędy rozruchu, które mogą pojawić się po uruchomieniu maszyny wirtualnej systemu Linux (VM) na platformie Microsoft Azure. Aby uzyskać więcej informacji na temat błędów, zobacz artykuły w sekcji **Błędy rozruchowe i rozwiązania.**

## <a name="boot-errors-and-solutions"></a>Błędy rozruchu i rozwiązania

* [Ratunek grub](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Następne kroki

- [Konsola szeregowa maszyny Wirtualnej](serial-console-linux.md)

Rozwiązywanie problemów z maszyną wirtualną systemu Linux, dołączając dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu platformy Azure:

- [Naprawa maszyny wirtualnej platformy Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Wymiana dysku — można to zautomatyzować za pomocą:
- [Skrypty odzyskiwania powłoki zasilania](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [skrypty odzyskiwania bash](https://github.com/sribs/azure-support-scripts)

- [Cli](troubleshoot-recovery-disks-linux.md)
- [Portal Azure](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Wideo wymiany dysku:

Jeśli nie masz dostępu do GRUB obejrzeć [ten](https://youtu.be/m5t0GZ5oGAc) film i zobaczyć, jak można łatwo zautomatyzować procedurę wymiany dysku, aby odzyskać maszynę wirtualną

## <a name="unofficial-solution"></a>Nieoficjalne rozwiązanie

Można również podjąć próbę odzyskania maszyny wirtualnej za pomocą nieobsługiconego skryptu BETA [ALAR](https://github.com/malachma/azure-auto-recover)