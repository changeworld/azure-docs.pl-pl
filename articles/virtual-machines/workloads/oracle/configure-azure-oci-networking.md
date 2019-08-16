---
title: Łączenie platformy Azure ExpressRoute z usługą Oracle Cloud Infrastructure | Microsoft Docs
description: Łączenie platformy Azure ExpressRoute z usługą Oracle Cloud Infrastructure (OCI) FastConnect w celu włączenia rozwiązań aplikacji Oracle dla wielu chmur
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2019
ms.author: rogirdh
ms.openlocfilehash: 2cab129556dcd0407793512f41f91311804abd0f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510216"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Skonfiguruj bezpośrednie połączenie między platformą Azure a infrastrukturą chmurową Oracle  

Aby utworzyć [zintegrowane środowisko z obsługą chmury](oracle-oci-overview.md) (wersja zapoznawcza), firma Microsoft i oprogramowanie Oracle oferują bezpośrednie połączenia między platformą Azure i usługą Oracle Cloud Infrastructure (OCI) za pośrednictwem usług [ExpressRoute](../../../expressroute/expressroute-introduction.md) i [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Za pośrednictwem połączeń ExpressRoute i FastConnect klienci mogą mieć małe opóźnienia, wysoką przepływność i prywatną łączność bezpośrednią między dwiema chmurami.

> [!IMPORTANT]
> Połączenie między Microsoft Azure i OCI jest na etapie wersji zapoznawczej. Aby włączyć łączność z małymi opóźnieniami między platformą Azure a OCI, Twoja subskrypcja platformy Azure musi być wyświetlona na liście dla tej funkcji. Musisz zarejestrować się w wersji zapoznawczej, wykonując ten krótki [formularz ankiety](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu). Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail. Nie możesz korzystać z tej możliwości, dopóki nie otrzymasz wiadomości e-mail z potwierdzeniem. Możesz również skontaktować się z przedstawicielem firmy Microsoft w celu włączenia tej wersji zapoznawczej. Dostęp do funkcji wersji zapoznawczej podlega dostępności i jest ograniczony przez firmę Microsoft w ramach własnego uznania. Zakończenie ankiety nie gwarantuje dostępu. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie powinna być używana na potrzeby obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

Na poniższej ilustracji przedstawiono ogólne omówienie połączeń:

![Połączenie sieciowe między chmurami](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Aby nawiązać połączenie między platformą Azure a OCI, musisz mieć aktywną subskrypcję platformy Azure i aktywną dzierżawę OCI.

* Połączenie jest możliwe tylko wtedy, gdy lokalizacja komunikacji równorzędnej usługi Azure ExpressRoute znajduje się w pobliżu lub w tej samej lokalizacji komunikacji równorzędnej co OCI FastConnect. Zobacz temat [ograniczenia wersji](oracle-oci-overview.md#preview-limitations)zapoznawczej.

* Twoja subskrypcja platformy Azure musi być listy dozwolonych dla tej funkcji w wersji zapoznawczej.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Konfigurowanie bezpośredniej łączności między ExpressRoute i FastConnect

1. Utwórz standardowy obwód usługi ExpressRoute w ramach subskrypcji platformy Azure w grupie zasobów. 
    * Podczas tworzenia ExpressRoute wybierz pozycję **Oracle Cloud FastConnect** jako dostawcę usług. Aby utworzyć obwód ExpressRoute, zobacz [Przewodnik krok po kroku](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Obwód usługi Azure ExpressRoute zapewnia szczegółowe opcje przepustowości, a FastConnect obsługuje 1, 2, 5 lub 10 GB/s. W związku z tym zalecane jest wybranie jednej z tych opcji dopasowania przepustowości w obszarze ExpressRoute.

    ![Utwórz obwód ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Zanotuj **klucz usługi**ExpressRoute. Musisz podać klucz podczas konfigurowania obwodu FastConnect.

    ![Klucz usługi ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Opłaty za ExpressRoute będą naliczane natychmiast po zainicjowaniu obwodu usługi ExpressRoute (nawet jeśli **stan dostawcy** **nie jest zainicjowany**).

1. Wydzielenie z dwóch prywatnych przestrzeni adresowych IP/30, które nie nakładają się na przestrzeń adresową IP sieci wirtualnej platformy Azure lub sieci wirtualnej OCI. Odwołujemy się do pierwszej przestrzeni adresów IP jako podstawowej przestrzeni adresowej, a druga przestrzeń adresów IP jako pomocnicza przestrzeń adresowa. Zanotuj adresy wymagane podczas konfigurowania obwodu FastConnect.
1. Utwórz bramę dynamicznej routingu (DRG). Będzie to potrzebne podczas tworzenia obwodu FastConnect. Aby uzyskać więcej informacji, zobacz dokumentację [dynamicznej bramy routingu](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) .
1. Utwórz obwód FastConnect w ramach dzierżawy firmy Oracle. Aby uzyskać więcej informacji, zobacz [dokumentację firmy Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * W obszarze Konfiguracja FastConnect wybierz **pozycję Microsoft Azure: ExpressRoute** jako dostawca.
    * Wybierz bramę dynamicznego routingu, która została zainicjowana w poprzednim kroku.
    * Wybierz przepustowość, która ma zostać zainicjowana. W celu uzyskania optymalnej wydajności przepustowość musi być zgodna z przepustowością wybraną podczas tworzenia obwodu usługi ExpressRoute.
    * W **kluczu usługi dostawcy**Wklej klucz usługi ExpressRoute.
    * Użyj pierwszej/30 prywatnej przestrzeni adresów IP używać miejsca w poprzednim kroku dla **podstawowego adresu IP protokołu BGP** i drugiej/30 prywatnej przestrzeni adresów IP dla pomocniczego adresu IP protokołu **BGP** .
        * Przypisz pierwszy możliwy do użycia adres dla adresów IP Oracle (podstawowy i pomocniczy), a drugi adres adres IP protokołu BGP klienta (z perspektywy FastConnect). Pierwszy użyteczny adres IP to drugi adres IP w przestrzeni adresowej/30 (pierwszy adres IP jest zastrzeżony przez firmę Microsoft).
    * Kliknij przycisk **Utwórz**.
1. Dokończ łączenie FastConnect z wirtualną siecią chmurową w ramach dzierżawy Oracle za pośrednictwem bramy routingu dynamicznego przy użyciu tabeli tras.
1. Przejdź do platformy Azure i upewnij się, że **stan dostawcy** dla obwodu usługi ExpressRoute został zmieniony na zainicjowany i że Zainicjowano obsługę komunikacji równorzędnej typu **Azure Private** . Jest to wymaganie wstępne dla następujących kroków.

    ![Stan dostawcy ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Kliknij prywatną komunikację równorzędną **platformy Azure** . Szczegóły dotyczące komunikacji równorzędnej zostaną automatycznie skonfigurowane na podstawie informacji wprowadzonych podczas konfigurowania obwodu FastConnect.

    ![Ustawienia prywatnej komunikacji równorzędnej](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Łączenie sieci wirtualnej z usługą ExpressRoute

1. Utwórz sieć wirtualną i bramę sieci wirtualnej, jeśli jeszcze tego nie zrobiono. Aby uzyskać szczegółowe informacje, zobacz [Przewodnik krok po kroku](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Skonfiguruj połączenie między bramą sieci wirtualnej i obwodem usługi ExpressRoute, wykonując [skrypt Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) lub wykonując polecenie programu PowerShell w celu skonfigurowania [FastPath ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Po zakończeniu konfiguracji sieci można sprawdzić poprawność konfiguracji, klikając pozycję **Pobierz rekordy ARP** i **Pobierz tabelę tras** w bloku prywatnej komunikacji równorzędnej ExpressRoute w Azure Portal.

## <a name="automation"></a>Automatyzacja

Firma Microsoft stworzyła skrypty Terraform, aby umożliwić automatyczne wdrażanie połączeń sieciowych. Przed wykonaniem skryptów Terraform należy przeprowadzić uwierzytelnienie na platformie Azure, ponieważ wymagają one odpowiednich uprawnień w ramach subskrypcji platformy Azure. Uwierzytelnianie można wykonać przy użyciu jednostki [usługi Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) lub przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją Terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Skrypty Terraform i pokrewna dokumentacja do wdrożenia połączenia wzajemnego można znaleźć w tym [repozytorium GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitorowanie

Zainstalowanie agentów w obu chmurach umożliwia monitorowanie wydajności kompleksowej sieci za pomocą usługi Azure [Network Performance Monitor (npm)](../../../expressroute/how-to-npm.md) . Program NPM ułatwia identyfikowanie problemów z siecią i pomaga je wyeliminować.

## <a name="delete-the-interconnect-link"></a>Usuwanie linku programu InterConnect

Aby można było usunąć połączenie, należy wykonać następujące czynności w określonej kolejności. Niewykonanie tej czynności spowoduje powstanie obwodu ExpressRoute o stanie "Niepowodzenie".

1. Usuń połączenie ExpressRoute. Usuń połączenie, klikając ikonę **usuwania** na stronie połączenia. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Usuń z programu Oracle FastConnect z poziomu konsoli programu Oracle Cloud.
1. Po usunięciu obwodu programu Oracle FastConnect można usunąć obwód usługi Azure ExpressRoute.

W tym momencie proces usuwania i anulowania aprowizacji został ukończony.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat połączenia międzychmurowego między OCI i platformą Azure, zapoznaj się z [dokumentacją firmy Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Za pomocą [skryptów Terraform](https://aka.ms/azureociinterconnecttf) można wdrażać infrastrukturę dla aplikacji przeznaczonych dla programu Oracle za pośrednictwem platformy Azure i konfigurować połączenia sieciowe. 
