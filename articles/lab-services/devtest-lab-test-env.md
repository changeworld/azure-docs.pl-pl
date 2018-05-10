---
title: Użyj Azure DevTest Labs dla maszyny Wirtualnej i PaaS środowisk testowania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Azure DevTest Labs dla maszyny Wirtualnej i PaaS scenariuszy środowiska testowego.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: d4e2c334-643a-40c9-9051-625b8f39fc86
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 58ab6d502ec5397604c562aedffddb9f48cbb699
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Użyj Azure DevTest Labs dla maszyny Wirtualnej i PaaS test środowisk

Azure DevTest Labs umożliwia wdrożenie wielu kluczowych scenariuszy, ale podstawowego scenariusza polega na użyciu DevTest Labs na komputerach hosta dla testerów. 

W tym scenariuszu DevTest Labs zapewnia następujące korzyści:

- Testerów można przetestować najnowszej wersji aplikacji przez szybkie Inicjowanie obsługi środowisk systemu Windows i Linux za pomocą szablonów wielokrotnego użytku i artefaktów.
- Testerów można zwiększać ich przez Inicjowanie obsługi wielu agentów testowych do testowania obciążenia.
- Administratorzy mogą kontrolować koszty przez zapewnienie, że:
  - Testerów nie można pobrać więcej maszyn wirtualnych, niż jest to wymagane.
  - Maszyny wirtualne są zamknięte podczas nieużywany.

![Użyć do trenowania DevTest Labs](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

W tym artykule dowiesz się o różnych funkcjach Azure DevTest Labs użyć, aby spełnić wymagania tester i szczegółowy opis kroków wykonaj skonfigurować laboratorium.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Implementowanie środowiska testowego o usłudze Azure DevTest Labs
1. **Tworzenie laboratorium** 
   
    Laboratoria stanowią punkt początkowy w usłudze Azure DevTest Labs. Po utworzeniu laboratorium, można wykonywać zadania, takie jak dodawanie użytkowników (testerów) do laboratorium, ustawienie zasad w celu kontrolowania kosztów, definiowanie obrazów maszyn wirtualnych, które można szybko utworzyć i inne.  
   
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
   | [Konfigurowanie portalu Azure Marketplace obrazów](devtest-lab-configure-marketplace-images.md) |Informacje na temat dozwolonych portalu Azure Marketplace obrazów, określ, co do wyboru dostępne tylko obrazy dla testerów.|
   | [Tworzenie niestandardowego obrazu](devtest-lab-create-template.md) |Tworzenie niestandardowego obrazu za pomocą wstępnie instalacji oprogramowania, które są potrzebne, aby testerów może szybko utworzyć Maszynę wirtualną przy użyciu niestandardowego obrazu.|
   | [Więcej informacji na temat fabryki obrazu](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Obejrzyj klip wideo, który opisuje sposób konfigurowania i używania fabrykę obrazu.|

3. **Tworzenie szablonów wielokrotnego użytku dla maszyny testowe** 
   
    Formuła w usłudze Azure DevTest Labs znajduje się lista domyślnych wartości właściwości używany do tworzenia maszyny Wirtualnej. Wybieranie obrazu, rozmiar maszyny Wirtualnej (kombinacja Procesora i pamięci RAM) i sieć wirtualną można utworzyć formuły w laboratorium. Każdy tester można formuła w laboratorium i go użyć do utworzenia maszyny Wirtualnej. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Zarządzanie DevTest Labs formuły można utworzyć maszyny wirtualne](devtest-lab-manage-formulas.md) |Dowiedz się, jak utworzyć formuły przez przechwycenie obrazu, rozmiar maszyny Wirtualnej (kombinację procesora CPU i pamięci RAM) i sieci wirtualnej.|

3. **Tworzenie wielu maszyn wirtualnych środowisk testowych.** 
   
    Szablony usługi Azure Resource Manager służy do definiowania infrastrukturze i konfiguracji rozwiązania Azure i wielokrotnie wdrażać testu wiele maszyn wirtualnych w spójnym stanie.

    Zasoby platformy Azure PaaS można udostępnić w środowisku z szablonem usługi Resource Manager i widoczny w śledzenie kosztów. Jednak maszyna wirtualna automatycznego zamykania nie ma zastosowania do zasobów typu PaaS.

    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md) |Dowiedz się, jak można wdrożyć wiele maszyn wirtualnych w spójnym stanie dla danego środowiska testowego.|

