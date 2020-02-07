---
title: Zasady punku końcowego usługi dla sieci wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak filtrować ruch w sieci wirtualnej do zasobów usługi platformy Azure przy użyciu zasad punktu końcowego usługi
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumi
ms.openlocfilehash: 1aa4328a6d5367ef356ce33807289a873c93d90f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77056703"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Zasady punktu końcowego usługi dla sieci wirtualnej (wersja zapoznawcza)

Zasady punktu końcowego usługi sieci wirtualnej umożliwiają filtrowanie ruchu sieci wirtualnej do usług platformy Azure, zezwalając na stosowanie tylko wybranych zasobów usług platformy Azure w punktach końcowych usługi. Zasady punktu końcowego zapewniają szczegółową kontrolę dostępu w przypadku ruchu w sieci wirtualnej do usług platformy Azure.

Ta funkcja jest dostępna w __wersji zapoznawczej__ we wszystkich publicznych regionach platformy Azure dla usługi Azure STorage.

Najbardziej aktualne powiadomienia dotyczące wersji zapoznawczej można znaleźć na stronie [aktualizacji usługi Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

> [!NOTE]  
> W okresie obowiązywania wersji zapoznawczej zasady punktu końcowego usługi dla sieci wirtualnej mogą nie oferować dostępności i niezawodności na tym samym poziomie, co funkcje w wersji ogólnodostępnej. Aby uzyskać więcej informacji, zobacz [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Najważniejsze korzyści

Zasady punktu końcowego usługi dla sieci wirtualnej zapewniają następujące korzyści:

- __Ulepszone zabezpieczenia dla ruchu sieci wirtualnej do usług platformy Azure__

  [Tagi usługi platformy Azure dla sieciowych grup zabezpieczeń](https://aka.ms/servicetags) umożliwiają ograniczanie ruchu wychodzącego sieci wirtualnej do konkretnych usług platformy Azure. Jednak to rozwiązanie zezwala na ruch do dowolnych zasobów tej usługi platformy Azure. 
  
  Za pomocą zasad punktu końcowego możesz teraz ograniczyć dostęp wychodzący sieci wirtualnej tylko do określonych zasobów platformy Azure. Dzięki temu zyskujesz dużo bardziej szczegółową kontrolę nad bezpieczeństwem danych, do których uzyskuje się dostęp w sieci wirtualnej. 

- __Skalowalne zasady o wysokiej dostępności umożliwiające filtrowanie ruchu w usługach platformy Azure__

   Zasady punktu końcowego zapewniają skalowalne w poziomie, wysoce dostępne rozwiązanie, które umożliwia filtrowanie ruchu w usługach platformy Azure z sieci wirtualnych za pośrednictwem punktów końcowych usługi. Żadne dodatkowe nakłady nie są wymagane, aby utrzymywać centralne urządzenia sieciowe do obsługi ruchu w sieciach wirtualnych.

## <a name="configuration"></a>Konfiguracja

- Możesz skonfigurować zasady punktu końcowego, aby ograniczyć ruch w sieci wirtualnej do wybranych zasobów usług platformy Azure. W ramach wersji zapoznawczej obsługujemy zasady punktu końcowego dla usługi Azure Storage. 
- Zasady punktu końcowego są konfigurowane w podsieci sieci wirtualnej. Aby zastosować zasady, punkty końcowe usług powinny być włączone w podsieci dla wszystkich usług platformy Azure wymienionych w tych zasadach.
- Zasady punktu końcowego umożliwiają dodawanie wybranych zasobów usług platformy Azure na listę dozwolonych przy użyciu formatu resourceID. Możesz ograniczyć dostęp do wszystkich zasobów w subskrypcji lub w grupie zasobów. Ponadto możesz ograniczyć dostęp do konkretnych zasobów. 
- Domyślnie jeśli żadne zasady nie zostaną dołączone do podsieci z punktami końcowymi, możesz uzyskać dostęp do wszystkich zasobów w usłudze. Po skonfigurowaniu zasad w tej podsieci tylko zasoby określone w zasadach będą dostępne z wystąpień obliczeniowych w tej podsieci. Dostęp do wszystkich innych zasobów dla tej konkretnej usługi zostanie odrzucony. 
- Możesz filtrować ruch do zasobów usługi platformy Azure w regionach, w których skonfigurowano punkt końcowy usługi. Domyślnie dostęp do zasobów usługi w innych regionach będzie możliwy. 

  > [!NOTE]  
  > Aby w pełni zablokować dostęp wychodzący w sieci wirtualnej do zasobów platformy Azure w regionach punktu końcowego usługi, musisz również mieć reguły sieciowej grupy zabezpieczeń skonfigurowane w sposób zezwalający tylko na ruch do regionów punktu końcowego usługi przy użyciu [tagów usługi](security-overview.md#service-tags).

- Do podsieci możesz zastosować wiele zasad. Jeżeli wiele zasad zostanie skojarzonych z podsiecią (dla tej samej usługi), ruch w sieci wirtualnej do zasobów określonych w jakiejkolwiek z tych zasad będzie dopuszczony. Dostęp do wszystkich innych zasobów usługi, które nie zostały określone w żadnej z zasad, zostanie zablokowany. 

  > [!NOTE]  
  > Zasady umożliwiają dostęp z sieci wirtualnej tylko do wymienionych zasobów usługi. Kiedy dodasz określone zasoby do zasad, cały pozostały ruch do usługi będzie automatycznie odrzucany. Upewnij się, że wszystkie zależności zasobów usługi dla aplikacji można zidentyfikować i wymienić w zasadach.

  > [!WARNING]  
  > Usługi platformy Azure wdrożone w sieci wirtualnej, np. usługa Azure HDInsight, uzyskują dostęp do innych usług platformy Azure, np. do usługi Azure Storage, w związku z wymaganiami infrastruktury. Ograniczenie zasad punktu końcowego do wybranych zasobów może uniemożliwić dostęp do tych zasobów infrastruktury dla usług wdrożonych w sieci wirtualnej. W przypadku niektórych usług (patrz [Ograniczenia](#limitations) w trakcie okresu zapoznawczego) zasady punktu końcowego usługi nie są obsługiwane dla żadnych usług zarządzanych platformy Azure wdrożonych w sieci wirtualnej.

- W przypadku usługi Azure Storage: 
  -  Możesz ograniczyć dostęp, podając identyfikator *resourceId* usługi Azure Resource Manager konta magazynu. Obejmuje to ruch do obiektów blob, tabel, kolejek, plików oraz usługi Azure Data Lake Storage Gen2.

     Przykładowo konta magazynu platformy Azure można wymienić w definicji zasad punktu końcowego, tak jak pokazano poniżej:
    
     Aby zezwolić na dostęp w przypadku konkretnego konta magazynu:         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     Aby zezwolić na dostęp w przypadku wszystkich kont w subskrypcji i grupie zasobów: `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     Aby zezwolić na dostęp w przypadku wszystkich kont w subskrypcji: `/subscriptions/subscriptionId`
    
- W zasadach punktu końcowego można określić tylko konta magazynu korzystające z modelu zasobów platformy Azure.  

  > [!NOTE]  
  > Dostęp do klasycznych kont magazynu jest blokowany przy użyciu zasad punktu końcowego.

- Główna lokalizacja wskazanego konta powinna znajdować się w regionach pary geograficznej punktu końcowego usługi dla podsieci. 

  > [!NOTE]  
  > Zasady umożliwiają określanie zasobów usługi z innych regionów. Dostęp sieci wirtualnej do usług platformy Azure jest filtrowany tylko dla regionów pary geograficznej. Jeśli sieciowe grupy zabezpieczeń nie są ograniczone do regionów pary geograficznej dla usługi Azure Storage, sieć wirtualna może uzyskać dostęp do wszystkich kont magazynu poza regionami pary geograficznej.

- Pomocniczy dostęp do odczytu z magazynu geograficznie nadmiarowego zostanie automatycznie zapewniony, jeśli wskazane będzie konto podstawowe. 
- Konta magazynu mogą znajdować się w tej samej lub innej subskrypcji bądź dzierżawie usługi Azure Active Directory co sieć wirtualna. 

## <a name="limitations"></a>Ograniczenia

- Możesz wdrożyć zasady punktu końcowego usługi tylko dla sieci wirtualnych wdrożonych za pomocą modelu wdrażania przy użyciu usługi Azure Resource Manager.
- Sieci wirtualne muszą znajdować się w tym samym regionie co zasady punktu końcowego usługi.
- Zasady punktu końcowego usługi możesz zastosować wobec podsieci tylko wtedy, gdy dla usług platformy Azure wymienionych w zasadach zostały skonfigurowane punkty końcowe usługi.
- Nie możesz użyć zasad punktu końcowego usługi w przypadku ruchu z sieci lokalnej do usług platformy Azure.
- Zasady punktu końcowego nie powinny być stosowane wobec podsieci z zarządzanymi usługami platformy Azure, które mają zależności od usług platformy Azure związane z wymaganiami infrastruktury. 

  > [!WARNING]  
  > Usługi platformy Azure wdrożone w sieci wirtualnej, np. usługa Azure HDInsight, uzyskują dostęp do innych usług platformy Azure, np. do usługi Azure Storage, w związku z wymaganiami infrastruktury. Ograniczenie zasad punktu końcowego do wybranych zasobów może uniemożliwić dostęp do tych zasobów infrastruktury dla usług platformy Azure wdrożonych w sieci wirtualnej.
  
  - Niektóre usługi platformy Azure można wdrażać w podsieciach z innymi wystąpieniami obliczeniowymi. Upewnij się, że zasady punktu końcowego nie są stosowane do podsieci, jeśli usługi zarządzane wymienione poniżej są wdrożone w tej podsieci.
   
    - Azure HDInsight
    - Azure Batch (Azure Resource Manager)
    - Azure Active Directory Domain Services (Azure Resource Manager)
    - Azure Application Gateway (Azure Resource Manager)
    - Azure VPN Gateway (Azure Resource Manager)
    - Azure Firewall

  - Niektóre usługi platformy Azure są wdrażane do dedykowanych podsieci. Zasady punktu końcowego są w trakcie okresu zapoznawczego zablokowane we wszystkich takich usługach (wymienionych poniżej). 

     - Środowisko App Service Environment platformy Azure
     - Azure Rediscache
     - Usługa Azure API Management
     - Wystąpienie zarządzane Azure SQL
     - Azure Active Directory Domain Services
     - Azure Application Gateway (wersja klasyczna)
     - Azure VPN Gateway (wersja klasyczna)

- Azure Storage: klasyczne konta magazynu nie są obsługiwane w zasadach punktu końcowego. Zasady będą domyślnie odrzucać dostęp do wszystkich klasycznych kont magazynu. Jeśli aplikacja potrzebuje dostępu do usługi Azure Resource Manager i klasycznych kont magazynu, zasady punktu końcowego nie powinny być stosowane dla tego ruchu. 

## <a name="nsgs-with-service-endpoint-policies"></a>Sieciowe grupy zabezpieczeń z zasadami punktu końcowego usługi
- Domyślnie sieciowe grupy zabezpieczeń zezwalają na ruch wychodzący z Internetu, w tym ruch sieci wirtualnej do usług platformy Azure.
- Jeśli chcesz odrzucić cały wychodzący ruch internetowy i zezwolić tylko na ruch do określonych zasobów usług platformy Azure: 

  Krok 1. Skonfiguruj sieciowe grupy zabezpieczeń, aby zezwolić na ruch wychodzący tylko do usług platformy Azure w regionach punktu końcowego, korzystając z *tagów usług platformy Azure*. Aby uzyskać więcej informacji, zobacz [tagi usług dla sieciowych grup zabezpieczeń](https://aka.ms/servicetags)
      
  Na przykład reguły sieciowych grup zabezpieczeń ograniczające dostęp tylko do regionów punktu końcowego wyglądają następująco:

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  Krok 2. Zastosuj zasady punktu końcowego usługi z dostępem tylko do wybranych zasobów usługi platformy Azure.

  > [!WARNING]  
  > Jeśli sieciowa grupa zabezpieczeń nie została skonfigurowana w celu ograniczania dostępu usługi platformy Azure sieci wirtualnej do regionów punktu końcowego, możesz uzyskać dostęp do zasobów usługi w innych regionach, nawet jeśli zostaną zastosowane zasady punktu końcowego.

## <a name="scenarios"></a>Scenariusze

- **Sieci wirtualne połączone równorzędnie, połączone sieci wirtualne lub wiele sieci wirtualnych**: aby odfiltrować ruch w połączonych równorzędnie sieciach wirtualnych, należy zastosować zasady punktu końcowego indywidualnie do tych sieci wirtualnych.
- **Filtrowanie ruchu internetowego przy użyciu urządzeń sieciowych lub usługi Azure Firewall**: filtruj ruch w usłudze platformy Azure przy użyciu zasad w punktach końcowych oraz filtruj pozostałą część ruchu internetowego lub na platformie Azure za pośrednictwem urządzeń lub usługi Azure Firewall. 
- **Filtrowanie ruchu w usługach platformy Azure wdrożonych w sieciach wirtualnych**: w trakcie okresu zapoznawczego zasady punktu końcowego usługi nie są obsługiwane dla żadnych zarządzanych usług platformy Azure wdrożonych w sieci wirtualnej. 
 Aby uzyskać informacje o konkretnych usługach, zobacz [ograniczenia](#limitations).
- **Filtrowanie ruchu do usług platformy Azure ze środowiska lokalnego**: zasady punktu końcowego usługi mają zastosowanie tylko do ruchu z podsieci związanych z zasadami. Aby umożliwić dostęp do konkretnych zasobów usługi platformy Azure ze środowisk lokalnych, ruch należy filtrować przy użyciu wirtualnych urządzeń sieciowych lub zapór.

## <a name="logging-and-troubleshooting"></a>Rejestrowanie i rozwiązywanie problemów
Nie ma dostępnego centralnego rejestrowania dla zasad punktu końcowego usługi. W przypadku dzienników diagnostycznych usługi zobacz [Rejestrowanie punktów końcowych usługi](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Scenariusze rozwiązywania problemów
- Dostęp dozwolony do kont magazynu niewymienionych w zasadach punktu końcowego
  - Sieciowe grupy zabezpieczeń mogą zapewnić dostęp do Internetu lub kont usługi Azure Storage w innych regionach.
  - Sieciowe grupy zabezpieczeń należy skonfigurować tak, aby zablokować cały wychodzący ruch internetowy i zezwalać tylko na ruch do konkretnych regionów usługi Azure Storage. Aby uzyskać szczegółowe informacje, zobacz „Sieciowe grupy zabezpieczeń”.
- Odmowa dostępu dla kont wymienionych w zasadach punktu końcowego
  - Filtrowanie przez sieciowe grupy zabezpieczeń lub zaporę może blokować dostęp
  - Jeśli usunięcie/ponowne zastosowanie zasad powoduje utratę połączenia:
    - Sprawdź, czy usługa platformy Azure jest skonfigurowana tak, aby umożliwiać dostęp z sieci wirtualnej przez punkty końcowe oraz czy domyślne zasady dla zasobu są ustawione na opcję *Zezwalaj na wszystko*.
      > [!NOTE]      
      > Zasoby usługi nie muszą być zabezpieczone względem sieci wirtualnych, aby uzyskać dostęp za pośrednictwem zasad punktu końcowego. Jednak w ramach najlepszego rozwiązania z zakresu zabezpieczeń zalecamy ochronę zasobów usługi względem zaufanych sieci, np. sieci wirtualnych platformy Azure, za pośrednictwem punktów końcowych usługi oraz lokalnie, za pośrednictwem zapory IP.
  
    - Sprawdź, czy diagnostyka usługi pokazuje ruch przez punkty końcowe.
    - Sprawdź, czy dzienniki przepływu sieciowej grupy zabezpieczeń oraz dzienniki magazynu pokazują oczekiwany dostęp za pośrednictwem punktów końcowych usługi.
    - Skontaktuj się z pomocą techniczną platformy Azure.
- Odmowa dostępu dla kont niewymienionych w zasadach punktu końcowego usługi
  - Filtrowanie przez sieciowe grupy zabezpieczeń lub przez zaporę może blokować dostęp. Upewnij się, że tag usługi *Azure Storage* jest dozwolony w regionach punktu końcowego. Aby uzyskać informacje o ograniczeniach zasad, zobacz [ograniczenia](#limitations).
  Jeśli na przykład zostaną zastosowane zasady, nastąpi odmowa dostępu dla klasycznych kont magazynu.
  - Sprawdź, czy usługa platformy Azure jest skonfigurowana tak, aby umożliwiać dostęp z sieci wirtualnej przez punkty końcowe, oraz czy domyślne zasady dla zasobu są ustawione na opcję *Zezwalaj na wszystko*.

## <a name="provisioning"></a>Inicjowanie obsługi

Zasady punktu końcowego usługi można konfigurować w podsieciach — czynność tę może wykonać użytkownik z uprawnieniami do zapisu w sieci wirtualnej. Dowiedz się więcej na temat [wbudowanych ról](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) na platformie Azure i przypisywania określonych uprawnień do [ról niestandardowych](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji, a także dzierżaw usługi Azure Active Directory. 

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

