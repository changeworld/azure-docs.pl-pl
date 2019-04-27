---
title: Użyj usługi Azure DevTest Labs dla środowisk testowych maszyn wirtualnych i PaaS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure DevTest Labs na potrzeby scenariuszy środowiska testowych maszyn wirtualnych i PaaS.
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
ms.openlocfilehash: c6b458091a8e5e22cca55d401e89e5e13bcf9de9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623186"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Użyj usługi Azure DevTest Labs dla maszyn wirtualnych i PaaS środowisk testowych

Usługi Azure DevTest Labs możesz używać do wdrażania wielu kluczowych scenariuszy, ale podstawowy scenariusz polega na używaniu usługi DevTest Labs do hostowania maszyn dla testerów. 

W tym scenariuszu usługa DevTest Labs zapewnia następujące korzyści:

- Testerzy mogą przetestować najnowszą wersję swoich aplikacji dzięki możliwości szybkiej aprowizacji środowisk systemów Windows i Linux przy użyciu szablonów wielokrotnego użytku i artefaktów.
- Testerzy mogą skalować w górę swoje testowane obciążenie, aprowizując wielu agentów testowych.
- Administratorzy mogą kontrolować koszty za zapewnienie, że:
  - Testerzy nie można pobrać więcej maszyn wirtualnych, niż jest to wymagane.
  - Maszyny wirtualne są zamykane podczas nieużywany.

![Korzystanie DevTest Labs na potrzeby szkolenia](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

W tym artykule poznasz różne funkcje usługi Azure DevTest Labs, użyć, aby spełnić wymagania testera i szczegółowy opis kroków wykonaj Konfigurowanie laboratorium.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Implementowanie środowiska testowe dzięki usłudze Azure DevTest Labs
1. **Tworzenie laboratorium** 
   
    Laboratoria stanowią punkt początkowy w usłudze Azure DevTest Labs. Po utworzeniu laboratorium, można wykonywać zadania, takie jak dodawanie użytkowników (testerów) do laboratorium, ustawienia zasad w celu kontrolowania kosztów, definiowanie obrazów maszyn wirtualnych, które można szybko tworzyć i nie tylko.  
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium w usłudze Azure DevTest Labs](devtest-lab-create-lab.md) |Dowiedz się, jak tworzenie laboratorium w usłudze Azure DevTest Labs w witrynie Azure portal. |
2. **Tworzenie maszyn wirtualnych w ciągu kilku minut przy użyciu gotowych do użycia w portalu marketplace, obrazy i obrazy niestandardowe** 
   
    Można wybrać obrazy gotowych do użycia z szerokiej gamy obrazów w portalu Azure Marketplace i udostępnić je w środowisku laboratoryjnym. Jeśli gotowych do użycia obrazy nie spełniają Twoich wymagań, możesz utworzyć niestandardowy obraz, od utworzenia laboratorium na maszynę Wirtualną przy użyciu gotowych do użycia obrazu z witryny Azure Marketplace, instalowania oprogramowania, które są potrzebne, i zapiszesz maszynę Wirtualną jako obraz niestandardowy w środowisku laboratoryjnym.

    Jeśli będziesz używać obrazów niestandardowych, należy wziąć pod uwagę przy użyciu usługi fabryka obrazu, aby utworzyć i dystrybuować swoje obrazy. Fabryki obrazu to rozwiązanie konfiguracji jako kodu, która regularnie tworzy dystrybuuje obrazy skonfigurowane automatycznie. To zaoszczędzić czas, o których trzeba ręcznie skonfigurować system po utworzeniu maszyny Wirtualnej przy użyciu podstawowego systemu operacyjnego.
  
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie obrazów z portalu Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Dowiedz się, jak można umieścić na liście dozwolonych obrazów portalu Azure Marketplace, co do wyboru dostępne obrazy dla testerów.|
   | [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) |Tworzenie niestandardowego obrazu, instalując wstępnie oprogramowanie, czego potrzebujesz, tak aby testerzy mogą szybko utworzyć Maszynę wirtualną przy użyciu niestandardowego obrazu.|
   | [Dowiedz się więcej o fabrycznie obrazu](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Obejrzyj film wideo, w którym opisano sposób konfigurowania i używania fabrykę obrazu.|

3. **Tworzenie szablonów wielokrotnego użycia dla maszyn testowych** 
   
    Formuły w usłudze Azure DevTest Labs znajduje się lista domyślnych wartości właściwości, używany do tworzenia maszyny Wirtualnej. Aby utworzyć formułę w środowisku laboratoryjnym, pobrania obrazu, rozmiar maszyny Wirtualnej (kombinację procesora CPU i pamięci RAM) i sieci wirtualnej. Każdego testera można wyświetlić formuły w laboratorium i użyć go do utworzenia maszyny Wirtualnej. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Zarządzanie formułami laboratorium do tworzenia maszyn wirtualnych](devtest-lab-manage-formulas.md) |Dowiedz się, jak można utworzyć formułę, wybierając obrazu, rozmiar maszyny Wirtualnej (kombinację procesora CPU i pamięci RAM) i sieci wirtualnej.|

