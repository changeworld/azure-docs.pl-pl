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
ms.openlocfilehash: 126b9ccefedee1f5cefdac8a8666a58e7a4a1fef
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548649"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Często zadawane pytania dotyczące sieci w usłudze Azure Functions

Poniżej znajduje się lista często zadawanych pytań sieci. Aby uzyskać bardziej szczegółowe omówienie, przeczytaj [funkcje sieciowe opcje dokumentu](functions-networking-options.md)

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Jak ustawić statyczny adres IP w przypadku funkcji?

Wdrażanie funkcji w App Service Environment (ASE) jest obecnie jedynym sposobem, aby mieć statyczny przychodzący i wychodzący adres IP dla funkcji. Aby uzyskać szczegółowe informacje na temat korzystania z ASE rozpoczynać artykuł tutaj: [Tworzenie i używanie środowisko ASE z wewnętrznym modułem równoważenia obciążenia](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Jak ograniczyć dostęp do Internetu do mojej funkcji?

Można ograniczyć dostęp do Internetu na różne sposoby, wymienionych poniżej.

* [Ograniczenia adresów IP](../app-service/app-service-ip-restrictions.md): ograniczanie ruchu przychodzącego do aplikacji funkcji przez zakres adresów IP.
* Usunięcie wszystkich wyzwalaczy protokołu HTTP. W przypadku niektórych aplikacji wystarczy po prostu uniknąć wyzwalaczy HTTP i wyzwalanie funkcji przy użyciu dowolnego innego źródła zdarzeń.

Najważniejsze zagadnienia, czyniąc jest zapewnienie należy pamiętać, że edytorze portalu platformy Azure musi mieć bezpośredni dostęp do uruchomionej funkcji do użycia. Urządzenie, którego używasz do przeglądanie portalu, aby jego listy dozwolonych adresów IP wymaga żadnych zmian w kodzie za pomocą witryny Azure portal. Nadal jednak można niczego na karcie funkcji platformy za pomocą ograniczeń sieci w miejscu.

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>Jak ograniczyć moją aplikację funkcji do sieci Wirtualnej?

Jedynym sposobem, aby całkowicie ograniczyć funkcję w taki sposób, że cały ruch przechodzi przez sieć Wirtualną jest użycie wewnętrznie ze zrównoważonym obciążeniem (ILB) App Service Environment (ASE). Ta opcja wdraża swoją witrynę w dedykowanej infrastruktury w sieci Wirtualnej i wysyła wszystkie wyzwalacze i ruchu za pośrednictwem sieci Wirtualnej. Aby uzyskać szczegółowe informacje na temat korzystania z ASE rozpoczynać artykuł tutaj: [Tworzenie i używanie środowisko ASE z wewnętrznym modułem równoważenia obciążenia](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>Jak dostęp zasobów w sieci Wirtualnej z aplikacji — funkcja

W uruchomionej funkcji przy użyciu integracji z siecią Wirtualną mogą uzyskiwać dostęp do zasobów w sieci Wirtualnej. Aby uzyskać więcej informacji, zobacz [Integracja sieci Wirtualnej](functions-networking-options.md#vnet-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Jak uzyskać dostęp do zasobów chronionych przez punkty końcowe usługi?

Korzystając z nowej integracji sieci Wirtualnej (obecnie w wersji zapoznawczej) są dostępne zasobów z uruchomionej funkcji zabezpieczonych punktu końcowego usługi. Aby uzyskać więcej informacji, zobacz [Integracja sieci Wirtualnej w wersji zapoznawczej](functions-networking-options.md#preview-vnet-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>Jak wyzwolić funkcję z zasobu w sieci Wirtualnej

Dzięki wdrażaniu aplikacji funkcji w środowisku usługi App Service może wyzwalać tylko funkcji z zasobów w sieci Wirtualnej. Aby uzyskać więcej informacji na temat korzystania z ASE, zobacz [tworzenie i używanie środowisko ASE z wewnętrznym modułem równoważenia obciążenia](../app-service/environment/create-ilb-ase.md).


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>Jak można wdrożyć aplikację funkcji w sieci Wirtualnej?

Wdrażanie do środowiska usługi App Service jest jedynym sposobem, aby utworzyć aplikację funkcji, która całkowicie znajduje się wewnątrz sieci Wirtualnej Aby uzyskać szczegółowe informacje na temat korzystania z ASE z wewnętrznym modułem równoważenia obciążenia, zacznij od artykuł tutaj: [Tworzenie i używanie środowisko ASE z wewnętrznym modułem równoważenia obciążenia](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

W przypadku scenariuszy wymagających tylko jednokierunkowe dostępu do zasobów sieci Wirtualnej lub mniej izolacji sieci kompleksowe zobacz [Functions — omówienie sieci](functions-networking-options.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat sieci i funkcje: 

* [Postępuj zgodnie z samouczkiem ułatwiającym integrację sieci Wirtualnej rozpoczęcie pracy](./functions-create-vnet.md)
* [Dowiedz się więcej na temat opcji sieciowych w funkcjach w tym miejscu](./functions-networking-options.md)
* [Dowiedz się więcej na temat integracji sieci Wirtualnej przy użyciu usługi App Service / funkcje w tym miejscu](../app-service/web-sites-integrate-with-vnet.md)
* [Dowiedz się więcej na temat sieci wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
* [Włączyć więcej sieciowych funkcji i kontrola przy użyciu środowisk usługi App Service](../app-service/environment/intro.md)
