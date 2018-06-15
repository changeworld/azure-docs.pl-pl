---
title: Użyć do trenowania Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure DevTest Labs scenariusze szkoleniowe.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 85eddaaf101c3e85eca7514b04660163d23c1c80
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787997"
---
# <a name="use-azure-devtest-labs-for-training"></a>Użyć do trenowania Azure DevTest Labs
Azure DevTest Labs umożliwia wdrożenie wielu kluczowych scenariuszy, oprócz: tworzenie i testowanie. Jest jednym z tych scenariuszy skonfigurować laboratorium do trenowania. Azure DevTest Labs umożliwia tworzenie laboratorium, w którym można podać szablonów niestandardowych, które każdego szkoleniowych służy do tworzenia środowisk identyczne i izolowane szkolenia. Można zastosować zasad w celu zapewnienia, że szkolenie środowiska są dostępne dla każdego szkoleniowych tylko wtedy, gdy są potrzebne i zawiera za mało zasobów — takich jak maszyny wirtualne - wymagane szkolenia. Na koniec można łatwo udostępniać laboratorium uczestnikom, które mogą uzyskiwać dostęp do za pomocą jednego kliknięcia.

![Użyć do trenowania DevTest Labs](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs spełnia następujące wymagania, które są wymagane do prowadzenia szkoleń w dowolnym środowisku wirtualnym: 

* Stażyści nie widzi maszyn wirtualnych utworzonych przez inne stażyści
* Każdym komputerze szkolenia powinny być identyczne
* Stażyści można szybko ustanowić środowiskami szkolenia
* Kontrolowanie kosztów przez zapewnienie, że stażyści nie można pobrać więcej maszyn wirtualnych, niż jest to wymagane dla szkolenia, a także zamykania maszyn wirtualnych nie używają ich
* Można łatwo udostępnić laboratorium szkolenia każdego szkoleniowych
* Ponowne użycie laboratorium szkolenia wielokrotnie

W tym artykule dowiesz się o różnych funkcjach Azure DevTest Labs, których można użyć w celu spełnienia wymagań opisany wcześniej szkolenia i szczegółowy opis kroków, które możesz wykonać, aby skonfigurować laboratorium do trenowania.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementowanie szkolenia Azure DevTest Labs
1. **Tworzenie laboratorium** 
   
    Laboratoria stanowią punkt początkowy w usłudze Azure DevTest Labs. Po utworzeniu laboratorium umożliwia wykonywanie zadań takich jak dodawanie użytkowników (stażyści) do laboratorium, określanie zasad w celu kontrolę kosztów, zdefiniuj obrazów maszyn wirtualnych, które można szybko utworzyć i inne.   
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium w usłudze Azure DevTest Labs](devtest-lab-create-lab.md) |Dowiedz się, jak tworzenie laboratorium w usłudze Azure DevTest Labs w portalu Azure. |
2. **Tworzenie maszyn wirtualnych szkolenia w kilka minut przy użyciu gotowych marketplace obrazów i niestandardowych obrazów** 
   
    Można pobrania gotowych obrazów z szerokiej gamy obrazów w portalu Azure Marketplace i udostępnić je stażystów w laboratorium. Jeśli gotowe do użycia obrazy nie spełniają wymagań, można utworzyć niestandardowego obrazu, tworząc laboratorium maszyny Wirtualnej przy użyciu gotowych obrazu z portalu Azure Marketplace, instalowanie całe oprogramowanie wymagane do szkolenia i zapisywanie maszyny Wirtualnej jako obraz niestandardowy w laboratorium. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie portalu Azure Marketplace obrazów](devtest-lab-configure-marketplace-images.md) |Dowiedz się, jak obrazy portalu Azure Marketplace listy dozwolonych adresów IP; wprowadzenie do wyboru dostępne tylko obrazy mają szkolenia. |
   | [Tworzenie niestandardowego obrazu](devtest-lab-create-template.md) |Tworzenie niestandardowego obrazu wstępnie instalując oprogramowanie potrzebnych szkolenia, dzięki czemu stażyści może szybko utworzyć Maszynę wirtualną przy użyciu niestandardowego obrazu. |
3. **Tworzenie szablonów wielokrotnego użytku maszyn szkolenia** 
   
    Formuła w usłudze Azure DevTest Labs znajduje się lista domyślnych wartości właściwości używany do tworzenia maszyny Wirtualnej. Wybieranie obrazu, rozmiar maszyny Wirtualnej (kombinacja Procesora i pamięci RAM) i sieć wirtualną można utworzyć formuły w laboratorium. Każdego szkoleniowych można zobaczyć formułę laboratorium i go użyć do utworzenia maszyny Wirtualnej. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Zarządzanie DevTest Labs formuły można utworzyć maszyny wirtualne](devtest-lab-manage-formulas.md) |Dowiedz się, jak utworzyć formuły przez przechwycenie obrazu, rozmiar maszyny Wirtualnej (kombinację procesora CPU i pamięci RAM) i sieci wirtualnej. |
4. **Kontrolę kosztów**
   
    Azure DevTest Labs umożliwia ustawienie zasad w środowisku laboratoryjnym, aby określić maksymalną liczbę maszyn wirtualnych, które mogą być tworzone przez szkoleniowych w laboratorium. 
   
    Jeśli są przeprowadzenie szkolenia wielodniowego i zatrzymanie wszystkich maszyn wirtualnych o określonej godzinie dnia, a następnie automatycznie uruchomić je ponownie następnego dnia, można łatwo realizacji tego przez ustawienie automatyczne zamykanie i automatyczne uruchamianie zasad w środowisku laboratoryjnym. 
   
    Na koniec po zakończeniu szkolenia można usunąć wszystkich maszyn wirtualnych jednocześnie za pomocą jednego skryptu środowiska PowerShell. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) |Kontrolę kosztów przez ustawienie zasad w środowisku laboratoryjnym. |
   | [Usuń wszystkie laboratorium maszyn wirtualnych za pomocą skryptu programu PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Po zakończeniu szkolenia, należy usunąć wszystkie laboratoriów w ramach jednej operacji. |
5. **Udostępnij każdego szkoleniowych laboratorium**
   
    Laboratoria są bezpośrednio dostępne przy użyciu łącza, które możesz udostępniać stażyści Twojego. Stażyści Twojego nie nawet muszą mieć konta platformy Azure, jak długo mają [konta Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Stażyści nie widzi utworzonych przez inne stażyści maszyn wirtualnych.  
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Dodaj szkoleniowych do laboratorium w usłudze Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Użyj portalu Azure, aby dodać stażyści do laboratorium szkolenia. |
   | [Dodaj stażyści do laboratorium za pomocą skryptu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Za pomocą programu PowerShell można zautomatyzować Dodawanie stażyści do laboratorium szkolenia. |
   | [Uzyskaj link do laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Dowiedz się, jak laboratorium jest bezpośrednio dostępny za pośrednictwem hiperłącza. |
6. **Ponowne użycie laboratorium wielokrotnie** 
   
    Można zautomatyzować tworzenie laboratorium, m.in. przez tworzenie szablonu usługi Resource Manager i używanie go ponownie i ponownie utworzyć labs identyczne ustawienia niestandardowe. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium przy użyciu szablonu usługi Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Utwórz laboratoriów w usłudze Azure DevTest Labs za pomocą szablonów usługi Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

