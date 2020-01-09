---
title: Przykłady interfejsu wiersza polecenia platformy Azure
description: Tabela zawierająca linki do przykładowych skryptów bash utworzonych przy użyciu interfejsu wiersza polecenia platformy Azure, takich jak tworzenie zestawu skalowania i zarządzanie nim.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 540941bd2d54c5d3c8d26186e700727e8d75f501
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459355"
---
# <a name="azure-cli-samples-for-virtual-machine-scale-sets"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla zestawów skalowania maszyn wirtualnych

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

| | |
|---|---|
|**Tworzenie zestawu skalowania i zarządzanie nim**||
| [Tworzenie zestawu skalowania maszyn wirtualnych](scripts/cli-sample-create-simple-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy zestaw skalowania maszyn wirtualnych z minimalną konfiguracją. |
| [Tworzenie zestawu skalowania na podstawie niestandardowego obrazu maszyny wirtualnej](scripts/cli-sample-create-scale-set-from-custom-image.md?toc=%2fcli%2fmodule%2ftoc.json) | Tworzy zestaw skalowania maszyn wirtualnych, który używa niestandardowego obrazu maszyny wirtualnej. |
| [Instalowanie aplikacji w zestawie skalowania](scripts/cli-sample-install-apps.md?toc=%2fcli%2fmodule%2ftoc.json) | Używa niestandardowego rozszerzenia skryptu platformy Azure, aby zainstalować podstawową aplikację internetową w zestawie skalowania. |
|**Zarządzanie magazynem**||
| [Tworzenie i dołączanie dysków do zestawu skalowania](scripts/cli-sample-attach-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Tworzy zestaw skalowania maszyn wirtualnych z dołączonymi dyskami danych. |
|**Zarządzanie skalą i nadmiarowością**||
| [Włączanie skalowania automatycznego opartego na hoście](scripts/cli-sample-enable-autoscale.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy zestaw skalowania maszyn wirtualnych skonfigurowany do automatycznego skalowania na podstawie użycia procesora CPU. |
| [Tworzenie zestawu skalowania z pojedynczą strefą](scripts/cli-sample-single-availability-zone-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy zestaw skalowania maszyn wirtualnych, który używa pojedynczej strefy dostępności. |
| [Tworzenie strefowo nadmiarowego zestawu skalowania](scripts/cli-sample-zone-redundant-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy zestaw skalowania maszyn wirtualnych z wieloma strefami dostępności. |
| | |