---
title: Interfejs wiersza polecenia Azure przykłady Windows | Dokumentacja firmy Microsoft
description: Windows — przykłady wiersza polecenia platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/01/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6861399b63b7f06bac7599704a6dd1aa87800ebf
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403342"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Przykłady interfejsu wiersza polecenia dla Windows maszyn wirtualnych

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą wiersza polecenia platformy Azure, które wdrażanie maszyn wirtualnych Windows.

| | |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Utwórz maszynę wirtualną](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy maszynę wirtualną Windows z minimalną konfiguracją. |
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy grupę zasobów, maszyna wirtualna i wszystkie powiązane zasoby.|
| [Tworzenie maszyn wirtualnych o wysokiej dostępności](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy kilka maszyn wirtualnych na platformie o wysokiej dostępności i konfiguracji ze zrównoważonym obciążeniem. |
| [Tworzenie maszyny Wirtualnej, a następnie uruchom skrypt konfiguracji](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy maszynę wirtualną, a następnie używa rozszerzenia skryptu niestandardowego usługi Azure, aby zainstalować usługi IIS. |
| [Tworzenie maszyny Wirtualnej i uruchamianie konfiguracji DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy maszynę wirtualną, a następnie używa rozszerzenia Azure Desired State Configuration (DSC), aby zainstalować usługi IIS. |
|**Sieci wirtualne**||
| [Zabezpieczanie ruchu sieciowego między maszynami wirtualnymi](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy dwie maszyny wirtualne i wszystkie pokrewne zasoby, wewnętrznej i zewnętrznej sieciowej grupy zabezpieczeń (NSG). |
|**Bezpieczne maszyn wirtualnych**||
| [Szyfrowanie dysków maszyn wirtualnych i danych](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy usługi Azure Key Vault, klucz szyfrowania i jednostki usługi, a następnie szyfruje maszyny Wirtualnej. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny Wirtualnej z usługą Log Analytics](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy maszynę wirtualną, a następnie instaluje agenta usługi Log Analytics i rejestruje maszyny Wirtualnej w obszarze roboczym usługi Log Analytics.  |
| | |