4. **Tworzenie artefaktów, aby umożliwić elastyczne dostosowanie maszyny Wirtualnej**

   Artefakty służą do wdrażania i konfigurowania aplikacji po zainicjowaniu obsługi maszyny Wirtualnej. Artefaktami mogą być:

   - Narzędzia, które chcesz zainstalować na maszynie Wirtualnej — np. agenci, program Fiddler i program Visual Studio.
   - Akcje, które chcesz uruchomić na maszynie Wirtualnej — np. klonowanie repozytorium.
   - Aplikacje, które chcesz przetestować.

   Wiele artefakty są już dostępne out-of--box. Ale jeśli chcesz więcej dostosowania do określonych potrzeb, możesz utworzyć własne niestandardowe artefakty.

   Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie niestandardowych artefaktów dla maszyny Wirtualnej DevTest Labs](devtest-lab-artifact-author.md) |Tworzenie własnych niestandardowych artefaktów dla maszyn wirtualnych w laboratorium.|
   | [Dodaj repozytorium Git do przechowywania niestandardowych artefaktów i szablony usługi Azure Resource Manager do użycia w usłudze Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Dowiedz się, jak zapisać Twoje niestandardowe artefakty w własne prywatne repozytorium Git.|

5. **Kontrolę kosztów**
   
    Azure DevTest Labs umożliwia ustawienie zasad w środowisku laboratoryjnym, aby określić maksymalną liczbę maszyn wirtualnych, które mogą być tworzone przez tester w laboratorium. 
   
    Jeśli zespół testu zawiera zestaw pracy harmonogramu i chcesz zatrzymanie wszystkich maszyn wirtualnych o określonej godzinie dnia, a następnie automatycznie uruchomić je ponownie następnego dnia, można łatwo realizacji tego przez ustawienie zasad automatycznego zamykania i automatycznego uruchamiania w środowisku laboratoryjnym. 
   
    Na koniec po zakończeniu tworzenia aplikacji, można usunąć wszystkich maszyn wirtualnych jednocześnie za pomocą jednego skryptu środowiska PowerShell. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) |Kontrolę kosztów przez ustawienie zasad w środowisku laboratoryjnym. |
   | [Usuń wszystkie laboratorium maszyn wirtualnych za pomocą skryptu programu PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Po zakończeniu testowania, należy usunąć wszystkie laboratoriów w ramach jednej operacji.|

1. **Dodaj sieć wirtualną w laboratorium** 
   
    DevTest Labs tworzy nową sieć wirtualną (VNET), przy każdym utworzeniu laboratorium. Jeśli skonfigurowano własnych sieci Wirtualnej — np. za pomocą programu ExpressRoute lub sieci VPN typu lokacja lokacja — można dodać tej sieci Wirtualnej do ustawień sieci wirtualnej środowiska laboratoryjnego, aby była dostępna podczas tworzenia maszyn wirtualnych.

    Ponadto jest usługa Azure Active Directory domeny sprzężenia artefaktu dostępne której jest przyłączany do domeny maszyny Wirtualnej, po utworzeniu maszyny Wirtualnej. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs](devtest-lab-configure-vnet.md) |Dowiedz się, jak skonfigurować sieć wirtualną w usłudze Azure DevTest Labs przy użyciu portalu Azure.|

6. **Udostępnij każdego tester laboratorium**
   
    Laboratoria są bezpośrednio dostępne przy użyciu łącza, które są udostępniane z testerów. Nie nawet muszą oni mieć konto platformy Azure, jak długo mają [konta Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Testerów nie widzi maszyny wirtualne utworzone przez inne testerów.  
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Dodaj tester do laboratorium w usłudze Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Użyj portalu Azure, aby dodać testerów do laboratorium.|
   | [Dodaj testerów do laboratorium za pomocą skryptu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Za pomocą programu PowerShell można zautomatyzować Dodawanie testerów do laboratorium. |
   | [Uzyskaj link do laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Dowiedz się, jak testerów bezpośrednio uzyskać dostęp za pośrednictwem hiperłącze laboratorium.|

7. **Zautomatyzować tworzenie laboratorium więcej zespołów** 
   
    Można zautomatyzować tworzenie laboratorium, m.in. przez tworzenie szablonu usługi Resource Manager i używanie go ponownie i ponownie utworzyć labs identyczne ustawienia niestandardowe. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium przy użyciu szablonu usługi Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Utwórz laboratoriów w usłudze Azure DevTest Labs za pomocą szablonów usługi Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