3. **Tworzenie wielu maszyn wirtualnych środowisk testowych.** 
   
    Szablony usługi Azure Resource Manager służy do definiowania infrastruktury i konfiguracji rozwiązania platformy Azure i wielokrotnego wdrażania wielu testów maszyn wirtualnych w spójnym stanie.

    Zasoby usługi Azure PaaS mogą być udostępniane w środowisku z szablonu usługi Resource Manager i widoczny w śledzenie kosztów. Wyłączenie automatycznego maszyny Wirtualnej nie ma zastosowania do zasobów PaaS.

    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md) |Dowiedz się, jak wdrożyć wiele maszyn wirtualnych w spójnym stanie dla danego środowiska testowego.|

4. **Tworzenie artefaktów, aby umożliwić elastyczne dostosowywania maszyny Wirtualnej**

   Artefakty są używane do wdrażania i konfigurowania aplikacji po zaaprowizowaniu maszyny Wirtualnej. Artefaktami mogą być:

   - Narzędzia, które chcesz zainstalować na maszynie Wirtualnej — np. agenci, narzędzie Fiddler i Visual Studio.
   - Akcje, które chcesz uruchomić na maszynie Wirtualnej — takimi jak klonowanie repozytorium.
   - Aplikacje, które chcesz przetestować.

   Wiele artefaktów są już dostępne out-of--box. Jednak chcąc większym stopniu do konkretnych potrzeb, można utworzyć własne niestandardowe artefakty.

   Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie niestandardowych artefaktów dla maszyn wirtualnych usługi DevTest Labs](devtest-lab-artifact-author.md) |Tworzenie niestandardowych artefaktów dla maszyn wirtualnych w środowisku laboratoryjnym.|
   | [Dodawanie repozytorium Git do przechowywania niestandardowych artefaktów i szablonów usługi Azure Resource Manager do użycia w usłudze Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Dowiedz się, jak przechowywać swoje niestandardowe artefakty w własne prywatnym repozytorium Git.|

5. **Kontrolowanie kosztów**
   
    Usługa Azure DevTest Labs umożliwia ustawienie zasad w środowisku laboratoryjnym, aby określić maksymalną liczbę maszyn wirtualnych, które mogą być tworzone według testera w środowisku laboratoryjnym. 
   
    Jeśli zespół testu ma zestaw pracy harmonogramu i chcesz zatrzymania wszystkich maszyn wirtualnych o określonej porze dnia, a następnie automatycznie ponownie uruchomić je następnego dnia, można z łatwością osiągnąć, ustawiając zasady automatycznego zamykania i automatyczne uruchamianie w środowisku laboratoryjnym. 
   
    Na koniec po zakończeniu tworzenia aplikacji, możesz usunąć wszystkich maszyn wirtualnych jednocześnie, uruchamiając skrypt programu PowerShell jednym. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) |Kontrolowanie kosztów przez ustawienie zasad w środowisku laboratoryjnym. |
   | [Usuń wszystkie laboratorium maszyn wirtualnych przy użyciu skryptu programu PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Po zakończeniu testowania, należy usunąć wszystkie laboratoriów w ramach jednej operacji.|

1. **Dodawanie sieci wirtualnej do laboratorium** 
   
    DevTest Labs tworzy nową sieć wirtualną (VNET), po każdym utworzeniu laboratorium. Skonfigurowanie własnej sieci wirtualnej — np. przy użyciu usługi ExpressRoute lub sieci VPN typu lokacja lokacja — aby była dostępna podczas tworzenia maszyn wirtualnych można dodać tej sieci Wirtualnej do ustawień sieci wirtualnej w środowisku laboratoryjnym.

    Ponadto jest usługi Azure Active Directory domeny sprzężenia artefaktu dostępnych łączy Maszynę wirtualną do domeny, po utworzeniu maszyny Wirtualnej. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs](devtest-lab-configure-vnet.md) |Dowiedz się, jak skonfigurować sieci wirtualnej w usłudze Azure DevTest Labs przy użyciu witryny Azure portal.|

6. **Udostępnianie laboratorium każdego testera**
   
    Laboratoria są bezpośrednio dostępne przy użyciu łącza, które są udostępniane testerom. Nie nawet muszą mieć konto platformy Azure, tak długo, jak długo mają [konta Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Testerzy nie może wyświetlić maszyny wirtualne utworzone przez innych testerów.  
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Dodawanie tester do laboratorium Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Użyj witryny Azure portal, aby dodać testerów do środowiska laboratoryjnego.|
   | [Dodaj testerów do laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Aby zautomatyzować Dodawanie testerów do środowiska laboratoryjnego, należy użyć programu PowerShell. |
   | [Utwórz link pozwalający laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Dowiedz się, jak testerów można uzyskać dostęp do laboratorium za pośrednictwem hiperłącze.|

7. **Automatyzacja tworzenia laboratorium dla więcej zespołów** 
   
    Można zautomatyzować tworzenie laboratorium, w tym ustawienia niestandardowe, tworząc szablon usługi Resource Manager i użycia w celu ponownego tworzenia laboratoriów identyczne. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium przy użyciu szablonu usługi Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Tworzenie laboratoriów w usłudze Azure DevTest Labs przy użyciu szablonów usługi Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

