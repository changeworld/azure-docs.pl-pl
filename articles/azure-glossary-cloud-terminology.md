---
title: Słownik platformy Azure — słownik platformy Azure | Dokumenty firmy Microsoft
description: Użyj glosariusza platformy Azure, aby zrozumieć terminologię w chmurze na platformie Azure. Ten krótki słownik platformy Azure zawiera definicje terminów w chmurze dla platformy Azure.
keywords: Słownik platformy Azure, terminologia w chmurze, glosariusz platformy Azure, definicje terminologii, terminy w chmurze
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
ms.openlocfilehash: 7c80ba6b15cec08da9bcefea243493a824ef66bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276480"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glosariusz platformy Microsoft Azure: słownik terminologii chmury na platformie Azure

Słowniczek platformy Microsoft Azure to krótki słownik terminologii chmury dla platformy Azure. Zobacz też:

* [Microsoft Azure i Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) — definicje usług platformy Azure i ich odpowiedników AWS.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Warunki przetwarzania w chmurze](https://azure.microsoft.com/overview/cloud-computing-dictionary/) — ogólne warunki chmury branżowej.

## <a name="account"></a>account
Konto używane do uzyskiwania dostępu do subskrypcji platformy Azure i zarządzania nią. Jest często określane jako konto platformy Azure, chociaż konto może być dowolne z nich: istniejące konto firmy, szkoła lub osobiste konto Microsoft lub nazwa użytkownika i hasło usługi Office 365. Możesz również utworzyć konto do zarządzania subskrypcją platformy Azure podczas rejestracji w celu [uzyskania bezpłatnej wersji próbnej.](https://azure.microsoft.com)  
Zobacz [Konfigurowanie subskrypcji platformy Azure za pomocą konta usługi Office 365](cost-management-billing/manage/office-365-account-for-azure-subscription.md) i [kont, których można używać do logowania](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)się.

## <a name="api-app"></a>Aplikacja interfejsu API
Inna nazwa [aplikacji Usługi App Service](#app-service-app).

## <a name="app-service-app"></a>Aplikacja usługi App Service
Zasoby obliczeniowe, które [usługa Azure App Service](app-service/overview.md) udostępnia do obsługi witryny sieci Web lub aplikacji sieci Web, interfejsu API sieci Web lub [zaplecza aplikacji mobilnej.](app-service-mobile/app-service-mobile-value-prop.md) Aplikacje usługi App Service są również nazywane *usługami app services,* *aplikacjami internetowymi,* *aplikacjami interfejsu API*i *aplikacjami mobilnymi.*

## <a name="availability-set"></a>zestaw dostępności
Kolekcja maszyn wirtualnych, które są zarządzane razem w celu zapewnienia nadmiarowości i niezawodności aplikacji. Użycie zestawu dostępności zapewnia, że podczas planowanego lub nieplanowanego zdarzenia konserwacji dostępna jest co najmniej jedna maszyna wirtualna.  
Zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i zarządzanie [dostępnością maszyn wirtualnych systemu Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Klasyczny model wdrażania platformy Azure
Jeden z dwóch [modeli wdrażania](resource-manager-deployment-model.md) używanych do wdrażania zasobów na platformie Azure (nowym modelem jest Usługa Azure Resource Manager). Niektóre usługi platformy Azure obsługują tylko model wdrażania usługi Resource Manager, niektóre obsługują tylko klasyczny model wdrażania, a niektóre obsługują oba te elementy. Dokumentacja dla każdej usługi platformy Azure określa, które modele obsługują.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Interfejs wiersza polecenia platformy Azure (CLI)
Interfejs wiersza polecenia, który może służyć do zarządzania usługami platformy Azure z systemów Windows, macOS i Linux.  Niektóre usługi lub funkcje usługi mogą być zarządzane tylko za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia. Zobacz [interfejsu wiersza polecenia platformy Azure](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Interfejs wiersza polecenia do zarządzania usługami platformy Azure za pośrednictwem wiersza polecenia z komputerów z systemem Windows. Niektóre usługi lub funkcje usługi mogą być zarządzane tylko za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia.
Zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Model wdrażania usługi Azure Resource Manager
Jeden z dwóch [modeli wdrażania](resource-manager-deployment-model.md) używanych do wdrażania zasobów na platformie Microsoft Azure (drugi to klasyczny model wdrażania). Niektóre usługi platformy Azure obsługują tylko model wdrażania usługi Resource Manager, niektóre obsługują tylko klasyczny model wdrażania, a niektóre obsługują oba te elementy. Dokumentacja dla każdej usługi platformy Azure określa, które modele obsługują.

## <a name="fault-domain"></a>domena błędów
Kolekcja maszyn wirtualnych w zestawie dostępności, który może ewentualnie zakończyć się niepowodzeniem w tym samym czasie. Przykładem jest grupa maszyn w stojaku, które mają wspólne źródło zasilania i przełącznik sieciowy. Na platformie Azure maszyny wirtualne w zestawie dostępności są automatycznie rozdzielane między wiele domen błędów.  
Zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub Zarządzanie [dostępnością maszyn wirtualnych systemu Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>Geo
Zdefiniowana granica dla rezydencji danych, która zazwyczaj zawiera dwa lub więcej regionów. Granice mogą znajdować się w granicach lub poza granicami kraju i są pod wpływem przepisów podatkowych. Każda geo ma co najmniej jeden region. Przykładami geos są Azja i Pacyfik i Japonia. Nazywane również *geografii*.  
Zobacz [Regiony platformy Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>replikacja geograficzna
Proces automatycznego replikowania zawartości, takiej jak obiekty blob, tabele i kolejki w parze regionalnej.  
Zobacz [Aktywna replikacja geograficzna dla bazy danych SQL usługi Azure](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Plik zawierający system operacyjny i konfigurację aplikacji, który może służyć do tworzenia dowolnej liczby maszyn wirtualnych. Na platformie Azure istnieją dwa typy obrazów: obraz maszyny Wirtualnej i obraz systemu operacyjnego. Obraz maszyny Wirtualnej zawiera system operacyjny i wszystkie dyski dołączone do maszyny wirtualnej podczas tworzenia obrazu. Obraz systemu operacyjnego zawiera tylko uogólniony system operacyjny bez konfiguracji dysku danych.  
Zobacz [Nawigowanie i wybieranie obrazów maszyn wirtualnych systemu Windows na platformie Azure za pomocą programu PowerShell lub interfejsu wiersza polecenia](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Limity
Liczba zasobów, które można utworzyć lub wskaźnik wydajności, który można osiągnąć. Limity są zazwyczaj skojarzone z subskrypcjami, usługami i ofertami.  
Zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>moduł równoważenia obciążenia
Zasób, który dystrybuuje ruch przychodzący między komputerami w sieci. Na platformie Azure moduł równoważenia obciążenia rozdziela ruch do maszyn wirtualnych zdefiniowanych w zestawie modułu równoważenia obciążenia. Moduł [równoważenia obciążenia](load-balancer/load-balancer-overview.md) może być skierowany do Internetu lub może być wewnętrzny.  

## <a name="mobile-app"></a>aplikacja mobilna
Inna nazwa [aplikacji Usługi app service](#app-service-app).

## <a name="offer"></a>offer
Ceny, kredyty i powiązane warunki mające zastosowanie do subskrypcji platformy Azure.  
Zobacz [stronę szczegółów oferty platformy Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
Bezpieczny portal sieci Web używany do wdrażania usług platformy Azure i zarządzania nimi.

## <a name="region"></a>region
Obszar w obszarze geograficznym, który nie przekracza granic krajowych i zawiera jedno lub więcej centrów danych. Ceny, usługi regionalne i typy ofert są widoczne na poziomie regionu. Region jest zazwyczaj sparowany z innym regionem, który może znajdować się w odległości do kilkuset mil. Pary regionalne mogą służyć jako mechanizm odzyskiwania po awarii i scenariuszy wysokiej dostępności. Określana również jako *lokalizacja*.  
Zobacz [Regiony platformy Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>zasób
Element, który jest częścią rozwiązania platformy Azure. Każda usługa platformy Azure umożliwia wdrażanie różnych typów zasobów, takich jak bazy danych lub maszyny wirtualne.   
Zobacz [omówienie usługi Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>grupa zasobów
Kontener w Menedżerze zasobów, który przechowuje powiązane zasoby dla aplikacji. Grupa zasobów może zawierać wszystkie zasoby dla aplikacji lub tylko te zasoby, które są logicznie zgrupowane razem. Zasoby mogą być przydzielane do grup na podstawie priorytetów organizacji.  
Zobacz [omówienie usługi Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Szablon Menedżera zasobów
Plik JSON, który deklaratywnie definiuje jeden lub więcej zasobów platformy Azure i który definiuje zależności między wdrożonymi zasobami. Szablon może służyć do spójnego i wielokrotnego wdrażania zasobów.  
Zobacz [Tworzenie szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>dostawca zasobów
Usługa, która dostarcza zasoby, które można wdrożyć i zarządzać za pośrednictwem Menedżera zasobów. Każdy dostawca zasobów udostępnia operacje do pracy z wdrażanymi zasobami. Dostawców zasobów można uzyskać za pośrednictwem witryny Azure portal, Azure PowerShell i kilka zestawów SDK programowania.  
Zobacz [omówienie usługi Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="role"></a>role (rola)
Środki kontroli dostępu, które można przypisać do użytkowników, grup i usług. Role są w stanie wykonywać akcje, takie jak tworzenie, zarządzanie i odczytywanie zasobów platformy Azure.  
Zobacz [RBAC: Wbudowane role](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>umowa dotyczącej poziomu usług (SLA)
Umowa, która opisuje zobowiązania firmy Microsoft dotyczące czasu pracy bez przestojów i łączności. Każda usługa platformy Azure ma określoną umowy SLA.  
Zobacz [umowy o gwarantowanym poziomie usług](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>podpis dostępu współdzielonego (SAS)
Podpis, który umożliwia udzielenie ograniczonego dostępu do zasobu bez ujawniania klucza konta. Na przykład [usługa Azure Storage używa sygnatury dostępu współdzielonego](storage/common/storage-dotnet-shared-access-signature-part-1.md) do udzielania klientowi dostępu do obiektów, takich jak obiekty blob. [Usługa IoT Hub używa sygnatury dostępu Współdzielonego](iot-hub/iot-hub-devguide-security.md#security-tokens) do udzielania urządzeniom uprawnień do wysyłania danych telemetrycznych.

## <a name="storage-account"></a>konto magazynu
Konto, które zapewnia dostęp do usług Azure Blob, Queue, Table i File w usłudze Azure Storage. Nazwa konta magazynu definiuje unikatowy obszar nazw dla obiektów danych usługi Azure Storage.  
Zobacz [Informacje o kontach magazynu platformy Azure](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>subskrypcja
Umowa klienta z firmą Microsoft, która umożliwia mu uzyskanie usług platformy Azure. Ceny subskrypcji i powiązane warunki podlegają ofercie wybranej dla subskrypcji.
Zobacz [Umowa subskrypcji online firmy Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/) i sposób [kojarzeń subskrypcji platformy Azure z usługą Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tag
Termin indeksowania, który umożliwia kategoryzowanie zasobów zgodnie z wymaganiami dotyczącymi zarządzania lub rozliczeń. Jeśli masz złożoną kolekcję zasobów, można użyć tagów do wizualizacji tych zasobów w sposób, który ma największy sens. Można na przykład oznaczyć tagami zasoby, które pełnią podobną rolę w organizacji lub należą do tego samego działu.  
Zobacz [Organizowanie zasobów platformy Azure przy użyciu tagów](resource-group-using-tags.md)

## <a name="update-domain"></a>aktualizowanie domeny
Kolekcja maszyn wirtualnych w zestawie dostępności, które są aktualizowane w tym samym czasie. Maszyny wirtualne w tej samej domenie aktualizacji są ponownie uruchamiane razem podczas planowanej konserwacji. Platforma Azure nigdy nie uruchamia ponownie więcej niż jednej domeny aktualizacji naraz. Nazywane również domeną uaktualnienia.  
Zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i zarządzanie [dostępnością maszyn wirtualnych systemu Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>maszyna wirtualna
Implementacja oprogramowania komputera fizycznego, na który działa system operacyjny. Wiele maszyn wirtualnych można uruchomić jednocześnie na tym samym sprzęcie. Na platformie Azure maszyny wirtualne są dostępne w różnych rozmiarach.  
Zobacz [dokumentację maszyn wirtualnych](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>rozszerzenie maszyny wirtualnej
Zasób, który implementuje zachowania lub funkcje, które pomagają innym programom pracować lub zapewniają możliwość interakcji z uruchomionym komputerem. Na przykład można użyć rozszerzenia dostępu do maszyny Wirtualnej, aby zresetować lub zmodyfikować wartości dostępu zdalnego na maszynie wirtualnej platformy Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Zobacz [Informacje o rozszerzeniach i funkcjach maszyn wirtualnych (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub [Informacje o rozszerzeniach i funkcjach maszyn wirtualnych (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network"></a><a name="vnet"></a>sieć wirtualna
Sieć, która zapewnia łączność między zasobami platformy Azure, która jest odizolowana od wszystkich innych dzierżaw platformy Azure. Brama [sieci VPN platformy Azure](vpn-gateway/vpn-gateway-about-vpngateways.md) umożliwia ustanawianie połączeń między sieciami wirtualnymi oraz między siecią wirtualną a siecią lokalną. Można w pełni kontrolować bloki adresów IP, ustawienia DNS, zasady zabezpieczeń i tabele tras w sieci wirtualnej.  
Zobacz [omówienie sieci wirtualnej](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Aplikacja internetowa
Inna nazwa [aplikacji Usługi app service](#app-service-app).

## <a name="see-also"></a>Zobacz też

* [Rozpoczynanie pracy z platformą Azure](https://azure.microsoft.com/get-started/)
* [Centrum zasobów w chmurze](https://azure.microsoft.com/resources/)  
* [Platforma Azure dla aplikacji biznesowej](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Platforma Azure w centrum danych](https://azure.microsoft.com/overview/business-apps-on-azure/)

