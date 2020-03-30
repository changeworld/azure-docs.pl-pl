---
title: Instalacja sap HANA na maszynach wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Przewodnik dotyczący instalacji systemu SAP HANA na maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123348"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Instalacja systemu SAP HANA na maszynach wirtualnych platformy Azure
## <a name="introduction"></a>Wprowadzenie
Ten przewodnik pomaga wskazać odpowiednie zasoby do wdrożenia hana na maszynach wirtualnych platformy Azure pomyślnie. Ten przewodnik będzie wskazywać zasoby dokumentacji, które należy sprawdzić przed zainstalowaniem sap HANA w maszynie Wirtualnej platformy Azure. Dzięki temu można wykonać odpowiednie kroki, aby zakończyć się obsługiwaną konfiguracją sap HANA na maszynach wirtualnych platformy Azure.  

> [!NOTE]
> W tym przewodniku opisano wdrożenia usługi SAP HANA na maszynach wirtualnych platformy Azure. Aby uzyskać informacje na temat wdrażania sap HANA w dużych wystąpieniach HANA, zobacz [Jak zainstalować i skonfigurować SAP HANA (Duże wystąpienia) na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation).
 
## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku założono również, że jesteś zaznajomiony z:
* SAP HANA i SAP NetWeaver i jak zainstalować je lokalnie.
* Jak zainstalować i obsługiwać wystąpienia aplikacji SAP HANA i SAP na platformie Azure.
* Pojęcia i procedury udokumentowane w:
   * Planowanie wdrożenia SAP na platformie Azure, która obejmuje planowanie sieci wirtualnej platformy Azure i użycie usługi Azure Storage. Zobacz [SAP NetWeaver na maszynach wirtualnych platformy Azure — przewodnik po planowaniu i implementacji](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Zasady wdrażania i sposoby wdrażania maszyn wirtualnych na platformie Azure. Zobacz [wdrażanie maszyn wirtualnych platformy Azure dla sap](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * Pojęcia o wysokiej dostępności dla systemu SAP HANA udokumentowane w [wysokiej dostępności sap HANA dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)

## <a name="step-by-step-before-deploying"></a>Krok po kroku przed wdrożeniem
W tej sekcji zostaną wyświetlone różne kroki, które należy wykonać przed rozpoczęciem instalacji usługi SAP HANA na maszynie wirtualnej platformy Azure. Kolejność jest wyliczona i jako takie powinny być następnie przez zgodnie z wyliczeniami:

1. Nie wszystkie możliwe scenariusze wdrażania są obsługiwane na platformie Azure. W związku z tym należy sprawdzić obciążenie SAP dokumentu [na maszynie wirtualnej platformy Azure obsługiwane scenariusze](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) dla scenariusza masz na uwadze z wdrożenia SAP HANA. Jeśli scenariusza nie ma na liście, należy założyć, że nie został przetestowany i w rezultacie nie jest obsługiwany
2. Zakładając, że masz przybliżony pomysł na wymagania dotyczące pamięci dla wdrożenia SAP HANA, należy znaleźć odpowiednie maszyny Wirtualnej platformy Azure. Nie wszystkie maszyny wirtualne, które są certyfikowane dla SAP NetWeaver, zgodnie z dokumentami w [#1928533 wsparcia SAP,](https://launchpad.support.sap.com/#/notes/1928533)posiadają certyfikat SAP HANA. Źródłem prawdy dla maszyn wirtualnych platformy Azure z certyfikatem SAP HANA jest [katalog sprzętu SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)na stronie internetowej. Jednostki rozpoczynające się od **S** są [jednostkami dużych wystąpień HANA,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) a nie maszynami wirtualnymi platformy Azure.
3. Różne typy maszyn wirtualnych platformy Azure mają różne minimalne wersje systemu operacyjnego dla SUSE Linux lub Red Hat Linux. Na stronie internetowej [SAP HANA katalog sprzętu](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), należy kliknąć na wpis na liście jednostek certyfikowanych SAP HANA, aby uzyskać szczegółowe dane tego urządzenia. Oprócz obsługiwanego obciążenia HANA, wymienione są wersje systemu operacyjnego obsługiwane przez te jednostki dla SAP HANA
4. W przypadku wersji systemu operacyjnego należy wziąć pod uwagę pewne minimalne wersje jądra. Te minimalne wersje są udokumentowane w tych uwagach do obsługi SAP:
    - [Uwaga pomocy technicznej SAP #2814271 sap HANA Backup kończy się niepowodzeniem na platformie Azure z błędem sumy kontrolnej](https://launchpad.support.sap.com/#/notes/2814271)
    - [Uwaga dotycząca obsługi SAP #2753418 potencjalnej degradacji wydajności z powodu rezerwy czasomierza](https://launchpad.support.sap.com/#/notes/2753418)
    - [Uwaga dotycząca pomocy technicznej SAP #2791572 pogorszenie wydajności z powodu braku obsługi funkcji VDSO dla funkcji Hyper-V na platformie Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. Na podstawie wersji systemu operacyjnego, który jest obsługiwany dla wybranego typu maszyny wirtualnej, należy sprawdzić, czy żądana wersja SAP HANA jest obsługiwana z tej wersji systemu operacyjnego. Przeczytaj [notatkę o pomocy technicznej SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) macierzy obsługi wersji SAP HANA z różnymi wersjami systemu operacyjnego.
5. Ponieważ być może znaleziono prawidłową kombinację typu maszyny Wirtualnej platformy Azure, wersji systemu operacyjnego i wersji SAP HANA, musisz zaewidencjonować macierz dostępności produktów SAP. W macierzy dostępności SAP można dowiedzieć się, czy produkt SAP, który chcesz uruchomić w bazie danych SAP HANA, jest obsługiwany.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Zagadnienia dotyczące wdrażania maszyny Wirtualnej i systemu operacyjnego gościa krok po kroku
W tej fazie należy przejść przez kroki wdrażania maszyn wirtualnych, aby zainstalować hana i ostatecznie zoptymalizować wybrany system operacyjny po instalacji.

1. Wybierz obraz podstawowy z galerii platformy Azure. Jeśli chcesz utworzyć własny obraz systemu operacyjnego dla SAP HANA, musisz znać wszystkie różne pakiety, które są niezbędne do pomyślnej instalacji SAP HANA. W przeciwnym razie zaleca się przy użyciu obrazów SUSE i Red Hat dla SAP lub SAP HANA z galerii obrazów platformy Azure. Obrazy te zawierają pakiety niezbędne do pomyślnej instalacji HANA. Na podstawie umowy pomocy technicznej z dostawcą systemu operacyjnego należy wybrać obraz, w którym można przynieść własną licencję. Możesz też wybrać obraz systemu operacyjnego, który zawiera obsługę
2. Jeśli wybrano obraz systemu operacyjnego gościa, który wymaga przywiezienia własnej licencji, musisz zarejestrować obraz systemu operacyjnego w ramach subskrypcji, aby można było pobrać i zastosować najnowsze poprawki. Ten krok będzie wymagał publicznego dostępu do Internetu. Chyba że skonfigurować wystąpienie prywatne, na przykład, serwer SMT na platformie Azure.
3. Zdecyduj o konfiguracji sieci maszyny Wirtualnej. Więcej informacji można znaleźć w dokumencie [Konfiguracje i operacje infrastruktury SAP HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Należy pamiętać, że nie ma żadnych przydziałów przepływności sieci, które można przypisać do kart sieci wirtualnej na platformie Azure. W rezultacie jedynym celem kierowania ruchu za pośrednictwem różnych vNIC jest na podstawie względów bezpieczeństwa. Ufamy, że znajdziesz możliwy do znalezienia kompromisu między złożonością routingu ruchu za pośrednictwem wielu kontrolerów wirtualnych a wymaganiami wymuszanymi przez aspekty zabezpieczeń.
3. Zastosuj najnowsze poprawki do systemu operacyjnego po wdrożeniu i zarejestrowaniu maszyny Wirtualnej. Zarejestrowany albo z własnej subskrypcji. Lub w przypadku, gdy wybrano obraz, który zawiera system operacyjny obsługuje maszynę wirtualną powinien mieć już dostęp do poprawek. 
4. Zastosuj melodie niezbędne dla SAP HANA. Te melodie są wymienione w tych uwagach do obsługi SAP:

    - [Uwaga dotycząca obsługi SAP #2694118 — dodatek Red Hat Enterprise Linux HA na platformie Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [Uwaga dotycząca obsługi sap #1984787 - SUSE LINUX Enterprise Server 12: Uwagi dotyczące instalacji](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Uwaga dotycząca obsługi sap #2578899 - SUSE Linux Enterprise Server 15: Uwaga dotycząca instalacji](https://launchpad.support.sap.com/#/notes/2578899)
    - [Uwaga dotycząca obsługi sap #2002167 - Red Hat Enterprise Linux 7.x: Instalacja i uaktualnienie](https://launchpad.support.sap.com/#/notes/0002002167)
    - [Uwaga dotycząca obsługi sap #2292690 - SAP HANA DB: Zalecane ustawienia systemu operacyjnego dla RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [Uwaga dotycząca obsługi SAP #2772999 - Red Hat Enterprise Linux 8.x: Instalacja i konfiguracja](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [Uwaga dotycząca obsługi sap #2777782 - SAP HANA DB: Zalecane ustawienia systemu operacyjnego dla RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [Uwaga dotycząca obsługi SAP #2455582 - Linux: Uruchamianie aplikacji SAP skompilowanych z GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Uwaga dotycząca obsługi SAP #2382421 — optymalizacja konfiguracji sieci na poziomie HANA i OS](https://launchpad.support.sap.com/#/notes/2382421)

1. Wybierz typ magazynu platformy Azure dla systemu SAP HANA. W tym kroku należy zdecydować się na układ magazynu dla instalacji SAP HANA. Będziesz używać dołączonych dysków platformy Azure lub natywnych udziałów usługi Azure NFS. Typy magazynu platformy Azure, które lub obsługiwane i kombinacje różnych typów magazynu platformy Azure, które mogą być używane, są udokumentowane w [konfiguracjach magazynu maszyn wirtualnych platformy SAP HANA Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) Weź konfiguracje udokumentowane jako punkt wyjścia. W przypadku systemów nieprodukcyjnych można skonfigurować niższą przepływność lub we/wy. Do celów produkcyjnych może być konieczne skonfigurowanie nieco większej przepływności i we/wy.
2. Upewnij się, że skonfigurowano [akcelerator zapisu azure](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) dla woluminów, które zawierają dzienniki transakcji DBMS lub ponownie dzienniki podczas korzystania z maszyn wirtualnych serii M lub Mv2 serii. Należy pamiętać o ograniczeniach akceleratora zapisu zgodnie z udokumentowanymi.
2. Sprawdź, czy [platforma Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona na wdrożonych maszynach wirtualnych.

> [!NOTE]
> Nie wszystkie polecenia w różnych profilach sap-tune lub zgodnie z opisem w notatkach mogą działać pomyślnie na platformie Azure. Polecenia, które manipulowałyby trybem zasilania maszyn wirtualnych zwykle zwracają z błędem, ponieważ nie można manipulować trybem zasilania bazowego sprzętu hosta platformy Azure.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Przygotowania krok po kroku specyficzne dla maszyn wirtualnych platformy Azure
Jedną z specyfiki platformy Azure jest instalacja rozszerzenia maszyny Wirtualnej platformy Azure, która dostarcza dane monitorowania dla agenta hosta SAP. Szczegóły dotyczące instalacji tego rozszerzenia monitorowania są udokumentowane w:

-  [Uwaga SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) omawia ulepszone monitorowanie SAP za pomocą maszyn wirtualnych z systemem Linux na platformie Azure 
-  [Uwaga SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) omawia informacje o SAPOSCOL w systemie Linux 
-  [Uwaga SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) omawia metryki monitorowania kluczy dla sap na platformie Microsoft Azure
-  [Wdrożenie maszyn wirtualnych platformy Azure dla usługi SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Instalacja SAP HANA
Po wdrożeniu maszyn wirtualnych platformy Azure i zarejestrowanych i skonfigurowanych systemach operacyjnych można zainstalować sap HANA zgodnie z instalacją SAP. Na dobry początek, aby dostać się do tej dokumentacji, zacznij od tej strony internetowej SAP [zasobów HANA](https://www.sap.com/products/hana/implementation/resources.html)

W przypadku konfiguracji skalowania w poziomie SAP HANA przy użyciu bezpośrednio dołączonych dysków usługi Azure Premium Storage lub Ultra można zapoznać się z szczegółowymi ustawieniami i operacjami w dokumencie [SAP HANA infrastructure configurations and operations na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Dodatkowe zasoby dla kopii zapasowej SAP HANA
Aby uzyskać informacje na temat sposobu utworzenia kopii zapasowej baz danych SAP HANA na maszynach wirtualnych platformy Azure, zobacz:
* [Przewodnik po kopiach zapasowych dla systemu SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Sap HANA Azure Backup na poziomie pliku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>Następne kroki
Przeczytaj dokumentację:

- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





