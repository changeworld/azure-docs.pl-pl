---
title: Łączenie się z maszynami wirtualnymi za pomocą przeglądarki Azure | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z maszynami wirtualnymi za pomocą przeglądarki.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 22d7afa889994068c3f906d0b9adbc1a74bc4839
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642593"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Łączenie się z maszynami wirtualnymi za pomocą przeglądarki 

DevTest Labs integrują się z [usługą Azure bastionu](https://docs.microsoft.com/azure/bastion/), która umożliwia łączenie się z maszynami wirtualnymi za pomocą przeglądarki. Aby uzyskać informacje na temat włączania tej funkcji w usłudze DevTest Labs, zobacz [Włączanie połączenia przeglądarki na maszynach wirtualnych laboratorium](enable-browser-connection-lab-virtual-machines.md).

Po włączeniu *połączenia przeglądarki* użytkownicy laboratorium mogą uzyskać dostęp do maszyn wirtualnych za pomocą przeglądarki.  

> [!NOTE]
> Włączenie połączenia przeglądarki na maszynach wirtualnych laboratorium jest w wersji zapoznawczej.

## <a name="create-a-lab-virtual-machine"></a>Tworzenie maszyny wirtualnej laboratorium

Najpierw musisz utworzyć maszynę wirtualną laboratorium w sieci wirtualnej, dla której skonfigurowano bastionu. Możesz wybrać sieć wirtualną podczas tworzenia maszyny wirtualnej, przechodząc do karty **Ustawienia zaawansowane** .

![Tworzenie maszyny wirtualnej](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Uruchom maszynę wirtualną w przeglądarce

Po utworzeniu maszyny wirtualnej można uruchomić ją w przeglądarce, klikając przycisk *przeglądarki Connect* i wprowadzając nazwę użytkownika i hasło dla komputera.  

![Uruchom w przeglądarce](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Następne kroki

[Dodawanie maszyny wirtualnej do laboratorium w Azure DevTest Labs](devtest-lab-add-vm.md)