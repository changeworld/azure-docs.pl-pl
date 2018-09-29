---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie przekształcenia | Microsoft Docs
description: Tworzenie przekształcenia interfejsu wiersza polecenia przy użyciu skryptu interfejsu wiersza polecenia platformy Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: cbe20c4b75fff27fad60446fb933a9c8ba2e3312
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091782"
---
# <a name="cli-example-create-a-transform"></a>Przykład interfejsu wiersza polecenia: tworzenie przekształcenia

Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje sposób tworzenia przekształcenia. Przekształcenia opisują prosty przepływ pracy zadań podczas przetwarzania plików wideo lub audio (często określany jako „przepis”). Zawsze należy sprawdzić, czy przekształcenie z wybraną nazwą i „przepisem” już istnieje. Jeśli tak, należy użyć go ponownie.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](../cli-samples.md).
