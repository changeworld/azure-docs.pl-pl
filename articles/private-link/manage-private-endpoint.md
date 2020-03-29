---
title: Zarządzanie połączeniem prywatnego punktu końcowego na platformie Azure
description: Dowiedz się, jak zarządzać połączeniami prywatnych punktów końcowych na platformie Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452959"
---
# <a name="manage-a-private-endpoint-connection"></a>Zarządzanie połączeniem z prywatnym punktem końcowym
Usługa Azure Private Link działa na modelu przepływu wywołania zatwierdzenia, w którym konsument usługi private link może zażądać połączenia z dostawcą usług w celu korzystania z usługi. Usługodawca może następnie zdecydować, czy zezwolić konsumentowi na nawiązanie połączenia, czy też nie. Usługa Azure Private Link umożliwia dostawcom usług zarządzanie połączeniem prywatnego punktu końcowego na swoich zasobach. Ten artykuł zawiera instrukcje dotyczące zarządzania połączeniami prywatnego punktu końcowego.

![Zarządzanie prywatnymi punktami końcowymi](media/manage-private-endpoint/manage-private-endpoint.png)

Istnieją dwie metody zatwierdzania połączeń, które konsument usługi Private Link może wybrać:
- **Automatyczne:** Jeśli konsument usługi ma uprawnienia RBAC do zasobu dostawcy usług, konsument może wybrać metodę automatycznego zatwierdzania. W takim przypadku, gdy żądanie dotrze do zasobu dostawcy usług, nie jest wymagana żadna akcja od dostawcy usług, a połączenie zostanie automatycznie zatwierdzone. 
- **Podręcznik:** Wręcz przeciwnie, jeśli konsument usługi nie ma uprawnień RBAC do zasobu dostawcy usług, konsument może wybrać metodę ręcznego zatwierdzania. W takim przypadku żądanie połączenia pojawia się w zasobach usługi jako **Oczekujące**. Dostawca usług musi ręcznie zatwierdzić żądanie przed nawiązaniem połączeń. W przypadkach ręcznych konsument usługi można również określić komunikat z żądaniem, aby zapewnić więcej kontekstu do dostawcy usług. Usługodawca ma następujące opcje do wyboru dla wszystkich połączeń prywatnych punktów końcowych: **Zatwierdzone**, **Odrzuć**, **Usuń**.

W poniższej tabeli przedstawiono różne akcje dostawcy usług i wynikowe stany połączenia dla prywatnych punktów końcowych.  Dostawca usług może również zmienić stan połączenia prywatnego punktu końcowego w późniejszym czasie bez interwencji konsumenta. Akcja zaktualizuje stan punktu końcowego po stronie konsumenta. 


|Akcja usługodawcy   |Prywatny stan punktu końcowego usługi konsumenta   |Opis   |
|---------|---------|---------|
|Brak    |    Oczekujące     |    Połączenie jest tworzone ręcznie i oczekuje na zatwierdzenie przez właściciela zasobu Łącza prywatnego.       |
|Zatwierdzenie    |  Approved (Zatwierdzono)       |  Połączenie zostało zatwierdzone automatycznie lub ręcznie i jest gotowe do użycia.     |
|Reject     | Odrzucone        | Połączenie zostało odrzucone przez właściciela zasobu łącza prywatnego.        |
|Remove    |  Odłączony       | Połączenie zostało usunięte przez właściciela zasobu łącza prywatnego, prywatny punkt końcowy staje się informacyjny i powinien zostać usunięty w celu oczyszczenia.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Zarządzanie połączeniami z prywatnym punktem końcowym w zasobach usługi Azure PaaS
Portal jest preferowaną metodą zarządzania połączeniami prywatnych punktów końcowych w zasobach usługi Azure PaaS. Obecnie nie mamy obsługi programu PowerShell/CLI do zarządzania połączeniami w zasobach usługi Azure PaaS.
1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
2. Przejdź do prywatnego centrum łączy.
3. W obszarze **Zasoby**wybierz typ zasobu, który ma być zarządzany połączeniami prywatnych punktów końcowych.
4. Dla każdego typu zasobu można wyświetlić liczbę skojarzonych z nim połączeń prywatnego punktu końcowego. W razie potrzeby można filtrować zasoby.
5. Wybierz połączenia prywatnego punktu końcowego.  W obszarze wymienionych połączeń wybierz połączenie, które chcesz zarządzać. 
6. Stan połączenia można zmienić, wybierając spośród opcji u góry.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Zarządzanie połączeniami prywatnych punktów końcowych w usłudze Private Link należącej do klienta/partnera

Narzędzia Azure PowerShell i azure cli to preferowane metody zarządzania połączeniami prywatnego punktu końcowego w usługach partnerów firmy Microsoft lub usługach należących do klientów. Obecnie nie mamy żadnej obsługi portalu do zarządzania połączeniami w usłudze Private Link.  
 
### <a name="powershell"></a>PowerShell 
  
Użyj następujących poleceń programu PowerShell do zarządzania połączeniami prywatnych punktów końcowych.  
#### <a name="get-private-link-connection-states"></a>Pobierz stany połączenia łącze prywatne 
Użyj `Get-AzPrivateLinkService` polecenia cmdlet, aby uzyskać połączenia prywatnego punktu końcowego i ich stany.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Zatwierdzanie połączenia prywatnego punktu końcowego 
 
Użyj `Approve-AzPrivateEndpointConnection` polecenia cmdlet, aby zatwierdzić połączenie prywatnego punktu końcowego. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Odmów połączenia prywatnego punktu końcowego 
 
Użyj `Deny-AzPrivateEndpointConnection` polecenia cmdlet, aby odrzucić połączenie prywatnego punktu końcowego. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Usuwanie połączenia prywatnego punktu końcowego 
 
Użyj `Remove-AzPrivateEndpointConnection` polecenia cmdlet, aby usunąć połączenie prywatnego punktu końcowego. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 
 
Służy `az network private-link-service update` do zarządzania połączeniami prywatnych punktów końcowych. Stan połączenia jest określony ```azurecli connection-status``` w parametrze. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej o prywatnych punktach końcowych](private-endpoint-overview.md)
 
