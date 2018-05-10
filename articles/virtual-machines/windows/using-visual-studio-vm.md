---
title: Za pomocą programu Visual Studio na maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Za pomocą programu Visual Studio na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 03/02/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 3e37edbf8d29441541e77ed0660dc9a71618fdee
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio obrazów na platformie Azure
Za pomocą programu Visual Studio w wstępnie skonfigurowane Azure maszyny wirtualnej (VM) jest szybki i łatwy sposób przejść z nic do środowiska projektowego w górę i uruchomiona. Obrazy systemu z różnymi konfiguracjami programu Visual Studio są dostępne w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=%22visual%20studio%202017%22&page=1).

Dopiero zaczynasz korzystać z platformy Azure? [Tworzenie bezpłatnego konta platformy Azure](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Jakie konfiguracje i wersje są dostępne?
Obrazy do najnowszej wersji głównych, Visual Studio 2017 i programu Visual Studio 2015, można znaleźć w portalu Azure Marketplace. Dla każdej wersji głównych Zobacz pierwotnie wydanej wersji (RTW) oraz najnowsze zaktualizowane wersje. Każda z tych wersji oferuje wersje programu Visual Studio Community i Visual Studio Enterprise. Te obrazy są aktualizowane co najmniej co miesiąc uwzględnienie najnowsze aktualizacje programu Visual Studio i systemu Windows. Podczas nazwy obrazów pozostają takie same, opis każdego obrazu zawiera wersję zainstalowanego produktu i "daty" obrazu.

| Wersja                                              | Wersje                     |     Wersja produktu     |
|:------------------------------------------------------------:|:----------------------------:|:-----------------------:|
| Visual Studio 2017: R (wersja 15.7)                    |    Enterprise, Community     |      Wersja 15.7.0     |
| Programu Visual Studio 2017: Najnowszej wersji zapoznawczej (wersja 15.8, wersja zapoznawcza 1) |    Enterprise, Community     |      Wersja 15.8.1     |
|         Visual Studio 2017: RTW                              |    Enterprise, Community     |      Wersja 15.0.13    |
|   Visual Studio 2015: R (Update 3)                      |    Enterprise, Community     |  Wersja 14.0.25431.01  |
|         Visual Studio 2015: RTW                              |             Brak             | (Wygasł obsługi) |

> [!NOTE]
> Zgodnie z zasadami obsługi firmy Microsoft wygasł pierwotnie pełnej wersji programu Visual Studio 2015 (RTW) obsługi. Visual Studio 2015 Update 3 jest tylko wersja pozostałych oferowane dla linii produktów Visual Studio 2015.

Aby uzyskać więcej informacji, zobacz [programu Visual Studio obsługi zasad](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Jakie funkcje są zainstalowane?
Obraz zawiera skonfigurowaną zalecane w przypadku tej wersji programu Visual Studio. Ogólnie rzecz biorąc instalacja obejmuje:

* Wszystkie dostępne obciążenia, w tym poszczególnych obciążeń zalecane składniki opcjonalne
* Program .NET 4.6.2 i .NET 4.7 zestawów SDK, określania wartości docelowej pakiety i narzędzia dla deweloperów
* Visual F #
* Rozszerzenie GitHub dla programu Visual Studio
* LINQ do SQL narzędzia

Wiersz polecenia użyty do zainstalowania programu Visual Studio, podczas tworzenia obrazów jest następująca:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Jeśli obrazy nie zawiera funkcji programu Visual Studio, która jest wymagana, wyrazić swoją opinię przy użyciu narzędzia opinii w prawym górnym rogu strony.

## <a name="what-size-vm-should-i-choose"></a>Jaki rozmiar maszyny Wirtualnej należy wybrać?
System Azure oferuje szeroką gamę rozmiarów maszyn wirtualnych. Ponieważ program Visual Studio jest aplikacji zaawansowane, wielowątkowych, ma rozmiar maszyny Wirtualnej, która zawiera co najmniej dwa procesory i 7 GB pamięci. Firma Microsoft zaleca następujących rozmiarów maszyn wirtualnych dla obrazów programu Visual Studio:

   * Standardowa_D2_v3
   * Standardowa_D2s_v3
   * Standardowa_D4_v3
   * Standardowa_D4s_v3
   * Standardowa_D2_v2
   * Standard_D2S_v2
   * Standardowa_D3_v2
    
Aby uzyskać więcej informacji na najnowszej rozmiary zobacz [rozmiary dla systemu Windows maszyny wirtualne na platformie Azure](/azure/virtual-machines/windows/sizes).

Przy użyciu platformy Azure możesz przeprowadzić ponowne równoważenie początkowy wybór zmiana rozmiaru maszyny Wirtualnej. Można udostępnić nowej maszyny Wirtualnej o rozmiarze bardziej odpowiednie lub zmień rozmiar istniejącej maszyny Wirtualnej na inny komputer źródłowy. Aby uzyskać więcej informacji, zobacz [Zmień rozmiar maszyny Wirtualnej systemu Windows](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>Po uruchomieniu maszyny Wirtualnej, co to jest dalej?
Visual Studio następuje modelu "bring your own license" na platformie Azure. Podobnie jak w przypadku instalacji na sprzęcie jest jednym z pierwszych kroków licencjonowania instalację programu Visual Studio. Aby odblokować programu Visual Studio, albo:
- Zaloguj się przy użyciu konta Microsoft, który został skojarzony z subskrypcją programu Visual Studio 
- Klucz produktu dołączony do początkowej zakupu odblokować program Visual Studio

Aby uzyskać więcej informacji, zobacz [Zaloguj się do programu Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) i [jak odblokować program Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Jak mogę zapisać programowanie maszyny Wirtualnej w przyszłości lub zespołu używają?

Spektrum środowisk deweloperskich jest ogromny, a istnieje rzeczywistych kosztów związanych z zbudowaniu bardziej złożonych środowiskach. Niezależnie od konfiguracji w danym środowisku można zapisać lub przechwycenia maszyny Wirtualnej skonfigurowanych jako "podstawowy obraz" do użytku w przyszłości lub innych członków zespołu. Następnie podczas rozruchu nowej maszyny Wirtualnej, należy ustanowić je z obrazu podstawowego zamiast obrazu portalu Azure Marketplace.

Krótkie podsumowanie: Użyj narzędzia przygotowywania systemu (Sysprep) i zamykania uruchomionej maszyny Wirtualnej, a następnie przechwycić *(rysunek 1)* maszyny Wirtualnej jako obraz za pomocą interfejsu użytkownika w portalu Azure. Zapisuje Azure `.vhd` pliku zawierającego obraz na koncie magazynu wybrane. Nowy obraz następnie jest wyświetlany jako zasób obrazu w Twojej subskrypcji listy zasobów.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Rysunek 1) Przechwycenie obrazu przy użyciu interfejsu użytkownika portalu Azure.*</center>

Aby uzyskać więcej informacji, zobacz [tworzenie zarządzanego obrazu uogólniony maszyny wirtualnej na platformie Azure](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Należy pamiętać użyć narzędzia Sysprep, aby przygotować maszyny Wirtualnej. Jeśli pominiesz ten krok Azure nie może obsłużyć maszyny Wirtualnej z obrazu.

> [!NOTE]
> Nadal naliczane pewien koszt związany z magazynu obrazów, ale czy przyrostowe koszt może być niewielka w porównaniu kosztów odbudować dla każdego członka zespołu, który wymaga jednej maszyny Wirtualnej od początku. Na przykład kosztuje kilka dolarów do tworzenia i przechowywania dla miesiąca, w którym jest wielokrotnego użytku przez Twój zespół całego obrazu 127 GB. Koszty te są jednak niewielka w porównaniu do godziny, w których każdy pracownik inwestuje do tworzenia, a następnie sprawdź okno deweloperów poprawnie skonfigurowane do własnego użytku.

Ponadto zadań rozwoju lub technologii mogą wymagać więcej skalowania, takich jak odmian konfiguracje programowanie i wielu konfiguracji maszyny. Azure DevTest Labs umożliwia utworzenie _przepisami_ który zautomatyzować konstrukcji "złotego obrazu". DevTest Labs umożliwia także zarządzanie zasadami dla Twojego zespołu uruchomionych maszyn wirtualnych. [Za pomocą usługi Azure DevTest Labs dla deweloperów](/azure/devtest-lab/devtest-lab-developer-lab) jest najlepsze źródła, aby uzyskać więcej informacji na temat DevTest Labs.

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy wiadomo na temat wstępnie skonfigurowane obrazów programu Visual Studio, następnym krokiem jest tworzenie nowej maszyny Wirtualnej:

* [Utwórz maszynę Wirtualną za pośrednictwem portalu Azure](quick-create-portal.md)
* [Maszyn wirtualnych systemu Windows — omówienie](overview.md)
