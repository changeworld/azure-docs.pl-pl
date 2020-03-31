---
title: Zasady punku końcowego usługi dla sieci wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak filtrować ruch w sieci wirtualnej do zasobów usługi platformy Azure przy użyciu zasad punktu końcowego usługi
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 926da07ffaf0c61ca2a7fd02351ef3635ec4d73b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651302"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Zasady punktu końcowego usługi sieci wirtualnej dla usługi Azure Storage

Zasady punktu końcowego usługi sieci wirtualnej (VNet) umożliwiają filtrowanie ruchu sieci wirtualnej wychodzącego do kont usługi Azure Storage za pośrednictwem punktu końcowego usługi i zezwalaj na eksfiltrację danych tylko do określonych kont usługi Azure Storage. Zasady punktu końcowego zapewniają szczegółową kontrolę dostępu dla wirtualnego ruchu sieciowego do usługi Azure Storage podczas łączenia się za pośrednictwem punktu końcowego usługi.

![Zabezpieczanie ruchu wychodzącego sieci wirtualnej na kontach usługi Azure Storage](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Ta funkcja jest ogólnie dostępna dla __usługi Azure Storage__ we wszystkich __globalnych regionach platformy Azure.__

## <a name="key-benefits"></a>Najważniejsze korzyści

Zasady punktu końcowego usługi dla sieci wirtualnej zapewniają następujące korzyści:

- __Ulepszone zabezpieczenia ruchu sieci wirtualnej w usłudze Azure Storage__

  [Tagi usługi platformy Azure dla grup zabezpieczeń sieci](https://aka.ms/servicetags) umożliwiają ograniczenie ruchu wychodzącego sieci wirtualnej do określonych regionów usługi Azure Storage. Umożliwia to jednak ruch do dowolnego konta w wybranym regionie usługi Azure Storage.
  
  Zasady punktu końcowego umożliwiają określenie kont usługi Azure Storage, które są dozwolone dostęp wychodzący sieci wirtualnej i ogranicza dostęp do wszystkich innych kont magazynu. Zapewnia to znacznie bardziej szczegółową kontrolę zabezpieczeń w celu ochrony eksfiltracji danych z sieci wirtualnej.

- __Skalowalne zasady o wysokiej dostępności umożliwiające filtrowanie ruchu w usługach platformy Azure__

   Zasady punktu końcowego zapewniają skalowalne w poziomie, wysoce dostępne rozwiązanie, które umożliwia filtrowanie ruchu w usługach platformy Azure z sieci wirtualnych za pośrednictwem punktów końcowych usługi. Żadne dodatkowe nakłady nie są wymagane, aby utrzymywać centralne urządzenia sieciowe do obsługi ruchu w sieciach wirtualnych.

## <a name="json-object-for-service-endpoint-policies"></a>Zasady JSON Object for Service Endpoint
Rów szybkie spojrzenie na zasady punktu końcowego usługi obiektu.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Konfigurowanie

-   Zasady punktu końcowego można skonfigurować w celu ograniczenia ruchu sieci wirtualnej do określonych kont usługi Azure Storage.
-   Zasady punktu końcowego są konfigurowane w podsieci sieci wirtualnej. Punkty końcowe usługi dla usługi Azure Storage powinny być włączone w podsieci, aby zastosować zasady.
-   Zasady punktu końcowego umożliwia dodawanie określonych kont usługi Azure Storage, aby zezwolić na listę, przy użyciu formatu resourceID. Można ograniczyć dostęp do
    - wszystkie konta magazynu w ramach subskrypcji<br>
      `E.g. /subscriptions/subscriptionId`

    - wszystkie konta magazynu w grupie zasobów<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - indywidualne konto magazynu, wystawiając na listę odpowiedniego identyfikatora zasobów usługi Azure Resource Manager. Obejmuje to ruch do obiektów blob, tabel, kolejek, plików oraz usługi Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Domyślnie jeśli żadne zasady nie są dołączone do podsieci z punktami końcowymi, można uzyskać dostęp do wszystkich kont magazynu w usłudze. Po skonfigurowaniu zasad w tej podsieci tylko zasoby określone w zasadach będą dostępne z wystąpień obliczeniowych w tej podsieci. Dostęp do wszystkich innych kont magazynu zostanie odrzucony.
-   Podczas stosowania zasad punktu końcowego usługi w podsieci *zakres punktu końcowego usługi* Azure Storage Service jest uaktualniany z regionalnego do **globalnego**. Oznacza to, że cały ruch do usługi Azure Storage jest zabezpieczony za pośrednictwem punktu końcowego usługi później. Zasady punktu końcowego usługi mają również zastosowanie globalnie, więc wszystkie konta magazynu, które nie są jawnie dozwolone, zostaną odrzucone dostęp. 
-   Do podsieci możesz zastosować wiele zasad. Gdy wiele zasad są skojarzone z podsiecią, wirtualny ruch sieciowy do zasobów określonych w dowolnej z tych zasad będą dozwolone. Dostęp do wszystkich innych zasobów usługi, które nie zostały określone w żadnej z zasad, zostanie zablokowany.

    > [!NOTE]  
    > Zasady punktu końcowego usługi są **zezwalają na zasady,** więc oprócz określonych zasobów wszystkie inne zasoby są ograniczone. Upewnij się, że wszystkie zależności zasobów usługi dla aplikacji są identyfikowane i wymienione w zasadach.

- W zasadach punktu końcowego można określić tylko konta magazynu korzystające z modelu zasobów platformy Azure. Klasyczne konta usługi Azure Storage nie będą obsługiwać zasad punktu końcowego usługi Azure.
- Pomocniczy dostęp do odczytu z magazynu geograficznie nadmiarowego zostanie automatycznie zapewniony, jeśli wskazane będzie konto podstawowe.
- Konta magazynu mogą znajdować się w tej samej lub innej subskrypcji bądź dzierżawie usługi Azure Active Directory co sieć wirtualna.

## <a name="scenarios"></a>Scenariusze

- **Sieci wirtualne połączone równorzędnie, połączone sieci wirtualne lub wiele sieci wirtualnych**: aby odfiltrować ruch w połączonych równorzędnie sieciach wirtualnych, należy zastosować zasady punktu końcowego indywidualnie do tych sieci wirtualnych.
- **Filtrowanie ruchu internetowego za pomocą urządzeń sieciowych lub zapory azure:** filtrowanie ruchu usługi Azure za pomocą zasad, punktów końcowych usługi i filtrowanie pozostałej części ruchu internetowego lub azure za pośrednictwem urządzeń lub Zapory platformy Azure.
- **Filtrowanie ruchu w usługach platformy Azure wdrożonych w sieciach wirtualnych:** w tej chwili zasady punktu końcowego usługi Azure nie są obsługiwane dla żadnych zarządzanych usług platformy Azure, które są wdrażane w sieci wirtualnej. 
- **Filtrowanie ruchu do usług platformy Azure ze środowiska lokalnego**: zasady punktu końcowego usługi mają zastosowanie tylko do ruchu z podsieci związanych z zasadami. Aby umożliwić dostęp do konkretnych zasobów usługi platformy Azure ze środowisk lokalnych, ruch należy filtrować przy użyciu wirtualnych urządzeń sieciowych lub zapór.

## <a name="logging-and-troubleshooting"></a>Rejestrowanie i rozwiązywanie problemów
Nie ma dostępnego centralnego rejestrowania dla zasad punktu końcowego usługi. W przypadku dzienników diagnostycznych usługi zobacz [Rejestrowanie punktów końcowych usługi](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Scenariusze rozwiązywania problemów
- Odmowa dostępu do kont magazynu, które działały w wersji zapoznawczej (nie w regionie połączonym z geo sparowaniem)
  - Dzięki uaktualnianiu usługi Azure Storage do używania globalnych tagów usług zakres punktu końcowego usługi, a tym samym zasady punktu końcowego usługi jest teraz globalny. Tak więc każdy ruch do usługi Azure Storage jest szyfrowany za pomocą punktów końcowych usługi i tylko konta magazynu, które są jawnie wymienione w zasadach są dozwolone dostępu.
  - Jawnie zezwolić na listę wszystkich kont magazynu wymagane, aby przywrócić dostęp.  
  - Skontaktuj się z pomocą techniczną platformy Azure.
- Odmowa dostępu dla kont wymienionych w zasadach punktu końcowego
  - Filtrowanie przez sieciowe grupy zabezpieczeń lub zaporę może blokować dostęp
  - Jeśli usunięcie/ponowne zastosowanie zasad powoduje utratę połączenia:
    - Sprawdź, czy usługa Azure jest skonfigurowana tak, aby zezwalać na dostęp z sieci wirtualnej za pośrednictwem punktów końcowych, czy domyślna zasada zasobu jest *ustawiona na Zezwalaj na wszystko*.
    - Sprawdź, czy diagnostyka usługi pokazuje ruch przez punkty końcowe.
    - Sprawdź, czy dzienniki przepływu sieciowej grupy zabezpieczeń oraz dzienniki magazynu pokazują oczekiwany dostęp za pośrednictwem punktów końcowych usługi.
    - Skontaktuj się z pomocą techniczną platformy Azure.
- Odmowa dostępu dla kont niewymienionych w zasadach punktu końcowego usługi
  - Sprawdź, czy usługa Azure Storage jest skonfigurowana tak, aby zezwalać na dostęp z sieci wirtualnej za pośrednictwem punktów końcowych, czy domyślna zasada zasobu jest *ustawiona na Zezwalaj wszystkim*.
  - Upewnij się, że konta nie są **klasyczne konta magazynu** z zasadami punktu końcowego usługi w podsieci.
- Zarządzana usługa Azure przestała działać po zastosowaniu zasad punktu końcowego usługi w podsieci
  - Usługi zarządzane nie są obecnie obsługiwane za pomocą zasad punktu końcowego usługi. *Obejrzyj to miejsce, aby otrzymywać aktualizacje*.

## <a name="provisioning"></a>Inicjowanie obsługi

Zasady punktu końcowego usługi można konfigurować w podsieciach — czynność tę może wykonać użytkownik z uprawnieniami do zapisu w sieci wirtualnej. Dowiedz się więcej na temat [wbudowanych ról](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) na platformie Azure i przypisywania określonych uprawnień do [ról niestandardowych](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Sieci wirtualne i konta usługi Azure Storage mogą znajdować się w tych samych lub różnych subskrypcjach lub dzierżawach usługi Azure Active Directory.

## <a name="limitations"></a>Ograniczenia

- Możesz wdrożyć zasady punktu końcowego usługi tylko dla sieci wirtualnych wdrożonych za pomocą modelu wdrażania przy użyciu usługi Azure Resource Manager.
- Sieci wirtualne muszą znajdować się w tym samym regionie co zasady punktu końcowego usługi.
- Zasady punktu końcowego usługi możesz zastosować wobec podsieci tylko wtedy, gdy dla usług platformy Azure wymienionych w zasadach zostały skonfigurowane punkty końcowe usługi.
- Nie możesz użyć zasad punktu końcowego usługi w przypadku ruchu z sieci lokalnej do usług platformy Azure.
- Usługi zarządzane platformy Azure obecnie nie obsługują zasad punktu końcowego. Obejmuje to usługi zarządzane wdrożone w podsieciach udostępnionych (np. *usługi Azure HDInsight, usługa Azure Batch, Azure ADDS, Azure APplication Gateway, Brama sieci VPN platformy Azure, Zapora azure)* lub w dedykowanych podsieciach (np. *środowisko usługi Azure App Service, pamięć podręczna Usługi Azure Redis, usługa Azure API Management, Azure SQL MI, klasyczne usługi zarządzane).*

 > [!WARNING]
 > Usługi platformy Azure wdrożone w sieci wirtualnej, np. usługa Azure HDInsight, uzyskują dostęp do innych usług platformy Azure, np. do usługi Azure Storage, w związku z wymaganiami infrastruktury. Ograniczenie zasad punktu końcowego do wybranych zasobów może uniemożliwić dostęp do tych zasobów infrastruktury dla usług platformy Azure wdrożonych w sieci wirtualnej.

- klasyczne konta magazynu nie są obsługiwane w zasadach punktu końcowego. Zasady będą domyślnie odrzucać dostęp do wszystkich klasycznych kont magazynu. Jeśli aplikacja potrzebuje dostępu do usługi Azure Resource Manager i klasycznych kont magazynu, zasady punktu końcowego nie powinny być stosowane dla tego ruchu.

## <a name="pricing-and-limits"></a>Ceny i limity

Za korzystanie z zasad punktu końcowego usługi nie są naliczane dodatkowe opłaty. Do punktów końcowych usługi stosowany jest bieżący model cen usług platformy Azure (takich jak Azure Storage).

Następujące limity są wymuszane w zasadach punktu końcowego usługi: 

 |Zasób | Limit domyślny |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować zasady punktu końcowego usługi dla sieci wirtualnej](virtual-network-service-endpoint-policies-portal.md)
- Dowiedz się więcej na temat [punktów końcowych usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md)
