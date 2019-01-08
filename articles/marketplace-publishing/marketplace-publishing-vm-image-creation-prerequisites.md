---
title: Techniczne wymagania wstępne dotyczące tworzenia obrazów maszyn wirtualnych w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Poznaj wymagania dotyczące tworzenia i wdrażania obrazu maszyny wirtualnej w portalu Azure Marketplace przez inne osoby do zakupu.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ROBOTS: NOINDEX
ms.openlocfilehash: 1fdf945645b2d5c89d7f3750ec836595adb90b06
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077083"
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Techniczne wymagania wstępne dotyczące tworzenia obrazów maszyn wirtualnych w portalu Azure Marketplace
Proces dokładnie przed rozpoczęciem przeczytaj pozwolą Ci zrozumieć, gdzie i dlaczego wykonywania kolejnych kroków. Jak to możliwe, możesz należy przygotować informacji o firmie i inne dane, Pobierz potrzebne narzędzia i/lub utworzyć zarówno składników technicznych przed rozpoczęciem procesu tworzenia oferty. Te elementy powinny być jasne z przeglądania w tym artykule.  

## <a name="download-needed-tools--applications"></a>Pobierz potrzebne narzędzia i aplikacje
Należy dysponować następującymi elementami gotowe przed rozpoczęciem procesu:

* W zależności od systemu operacyjnego przeznaczonych do pracy, należy zainstalować [poleceń cmdlet programu Azure PowerShell](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) lub [narzędzia interfejsu wiersza polecenia systemu Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) z [pliki do pobrania Azure](https://azure.microsoft.com/downloads/) strony.
* Instalowanie Eksploratora usługi Azure Storage z witryny CodePlex.
* Pobierz i zainstaluj narzędzie Test certyfikacji do certyfikatu platformy Azure:
  * [http://go.microsoft.com/fwlink/?LinkID=526913](https://go.microsoft.com/fwlink/?LinkID=526913). Należy komputera z systemem Windows, aby uruchomić narzędzie certyfikacji. Jeśli nie masz komputerze z systemem Windows dostępne, można uruchomić narzędzie z maszyny Wirtualnej z systemem Windows na platformie Azure.

## <a name="platforms-supported"></a>Obsługiwane platformy
Można tworzyć oparte na platformie Azure maszyn wirtualnych w systemie Windows lub Linux. Niektóre elementy proces publikowania — takich jak tworzenie zgodnych z platformą Azure wirtualnego dysku twardego (VHD) — Korzystaj z różnych narzędzi i czynności, w zależności od systemu operacyjnego używasz:  

* Jeśli używasz systemu Linux, zapoznaj się z sekcją "Utwórz wirtualny dysk twardy zgodnych z platformą Azure (opartych na systemie Linux)" [Podręcznik publikowania obrazu maszyny wirtualnej](marketplace-publishing-vm-image-creation.md).
* Jeśli używasz Windows, zapoznaj się z sekcją "Utwórz wirtualny dysk twardy zgodnych z platformą Azure (z systemem Windows)" [Podręcznik publikowania obrazu maszyny wirtualnej](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> Musisz mieć dostęp do maszyny z systemem Windows, aby:
> 
> * Uruchamianie narzędzia sprawdzającego poprawność certyfikacji.
> * Utwórz adres URL sygnatury dostępu udostępnionego wirtualnego dysku twardego do przesłania certyfikacji wirtualnego dysku twardego.
> 
> 

## <a name="develop-your-vhd"></a>Tworzenie wirtualnego dysku twardego
Możesz tworzyć wirtualnych dysków twardych platformy Azure, w chmurze lub lokalnie:

* Programowania w chmurze oznacza, że wszystkie kroki tworzenia są wykonywane zdalnie na wirtualny dysk twardy rezydentnego na platformie Azure.
* Programowanie w środowisku lokalnym wymaga pobierania wirtualnego dysku twardego i tworzenie za pomocą infrastruktury lokalnej. Mimo że takie podejście jest możliwe, nie zalecamy tego. Tworzenie aplikacji dla Windows lub SQL środowiska lokalnego wymaga posiadania odpowiednich lokalnych kluczy licencji. Nie można dołączyć lub zainstaluj program SQL Server po utworzeniu maszyny Wirtualnej. Należy również utworzyć oferty na zatwierdzonym obrazie SQL w witrynie Azure portal. Jeśli zdecydujesz się lokalnie, należy wykonać pewne kroki inaczej niż jeśli były rozwoju, w chmurze. Można znaleźć odpowiednie informacje w [utworzyć obraz maszyny Wirtualnej w środowisku lokalnym](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
