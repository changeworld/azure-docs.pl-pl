---
title: Często zadawane pytania dotyczące sieci w usłudze Azure Functions
description: Odpowiedzi na niektóre z najczęstszych pytań i scenariuszy dotyczących sieci za pomocą usługi Azure Functions.
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75409528"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Często zadawane pytania dotyczące sieci w usłudze Azure Functions

W tym artykule wymieniono często zadawane pytania dotyczące sieci w usłudze Azure Functions. Aby uzyskać bardziej kompleksowe omówienie, zobacz [Opcje sieci funkcji](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Jak ustawić statyczny adres IP w funkcjiach?

Wdrażanie funkcji w środowisku usługi aplikacji jest obecnie jedynym sposobem, aby mieć statyczny przychodzący i wychodzący adres IP dla funkcji. Aby uzyskać szczegółowe informacje na temat korzystania ze środowiska usługi app service, zacznij od artykułu [Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia ze środowiskiem usługi app service](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Jak ograniczyć dostęp do Internetu do mojej funkcji?

Dostęp do Internetu można ograniczyć na kilka sposobów:

* [Ograniczenia adresów IP:](../app-service/app-service-ip-restrictions.md)Ogranicz ruch przychodzący do aplikacji funkcji według zakresu ADRESÓW IP.
    * W obszarze Ograniczenia adresów IP można również skonfigurować [punkty końcowe usługi,](../virtual-network/virtual-network-service-endpoints-overview.md)które ograniczają funkcję do akceptowania tylko ruchu przychodzącego z określonej sieci wirtualnej.
* Usunięcie wszystkich wyzwalaczy HTTP. W przypadku niektórych aplikacji wystarczy po prostu uniknąć wyzwalaczy HTTP i użyć dowolnego innego źródła zdarzeń, aby wyzwolić funkcję.

Należy pamiętać, że edytor portalu Azure wymaga bezpośredniego dostępu do uruchomionej funkcji. Wszelkie zmiany kodu za pośrednictwem witryny Azure portal będzie wymagać urządzenia, którego używasz do przeglądania portalu, aby jego adres IP białej listy. Ale nadal można używać niczego w zakładce funkcji platformy z ograniczeniami sieciowymi.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Jak ograniczyć aplikację funkcji do sieci wirtualnej?

Ruch **przychodzący** aplikacji jest w stanie ograniczyć do sieci wirtualnej przy użyciu [punktów końcowych usługi](./functions-networking-options.md#private-site-access). Ta konfiguracja nadal umożliwia aplikacji funkcji wykonywanie połączeń wychodzących do Internetu.

Jedynym sposobem, aby całkowicie ograniczyć funkcję, tak, że cały ruch przepływa przez sieć wirtualną jest użycie wewnętrznie równoważącego obciążenia środowiska usługi app service. Ta opcja wdraża witrynę w dedykowanej infrastrukturze w sieci wirtualnej i wysyła wszystkie wyzwalacze i ruch za pośrednictwem sieci wirtualnej. 

Aby uzyskać szczegółowe informacje na temat korzystania ze środowiska usługi app service, zacznij od artykułu [Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia ze środowiskiem usługi app service](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Jak uzyskać dostęp do zasobów w sieci wirtualnej z aplikacji funkcji?

Dostęp do zasobów w sieci wirtualnej można uzyskać za pomocą uruchomionej funkcji przy użyciu integracji sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Integracja sieci wirtualnej](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Jak uzyskać dostęp do zasobów chronionych przez punkty końcowe usługi?

Za pomocą integracji sieci wirtualnej można uzyskać dostęp do zasobów zabezpieczonych przez punkt końcowy usługi z uruchomionej funkcji. Aby uzyskać więcej informacji, zobacz [integracja sieci wirtualnej](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Jak wyzwolić funkcję z zasobu w sieci wirtualnej?

Można zezwolić na wywoływanie wyzwalaczy HTTP z sieci wirtualnej przy użyciu [punktów końcowych usługi](./functions-networking-options.md#private-site-access). 

Można również wyzwolić funkcję ze wszystkich innych zasobów w sieci wirtualnej, wdrażając aplikację funkcji do planu Premium, planu usługi app service lub środowiska usługi aplikacji. Więcej informacji można znaleźć w [wyzwalaczach sieci wirtualnej innych](./functions-networking-options.md#virtual-network-triggers-non-http) niż HTTP

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Jak wdrożyć aplikację funkcji w sieci wirtualnej?

Wdrażanie w środowisku usługi aplikacji jest jedynym sposobem, aby utworzyć aplikację funkcji, która jest całkowicie wewnątrz sieci wirtualnej. Aby uzyskać szczegółowe informacje na temat używania wewnętrznego modułu równoważenia obciążenia ze środowiskiem usługi app service, zacznij od artykułu [Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia ze środowiskiem usługi app service](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

W przypadku scenariuszy, w których potrzebny jest tylko jednokierunkowy dostęp do zasobów sieci wirtualnej lub mniej kompleksowej izolacji sieci, zobacz [omówienie sieci funkcji](functions-networking-options.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sieci i funkcjach: 

* [Postępuj zgodnie z samouczkiem na temat rozpoczynania integracji z siecią wirtualną](./functions-create-vnet.md)
* [Dowiedz się więcej o opcjach sieci w usłudze Azure Functions](./functions-networking-options.md)
* [Dowiedz się więcej o integracji sieci wirtualnej z usługą App Service i funkcjami](../app-service/web-sites-integrate-with-vnet.md)
* [Dowiedz się więcej o sieciach wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
* [Włącz więcej funkcji sieciowych i kontroluj za pomocą środowisk usługi app service](../app-service/environment/intro.md)
