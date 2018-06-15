---
title: Użyj Azure DevTest Labs dla deweloperów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure DevTest Labs dla deweloperów.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 22e070e5-3d1a-49fe-9d4c-5e07cb0b7fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 96432abe619ea23c1a06735567d00660e5430550
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787563"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Użyj Azure DevTest Labs dla deweloperów
Azure DevTest Labs umożliwia wdrożenie wielu kluczowych scenariuszy, ale jednego ze scenariuszy głównej polega na użyciu DevTest Labs umożliwiający przechowywanie tam maszyn programowanie dla deweloperów. W tym scenariuszu DevTest Labs zapewnia następujące korzyści:

- Deweloperzy mogą szybko ustanowić maszynami programowanie na żądanie.
- Deweloperzy można łatwo dostosować ich maszyn rozwój w miarę potrzeby.
- Administratorzy mogą kontrolować koszty przez zapewnienie, że:
  - Deweloperzy nie można pobrać więcej maszyn wirtualnych, niż jest to wymagane do tworzenia aplikacji.
  - Maszyny wirtualne są zamknięte podczas nieużywany. 

![Użyć do trenowania DevTest Labs](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

W tym artykule informacje o różnych funkcji Azure DevTest Labs, które mogą służyć do wymagań deweloperów i szczegółowy opis kroków, które można wykonać, aby skonfigurować laboratorium.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementowanie środowiska dewelopera z Azure DevTest Labs
1. **Tworzenie laboratorium** 
   
    Laboratoria stanowią punkt początkowy w usłudze Azure DevTest Labs. Po utworzeniu laboratorium, można wykonywać zadania, takie jak dodawanie użytkowników (deweloperzy) do laboratorium, ustawienie zasad w celu kontrolowania kosztów, definiowanie obrazów maszyn wirtualnych, które można szybko utworzyć i inne.  
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium w usłudze Azure DevTest Labs](devtest-lab-create-lab.md) |Dowiedz się, jak tworzenie laboratorium w usłudze Azure DevTest Labs w portalu Azure. |
2. **Tworzenie maszyn wirtualnych w kilka minut przy użyciu gotowych marketplace obrazów i niestandardowych obrazów** 
   
    Możesz pobrania gotowych obrazów z szerokiej gamy obrazów w portalu Azure Marketplace i udostępnić je w środowisku laboratoryjnym. Jeśli gotowe do użycia obrazy nie spełniają wymagań, można utworzyć niestandardowego obrazu, tworząc laboratorium maszyny Wirtualnej przy użyciu gotowych obrazu z portalu Azure Marketplace, instalacja oprogramowania, które są potrzebne, i zapisywanie maszyny Wirtualnej jako obraz niestandardowy w laboratorium.

    Jeśli będziesz używać niestandardowych obrazów, rozważ użycie fabrykę obrazu do tworzenia i rozpowszechniania obrazów. Fabryka obrazu jest rozwiązanie konfiguracji jako kod, który regularnie tworzy i rozpowszechnia obrazy skonfigurowany automatycznie. Spowoduje to zapisanie czasu, należy ręcznie skonfigurować system po utworzeniu maszyny Wirtualnej z podstawowego systemu operacyjnego.
  
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie portalu Azure Marketplace obrazów](devtest-lab-configure-marketplace-images.md) |Informacje na temat dozwolonych portalu Azure Marketplace obrazów, określ, co do wyboru dostępne tylko obrazy dla deweloperów.|
   | [Tworzenie niestandardowego obrazu](devtest-lab-create-template.md) |Tworzenie niestandardowego obrazu za pomocą wstępnie instalacji oprogramowania, które są potrzebne, dzięki czemu deweloperzy mogą szybko utworzyć Maszynę wirtualną przy użyciu niestandardowego obrazu.|
   | [Więcej informacji na temat fabryki obrazu](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Obejrzyj klip wideo, który opisuje sposób konfigurowania i używania fabrykę obrazu.|

3. **Tworzenie szablonów wielokrotnego użytku na komputerach deweloperów** 
   
    Formuła w usłudze Azure DevTest Labs znajduje się lista domyślnych wartości właściwości używany do tworzenia maszyny Wirtualnej. Wybieranie obrazu, rozmiar maszyny Wirtualnej (kombinacja Procesora i pamięci RAM) i sieć wirtualną można utworzyć formuły w laboratorium. Każdy deweloper może formuła w laboratorium i go użyć do utworzenia maszyny Wirtualnej. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Zarządzanie DevTest Labs formuły można utworzyć maszyny wirtualne](devtest-lab-manage-formulas.md) |Dowiedz się, jak utworzyć formuły przez przechwycenie obrazu, rozmiar maszyny Wirtualnej (kombinację procesora CPU i pamięci RAM) i sieci wirtualnej.|

