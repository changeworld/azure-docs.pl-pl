---
title: Słownik platformy Azure — słownik platformy Azure | Dokumentacja firmy Microsoft
description: Słownik platformy Azure umożliwia zrozumienie terminologii chmury na platformie Azure. Ten krótki słownik platformy Azure definicje typowe terminy w chmurze na platformie Azure.
keywords: Słownik platformy Azure, terminologią chmury, słownik platformy Azure, definicji, warunki chmury
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
ms.openlocfilehash: 9a93786759941def4cf8677509b1b2565cac5090
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325429"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Słownik programu Microsoft Azure: Słownik terminologii chmury na platformie Azure

Słownik programu Microsoft Azure to krótki słownik terminologii chmury na platformie Azure. Zobacz też:

* [Microsoft Azure i Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) -usług definicje platformy Azure i ich odpowiedniki usługi AWS.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Przetwarzania terminy związane z chmurą](https://azure.microsoft.com/overview/cloud-computing-dictionary/) -terminów chmury ogólne branżowych.

## <a name="account"></a>account
Konto, które jest używane do dostępu i zarządzania subskrypcją platformy Azure. Często nazywa do konta platformy Azure, mimo że konto może być dowolną z tych: niezapisaną pracę, szkoły, lub osobiste konto Microsoft lub usługi Office 365 nazwę użytkownika i hasło. Można również utworzyć konto w celu zarządzania subskrypcją platformy Azure, po zalogowaniu się w celu [bezpłatna wersja próbna](https://azure.microsoft.com).  
Zobacz [Zamów subskrypcję platformy Azure przy użyciu konta usługi Office 365](billing/billing-use-existing-office-365-account-azure-subscription.md) i [można użyć do logowania konta](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>Aplikacja interfejsu API
Inną nazwę dla [aplikację usługi app Service](#app-service-app).

## <a name="app-service-app"></a>Aplikacja usługi App Service
Zasoby obliczeniowe, [usługi Azure App Service](app-service/overview.md) udostępnia do hostowania witryny sieci Web lub aplikacji sieci web, w przypadku interfejsu API sieci web lub [zaplecza aplikacji mobilnej](app-service-mobile/app-service-mobile-value-prop.md). Aplikacje usługi App Service są również nazywane *App Services*, *aplikacje sieci web*, *aplikacje API apps*, i *aplikacje mobilne*.

## <a name="availability-set"></a>zestaw dostępności
Kolekcja maszyn wirtualnych, które są ze sobą zarządzane w celu zapewnienia nadmiarowości aplikacji i niezawodności. Korzystanie z zestawu dostępności temu podczas zdarzenia planowaną lub nieplanowaną konserwacją co najmniej jednej maszyny wirtualnej są dostępne.  
Zobacz [Zarządzanie dostępnością maszyn wirtualnych Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [Zarządzanie dostępnością maszyn wirtualnych systemu Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Klasycznego modelu wdrażania platformy
Jeden z dwóch [modeli wdrażania](resource-manager-deployment-model.md) służące do wdrażania zasobów na platformie Azure (nowy model jest usługi Azure Resource Manager). Niektórych usług platformy Azure obsługują tylko model wdrażania usługi Resource Manager, niektóre obsługują tylko klasycznego modelu wdrażania, a niektóre obsługują oba. Dokumentacja dla każdej usługi platformy Azure Określa modele obsługują.

## <a name="cli"></a>Interfejs wiersza polecenia platformy Azure (CLI)
Interfejs wiersza polecenia, który może służyć do zarządzania usługami platformy Azure z Windows, macOS i Linux.  Niektóre usługi i funkcje mogą być zarządzane tylko za pomocą programu PowerShell lub interfejsu wiersza polecenia. Zobacz [wiersza polecenia platformy Azure](/cli/azure)

## <a name="powershell"></a>Azure PowerShell
Interfejs wiersza polecenia do zarządzania usługami platformy Azure za pomocą wiersza polecenia z komputerów z systemem Windows. Niektóre usługi i funkcje mogą być zarządzane tylko za pomocą programu PowerShell lub interfejsu wiersza polecenia.
Zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview)

## <a name="arm-model"></a>Model wdrażania usługi Azure Resource Manager
Jeden z dwóch [modeli wdrażania](resource-manager-deployment-model.md) służące do wdrażania zasobów na platformie Microsoft Azure (drugi jest klasycznego modelu wdrażania). Niektórych usług platformy Azure obsługują tylko model wdrażania usługi Resource Manager, niektóre obsługują tylko klasycznego modelu wdrażania, a niektóre obsługują oba. Dokumentacja dla każdej usługi platformy Azure Określa modele obsługują.

## <a name="fault-domain"></a>Domena błędów
Kolekcja maszyn wirtualnych w zestawie dostępności, który prawdopodobnie może zakończyć się niepowodzeniem w tym samym czasie. Przykładem jest grupą maszyn w stojaku, które korzystają ze wspólnego zasilania źródła i przełącznika sieciowego. Maszyny wirtualne w zestawie dostępności na platformie Azure automatycznie rozdziela się w wielu domenach błędów.  
Zobacz [Zarządzanie dostępnością maszyn wirtualnych Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub [Zarządzanie dostępnością maszyn wirtualnych systemu Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>Geograficzna
Zdefiniowanych granicach dla rezydencji danych, która zwykle zawiera co najmniej dwóch regionach. Granice może być w obrębie lub poza granice państwowe i są zależne od rozporządzenie podatku. Każdy geograficzna ma co najmniej jednego regionu. Przykłady obszarach geograficznych Azji i Pacyfiku oraz Japonii. Nazywane również *Geografia*.  
Zobacz [regiony platformy Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>Replikacja geograficzna
Proces automatycznie replikowania zawartości, takiej jak obiekty BLOB, tabel i kolejek w parę regionalną.  
Zobacz [aktywnej replikacji geograficznej dla usługi Azure SQL Database](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Plik, który zawiera system operacyjny i konfiguracji aplikacji, które można utworzyć dowolną liczbę maszyn wirtualnych. Istnieją dwa typy obrazów, na platformie Azure: Obraz maszyny Wirtualnej i obrazu systemu operacyjnego. Obraz maszyny Wirtualnej zawiera system operacyjny i wszystkie dyski dołączone do maszyny wirtualnej, po utworzeniu obrazu. Obraz systemu operacyjnego zawiera tylko uogólnionego systemu operacyjnego z konfiguracjami dysków nie danych.  
Zobacz [Navigate i wybierz pozycję Windows obrazów maszyn wirtualnych na platformie Azure przy użyciu programu PowerShell lub interfejsu wiersza polecenia](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>limity
Liczba zasobów, które mogą być tworzone lub testów porównawczych wydajności, który można osiągnąć. Limity są zwykle skojarzone z subskrypcji, usługi i oferty.  
Zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>moduł równoważenia obciążenia
Zasób, który dystrybuuje ruch przychodzący między komputerami w sieci. Na platformie Azure modułu równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych zdefiniowanych w zestawie modułu równoważenia obciążenia. A [moduł równoważenia obciążenia](load-balancer/load-balancer-overview.md) mogą być połączone z Internetem lub może być wewnętrzny.  

## <a name="mobile-app"></a>aplikacja mobilna
Inną nazwę dla [aplikację usługi App Service](#app-service-app).

## <a name="offer"></a>oferty
Ceny, środki i powiązane z nią postanowienia mające zastosowanie do subskrypcji platformy Azure.  
Zobacz [strony szczegółów oferty Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
Portal bezpieczne sieci web, używany do wdrażania i zarządzania usługami platformy Azure.

## <a name="region"></a>region
Obszar w obrębie georegionu, które nie nie cross krajami i zawiera co najmniej jedno centrum danych. Cennik usług regionalnych i typów ofert są widoczne na poziomie regionu. Region zwykle jest powiązany z innym regionem, który może zawierać maksymalnie kilka natychmiast kilkuset mil. Pary regionalne może służyć jako mechanizm odzyskiwania po awarii i scenariuszy wysokiej dostępności. Nazywana także *lokalizacji*.  
Zobacz [regiony platformy Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>zasób
Element, który jest częścią rozwiązania platformy Azure. Każda z usług Azure umożliwia wdrażanie różnych typów zasobów, takich jak bazy danych lub maszyn wirtualnych.   
Zobacz [Omówienie usługi Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>grupa zasobów
Kontener w usłudze Resource Manager, który zawiera powiązane zasoby dla aplikacji. Grupa zasobów może obejmować wszystkie zasoby danej aplikacji lub tylko tych zasobów, które są logicznie pogrupowane. Zasoby mogą być przydzielane do grup na podstawie priorytetów organizacji.  
Zobacz [Omówienie usługi Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>Szablon usługi Resource Manager
Plik JSON definiujący deklaratywne co najmniej jednego zasobu platformy Azure i definiuje zależności między wdrożonymi zasobami. Szablon może służyć do spójnego i wielokrotnego wdrażania zasobów.  
Zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>Dostawca zasobów
Usługa dostarczająca zasoby, które można wdrażać i zarządzać nimi za pomocą usługi Resource Manager. Każdy dostawca zasobów udostępnia operacje do pracy z wdrażanymi zasobami. Dostawcy zasobów jest możliwy za pośrednictwem witryny Azure portal, programu Azure PowerShell i zestawów SDK programowania.  
Zobacz [Omówienie usługi Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>role (rola)
Sposób kontrolowania dostępu, który można przypisać do użytkowników, grup i usług. Role są możliwe do wykonania akcji, takie jak tworzenie, zarządzanie i Odczyt dla zasobów platformy Azure.  
Zobacz [RBAC: Wbudowane role](role-based-access-control/built-in-roles.md)

## <a name="sla"></a>Umowa dotycząca poziomu usług (SLA)
Umowa, która opisuje zobowiązania firmy Microsoft dotyczące czasu dostępności i łączności. Każda usługa ma określonego progu umowy SLA.  
Zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>Sygnatura dostępu współdzielonego (SAS)
Podpis, która umożliwia przyznawanie ograniczonego dostępu do zasobu, bez narażania klucz konta. Na przykład [usługi Azure Storage korzysta z sygnatury dostępu Współdzielonego](storage/common/storage-dotnet-shared-access-signature-part-1.md) do udzielania dostępu klienta do obiektów, takich jak obiekty BLOB. [Usługa IoT Hub korzysta z sygnatury dostępu Współdzielonego](iot-hub/iot-hub-devguide-security.md#security-tokens) udzielenia uprawnienia do wysyłania danych telemetrycznych urządzenia.

## <a name="storage-account"></a>konto magazynu
Konta, które zapewnia dostęp do usługi Azure Blob, kolejki, tabeli i plików w usłudze Azure Storage. Nazwa konta magazynu definiuje unikatową przestrzeń nazw dla obiektów danych usługi Azure Storage.  
Zobacz [kontach magazynu Azure — informacje](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>subskrypcja
Umowy klienta z firmą Microsoft, który pozwoli uzyskać usług platformy Azure. Cennik subskrypcji oraz powiązanych z niąwarunków podlegają ofertę dla subskrypcji.
Zobacz [Microsoft Online Subscription — Umowa](https://azure.microsoft.com/support/legal/subscription-agreement/) i [jak subskrypcje platformy Azure są skojarzone z usługą Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tag
Termin indeksowania, umożliwiające kategoryzowanie zasobów zgodnie z wymaganiami zarządzania lub rozliczeń. W przypadku złożonych kolekcji zasobów można użyć tagów, aby zwizualizować te zasoby w taki sposób, który jest najlepszym rozwiązaniem. Można na przykład oznaczyć tagami zasoby, które pełnią podobną rolę w organizacji lub należą do tego samego działu.  
Zobacz [za pomocą tagów do organizowania zasobów platformy Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>Domena aktualizacji
Kolekcja maszyn wirtualnych w zestawie dostępności, które są aktualizowane w tym samym czasie. Maszyny wirtualne w tej samej domenie aktualizacji będą ponownie uruchamiane razem podczas zaplanowanej konserwacji. Azure nigdy nie powoduje ponowne uruchomienie więcej niż jednej domenie aktualizacji naraz. Również określany jako domeny uaktualnienia.  
Zobacz [Zarządzanie dostępnością maszyn wirtualnych Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [Zarządzanie dostępnością maszyn wirtualnych systemu Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>Maszyna wirtualna
Implementacja oprogramowania komputer fizyczny, który jest uruchamiany system operacyjny. Wiele maszyn wirtualnych może działać jednocześnie na tym samym sprzęcie. Na platformie Azure maszyny wirtualne są dostępne w różnych rozmiarach.  
Zobacz [dokumentacja dotycząca maszyn wirtualnych](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>rozszerzenie maszyny wirtualnej
Zasób, który implementuje zachowania lub funkcje, które ułatwiają albo inne programy pracy lub zapewnić możliwość interakcji z uruchomionego komputera. Na przykład można użyć rozszerzenia VM Access zresetować lub zmodyfikuj wartości dostępu zdalnego na maszynie wirtualnej platformy Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Zobacz [o rozszerzeniach maszyn wirtualnych i funkcje (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub [o rozszerzeniach maszyn wirtualnych i funkcji (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>Sieć wirtualna
Sieć, która zapewnia łączność między zasobami platformy Azure, które jest odizolowana od innych dzierżaw usługi Azure. [Bramy Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) umożliwia ustanawianie połączeń między sieciami wirtualnymi oraz między siecią wirtualną i siecią lokalną. Możesz w pełni kontrolować bloki adresów IP, ustawienia DNS, zasady zabezpieczeń i tabel tras w ramach sieci wirtualnej.  
Zobacz [Omówienie usługi Virtual Network](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Aplikacja internetowa
Inną nazwę dla [aplikację usługi App Service](#app-service-app).

## <a name="see-also"></a>Zobacz także

* [Rozpoczynanie pracy z platformą Azure](https://azure.microsoft.com/get-started/)
* [Centrum zasobów w chmurze](https://azure.microsoft.com/resources/)  
* [Platforma Azure dla aplikacji biznesowych](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Platforma Azure w centrum danych](https://azure.microsoft.com/overview/business-apps-on-azure/)

