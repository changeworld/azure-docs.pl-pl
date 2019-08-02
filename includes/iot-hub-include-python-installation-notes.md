---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640428"
---
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

* **Windows**

    * [Python 2. x lub 3. x](https://www.python.org/downloads/). Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*](https://pip.pypa.io/en/stable/installing/).

    * Jeśli używasz systemu operacyjnego Windows, upewnij się, że masz odpowiednią wersję [pakietu redystrybucyjnego C++ wizualizacji](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) , aby umożliwić korzystanie z natywnych bibliotek DLL w języku Python. Zalecamy użycie najnowszej wersji.

    * W razie konieczności Zainstaluj pakiet [Azure-iothub-Device-Client](https://pypi.org/project/azure-iothub-device-client/) przy użyciu polecenia`pip install azure-iothub-device-client`

    * W razie konieczności Zainstaluj pakiet [Azure-iothub-Service-Client](https://pypi.org/project/azure-iothub-service-client/) przy użyciu polecenia`pip install azure-iothub-service-client`

* **Mac OS**

    W przypadku Mac OS wymagany jest język Python 3.7.0 (lub 2,7) + libboost-1,67 + zwinięcie 7.61.1 (wszystkie zainstalowane za pośrednictwem oprogramowania homebrew). Każda inna dystrybucja/system operacyjny prawdopodobnie osadzi różne wersje zwiększania & zależności, które nie będą działały i spowodują ImportError w czasie wykonywania.

    Pakiety *pip* dla składników `azure-iothub-service-client` i `azure-iothub-device-client` są obecnie dostępne tylko w przypadku systemu operacyjnego Windows. W przypadku systemu Linux/Mac OS zapoznaj się z sekcjami dotyczącymi systemu Linux i Mac OS w sekcji [przygotowanie środowiska programistycznego dla języka Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .

> [!NOTE]
> Podczas importowania iothub_client w przykładzie wystąpił kilka raportów o błędach. Aby uzyskać więcej informacji na temat rozwiązywania problemów z usługą **ImportError** , zobacz temat Rozwiązywanie [problemów z ImportError](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).
