---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — resetowanie poświadczeń konta | Microsoft Docs
description: Resetowanie poświadczeń konta i przywracanie ustawień pliku app.config przy użyciu skryptu interfejsu wiersza polecenia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 1c70441ea5b35a55ba39f934e74a6512d783fcf0
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615392"
---
# <a name="cli-example-reset-the-account-credentials"></a>Przykład interfejsu wiersza polecenia: resetowanie poświadczeń konta

Skrypt interfejsu wiersza polecenia platformy Azure pokazuje sposób resetowania poświadczeń konta i przywracania ustawień pliku app.config.

## <a name="prerequisites"></a>Wymagania wstępne 

- Zainstaluj interfejs wiersza polecenia i korzystaj z niego lokalnie. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

    Obecnie nie wszystkie polecenia [interfejsu wiersza polecenia usługi Media Services w wersji 3](https://aka.ms/ams-v3-cli-ref) działają w usłudze Azure Cloud Shell. Zaleca się używanie interfejsu wiersza polecenia lokalnie.

- [Utwórz konto usługi Media Services](../create-account-cli-how-to.md).

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/reset-account-credentials/Reset-Account-Credentials.sh "Reset credentials")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](../cli-samples.md).