4. **Tworzenie artefaktów, aby umożliwić elastyczne dostosowanie maszyny Wirtualnej**

   Artefakty służą do wdrażania i konfigurowania aplikacji po zainicjowaniu obsługi maszyny Wirtualnej. Artefaktami mogą być:

   - Narzędzia, które chcesz zainstalować na maszynie Wirtualnej — np. agenci, program Fiddler i program Visual Studio.
   - Akcje, które chcesz uruchomić na maszynie Wirtualnej — np. klonowanie repozytorium.
   - Aplikacje, które chcesz przetestować.

   Wiele artefakty są już dostępne out-of--box. Można utworzyć własne niestandardowe artefakty, jeśli chcesz więcej dostosowania do określonych potrzeb.

   Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie niestandardowych artefaktów dla maszyny Wirtualnej DevTest Labs](devtest-lab-artifact-author.md) |Tworzenie własnych niestandardowych artefaktów dla maszyn wirtualnych w laboratorium.|
   | [Dodaj repozytorium Git do przechowywania niestandardowych artefaktów i szablony usługi Azure Resource Manager do użycia w usłudze Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Dowiedz się, jak zapisać Twoje niestandardowe artefakty w własne prywatne repozytorium Git.|

5. **Kontrolę kosztów**
   
    Azure DevTest Labs umożliwia ustawienie zasad w środowisku laboratoryjnym, aby określić maksymalną liczbę maszyn wirtualnych, które mogą zostać utworzone przez dewelopera w laboratorium. 
   
    Jeśli zespół deweloperów zawiera zestaw pracy harmonogramu i chcesz zatrzymanie wszystkich maszyn wirtualnych o określonej godzinie dnia, a następnie automatycznie uruchomić je ponownie następnego dnia, można łatwo realizacji tego przez ustawienie zasad automatycznego zamykania i automatycznego uruchamiania w środowisku laboratoryjnym. 
   
    Na koniec po zakończeniu tworzenia aplikacji, można usunąć wszystkich maszyn wirtualnych jednocześnie za pomocą jednego skryptu środowiska PowerShell. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) |Kontrolę kosztów przez ustawienie zasad w środowisku laboratoryjnym. |
   | [Usuń wszystkie laboratorium maszyn wirtualnych za pomocą skryptu programu PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Po zakończeniu tworzenia, należy usunąć wszystkie laboratoriów w ramach jednej operacji.|

1. **Dodawanie sieci wirtualnej do maszyny Wirtualnej** 
   
    DevTest Labs tworzy nową sieć wirtualną (VNET), przy każdym utworzeniu laboratorium. Jeśli skonfigurowano własnych sieci Wirtualnej — np. za pomocą programu ExpressRoute lub sieci VPN typu lokacja lokacja — można dodać tej sieci Wirtualnej do ustawień sieci wirtualnej środowiska laboratoryjnego, aby była dostępna podczas tworzenia maszyn wirtualnych.

    Ponadto jest dostępny, które zostaną dołączone Maszynę wirtualną do domeny po utworzeniu maszyny Wirtualnej artefaktu sprzężenia domeny usługi Azure Active Directory. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs](devtest-lab-configure-vnet.md) |Dowiedz się, jak skonfigurować sieć wirtualną w usłudze Azure DevTest Labs przy użyciu portalu Azure.|

6. **Udostępnij laboratorium wszystkich deweloperów**
   
    Laboratoria są bezpośrednio dostępne przy użyciu łącza, które są udostępniane deweloperów. Nie nawet muszą oni mieć konto platformy Azure, jak długo mają [konta Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Deweloperzy nie widzi tworzone przez innych maszyn wirtualnych.  
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Dodawanie projektanta do laboratorium w usłudze Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Użyj portalu Azure, aby dodać deweloperów do laboratorium.|
   | [Dodawanie do laboratorium za pomocą skryptu programu PowerShell deweloperów](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Za pomocą programu PowerShell można zautomatyzować Dodawanie deweloperom laboratorium. |
   | [Uzyskaj link do laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Dowiedz się, jak deweloperzy mogą bezpośrednio uzyskać dostępu do laboratorium za pośrednictwem hiperłącza.|

7. **Zautomatyzować tworzenie laboratorium więcej zespołów** 
   
    Można zautomatyzować tworzenie laboratorium, m.in. przez tworzenie szablonu usługi Resource Manager i używanie go ponownie i ponownie utworzyć labs identyczne ustawienia niestandardowe. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium przy użyciu szablonu usługi Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Utwórz laboratoriów w usłudze Azure DevTest Labs za pomocą szablonów usługi Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

