---
title: Łączenie usługi Azure ExpressRoute przy użyciu infrastruktury Chmurowej Oracle | Dokumentacja firmy Microsoft
description: Łączenie usługi Azure ExpressRoute z FastConnect Oracle chmurze infrastruktury (OCI), aby włączyć rozwiązania aplikacji Oracle wielu chmur
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
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: 671d7c8eb9f10e346b49056e1cc117c9882bb6e8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707571"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Konfigurowanie bezpośredniego połączenia między platformą Azure i infrastruktury chmury programu Oracle  

Aby utworzyć [to dzięki zintegrowanemu środowisku z wieloma chmurami](oracle-oci-overview.md) (wersja zapoznawcza) firmy Microsoft i Oracle oferuje bezpośrednie połączenie między platformą Azure i infrastruktury chmury Oracle (OCI) za pośrednictwem [ExpressRoute](../../../expressroute/expressroute-introduction.md) i [ FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Za pośrednictwem połączenia usługi ExpressRoute i FastConnect klienci mogą występować małe opóźnienia, wysoką przepływnością, prywatne połączenie bezpośrednie między dwoma chmury.

> [!IMPORTANT]
> Połączenie między usługą Microsoft Azure i OCI jest na etapie wersji zapoznawczej. Aby włączyć łączność małe opóźnienia między platformą Azure i OCI, subskrypcja platformy Azure i dzierżawców OCI najpierw należy się na białej liście dla tej funkcji.

Na poniższej ilustracji przedstawiono ogólne omówienie wzajemne połączenie:

![Połączenie sieciowe między chmurą](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Aby ustanowić łączność między platformą Azure i OCI, konieczne jest posiadanie aktywnej subskrypcji platformy Azure i aktywne dzierżawy OCI.

* Połączenie jest możliwe tylko gdzie lokalizacji komunikacji równorzędnej usługi ExpressRoute systemu Azure jest w sąsiedztwie lub w tej samej lokalizacji komunikacji równorzędnej, OCI FastConnect. Zobacz [ograniczenia wersji zapoznawczej](oracle-oci-overview.md#preview-limitations).

* Twoja subskrypcja platformy Azure musi być umieszczona na białej liście dla tej funkcji w wersji zapoznawczej. Skontaktuj się z przedstawicielem firmy Microsoft, aby włączyć tę funkcję w ramach Twojej subskrypcji.

* Twoje dzierżawy OCI musi być umieszczona na białej liście dla tej funkcji w wersji zapoznawczej. Aby uzyskać szczegółowe informacje, skontaktuj się z przedstawicielem handlowym firmy Oracle.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Konfigurowanie bezpośrednich połączeń między połączeń usługi ExpressRoute i FastConnect

1. Utwórz standardowy obwód usługi ExpressRoute w subskrypcji platformy Azure w ramach grupy zasobów. 
    * Podczas tworzenia usługi ExpressRoute, wybierz **Oracle chmurze FastConnect** jako dostawcy usług. Aby utworzyć obwód usługi ExpressRoute, zobacz [przewodnik krok po kroku](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Obwód usługi ExpressRoute systemu Azure udostępnia opcje szczegółowe przepustowości, natomiast FastConnect obsługuje 1, 2, 5 lub 10 GB/s. W związku z tym zalecane jest wybranie jednej z tych zgodnych opcji przepustowości w ramach usługi ExpressRoute.

    ![Utwórz obwód usługi ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Zanotuj usługi ExpressRoute **klucza usługi**. Musisz podać klucz podczas konfigurowania FastConnect obwodu.

    ![Klucz usługi ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Opłata zostanie naliczona na opłaty za usługę ExpressRoute tak szybko, jak obwód usługi ExpressRoute jest obsługiwany (nawet jeśli **stan dostawcy** jest **nie zainicjowano obsługi administracyjnej**).

1. Wydzielenie przez niego dwóch prywatnych przestrzeni adresów IP/30, każdy, nie pokrywają się z siecią wirtualną platformy Azure lub sieci wirtualnej chmury OCI przestrzeń adresów IP. Będziemy nazywać pierwszą przestrzeń adresową IP jako podstawowej przestrzeni adresów i drugi przestrzeń adresów IP jako dodatkowej przestrzeń adresowa. Zanotuj adresów, które będą potrzebne podczas konfigurowania FastConnect obwodu.
1. Tworzenie bramy o dynamicznym routingu (DRG). Będzie on potrzebny podczas tworzenia FastConnect obwodu. Aby uzyskać więcej informacji, zobacz [bramą routingu dynamicznego](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) dokumentacji.
1. Utwórz obwód FastConnect w dzierżawie programu Oracle. Aby uzyskać więcej informacji, zobacz [dokumentacji Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * W obszarze Konfiguracja FastConnect wybierz **Microsoft Azure: Usługa ExpressRoute** jako dostawcy.
    * Wybierz bramą routingu dynamicznego aprowizowana przez Ciebie w poprzednim kroku.
    * Wybierz przepustowości do udostępnienia. Aby uzyskać optymalną wydajność przepustowości musi być zgodna przepustowości wybrane podczas tworzenia obwodu usługi ExpressRoute.
    * W **klucz usługi dostawcy**, Wklej klucz usługi ExpressRoute.
    * Użyj pierwszego/30 przestrzeń prywatnych adresów IP używać w poprzednim kroku dla **podstawowego adresu IP protokołu BGP** i drugi/30 przestrzeń prywatnych adresów IP dla **pomocniczego adresu IP protokołu BGP** adresu.
        * Przypisz pierwszego adresu użyteczny dwa zakresy dla adresu IP protokołu BGP Oracle (podstawowe i pomocnicze) i drugi adres klientowi adres IP protokołu BGP (z punktu widzenia FastConnect). Pierwszy adres IP niemożliwe jest drugi adres IP w/30 przestrzeń adresowa (pierwszy adres IP jest zastrzeżony przez firmę Microsoft).
    * Kliknij przycisk **Utwórz**.
1. Ukończ FastConnect — łączenie sieci wirtualnej chmury w ramach dzierżawy Oracle za pośrednictwem bramą routingu dynamicznego, za pomocą tabeli tras.
1. Przejdź do platformy Azure, a następnie upewnij się, że **stan dostawcy** dla usługi ExpressRoute circuit została zmieniona na **Aprowizowana** i element równorzędny typu **Azure prywatne** został Zainicjowano obsługę administracyjną. Jest to wymaganie wstępne dla następujących kroków.

    ![Stan dostawcy usługi ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Kliknij pozycję **Azure prywatne** komunikacji równorzędnej. Widoczne będą szczegóły dotyczące komunikacji równorzędnej automatycznie skonfigurowano oparte na informacjach wprowadzony przez ustawienie zapasowej FastConnect obwodu.

    ![Ustawienia komunikacji równorzędnej prywatnej](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Łączenie sieci wirtualnej usługi expressroute

1. Tworzenie sieci wirtualnej i bramy sieci wirtualnej, jeśli jeszcze go. Aby uzyskać więcej informacji, zobacz [przewodnik krok po kroku](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Konfigurowanie połączenia między bramą sieci wirtualnej i obwód usługi ExpressRoute, wykonując [skryptu Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) lub wykonując polecenie programu PowerShell, aby [Konfigurowanie usługi ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Po zakończeniu konfiguracji sieci, można sprawdzić poprawność konfiguracji, klikając **Pobierz rekordy ARP** i **tabeli tras Get** w ramach bloku komunikacji równorzędnej usługi ExpressRoute prywatnych w Witryna Azure portal.

## <a name="automation"></a>Automatyzacja

Firma Microsoft opracowała skrypty narzędzia Terraform, aby umożliwić automatycznego wdrażania połączenie sieciowe. Skrypty programu Terraform wymaga uwierzytelnienia przy użyciu platformy Azure przed realizacją, ponieważ wymagają one odpowiednie uprawnienia w ramach subskrypcji Azure. Uwierzytelnianie jest przeprowadzane przy użyciu [jednostki usługi Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) lub przy użyciu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [dokumentacja programu Terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Skrypty programu Terraform i powiązanej dokumentacji, aby wdrożyć inter-connect znajdują się w tym [repozytorium GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitorowanie

Instalowanie agentów na obu chmurach, może wykorzystać platformę Azure [Network Performance Monitor (NPM)](../../../expressroute/how-to-npm.md) do monitorowania wydajności sieci end-to-end. NPM pomaga łatwo zidentyfikować problemy z siecią, a także pomaga wyeliminować sytuację je.

## <a name="delete-the-interconnect-link"></a>Usuń łącze między połączeniami wzajemnymi

Aby usunąć połączenie, poniższe kroki musi następować, w określonej kolejności. Niewykonanie tej czynności spowoduje obwód usługi ExpressRoute "nie powiodło się stan".

1. Usuń połączenie usługi ExpressRoute. Usuń połączenie, klikając **Usuń** ikonę na stronie połączenia. Aby uzyskać więcej informacji, zobacz [dokumentacja usługi ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Usuń Oracle FastConnect z bazy danych Oracle Cloud Console.
1. Po usunięciu obwodu Oracle FastConnect można usunąć obwodu usługi ExpressRoute platformy Azure.

W tym momencie usuwania i anulowania obsługi procesu zostało ukończone.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat połączenia wielu chmur między OCI i platformą Azure, zobacz [dokumentacji Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Użyj [skrypty narzędzia Terraform](https://aka.ms/azureociinterconnecttf) wdrażanie infrastruktury dla docelowych aplikacji Oracle za pośrednictwem platformy Azure i skonfigurować połączenie sieciowe. 