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
ms.openlocfilehash: 2f4c74076f2763addc1f2b971d8ea0ca29ce6fd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203979"
---
# <a name="additional-network-requirements-for-large-instances"></a>Wymagania dodatkowe sieci dla dużych wystąpień

Wymagania dodatkowe sieci może być jako część wdrożenia dużych wystąpień SAP HANA na platformie Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Dodaj więcej adresów IP lub podsieci

Po dodaniu więcej adresów IP lub podsieci, należy użyć witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Dodaj nowy zakres adresów IP jako nowy zakres do przestrzeni adresowej sieci wirtualnej, zamiast generować nowy zakres zagregowane. Prześlij tej zmiany do firmy Microsoft. Dzięki temu można nawiązać połączenia z tego nowego zakresu adresów IP jednostek duże wystąpienie oprogramowania HANA w swoim kliencie. Możesz otworzyć żądania pomocy technicznej platformy Azure można pobrać nowych przestrzeni adresowej sieci wirtualnej, dodać. Po otrzymaniu potwierdzenia, należy wykonać kolejne kroki.

Aby utworzyć dodatkowe podsieci w witrynie Azure portal, zobacz [Utwórz sieć wirtualną przy użyciu witryny Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Aby go utworzyć za pomocą programu PowerShell, zobacz [Utwórz sieć wirtualną przy użyciu programu PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Dodawanie sieci wirtualnej

Po początkowym nawiązaniu połączenia jeden lub więcej sieci wirtualnych platformy Azure, możesz chcieć połączyć także dodatkowe uzyskujących dostęp do oprogramowania SAP HANA na platformie Azure (duże wystąpienia). Najpierw należy przesłać żądanie pomocy technicznej platformy Azure. W tym żądaniu obejmują informacje identyfikujące określonego wdrożenia platformy Azure. Również obejmować zakres przestrzeni adresów IP lub zakresów przestrzeni adresowej sieci wirtualnej platformy Azure. Oprogramowanie SAP HANA na zarządzanie usługą Microsoft następnie dostarcza niezbędne informacje potrzebne do łączenia z dodatkowe sieci wirtualne i usługi Azure ExpressRoute. Dla każdej sieci wirtualnej, potrzebujesz klucza autoryzacji unikatowy połączyć się z obwodem usługi ExpressRoute do dużych wystąpień HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Zwiększenie przepustowości obwodu usługi ExpressRoute

Zapoznaj się z platformą SAP HANA w zakresie zarządzania usługą firmy Microsoft. Jeśli oni zaproponować zwiększyć przepustowość SAP HANA na obwód usługi ExpressRoute platformy Azure (duże wystąpienia), Utwórz żądanie pomocy technicznej platformy Azure. (Możesz poprosić o zwiększenie przepustowości jednego obwodu, maksymalnie do 10 GB/s). Następnie otrzymasz powiadomienie po zakończeniu operacji; nie trzeba nic robić, aby włączyć ten wyższe szybkości na platformie Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Dodaj dodatkowe obwód usługi ExpressRoute

Zapoznaj się z platformą SAP HANA w zakresie zarządzania usługą firmy Microsoft. Jeśli one więcej informacji można dodać dodatkowe obwodu usługi ExpressRoute, należy utworzyć żądanie pomocy technicznej platformy Azure (w tym żądania, aby uzyskać informacje dotyczące autoryzacji, aby nawiązać połączenie z nowego obwodu). Przed wykonaniem żądania, należy zdefiniować przestrzeni adresowej używany w sieciach wirtualnych. Oprogramowanie SAP HANA na zarządzanie usługami firmy Microsoft mogą udzielić im autoryzacji.

Po utworzeniu nowego obwodu, i SAP HANA w konfiguracji zarządzania usługami firmy Microsoft jest pełna, otrzymasz powiadomienie o informacje potrzebne do kontynuowania. Nie jest możliwe połączyć sieci wirtualne platformy Azure ten obwód dodatkowe, jeśli jesteś podłączony do innego oprogramowania SAP HANA na obwód usługi ExpressRoute platformy Azure (duże wystąpienie), w tym samym regionie platformy Azure.

## <a name="delete-a-subnet"></a>Usuwanie podsieci

Aby usunąć podsieć sieci wirtualnej, można użyć witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Usługa Azure virtual network przestrzenią adresów IP zakresu lub adres został zagregowanych zakresu, czy nie wykonaj dla Ciebie w firmie Microsoft. (Należy jednak pamiętać, że sieć wirtualna jest nadal propagowanie przestrzeni adresowej trasy protokołu BGP, która obejmuje usunięto podsieć.) Może być sieci wirtualnej platformy Azure zakres lub adres przestrzeni adresowej zdefiniowane jako wielu zakresów adresów IP, z których jeden przypisano usunięto podsieci. Pamiętaj usunąć który przestrzeni adresowej sieci wirtualnej. Następnie informuje oprogramowanie SAP HANA na zarządzanie usługami firmy Microsoft do usuwania zakresów, które może komunikować się z oprogramowania SAP HANA na platformie Azure (duże wystąpienia).

Aby uzyskać więcej informacji, zobacz [usuwanie podsieci](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Usuwanie sieci wirtualnej

Aby uzyskać informacje, zobacz [usunąć sieci wirtualnej](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

Oprogramowanie SAP HANA na zarządzanie usługą Microsoft usuwa istniejące zezwolenia na platformie SAP HANA na obwód usługi ExpressRoute platformy Azure (duże wystąpienia). Usuwa również sieć wirtualną platformy Azure przestrzeń adresów IP zakresu lub adres do komunikacji z dużymi wystąpieniami platformy HANA.

Po usunięciu sieć wirtualną, otwórz żądanie pomocy technicznej platformy Azure do zakresu przestrzeni adresów IP lub zakresu do usunięcia.

Aby upewnić się, że wszystko, co usunąć, Usuń połączenia ExpressRoute, Brama sieci wirtualnej, sieci wirtualnej brama publicznego adresu IP i sieci wirtualnej.

## <a name="delete-an-expressroute-circuit"></a>Usuwanie obwodu usługi ExpressRoute

Aby usunąć dodatkowe oprogramowanie SAP HANA na obwód usługi ExpressRoute platformy Azure (duże wystąpienia), otwórz żądanie pomocy technicznej platformy Azure przy użyciu oprogramowania SAP HANA na zarządzanie usługami firmy Microsoft. Żądanie usunięcia obwodu. W ramach subskrypcji platformy Azure możesz usunąć lub zachować sieci wirtualnej, zgodnie z potrzebami. Jednakże należy usunąć połączenie między obwodu usługi ExpressRoute dużych wystąpień HANA i bramy sieci wirtualnej połączonej.

## <a name="next-steps"></a>Kolejne kroki

- [Jak zainstalować i skonfigurować oprogramowanie SAP HANA (duże wystąpienia) na platformie Azure](hana-installation.md)
