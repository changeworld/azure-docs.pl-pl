---
title: Przykładowy przewodnik po infrastrukturze platformy Azure
description: Dowiedz się więcej o kluczowych wskazówki dotyczące projektowania i implementacji dotyczące wdrażania przykładowej infrastruktury na platformie Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 6040bf8862131f5a8a5564cd2f5d845fa0490a95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944899"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Przykładowy przewodnik po infrastrukturze platformy Azure dla maszyn wirtualnych z systemem Linux
W tym artykule o wiele więcej niż w artykule. Szczegółowo projektujemy infrastrukturę dla prostego sklepu on-line, który łączy wszystkie wytyczne i decyzje dotyczące konwencji nazewnictwa, zestawów dostępności, sieci wirtualnych i modułów równoważenia obciążenia, a także faktycznego wdrażania maszyn wirtualnych.We detailing an infrastructure for a simple on-line store that brings together all the guidelines and decision around naming conventions, availability sets, virtual networks and loadbalrs, and actually deploying your virtual machines (VMs).

## <a name="example-workload"></a>Przykładowe obciążenie pracą
Adventure Works Cycles chce utworzyć aplikację sklepu on-line na platformie Azure, która składa się z:

* Dwa serwery nginx z front-endem klienta w warstwie sieci web
* Dwa serwery nginx przetwarzają dane i zamówienia w warstwie aplikacji
* Dwa serwery MongoDB należące do podzielonego klastra do przechowywania danych produktów i zamówień w warstwie bazy danych
* Dwa kontrolery domeny usługi Active Directory dla kont klientów i dostawców w warstwie uwierzytelniania
* Wszystkie serwery znajdują się w dwóch podsieciach:
  * podsieci front-end dla serwerów sieci web 
  * podsieć zaplecza dla serwerów aplikacji, klastra MongoDB i kontrolerów domeny

![Diagram różnych warstw infrastruktury aplikacji](./media/infrastructure-example/example-tiers.png)

Przychodzący bezpieczny ruch internetowy musi być równoważący obciążenie między serwerami sieci web, ponieważ klienci przeglądają sklep on-line. Ruch przetwarzania zamówień w postaci żądań HTTP z serwerów sieci web musi być równoważenia obciążenia między serwerami aplikacji. Ponadto infrastruktura musi być zaprojektowana z myślą o wysokiej dostępności.

Powstały wzór musi zawierać:

* Subskrypcja i konto platformy Azure
* Pojedyncza grupa zasobów
* Dyski zarządzane platformy Azure
* Sieć wirtualna z dwiema podsieciami
* Zestawy dostępności dla maszyn wirtualnych o podobnej roli
* Maszyny wirtualne

Wszystkie powyższe są zgodne z tymi konwencjami nazewnictwa:

* Adventure Works Cycles używa **[obciążenia IT]-[lokalizacja]-[Zasób platformy Azure]** jako prefiksu
  * W tym przykładzie **"azos**" (Azure On-line Store) to nazwa obciążenia IT i "**use**" (East US 2) jest lokalizacją
* Sieci wirtualne używają AZOS-USE-VN<strong>[numer]</strong>
* Zestawy dostępności używają azos-use-as-**[rola]**
* Nazwy maszyn wirtualnych używają azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Subskrypcje i konta platformy Azure
Adventure Works Cycles używa subskrypcji Enterprise o nazwie Adventure Works Enterprise Subscription, aby zapewnić rozliczenia za to obciążenie IT.

## <a name="storage"></a>Magazyn
Adventure Works Cycles określa, że należy używać dysków zarządzanych platformy Azure. Podczas tworzenia maszyn wirtualnych używane są obie warstwy magazynu dostępne do magazynowania:

* **Standardowy magazyn** dla serwerów sieci web, serwerów aplikacji i kontrolerów domeny oraz ich dysków danych.
* **Magazyn w wersji premium** dla podzielonych na fragmenty serwerów klastrowych mongodb i ich dysków danych.

## <a name="virtual-network-and-subnets"></a>Sieć wirtualna i podsieci
Ponieważ sieć wirtualna nie wymaga ciągłej łączności z siecią lokalną Adventure Work Cycles, zdecydowali się na sieć wirtualną tylko w chmurze.

Utworzyli sieć wirtualną tylko w chmurze z następującymi ustawieniami przy użyciu witryny Azure portal:

* Nazwa: AZOS-USE-VN01
* Lokalizacja: Wschodnie stany USA 2
* Przestrzeń adresowa sieci wirtualnej: 10.0.0.0/8
* Pierwsza podsieć:
  * Nazwa: FrontEnd
  * Przestrzeń adresowa: 10.0.1.0/24
* Druga podsieć:
  * Nazwa: BackEnd
  * Przestrzeń adresowa: 10.0.2.0/24

## <a name="availability-sets"></a>Zestawy dostępności
Aby utrzymać wysoką dostępność wszystkich czterech poziomów ich sklepu on-line, Adventure Works Cycles zdecydowało się na cztery zestawy dostępności:

* **azos-use-as-web** dla serwerów internetowych
* **azos-use-as-app** dla serwerów aplikacji
* **azos-use-as-db** dla serwerów w klastrze podzielonym na fragmenty MongoDB
* **azos-use-as-dc** dla kontrolerów domeny

## <a name="virtual-machines"></a>Maszyny wirtualne
Adventure Works Cycles zdecydował o następujących nazwach dla swoich maszyn wirtualnych platformy Azure:

* **azos-use-vm-web01** dla pierwszego serwera www
* **azos-use-vm-web02** dla drugiego serwera www
* **azos-use-vm-app01** dla pierwszego serwera aplikacji
* **azos-use-vm-app02** dla drugiego serwera aplikacji
* **azos-use-vm-db01** dla pierwszego serwera MongoDB w klastrze
* **azos-use-vm-db02** dla drugiego serwera MongoDB w klastrze
* **azos-use-vm-dc01** dla pierwszego kontrolera domeny
* **azos-use-vm-dc02** dla drugiego kontrolera domeny

Oto wynikowa konfiguracja.

![Ostateczna infrastruktura aplikacji wdrożona na platformie Azure](./media/infrastructure-example/example-config.png)

Ta konfiguracja zawiera:

* Sieć wirtualna tylko w chmurze z dwiema podsieciami (FrontEnd i BackEnd)
* Dyski zarządzane platformy Azure przy użyciu dysków standardowych i premium
* Cztery zestawy dostępności, po jednym dla każdej warstwy sklepu on-line
* Maszyny wirtualne dla czterech warstw
* Zestaw z równoważeniem obciążenia zewnętrznego dla ruchu internetowego opartego na protokoi https z Internetu do serwerów sieci web
* Wewnętrzny zestaw z równoważeniem obciążenia dla niezaszyfrowanego ruchu sieci web z serwerów sieci web do serwerów aplikacji
* Pojedyncza grupa zasobów
