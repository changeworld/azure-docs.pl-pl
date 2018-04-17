---
title: Słownik platformy Azure — słownik platformy Azure | Dokumentacja firmy Microsoft
description: Słownik Azure umożliwia zrozumieć terminologii chmurze na platformie Azure. To krótkie słownika Azure definicje typowe terminy w chmurze dla platformy Azure.
keywords: Słownik platformy Azure, terminologii chmury, słownik platformy Azure, definicje terminów, warunki chmury
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 953320734c1601c411144bd7a6496716a65e452e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Słownik Microsoft Azure: słownik terminów w chmurze na platformie Azure

Słownik Microsoft Azure jest słownikiem krótkich terminologii chmurze dla platformy Azure. Zobacz też:

* [Microsoft Azure i usług Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) — definicje Azure usługi i ich odpowiedniki usług AWS.<!-- I propose to link to https://azure.microsoft.com/en-us/services/ instead of this -->
* [Warunki obliczeniowych w chmurze](https://azure.microsoft.com/overview/cloud-computing-dictionary/) — ogólne branży chmury warunki.

## <a name="account"></a>account
Konto używane do uzyskania dostępu i zarządzania subskrypcją platformy Azure. Jego jest często określany jako konto platformy Azure, mimo że konto może być dowolną z tych: niezapisaną pracę, szkoły, lub osobistego konta Microsoft lub usługi Office 365 nazwę użytkownika i hasło. Można również utworzyć konto w celu zarządzania subskrypcją platformy Azure, po zarejestrowaniu się w celu [bezpłatnej wersji próbnej](https://azure.microsoft.com).  
Zobacz [Załóż subskrypcji platformy Azure z Twoim kontem usługi Office 365](billing/billing-use-existing-office-365-account-azure-subscription.md) i [służy do logowania konta](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>Aplikacja interfejsu API
Inną nazwę dla [aplikacji usługi app Service](#app-service-app).

## <a name="app-service-app"></a>Aplikacja usługi App Service
Zasoby obliczeniowe który [usłudze Azure App Service](app-service/app-service-web-overview.md) udostępnia do hostowania witryny sieci Web lub aplikacji sieci web, interfejs API sieci web, lub [zaplecza aplikacji mobilnej](app-service-mobile/app-service-mobile-value-prop.md). Usługi aplikacji — aplikacje są również nazywane *usługi aplikacji*, *sieci web apps*, *aplikacje interfejsu API*, i *aplikacje mobilne*.

## <a name="availability-set"></a>Zestaw dostępności
Kolekcja maszyn wirtualnych, które są razem zarządzane w celu zapewnienia nadmiarowości aplikacji i niezawodności. Korzystanie z zestawu dostępności gwarantuje, że podczas albo planowanego lub nieplanowanego zdarzenia konserwacji co najmniej jednej maszyny wirtualnej jest dostępny.  
Zobacz [Zarządzaj dostępnością maszyn wirtualnych systemu Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [Zarządzaj dostępnością maszyn wirtualnych systemu Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Azure klasycznego modelu wdrażania
Jeden z dwóch [modele wdrażania](resource-manager-deployment-model.md) używane do wdrażania zasobów platformy Azure (nowy model jest usługi Azure Resource Manager). Niektóre usługi Azure obsługuje modelu wdrażania usługi Resource Manager, niektóre obsługują tylko klasycznym modelu wdrożenia, a niektóre obsługiwać zarówno. Dokumentacja dla każdej usługi Azure Określa modele obsługują.

## <a name="cli"></a>Azure interfejsu wiersza polecenia (CLI)
Interfejs wiersza polecenia, który może służyć do zarządzania usługami platformy Azure z systemem Windows, system macOS i Linux.  Niektórych usług lub funkcji usługi można zarządzać tylko przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Zobacz [Azure CLI 2.0](/cli/azure)

## <a name="powershell"></a>Azure PowerShell
Interfejs wiersza polecenia do zarządzania usługami platformy Azure przy użyciu wiersza polecenia z komputerów z systemem Windows. Niektórych usług lub funkcji usługi można zarządzać tylko przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
Zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)

## <a name="arm-model"></a>Model wdrażania usługi Azure Resource Manager
Jeden z dwóch [modele wdrażania](resource-manager-deployment-model.md) używane do wdrażania zasobów na platformie Microsoft Azure (drugi to klasycznym modelu wdrażania). Niektóre usługi Azure obsługuje modelu wdrażania usługi Resource Manager, niektóre obsługują tylko klasycznym modelu wdrożenia, a niektóre obsługiwać zarówno. Dokumentacja dla każdej usługi Azure Określa modele obsługują.

## <a name="fault-domain"></a>Domena awarii
Kolekcja maszyn wirtualnych w zbiorze dostępności, który prawdopodobnie może zakończyć się niepowodzeniem w tym samym czasie. Przykładem jest grupy komputerów w stojaku, które współużytkują wspólne przełącznik źródła i sieci zasilania. Na platformie Azure maszyny wirtualne w zestawie dostępności automatycznie są rozdzielone przez wiele domen błędów.  
Zobacz [Zarządzaj dostępnością maszyn wirtualnych systemu Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub [Zarządzaj dostępnością maszyn wirtualnych systemu Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>Geo
Zdefiniowanych granic dla siedziby danych, który zwykle zawiera co najmniej dwóch regionach. Granice mogą należeć do zakresu lub poza granice państwowe i są zależne od rozporządzenia podatku. Każdy z magazynu geograficznie ma co najmniej jeden region. Przykłady obszarach geograficznych to Azja i Pacyfik i Japonii. Skrót *geograficzne*.  
Zobacz [regiony platformy Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>Replikacja geograficzna
Proces automatycznego replikacji zawartości, takich jak obiekty BLOB, tabel i kolejek w regionalnych pary.  
Zobacz [aktywna replikacja geograficzna dla bazy danych Azure SQL](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>Obraz
Plik, który zawiera system operacyjny i konfiguracji aplikacji, który może służyć do tworzenia dowolnej liczby maszyn wirtualnych. Na platformie Azure są dwa typy obrazów: VM obrazu i obrazu systemu operacyjnego. Obraz maszyny Wirtualnej zawiera system operacyjny i wszystkich dysków dołączonych do maszyny wirtualnej, podczas tworzenia obrazu. Obraz systemu operacyjnego zawiera tylko uogólniony systemu operacyjnego z konfiguracjami dysków nie danych.  
Zobacz [Nawigacja i wybierz obrazy maszyny wirtualnej systemu Windows Azure PowerShell lub interfejsu wiersza polecenia](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Limity
Liczba zasobów, które mogą zostać utworzone lub testów porównawczych wydajności, który może zostać osiągnięty. Limity są zwykle skojarzone z subskrypcjami, usługi i oferty.  
Zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>Usługa równoważenia obciążenia
Zasób, który rozdziela przychodzący ruch między komputerami w sieci. Na platformie Azure usługi równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych zdefiniowanych w zestawie usługi równoważenia obciążenia. A [modułu równoważenia obciążenia](load-balancer/load-balancer-overview.md) może być połączonych z Internetem lub mogą być wewnętrzne.  

## <a name="mobile-app"></a>aplikacja mobilna
Inną nazwę dla [aplikację usługi aplikacji](#app-service-app).

## <a name="offer"></a>oferta
Cennik, uznania i powiązane z nią postanowienia mające zastosowanie do subskrypcji platformy Azure.  
Zobacz [strony szczegółów platformy Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
Portalu sieci web bezpiecznego, używany do wdrażania i zarządzania usługami Azure.

## <a name="region"></a>region
Obszar wewnątrz geograficznie, które nie nie cross national obramowania i zawiera co najmniej jeden centrów danych. Cennik usługi regionalne i typów ofert są widoczne na poziomie regionu. Region jest zwykle skojarzone z innego regionu, który może być do kilku mil kilkuset optymalizacji. Regionalne pary może służyć jako mechanizm dla środowisk wysokiej dostępności i odzyskiwania po awarii. Nazywane także *lokalizacji*.  
Zobacz [regiony platformy Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>zasób
Element, który jest częścią rozwiązania Azure. Każda usługa Azure umożliwiają wdrażanie różnych typów zasobów, takich jak bazy danych lub maszyn wirtualnych.   
Zobacz [Omówienie usługi Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>grupa zasobów
Kontener w Menedżerze zasobów, który zawiera powiązane zasoby dla aplikacji. Grupa zasobów może zawierać wszystkich zasobów dla aplikacji lub tylko tych zasobów, które są logicznie pogrupowane. Zasoby mogą być przydzielane do grup na podstawie priorytetów organizacji.  
Zobacz [Omówienie usługi Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>Szablon usługi Resource Manager
Plik JSON definiujący deklaratywnie jeden lub więcej zasobów platformy Azure i definiuje zależności między zasobami wdrożone. Szablon może służyć do spójnego i wielokrotnego wdrażania zasobów.  
Zobacz [szablonów Authoring Azure Resource Manager](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>dostawca zasobów
Usługa dostarczająca zasoby, można wdrażać i zarządzać nimi za pomocą Menedżera zasobów. Każdy dostawca zasobów udostępnia operacje do pracy z wdrażanymi zasobami. Dostawcy zasobów jest możliwy za pośrednictwem portalu Azure, programu Azure PowerShell i zestawów SDK programowania.  
Zobacz [Omówienie usługi Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>rola
Sposób kontrolowania dostępu, które mogą być przypisane do użytkowników, grup i usług. Role są w stanie wykonać akcje, takie jak tworzenie, zarządzanie i odczytu zasobów platformy Azure.  
Zobacz [RBAC: role wbudowane](role-based-access-control/built-in-roles.md)

## <a name="sla"></a>Umowa dotycząca poziomu usług (SLA)
Umowa opisujący zobowiązania firmy Microsoft dla przestojów i łączności. Każda usługa Azure ma określonych umowy SLA.  
Zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>Sygnatury dostępu współdzielonego (SAS)
Podpisie, który umożliwia przyznać ograniczony dostęp do zasobu, bez narażania klucz konta. Na przykład [SAS używa usługi Azure Storage](storage/common/storage-dotnet-shared-access-signature-part-1.md) przyznanie dostępu klienta do obiektów, takich jak obiekty BLOB. [Centrum IoT używa SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) udzielenia uprawnienia do wysyłania danych telemetrycznych urządzeń.

## <a name="storage-account"></a>konto magazynu
Konta, które umożliwia dostęp do usług obiektów Blob platformy Azure, kolejki, tabel i plików w usłudze Azure Storage. Nazwa konta magazynu definiuje unikatową przestrzeń nazw dla obiektów danych usługi Azure Storage.  
Zobacz [konta magazynu Azure — informacje](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>subskrypcja
Umowy klienta z firmą Microsoft, umożliwiające uzyskanie usług platformy Azure. Oferta wybrany dla subskrypcji zależą subskrypcji i powiązane z nią postanowienia.
Zobacz [umowę subskrypcji Online firmy Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/) i [jak subskrypcje platformy Azure są kojarzone z usługi Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tag
Termin indeksowania, który umożliwia klasyfikowanie zasobów zgodnie z wymaganiami zarządzania lub rozliczeń. W przypadku złożonych kolekcji zasobów, można użyć znaczników do wizualizacji tych zasobów w taki sposób jak najwygodniejszy. Można na przykład oznaczyć tagami zasoby, które pełnią podobną rolę w organizacji lub należą do tego samego działu.  
Zobacz [przy użyciu tagów do organizowania zasobów platformy Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>Aktualizowanie domeny
Kolekcja maszyn wirtualnych w zestawie dostępności, które są aktualizowane w tym samym czasie. Maszyny wirtualne w tej samej domenie aktualizacji są ponownie uruchamiane razem podczas zaplanowanej konserwacji. Nigdy nie Azure uruchamia ponownie więcej niż jednej domeny aktualizacji w czasie. Nazywana także domeny uaktualnienia.  
Zobacz [Zarządzaj dostępnością maszyn wirtualnych systemu Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [Zarządzaj dostępnością maszyn wirtualnych systemu Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>Maszyny wirtualne
Implementacja oprogramowania komputer fizyczny, który ma zainstalowany system operacyjny. Wiele maszyn wirtualnych może działać jednocześnie na tym samym sprzęcie. Na platformie Azure maszyny wirtualne są dostępne w różnych rozmiarach.  
Zobacz [dokumentacji maszyny wirtualne](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>rozszerzenie maszyny wirtualnej
Zasób, który implementuje zachowania lub funkcje, które ułatwiają albo inne programy pracy lub umożliwiają korzystanie z komputera. Na przykład można użyć rozszerzenia dostępu do maszyny Wirtualnej zresetować lub zmodyfikuj wartości dostępu zdalnego na maszynie wirtualnej platformy Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Zobacz [o rozszerzenia maszyn wirtualnych i funkcji (system Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub [o rozszerzenia maszyn wirtualnych i funkcji (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>Sieć wirtualna
Sieć, która zapewnia łączność między zasobami Azure, które jest odizolowany od pozostałych dzierżawców Azure. [Bramy sieci VPN Azure](vpn-gateway/vpn-gateway-about-vpngateways.md) użytkownik może nawiązać połączenia między sieciami wirtualnymi i [między sieci wirtualnej i lokalnej](vpn-gateway/vpn-gateway-plan-design.md). Można w pełni kontrolować bloki adresów IP, ustawienia DNS, zasady zabezpieczeń i tabele tras w ramach sieci wirtualnej.  
Zobacz [omówienie sieci wirtualnej](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Aplikacja internetowa
Inną nazwę dla [aplikację usługi aplikacji](#app-service-app).

## <a name="see-also"></a>Zobacz także

* [Rozpoczynanie pracy z platformą Azure](https://azure.microsoft.com/get-started/)
* [Centrum zasobów w chmurze](https://azure.microsoft.com/resources/)  
* [Azure dla aplikacji biznesowych](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure w centrum danych.](https://azure.microsoft.com/overview/business-apps-on-azure/)

