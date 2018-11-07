---
title: Dodawanie węzłów dużego ruchu do klastra pakietu HPC Pack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozszerzyć klastra pakietu HPC Pack w na żądanie platformy Azure poprzez dodanie wystąpień roli procesu roboczego w usłudze w chmurze
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 24b79a8a-24ad-4002-ae76-75abc9b28c83
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: 7d42c026975a18c7574e4bc64ec28ab3ed0082bc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248456"
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Dodawanie węzłów na żądanie "serii" do klastra pakietu HPC Pack na platformie Azure
Jeśli skonfigurowano [pakietu Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) klastra na platformie Azure, możesz zechcieć sposobem na szybkie skalowanie pojemności klastra w górę lub w dół, bez potrzeby utrzymywania zestaw węzła obliczeniowego wstępnie skonfigurowanych maszyn wirtualnych. W tym artykule przedstawiono sposób dodawania węzłów na żądanie "serii" (wystąpienia roli procesu roboczego z systemem w usłudze w chmurze) jako zasoby obliczeniowe z węzłem głównym na platformie Azure. 

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

![Węzły serii][burst]

Kroki opisane w tym artykule pomóc szybko dodać węzły na platformie Azure opartych na chmurze pakiet HPC Pack węzła głównego maszyn wirtualnych dla testowej lub weryfikacji koncepcji wdrożenia. Ogólne kroki są takie same jak kroki "przejścia na platformę Azure" do dodania obliczeniowej przygotowanie wydajności niezbędnej do klastra pakietu HPC Pack lokalnie w chmurze. Aby zapoznać się z samouczkiem, zobacz [skonfiguruj hybrydowy klaster obliczeniowy za pomocą pakietu Microsoft HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Aby uzyskać szczegółowe wskazówki i uwagi dotyczące wdrożeń produkcyjnych zobacz [przejścia na platformę Azure za pomocą pakietu Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

## <a name="prerequisites"></a>Wymagania wstępne
* **Węzeł główny HPC Pack wdrożony w Maszynie wirtualnej platformy Azure** — możesz użyć węzła głównego autonomicznej maszyny Wirtualnej lub taki, który jest częścią większego klastra. Aby utworzyć autonomiczny węzła głównego, zobacz [wdrażanie węźle głównym HPC Pack na Maszynie wirtualnej platformy Azure](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Automatyczne opcjach wdrażania klastra pakietu HPC Pack, zobacz temat [funkcje umożliwiające tworzenie i zarządzanie nimi Windows HPC klastra na platformie Azure przy użyciu pakietu Microsoft HPC Pack](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
  > [!TIP]
  > Jeśli używasz [skryptem wdrażania IaaS pakietu HPC Pack](hpcpack-cluster-powershell-script.md) do utworzenia klastra na platformie Azure, może zawierać węzłów dużego ruchu platformy Azure w danym wdrożeniu automatyczne. Zobacz przykłady w tym artykule.
  > 
  > 
* **Subskrypcja platformy Azure** — Aby dodać węzły platformy Azure, możesz wybrać tej samej subskrypcji, które są używane do wdrażania węzła głównego maszyny Wirtualnej lub innej subskrypcji (lub subskrypcji).
* **Limit przydziału rdzeni** — może być konieczne zwiększenie limitu przydziału rdzeni, zwłaszcza, jeśli zdecydujesz się wdrożyć kilka węzłów platformy Azure o rozmiarze wielordzeniowych. Aby zwiększyć limit przydziału, [Otwórz żądanie obsługi klienta online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) bez dodatkowych opłat.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Krok 1: Tworzenie usługi w chmurze i konto magazynu dla węzłów platformy Azure
Użyj witryny Azure portal lub analogicznych narzędzi do konfigurowania następujących zasobów, które są wymagane do wdrożenia węzłów platformy Azure:

* Nową usługę w chmurze platformy Azure (wersja klasyczna)
* Nowe konto usługi Azure storage (wersja klasyczna)

> [!NOTE]
> Nie używaj ponownie plików istniejącej usługi w chmurze w Twojej subskrypcji. 
> 
> 

**Zagadnienia do rozważenia**

* Konfigurowanie usługi w chmurze osobne dla każdego szablonu węzła platformy Azure, który ma zostać utworzone. Jednak można użyć tego samego konta magazynu dla wielu szablonów węzła.
* Firma Microsoft zaleca, znajdź usługę w chmurze i konto magazynu do wdrożenia w tym samym regionie platformy Azure.

## <a name="step-2-configure-an-azure-management-certificate"></a>Krok 2: Skonfiguruj certyfikat zarządzania platformy Azure
Aby dodać węzły platformy Azure jako zasoby obliczeniowe, potrzebny jest certyfikat zarządzania, na węzeł główny i przekaż, odpowiedni certyfikat z subskrypcją platformy Azure używanej we wdrożeniu.

W tym scenariuszu można wybrać **domyślny certyfikat zarządzania platformy Azure HPC** pakietu HPC Pack instaluje i konfiguruje się automatycznie w węźle głównym. Ten certyfikat jest przydatna przy testowaniu celów i weryfikacji koncepcji wdrożenia. Aby użyć tego certyfikatu, Przekaż plik 2012\Bin\hpccert.cer C:\Program Files\Microsoft HPC Pack z węzła głównego maszyny Wirtualnej do subskrypcji. Aby przekazać certyfikat w [witryny Azure portal](https://portal.azure.com):

1. Kliknij przycisk **subskrypcje** > *your_subscription_name*.

2. Kliknij przycisk **certyfikaty zarządzania** > **przekazywanie**.

Aby uzyskać dodatkowe opcje umożliwiające skonfigurowanie certyfikatu zarządzania, zobacz [scenariusze umożliwiające konfigurowanie certyfikat zarządzania platformy Azure dla platformy Azure — seria wdrożeń](https://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Krok 3: Wdrażanie węzły na platformie Azure w klastrze
Kroki, aby dodać i uruchomienie węzłów platformy Azure, w tym scenariuszu są generalnie takie same jak kroki z węzłem w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz następujące sekcje w [kroki, aby wdrożyć węzły platformy Azure za pomocą pakietu Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Tworzenie szablonu węzła platformy Azure
* Dodaj węzły na platformie Azure do klastrów Windows HPC
* Uruchomienie węzłów (zainicjować obsługi administracyjnej) platformy Azure

Po dodaniu i uruchomienie węzłów są gotowe do użycia do obsługi zadań klastra.

Jeśli napotkasz problemy podczas wdrażania węzły na platformie Azure, zobacz [Rozwiązywanie problemów z wdrożeniami platformy Azure węzłów za pomocą pakietu Microsoft HPC Pack](https://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Kolejne kroki
* Aby użyć rozmiaru wystąpienia intensywnie węzłów dużego ruchu, zobacz uwagi w [o wysokiej wydajności obliczeń rozmiarów maszyn wirtualnych](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Jeśli chcesz automatycznie zwiększać lub zmniejszać zasoby zależnie od obciążenia klastra obliczeniowe platformy Azure, zobacz [automatyczne powiększanie i zmniejszanie zasobów obliczeniowych platformy Azure w klastrze pakietu HPC Pack](hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
