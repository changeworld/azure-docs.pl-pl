---
title: Techniczne wymagania wstępne dotyczące tworzenia obrazu maszyny wirtualnej dla portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Poznaj wymagania dotyczące tworzenia i wdrażania obrazu maszyny wirtualnej w portalu Azure Marketplace innym osobom do zakupu.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: mbaldwin
ms.openlocfilehash: cf1f061c28dd0c106823d34ad39aac5e577c8b41
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29936659"
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Techniczne wymagania wstępne dotyczące tworzenia obrazu maszyny wirtualnej dla portalu Azure Marketplace
Przeczytaj dokładnie przed rozpoczęciem procesu i zrozumienie, dlaczego i w którym każdy krok jest wykonywane. Jak to możliwe, należy należy przygotować informacji o swojej firmie i innych danych, Pobierz niezbędne narzędzia i/lub tworzenia składników techniczne przed rozpoczęciem procesu tworzenia oferty. Te elementy powinno być wolne od przeglądania w tym artykule.  

## <a name="download-needed-tools--applications"></a>Pobierz potrzebne narzędzia i aplikacje
Musisz mieć następujące elementy gotowe przed rozpoczęciem procesu:

* W zależności od systemu operacyjnego przeznaczonych, zainstaluj [poleceń cmdlet programu Azure PowerShell](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) lub [Linux interfejsu wiersza polecenia narzędzia](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) z [Azure pobiera](https://azure.microsoft.com/downloads/) strony.
* Instalowanie Eksploratora usługi Storage platformy Azure w witrynie CodePlex.
* Pobierz i zainstaluj narzędzie Test certyfikacji dla certyfikowane Azure:
  * [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Potrzebny jest komputer z systemem Windows uruchom narzędzie certyfikacji. Jeśli nie masz komputera z systemem Windows dostępne, można uruchomić narzędzie Maszynę wirtualną z systemem Windows na platformie Azure.

## <a name="platforms-supported"></a>Obsługiwane platformy
Można tworzyć maszyn wirtualnych w usłudze Azure w systemie Windows lub Linux. Niektóre elementy proces publikowania — takich jak tworzenie Azure zgodnego wirtualnego dysku twardego (VHD)--Użyj różnych narzędzi i kroki w zależności od systemu operacyjnego są przy użyciu:  

* Jeśli używasz systemu Linux, zapoznaj się z sekcją "Tworzenie wirtualnego dysku twardego zgodnego Azure (opartych na systemie Linux)" [Podręcznik publikowania obrazu maszyny wirtualnej](marketplace-publishing-vm-image-creation.md).
* Jeśli korzystasz z systemu Windows, zapoznaj się z sekcją "Tworzenie wirtualnego dysku twardego zgodnego Azure (z systemem Windows)" [Podręcznik publikowania obrazu maszyny wirtualnej](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> Potrzebny jest dostęp do komputera z systemem Windows w celu:
> 
> * Uruchom narzędzie sprawdzania poprawności certyfikacji.
> * Utwórz adres URL podpisu, dostęp do udostępnionego wirtualnego dysku twardego składania certyfikacji wirtualnego dysku twardego.
> 
> 

## <a name="develop-your-vhd"></a>Opracowywanie dysk VHD
Można tworzyć wirtualne dyski twarde Azure w chmurze lub lokalne:

* Programowanie oparte na chmurze oznacza, że wszystkie kroki tworzenia są wykonywane zdalnie na dysku VHD znajdują się na platformie Azure.
* Programowanie lokalnymi wymaga pobierania wirtualnego dysku twardego i tworzenie za pomocą infrastruktury lokalnej. Jest to możliwe, zaleca się jej. Należy pamiętać, że tworzenie aplikacji dla systemu Windows lub SQL lokalnymi wymaga posiadania odpowiednich lokalnymi klucze licencji. Nie można dołączyć lub instalowania programu SQL Server po utworzeniu maszyny Wirtualnej. Należy również utworzyć ofertę na zatwierdzonych obrazu SQL z portalu Azure. Jeśli zdecydujesz się tworzenie lokalnych, należy wykonać kilka czynności inaczej niż jeśli zostały tworzenie aplikacji w chmurze. Odpowiednie informacje w można znaleźć [Tworzenie obrazu maszyny Wirtualnej lokalnymi](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
