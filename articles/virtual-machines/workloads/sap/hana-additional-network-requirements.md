---
title: Dodatkowe wymagania sieciowe dla systemu SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Dodatkowe wymagania sieciowe dla usługi SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 66bbd8b462ac35756be0fae6eba940ba0aba6c4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614582"
---
# <a name="additional-network-requirements-for-large-instances"></a>Dodatkowe wymagania sieciowe dla dużych wystąpień

Może być dodatkowe wymagania sieciowe w ramach wdrażania dużych wystąpień SAP HANA na platformie Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Dodawanie większej liczby adresów IP lub podsieci

Podczas dodawania większej liczby adresów IP lub podsieci należy używać witryny Azure portal, PowerShell lub interfejsu wiersza polecenia platformy Azure.

Dodaj nowy zakres adresów IP jako nowy zakres do przestrzeni adresów sieci wirtualnej, zamiast generować nowy zagregowany zakres. Prześlij tę zmianę do firmy Microsoft. Dzięki temu można połączyć się z tego nowego zakresu adresów IP do jednostek dużych wystąpień HANA w kliencie. Możesz otworzyć żądanie pomocy technicznej platformy Azure, aby uzyskać nową przestrzeń adresową sieci wirtualnej. Po otrzymaniu potwierdzenia wykonaj kolejne kroki.

Aby utworzyć dodatkową podsieć z witryny Azure portal, zobacz [Tworzenie sieci wirtualnej przy użyciu portalu Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Aby utworzyć go z programu PowerShell, zobacz [Tworzenie sieci wirtualnej przy użyciu programu PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Dodawanie sieci wirtualnych

Po początkowym połączeniu jednej lub więcej sieci wirtualnych platformy Azure można połączyć dodatkowe sieci, które uzyskują dostęp do usługi SAP HANA na platformie Azure (duże wystąpienia). Najpierw prześlij żądanie pomocy technicznej platformy Azure. W tym żądaniu dołącz szczegółowe informacje identyfikujące określone wdrożenie platformy Azure. Obejmują również zakres przestrzeni adresowej IP lub zakresy przestrzeni adresowej sieci wirtualnej platformy Azure. Sap HANA w zarządzania usługami firmy Microsoft udostępnia następnie niezbędne informacje potrzebne do połączenia dodatkowych sieci wirtualnych i usługi Azure ExpressRoute. Dla każdej sieci wirtualnej potrzebny jest unikatowy klucz autoryzacji do łączenia się z obwodem usługi ExpressRoute z dużymi wystąpieniami HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Zwiększenie przepustowości obwodu usługi ExpressRoute

Skonsultuj się z SAP HANA w zakresie zarządzania usługami firmy Microsoft. Jeśli zalecają zwiększenie przepustowości usługi SAP HANA na platformie Azure (duże wystąpienia) obwód usługi ExpressRoute, utwórz żądanie pomocy technicznej platformy Azure. (Można zażądać zwiększenia przepustowości pojedynczego obwodu do maksymalnie 10 Gb/s). Następnie otrzymasz powiadomienie po zakończeniu operacji; nie musisz nic robić, aby włączyć tę wyższą szybkość na platformie Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Dodawanie dodatkowego obwodu usługi ExpressRoute

Skonsultuj się z SAP HANA w zakresie zarządzania usługami firmy Microsoft. Jeśli zaleci dodanie dodatkowego obwodu usługi ExpressRoute, utwórz żądanie pomocy technicznej platformy Azure (w tym żądanie uzyskania informacji o autoryzacji w celu połączenia się z nowym obwodem). Przed złożeniem żądania należy zdefiniować przestrzeń adresową używaną w sieciach wirtualnych. SAP HANA w zarządzania usługami firmy Microsoft może następnie zapewnić autoryzację.

Po utworzeniu nowego obwodu i zakończeniu konfiguracji SAP HANA w usłudze Microsoft Service Management zostanie wyświetlone powiadomienie z informacjami, które należy wykonać. Nie można połączyć sieci wirtualnych platformy Azure z tym dodatkowym obwodem, jeśli są one już połączone z innym obwodem sap HANA na platformie Azure (duże wystąpienie) obwód usługi ExpressRoute w tym samym regionie platformy Azure.

## <a name="delete-a-subnet"></a>Usuwanie podsieci

Aby usunąć podsieć sieci wirtualnej, można użyć witryny Azure portal, PowerShell lub interfejsu wiersza polecenia platformy Azure. Jeśli zakres adresów IP sieci wirtualnej platformy Azure lub przestrzeni adresowej był zakresem zagregowanym, nie ma żadnych działań następczych w firmie Microsoft. (Należy jednak zauważyć, że sieć wirtualna nadal propaguje przestrzeń adresową trasy BGP, która zawiera usuniętą podsieć). Być może zdefiniowano zakres adresów sieci wirtualnej platformy Azure lub przestrzeń adresową jako wiele zakresów adresów IP, z których jeden został przypisany do usuniętej podsieci. Pamiętaj, aby usunąć to z przestrzeni adresowej sieci wirtualnej. Następnie poinformuj SAP HANA w usłudze Microsoft Service Management, aby usunąć go z zakresów, z którymi sap HANA na platformie Azure (duże wystąpienia) mogą się komunikować.

Aby uzyskać więcej informacji, zobacz [Usuwanie podsieci](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Usuwanie sieci wirtualnej

Aby uzyskać więcej informacji, zobacz [Usuwanie sieci wirtualnej](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

Sap HANA w zarządzania usługami firmy Microsoft usuwa istniejące autoryzacje w usłudze SAP HANA na platformie Azure (duże wystąpienia) obwód usługi ExpressRoute. Usuwa również zakres adresów IP sieci wirtualnej platformy Azure lub przestrzeń adresową dla komunikacji z dużymi wystąpieniami HANA.

Po usunięciu sieci wirtualnej otwórz żądanie pomocy technicznej platformy Azure, aby zapewnić zakres przestrzeni adresowej IP lub zakresy do usunięcia.

Aby upewnić się, że usuniesz wszystko, usuń połączenie Usługi ExpressRoute, bramę sieci wirtualnej, publiczny adres IP bramy sieci wirtualnej i sieć wirtualną.

## <a name="delete-an-expressroute-circuit"></a>Usuwanie obwodu usługi ExpressRoute

Aby usunąć dodatkowe SAP HANA na platformie Azure (duże wystąpienia) obwód usługi ExpressRoute, otwórz żądanie pomocy technicznej platformy Azure z SAP HANA w zarządzania usługami firmy Microsoft. Zażądaj usunięcia obwodu. W ramach subskrypcji platformy Azure można usunąć lub zachować sieć wirtualną, w razie potrzeby. Należy jednak usunąć połączenie między obwodem usługi ExpressRoute dużych wystąpień HANA a stacją połączonej sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

- [Jak zainstalować i skonfigurować oprogramowanie SAP HANA (duże wystąpienia) na platformie Azure](hana-installation.md)
