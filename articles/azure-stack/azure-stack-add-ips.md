---
title: Dodaj publiczne adresy IP w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać więcej publiczne adresy IP do usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: cbfe4fcf30e9529f191f2a80de8ab83e7e0811f7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735901"
---
# <a name="add-public-ip-addresses"></a>Dodaj publiczne adresy IP
*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*  

Dowiedz się, jak dodać więcej publiczne adresy IP do usługi Azure Stack.  W tym artykule nazywamy zewnętrzne adresy jako publiczne adresy IP, ale w usłudze Azure Stack jest on przeznaczony do odwoływania się do dodawania bloki adresów IP do sieci zewnętrznej.  Czy zewnętrzne sieci publicznej sieci Internet, Routing lub znajduje się w intranecie i korzysta z prywatnej przestrzeni adresowej nie ma znaczenia na potrzeby tego artykułu.  Kroki są takie same. 

## <a name="add-a-public-ip-address-pool"></a>Dodawanie puli publicznych adresów IP
Publiczne adresy IP można dodać do systemu Azure Stack, w dowolnym momencie po początkowym wdrożeniu systemu Azure Stack. Zapoznaj się z jak [zużycia adresu widok publiczny adres IP](azure-stack-viewing-public-ip-address-consumption.md) aby zobaczyć, jakie bieżącego użycia i publiczny adres IP jest dostępność na usługi Azure Stack.

Na wysokim poziomie proces dodawania nowego bloku publicznego adresu IP do usługi Azure Stack wygląda następująco:

 ![Dodawanie przepływu dla adresu IP](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>Uzyskaj blok adresów dostawcy
Pierwszą rzeczą, jaką będzie konieczne jest uzyskanie blok adresów, które chcesz dodać do usługi Azure Stack.  W zależności od tego, gdzie można uzyskać z bloku adresu należy wziąć pod uwagę co czas realizacji jest i zarządzanie nim względem szybkość jaką zużywają publiczne adresy IP w usłudze Azure Stack.  

> [!IMPORTANT]
> Usługa Azure Stack będzie akceptować żadnych blok adres, który podasz, tak długo, jak to blok prawidłowy adres i nie nakłada się zakres adresów w istniejących w usłudze Azure Stack.  Upewnij się, że można uzyskać prawidłowy adres blok routowalne i mieć nienakładające z sieci zewnętrznej, do którego jest podłączony usługi Azure Stack.  Po dodaniu zakresu do usługi Azure Stack, nie można go usunąć.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Dodaj zakres adresów IP do usługi Azure Stack

1. W przeglądarce internetowej przejdź do pulpitu nawigacyjnego portalu administratora.  W tym przykładzie użyjemy https://adminportal.local.azurestack.external.  
2.  Zaloguj się do portalu administratora usługi Azure Stack jako operator chmury.
3.  Na domyślny pulpit nawigacyjny — Znajdź na liście zarządzania Region i wybierz region, który chcesz zarządzać, w tym przykładzie lokalnego.
4.  Znajdź Kafelek dostawców zasobów i kliknij pozycję Dostawca zasobów sieciowych.
5.  Kliknij publiczny adres IP pul Kafelek użycie.
6.  Kliknij przycisk Dodaj adres IP w puli.
7.  Podaj nazwę dla puli adresów IP.  Nazwa, którą możesz wybrać, jest po prostu, aby możliwe było łatwo zidentyfikować puli adresów IP, dzięki czemu można też wywołać wedle uznania.  Jest dobrą praktyką jest taki sam jak ten zakres adresów wprowadzić nazwę, ale nie jest wymagane.
8.   Należy podać blok adresu, który ma zostać dodany w notacji CIDR.  Na przykład: 192.168.203.0/24
9.  Jeśli podasz prawidłowego zakresu CIDR adresów (blok CIDR) zakres początkowy adres IP, końcowy adres IP i pola adresów IP dostępnych automatycznie zostaną wypełnione.  Są one tylko do odczytu i generowane automatycznie, więc nie można zmienić bez konieczności modyfikacji wartość w polu zakresu adresów.
10. Po zapoznaniu się z informacjami w bloku i Potwierdzanie wszystko wygląda poprawić, kliknij przycisk Ok, aby zatwierdzić zmiany i Dodaj zakres adresów do usługi Azure Stack.


## <a name="next-steps"></a>Kolejne kroki 
[Liczba akcji węzła jednostki skalowania przeglądu](azure-stack-node-actions.md) 
