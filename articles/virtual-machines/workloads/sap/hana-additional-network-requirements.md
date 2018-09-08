---
title: Wymagania dodatkowe sieci dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Wymagania dodatkowe sieci dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ee89553846309bd96f3cad3648e337488cf07d3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164732"
---
# <a name="additional-network-requirements"></a>Wymagania dodatkowe sieci

Czasami może być wymagania dodatkowe sieci podczas wdrażania dużych wystąpień HANA. W tym artykule przedstawiono następujące wymagania sieciowe:
- [Dodawanie więcej adresów IP lub podsieci ](#adding-more-ip-addresses-or-subnets)
- [Dodawanie wirtualnej vetworks](#adding-vnets)
- [Zwiększenie przepustowości obwodu expressroute](#increasing-expressroute-circuit-bandwidth)
- [Dodawanie obwód usługi expressroute dodatkowe](#adding-an-additional-expressroute-circuit)
- [Usuwanie podsieci](#deleting-a-subnet)
- [Usuwanie sieci wirtualnej](#deleting-a-vnet)
- [Usuwanie obwodu usługi express routera](#deleting-an-expressroute-circuit)


## <a name="adding-more-ip-addresses-or-subnets"></a>Dodawanie więcej adresów IP lub podsieci

Użyj witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia podczas dodawania więcej adresów IP adresów albo podsieci.

W tym przypadku zaleca się dodać nowy zakres adresów IP jako nowy zakres do przestrzeni adresowej sieci wirtualnej, zamiast generować nowy zakres zagregowane. W obu przypadkach należy przesłać tej zmiany do firmy Microsoft, aby umożliwić łączność z tego nowego zakresu adresów IP z jednostkami duże wystąpienie oprogramowania HANA w swoim kliencie. Możesz otworzyć żądania pomocy technicznej platformy Azure można pobrać nowych przestrzeni adresowej sieci wirtualnej dodane. Po otrzymaniu potwierdzenia, należy wykonać kolejne kroki.

Aby utworzyć dodatkowe podsieci w witrynie Azure portal, zobacz artykuł [Utwórz sieć wirtualną przy użyciu witryny Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)oraz aby utworzyć za pomocą programu PowerShell, zobacz [Utwórz sieć wirtualną przy użyciu programu PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Dodawanie sieci wirtualnych

Po początkowym nawiązaniu połączenia z sieciami wirtualnymi platformy Azure, można dodać dodatkowe uzyskujących dostęp do oprogramowania SAP HANA na platformie Azure (duże wystąpienia). Najpierw należy przesłać żądanie pomocy technicznej platformy Azure, w tym żądaniu zawiera zarówno określone informacje identyfikujące określonego wdrożenia platformy Azure oraz zakresy przestrzeni adresów IP przestrzeni adresowej sieci wirtualnej platformy Azure. Oprogramowanie SAP HANA na zarządzanie usługami platformy Azure następnie dostarcza niezbędne informacje potrzebne do łączenia z dodatkowe sieci wirtualne i usługi ExpressRoute. Dla każdej sieci wirtualnej należy połączyć się z obwodem usługi ExpressRoute do dużych wystąpień HANA unikatowego klucza autoryzacji.

Kroki, aby dodać nową sieć wirtualną platformy Azure:

1. Zobacz pełną pierwszym etapem procesu dołączania _tworzenie sieci wirtualnej platformy Azure_ sekcji.
2. Zobacz drugi etap procesu dołączania, pełną _Tworzenie podsieci bramy_ sekcji.
3. Aby połączyć Twoje dodatkowe sieci wirtualne z obwodem usługi ExpressRoute platformy HANA duże wystąpienie, otwórz żądanie pomocy technicznej platformy Azure przy użyciu informacji o nowej sieci wirtualnej i zażądaj nowego klucza autoryzacji.
4. Po powiadomienie, że autoryzacja została zakończona, użyj informacji o autoryzacji dostarczonych przez firmę Microsoft, aby ukończyć trzeci krok w realizacji procesu dołączania, zapoznaj się z _łączenia sieci wirtualnych_ sekcji.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Zwiększenie przepustowości obwodu usługi ExpressRoute

Zapoznaj się z platformą SAP HANA w usłudze Azure Service Management. Jeśli zaleca się zwiększać przepustowość środowiska SAP Hana na obwód usługi ExpressRoute platformy Azure (duże wystąpienia), Utwórz żądanie pomocy technicznej platformy Azure. (Możesz poprosić o zwiększenie przepustowości jednego obwodu, maksymalnie do 10 GB/s). Następnie otrzymasz powiadomienie po zakończeniu operacji; żadne dodatkowe działania wymagane do włączenia tej wyższe szybkości na platformie Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Dodawanie dodatkowych obwodu usługi ExpressRoute

Skonsultuj się z platformą SAP HANA w usłudze Azure Service Management, czy zaleca się, że potrzebne są dodatkowe obwód usługi ExpressRoute, platforma Azure obsługuje żądania, aby utworzyć nowy obwód usługi ExpressRoute (i aby uzyskać informacje dotyczące autoryzacji, aby nawiązać z nim). Przestrzeń adresowa, która będzie służyć w sieci wirtualne muszą być zdefiniowane przed wykonaniem żądania, aby oprogramowanie SAP HANA na zarządzanie usługami platformy Azure zapewniają autoryzację.

Po utworzeniu nowego obwodu i SAP HANA w konfiguracji usługi Azure Service Management zostało zakończone, którą chcesz otrzymywać powiadomienia o informacje potrzebne do kontynuowania. Wykonaj kroki podane powyżej za tworzenie i łączenie nową sieć wirtualną z tego dodatkowe obwodu. Nie jest możliwe nawiązać połączenie sieci wirtualnych platformy Azure ten obwód dodatkowe Jeśli już połączony, do innego oprogramowania SAP HANA na obwód usługi ExpressRoute platformy Azure (duże wystąpienie), w tym samym regionie platformy Azure.

## <a name="deleting-a-subnet"></a>Usuwanie podsieci

Aby usunąć podsieć sieci wirtualnej, można się witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia. W przypadku, gdy Twoje adresów IP sieci wirtualnej platformy Azure adres zakresu/Azure przestrzeni adresowej sieci wirtualnej była zagregowanych zakresu, nie ma żadnych wykonaj dla Ciebie z firmą Microsoft. Z tą różnicą, że sieć wirtualna jest nadal propagowanie przestrzeni adresowej trasy protokołu BGP, obejmującą usunięto podsieć. Jeśli adresów IP sieci wirtualnej platformy Azure adres zakresu/Azure przestrzeni adresowej sieci wirtualnej jest zdefiniowany jako wiele zakresów adresów IP, z których jeden przypisano usunięto podsieci, należy usunąć, poza przestrzeń adresowa sieci wirtualnej i następnie informuje oprogramowanie SAP HANA na zarządzanie usługami platformy Azure do Usuń go z zakresów, które może komunikować się z oprogramowania SAP HANA na platformie Azure (duże wystąpienia).

Aby usunąć podsieć, zobacz [usuwanie podsieci](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) Aby uzyskać więcej informacji na temat tworzenia podsieci.

## <a name="deleting-a-vnet"></a>Usuwanie sieci wirtualnej

Aby usunąć sieć wirtualną, zobacz [usunąć sieci wirtualnej](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network). Oprogramowanie SAP HANA na zarządzanie usługami platformy Azure spowoduje usunięcie istniejących zezwolenia na platformie SAP HANA na obwód ExpressRoute usługi platformy Azure (duże wystąpienia) i Usuń adresów IP sieci wirtualnej platformy Azure adres zakresu/Azure przestrzeni adresowej sieci wirtualnej do komunikacji z dużymi wystąpieniami platformy HANA.

Po usunięciu sieci wirtualnej, otwórz żądanie pomocy technicznej platformy Azure do zapewnienia zakresy przestrzeni adresów IP do usunięcia.

Aby upewnić się, że wszystko jest usuwany, Usuń następujące elementy:

- Połączenia usługi ExpressRoute Brama sieci wirtualnej, publiczny adres IP bramy sieci wirtualnej i sieci wirtualnej

## <a name="deleting-an-expressroute-circuit"></a>Usuwanie obwodu usługi ExpressRoute

Aby usunąć dodatkowe oprogramowanie SAP HANA na obwód usługi ExpressRoute platformy Azure (duże wystąpienia), otwórz żądanie pomocy technicznej platformy Azure przy użyciu oprogramowania SAP HANA na zarządzanie usługami platformy Azure i żądania, że należy usunąć obwód. W ramach subskrypcji platformy Azure możesz usunąć lub zachować sieci wirtualnej, zgodnie z potrzebami. Jednakże należy usunąć połączenie między HANA dużych wystąpień obwodu usługi ExpressRoute i bramy sieci wirtualnej połączonej.

Jeśli chcesz również usunąć sieci wirtualnej, zgodnie z wytycznymi na temat usuwania sieci wirtualnej w powyższej sekcji.

**Następne kroki**

- Zapoznaj się [sposobu instalowania i konfigurowania platformy SAP HANA (duże wystąpienia) na platformie Azure](hana-installation.md).
