---
title: Często zadawane pytania dotyczące sieci w usłudze Azure Functions
description: Odpowiedzi na niektóre najbardziej typowe pytania i scenariusze dotyczące sieci w środowisku Azure Functions.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: b673e625e1a50c32e3d8580ec442792ed8611703
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64572595"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Często zadawane pytania dotyczące sieci w usłudze Azure Functions

Ten artykuł zawiera listę często zadawanych pytań dotyczących sieci w usłudze Azure Functions. Aby uzyskać bardziej szczegółowe omówienie, zobacz [funkcje opcji sieciowych](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Jak ustawić statyczny adres IP w przypadku funkcji?

Wdrażanie funkcji w środowisku usługi App Service jest obecnie jedynym sposobem, aby mieć statyczny przychodzący i wychodzący adres IP dla funkcji. Aby uzyskać szczegółowe informacje na temat używania środowiska usługi App Service, należy uruchomić z artykułem [tworzenia i używania wewnętrznego modułu równoważenia obciążenia w środowisku usługi App Service](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Jak ograniczyć dostęp do Internetu do mojej funkcji?

Można ograniczyć dostęp do Internetu na kilka sposobów:

* [Ograniczenia adresów IP](../app-service/app-service-ip-restrictions.md): Ograniczyć zakres adresów IP ruchu przychodzącego do aplikacji funkcji.
    * W obszarze ograniczenia adresów IP, jesteś także możliwość skonfigurowania [punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md), które ograniczają funkcję, aby akceptować tylko ruch przychodzący z określonej sieci wirtualnej.
* Usunięcie wszystkich wyzwalaczy protokołu HTTP. W przypadku niektórych aplikacji jest wystarczający, aby po prostu uniknąć wyzwalaczy HTTP i wyzwalanie funkcji przy użyciu dowolnego innego źródła zdarzeń.

Należy pamiętać, że edytorze portalu Azure wymaga bezpośredniego dostępu do uruchomionej funkcji. Urządzenie, którego używasz do przeglądanie portalu, aby jego listy dozwolonych adresów IP wymaga żadnych zmian w kodzie za pomocą witryny Azure portal. Ale można nadal używać niczego na karcie funkcje platformy za pomocą ograniczeń sieci w miejscu.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Jak ograniczyć moją aplikację funkcji do sieci wirtualnej?

Można ograniczyć **dla ruchu przychodzącego** ruchu dla aplikacji funkcji do sieci wirtualnej za pomocą [punktów końcowych usługi](./functions-networking-options.md#private-site-access). Ta konfiguracja nadal pozwala aplikację funkcji, aby udostępnić połączenia wychodzące z Internetem.

Jedynym sposobem, aby całkowicie ograniczyć funkcję w taki sposób, że cały ruch przechodzi przez sieć wirtualną ma używać wewnętrznego równoważenia obciążenia środowiska usługi App Service. Ta opcja wdraża swoją witrynę w dedykowanej infrastruktury w sieci wirtualnej i wysyła wszystkie wyzwalacze i ruchu za pośrednictwem sieci wirtualnej. 

Aby uzyskać szczegółowe informacje na temat używania środowiska usługi App Service, należy uruchomić z artykułem [tworzenia i używania wewnętrznego modułu równoważenia obciążenia w środowisku usługi App Service](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Jak dostęp zasobów w sieci wirtualnej z aplikacji — funkcja

Zasoby w sieci wirtualnej z uruchomionej funkcji dostęp za pomocą integracji sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Integracja sieci wirtualnej](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Jak uzyskać dostęp do zasobów chronionych przez punkty końcowe usługi?

Za pomocą integracji sieci wirtualnej (obecnie w wersji zapoznawczej), mogą uzyskiwać dostęp do zasobów zabezpieczonych punktu końcowego usługi z uruchomionej funkcji. Aby uzyskać więcej informacji, zobacz [Integracja sieci wirtualnej w wersji zapoznawczej](functions-networking-options.md#preview-version-of-virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Jak wyzwolić funkcję z zasobu w sieci wirtualnej

Można umożliwić wyzwalaczy HTTP do wywoływania z siecią wirtualną przy użyciu [punktów końcowych usługi](./functions-networking-options.md#private-site-access). 

Można również wyzwalać funkcji z zasobów w sieci wirtualnej, wdrażając aplikację funkcji do środowiska usługi App Service. Aby uzyskać więcej informacji na temat używania środowiska usługi App Service, zobacz [tworzenia i używania wewnętrznego modułu równoważenia obciążenia w środowisku usługi App Service](../app-service/environment/create-ilb-ase.md).

— Wersja Premium i usługi App Service plan pomocy technicznej wyzwalaczy HTTP z sieci wirtualnej, ale środowisko App Service environment obsługują wszystkich innych funkcji wyzwalacza typów za pośrednictwem sieci wirtualnej.

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Jak można wdrożyć aplikację funkcji w sieci wirtualnej?

Wdrażanie w środowisku usługi App Service jest jedynym sposobem, aby utworzyć aplikację funkcji, która jest w całości w sieci wirtualnej. Aby uzyskać szczegółowe informacje na temat korzystania z wewnętrznego modułu równoważenia obciążenia w środowisku usługi App Service, należy uruchomić z artykułem [tworzenia i używania wewnętrznego modułu równoważenia obciążenia w środowisku usługi App Service](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

W przypadku scenariuszy wymagających jednokierunkowe dostęp tylko do zasobów sieci wirtualnej lub mniej izolacji sieci kompleksowe zobacz [Functions — omówienie sieci](functions-networking-options.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat sieci i funkcje: 

* [Postępuj zgodnie z samouczkiem o wprowadzenie do Integracja sieci wirtualnej](./functions-create-vnet.md)
* [Dowiedz się więcej na temat opcji sieciowych w usłudze Azure Functions](./functions-networking-options.md)
* [Dowiedz się więcej na temat integracji sieci wirtualnej za pomocą usługi App Service i Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Dowiedz się więcej na temat sieci wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
* [Włączyć więcej sieciowych funkcji i kontrola przy użyciu środowisk usługi App Service](../app-service/environment/intro.md)
