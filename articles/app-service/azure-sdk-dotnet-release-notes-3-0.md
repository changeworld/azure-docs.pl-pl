---
title: Zestaw Azure SDK dla platformy .NET 3.0 informacje o wersji | Dokumentacja firmy Microsoft
description: Informacje o wersji zestawu Azure SDK dla platformy .NET 3.0
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/07/2017
ms.author: juliako
ms.openlocfilehash: 207aa137b25e44baf73e7f481ebc8b6362dfa245
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30162098"
---
# <a name="azure-sdk-for-net-30-release-notes"></a>Informacje o wersji zestawu Azure SDK for .NET 3.0

Ten temat zawiera informacje o wersji dla wersji 3.0 zestawu Azure SDK dla platformy .NET.

## <a name="azure-sdk-for-net-30-release-summary"></a>Zestaw Azure SDK dla wersji .NET 3.0 podsumowania

Data wydania: 2017-03/07
 
W tej wersji zostały wprowadzone żadne zmiany podziału 3.0 SDK platformy Azure. Nie ma również żadnych procesów uaktualniania konieczne korzystać z tego zestawu SDK z istniejącymi projektami usługi w chmurze. Umożliwia korzystanie z usługi Azure SDK 3.0 bez konieczności procesu uaktualniania, Azure SDK 3.0 instaluje samych katalogach jako zestaw Azure SDK 2.9. Najbardziej składników nie został zmieniony wersji głównej z 2.9, ale zamiast tego po zaktualizowaniu numer kompilacji.

## <a name="visual-studio-2017-rtw"></a>Visual Studio 2017 RTW

- W programie Visual Studio 2017 r. tej wersji zestawu Azure SDK dla platformy .NET korzysta z wbudowanej w obciążenia Azure. Wszystkie narzędzia należy wykonać rozwoju platformy Azure będzie częścią Visual Studio 2017 r idąc dalej. Dla programu Visual Studio 2015 SDK nadal będą dostępne za pośrednictwem WebPI. Firma Microsoft ma przerywane zestawu Azure SDK dla wersji platformy .NET dla programu Visual Studio 2013 teraz, Visual Studio 2017 został zwolniony.

### <a name="azure-diagnostics"></a>Diagnostyka Azure

- Zmienić zachowanie, które będzie przechowywać parametry połączenia z częściowa tylko przy użyciu klucza zastępuje token dla parametrów połączenia magazynu diagnostyki usługi w chmurze. Klucz magazynu rzeczywista są obecnie przechowywane w folderze profilu użytkownika, można kontrolować dostęp do niej. Visual Studio odczyta klucza magazynu z folderu profilu użytkownika dla debugowania lokalnego i proces publikowania. 
- W odpowiedzi na zmianę opisane powyżej zespołu Visual Studio Online rozszerzone szablon zadania wdrożenia usługi w chmurze Azure, użytkownicy można określić klucz magazynu do ustawiania rozszerzenia diagnostyki podczas publikowania na platformie Azure w ciągłej integracji i wdrażania.
- Ułatwiliśmy możliwość przechowywania i tokenizacji dla Azure Diagnostics (WAD), aby ułatwić rozwiązywanie problemów z konfiguracją w environements ciąg bezpiecznego połączenia.
 
### <a name="windows-server-2016-virtual-machines"></a>Maszyny wirtualne systemu Windows Server 2016

- Program Visual Studio teraz obsługuje wdrażanie usługi w chmurze do maszyn wirtualnych 5 rodziny systemu operacyjnego (Windows Server 2016). Istniejących usług w chmurze można zmienić ustawienia pod kątem nowych rodziny systemów operacyjnych. Podczas tworzenia nowych usług w chmurze, jeśli użytkownik chce utworzyć usługę przy użyciu platformy .net 4.6 lub nowszej, będzie on domyślnej usługi do 5 rodziny systemu operacyjnego.  Aby uzyskać więcej informacji, możesz przejrzeć [rodziny systemów operacyjnych gościa obsługuje tabeli](../cloud-services/cloud-services-guestos-update-matrix.md).

### <a name="known-issues"></a>Znane problemy

- Azure .NET SDK 3.0 wprowadzono wystąpił problem podczas usuwania programu Visual Studio 2017 w konfiguracji obok siebie z programem Visual Studio 2015.  Jeśli masz zainstalowany program Visual Studio 2015 zestaw SDK usługi Azure Emulator magazynu Microsoft Azure i Microsoft Azure obliczeniowe emulatora zostaną usunięte, po odinstalowaniu programu Visual Studio 2017 r.  Spowoduje to utworzenie wystąpił błąd podczas tworzenia i debugowanie nowe projekty usługi w chmurze w programie Visual Studio 2015. Aby rozwiązać ten problem, ponownie zainstaluj zestaw Azure SDK z Instalatora platformy sieci Web.  Problem zostanie rozwiązany w przyszłej aktualizacji programu Visual Studio 2017 r.  .

 
### <a name="azure-in-role-cache"></a>Azure In-Role Cache 

- Obsługę pamięci podręcznej na roli Azure została zakończona w dniu 30 listopada 2016 r. Aby uzyskać więcej informacji, kliknij przycisk [tutaj](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).




