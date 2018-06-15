---
title: Dodaj serii węzły w klastrze HPC Pack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i rozwiń klaster HPC Pack w Azure na żądanie przez dodanie wystąpień roli procesów roboczych uruchomionych w usłudze chmury
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
ms.openlocfilehash: eee9183321f21676271c8a9c7e023c80c4daf554
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915111"
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Dodaj węzły na żądanie "serii" do klastra HPC Pack na platformie Azure
Po skonfigurowaniu [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) klastra na platformie Azure, może być sposobem szybkie skalowanie pojemności klastra, bez zachowania zestaw węzeł obliczeniowy wstępnie skonfigurowanych maszyn wirtualnych. W tym artykule przedstawiono sposób dodawania węzłów na żądanie "serii" (uruchomionych w usłudze chmury wystąpień roli proces roboczy) jako zasoby obliczeniowe z węzłem głównym na platformie Azure. 

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

![Węzły serii][burst]

Kroki opisane w tym artykule pomóc szybko dodać węzły platformy Azure opartej na chmurze HPC Pack węzła głównego maszyny Wirtualnej dla testu lub Weryfikacja koncepcji wdrożenia. Ogólne kroki są takie same jak kroki, aby "serii Azure" Aby dodać chmury obliczeń pojemności do lokalnego klastra HPC Pack. Samouczek, zobacz [skonfiguruj hybrydowych klastra obliczeniowego z pakietem Microsoft HPC](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Szczegółowe wskazówki i zagadnienia dotyczące wdrożeń produkcyjnych, zobacz [serii na platformie Azure z pakietem Microsoft HPC](https://technet.microsoft.com/library/gg481749.aspx).

## <a name="prerequisites"></a>Wymagania wstępne
* **Węzłem głównym HPC Pack wdrożony w maszynie Wirtualnej platformy Azure** — możesz użyć węzła głównego autonomicznej maszyny Wirtualnej lub taki, który jest częścią większego klastra. Aby utworzyć autonomiczny węzła głównego, zobacz [wdrażanie węźle głównym HPC Pack w maszynie Wirtualnej platformy Azure](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Automatyczne opcji wdrażania klastra HPC Pack, zobacz [klastra opcje tworzenia i zarządzania nimi HPC systemu Windows na platformie Azure z pakietem Microsoft HPC](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
  > [!TIP]
  > Jeśli używasz [skrypt wdrożenia HPC Pack IaaS](hpcpack-cluster-powershell-script.md) do utworzenia klastra na platformie Azure, można dołączyć węzłów Azure serii automatycznego wdrożenia. Przykłady w tym artykule.
  > 
  > 
* **Subskrypcja platformy Azure** — Aby dodać węzły platformy Azure, możesz wybrać tej samej subskrypcji, które są używane do wdrażania węzła głównego maszyny Wirtualnej, lub innej subskrypcji (lub subskrypcji).
* **Limit przydziału rdzeni** — należy zwiększyć limit przydziału rdzeni, zwłaszcza, jeśli użytkownik chce wdrożyć kilka węzłów Azure o rozmiarze wielordzeniowych. Aby zwiększyć przydział, [otwarcia żądania pomocy technicznej online klienta](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) bez dodatkowych opłat.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Krok 1: Tworzenie usługi w chmurze i konto magazynu Azure węzłów
Umożliwia skonfigurowanie następujących zasobów, które są wymagane do wdrożenia węzłów Azure w portalu Azure lub analogicznych narzędzi:

* Nową usługę w chmurze Azure (klasyczne)
* Nowe konto magazynu platformy Azure (klasyczne)

> [!NOTE]
> Nie ponownego użycia istniejącej usługi w chmurze w ramach subskrypcji. 
> 
> 

**Zagadnienia do rozważenia**

* Konfigurowanie usługi w chmurze osobne dla każdego szablonu Azure węzła, który chcesz utworzyć. Jednak można użyć tego samego konta magazynu dla wielu szablonów węzła.
* Zaleca się, znajdź usługę w chmurze i konto magazynu do wdrożenia w tym samym regionie Azure.

## <a name="step-2-configure-an-azure-management-certificate"></a>Krok 2: Konfigurowanie certyfikat zarządzania platformy Azure
Aby dodać węzły platformy Azure jako zasoby obliczeniowe, wymagany jest certyfikat zarządzania na węzła głównego i przekazywanie certyfikatu odpowiedniego do subskrypcji platformy Azure używaną na potrzeby wdrożenia.

W tym scenariuszu użytkownik może **domyślne certyfikat zarządzania platformy Azure HPC** HPC Pack instaluje i konfiguruje się automatycznie w węźle głównym. Ten certyfikat jest przydatna przy testowaniu celów i weryfikacja koncepcji wdrożenia. Aby użyć tego certyfikatu, Przekaż plik 2012\Bin\hpccert.cer C:\Program Files\Microsoft HPC Pack z węzłem głównym maszyny Wirtualnej do subskrypcji. Aby przekazać certyfikat w [portalu Azure](https://portal.azure.com):

1. Kliknij przycisk **subskrypcje** > *your_subscription_name*.

2. Kliknij przycisk **certyfikaty zarządzania** > **przekazać**.

Aby uzyskać dodatkowe opcje umożliwiające skonfigurowanie certyfikatu zarządzania, zobacz [scenariuszami, aby skonfigurować certyfikat zarządzania platformy Azure w przypadku wdrożeń serii Azure](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Krok 3: Wdrażanie usługi Azure węzły do klastra
Kroki, aby dodać i uruchomić węzłów Azure w tym scenariuszu zazwyczaj są takie same jak kroki za pomocą lokalnego węzła głównego. Aby uzyskać więcej informacji, zobacz następujące sekcje w [kroki, aby wdrożyć węzły Azure z pakietem Microsoft HPC](https://technet.microsoft.com/library/gg481758.aspx):

* Tworzenie szablonu Azure węzła
* Dodaj węzły platformy Azure do klastra HPC systemu Windows
* Uruchom węzłów (zainicjować obsługi administracyjnej) platformy Azure

Po dodaniu i uruchomić węzły są gotowe do uruchomienia zadań klastra.

W razie wystąpienia problemów podczas wdrażania węzły platformy Azure, zobacz [Rozwiązywanie problemów z wdrożeniami Azure węzłów z pakietem Microsoft HPC](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Kolejne kroki
* Aby skorzystać rozmiar wystąpienia obliczeniowych dla węzłów serii, zobacz uwagi w [wysokiej wydajności obliczeniowe rozmiarów maszyn wirtualnych](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Jeśli chcesz automatycznie Zwiększ lub Zmniejsz Azure zasobów zgodnie z obciążenia klastrów obliczeniowych, zobacz [automatycznie zwiększyć lub zmniejszyć zasoby obliczeniowe systemu Azure w klastrze HPC Pack](hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
