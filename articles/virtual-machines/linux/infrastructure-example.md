---
title: Przewodnik po przykładzie infrastruktury platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat klucza wytycznych w zakresie projektowania i implementowania wdrażania infrastruktury przykład na platformie Azure.
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63bdfa6f419d97696faa6545cbb1017a66cf0e2d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667533"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Przykład Przegląd infrastruktury platformy Azure dla maszyn wirtualnych systemu Linux
W tym artykule przedstawiono tworzenie infrastruktury aplikacji na przykładzie. Firma Microsoft szczegółowo projektowania infrastruktury dla prostych sklepie internetowym łączącym w sobie wszystkie wskazówki i decyzji związanych z konwencji nazewnictwa, zestawy dostępności, sieci wirtualne i usługi równoważenia obciążenia i faktycznego wdrażania maszyn wirtualnych (VM).

## <a name="example-workload"></a>Przykładowe obciążenie
Adventure Works Cycles chce, aby skompilować aplikację magazynu online na platformie Azure, która składa się z:

* Dwa serwery nginx uruchomienie frontonu w warstwie sieci web klienta
* Dwa serwery nginx, takich jak przetwarzanie danych i zamówienia w warstwie aplikacji
* Dwie bazy danych MongoDB serwerów część podzielonej na fragmenty klastra do przechowywania danych produktu i zamówienia w warstwie bazy danych
* Dwa kontrolery domeny usługi Active Directory dla kont klientów i dostawców w warstwie uwierzytelniania
* Wszystkie serwery znajdują się w dwóch podsieci:
  * podsieci frontonu dla serwerów sieci web 
  * podsieć zaplecza dla serwerów aplikacji, klaster bazy danych MongoDB i kontrolery domeny

![Diagram przedstawiający różne warstwy dla infrastruktury aplikacji](./media/infrastructure-example/example-tiers.png)

Przychodzące bezpieczny ruch w sieci web musi być równoważenia obciążenia między serwerami sieci web jako klienci Przeglądaj sklepie internetowym. Kolejność przetwarzania ruchu sieciowego w postaci HTTP żąda od serwerów musi być równoważenia obciążenia między serwerami aplikacji sieci web. Ponadto infrastruktury muszą być zaprojektowane w celu zapewnienia wysokiej dostępności.

Projekt wynikowy musi uwzględniać:

* Subskrypcja platformy Azure i konto
* Pojedyncza grupa zasobów
* Dyski zarządzane platformy Azure
* Sieć wirtualną z dwiema podsieciami
* Zestawy dostępności dla maszyn wirtualnych z podobną rolę
* Maszyny wirtualne

Wszystkie powyższe postępuj zgodnie z tymi konwencji nazewnictwa:

* Adventure Works Cycles używa **[IT obciążenia]-[lokalizacja]-[zasobów platformy Azure]** jako prefiksu
  * Na przykład "**azos**" (Azure Store On-line) jest nazwą obciążenia IT i "**użyj**" (wschodnie stany USA 2) to lokalizacja
* Sieci wirtualne używać AZOS — UŻYJ-VN<strong>[liczba]</strong>
* Zestawy dostępności Użyj azos — Użyj-jako- **[rola]**
* Nazwy maszyn wirtualnych Użyj azos — Użyj-vm - **[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Konta i subskrypcji platformy Azure
Adventure Works Cycles korzysta z ich subskrypcji Enterprise, o nazwie Adventure Works subskrypcji Enterprise, zapewnienie rozliczenia dla tego obciążenia IT.

## <a name="storage"></a>Magazyn
Adventure Works Cycles ustalić, czy powinny używać usługi Azure Managed Disks. Podczas tworzenia maszyn wirtualnych, używane są obu warstwach magazynowania w dostępnym magazynie:

* **Magazynu w warstwie standardowa** dla serwerów sieci web, serwery aplikacji i kontrolery domeny i dysków z danymi.
* **Usługa Premium storage** dla serwerów klastra podzielonej na fragmenty bazy danych MongoDB i dysków z danymi.

## <a name="virtual-network-and-subnets"></a>Sieć wirtualna i podsieci
Ponieważ sieć wirtualna nie musi trwającą łączności z siecią lokalną cyklów pracy Adventure, zespół postanowił w sieci wirtualnej tylko w chmurze.

Sieci wirtualnej tylko w chmurze są tworzone przy użyciu następujących ustawień, korzystając z portalu Azure:

* Nazwa: AZOS-USE-VN01
* Lokalizacja: Wschodnie stany USA 2
* Przestrzeń adresowa sieci wirtualnej: 10.0.0.0/8
* Pierwszej podsieci:
  * Nazwa: FrontEnd
  * Przestrzeń adresowa: 10.0.1.0/24
* Drugą podsieć:
  * Nazwa: BackEnd
  * Przestrzeń adresowa: 10.0.2.0/24

## <a name="availability-sets"></a>Zestawy dostępności
Aby zapewnić wysoką dostępność wszystkich czterech warstwach swojego Sklepu online, firma Adventure Works Cycles decyzję cztery zestawy dostępności:

* **azos użycia jako web** dla serwerów sieci web
* **azos użycia jako aplikacja** dla serwerów aplikacji
* **azos użycia jako db** dla serwerów w klastrze podzielonej na fragmenty bazy danych MongoDB
* **azos użycia jako dc** dla kontrolerów domeny

## <a name="virtual-machines"></a>Maszyny wirtualne
Adventure Works Cycles zdecydowała się na następujących nazw dla maszyn wirtualnych platformy Azure:

* **azos — Użyj vm-web01** dla pierwszego serwera sieci web
* **azos — Użyj vm-web02** drugiego serwera sieci web
* **azos — Użyj vm-app01** dla pierwszego serwera aplikacji
* **azos — Użyj vm-app02** dla drugiego serwera aplikacji
* **azos — Użyj vm-db01** dla pierwszego serwera bazy danych MongoDB w klastrze
* **azos — Użyj vm-db02** na drugim serwerze bazy danych MongoDB w klastrze
* **azos — Użyj vm-dc01** dla pierwszego kontrolera domeny
* **azos — Użyj vm-dc02** dla drugiego kontrolera domeny

Oto Wynikowa konfiguracja.

![Infrastruktura gotowych aplikacji wdrożonych na platformie Azure](./media/infrastructure-example/example-config.png)

Ta konfiguracja obejmuje:

* Tylko w chmurze sieci wirtualnej z dwiema podsieciami (frontonu i wewnętrznej bazy danych)
* Usługa Azure Managed Disks przy użyciu dysków w warstwach standardowa i Premium
* Cztery zestawy dostępności: jeden dla każdej warstwy magazynu online
* Maszyny wirtualne w czterech warstwach
* Zestawu z równoważeniem obciążenia zewnętrznych dla sieci web opartych na HTTPS ruch z Internetu do serwerów sieci web
* Wewnętrzny zestaw o zrównoważonym dla ruchu w sieci web niezaszyfrowane z serwerów sieci web na serwerach aplikacji
* Pojedyncza grupa zasobów
