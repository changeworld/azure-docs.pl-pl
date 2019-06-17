---
title: Użyj usługi Azure DevTest Labs dla deweloperów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure DevTest Labs na potrzeby scenariuszy dla deweloperów.
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
ms.openlocfilehash: 5a293946e4672e7737f912f42511ad0907ba4a81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61294003"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Użyj usługi Azure DevTest Labs dla deweloperów
Usługi Azure DevTest Labs można używać do wdrażania wielu kluczowych scenariuszy, ale jeden z podstawowych scenariuszy obejmuje używanie usługi DevTest Labs do hostowania maszyn deweloperskich dla deweloperów. W tym scenariuszu usługa DevTest Labs zapewnia następujące korzyści:

- Deweloperzy mogą szybko inicjować obsługę swoich komputerach deweloperskich na żądanie.
- Deweloperzy mogą łatwo dostosowywać swoje maszyny deweloperskie w miarę potrzeb.
- Administratorzy mogą kontrolować koszty za zapewnienie, że:
  - Deweloperzy nie można pobrać więcej maszyn wirtualnych, niż jest to wymagane do tworzenia aplikacji.
  - Maszyny wirtualne są zamykane podczas nieużywany. 

![Korzystanie DevTest Labs na potrzeby szkolenia](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

W tym artykule poznasz różne funkcje usługi Azure DevTest Labs, które mogą służyć do wymagań dla deweloperów i szczegółowy opis kroków, które można wykonać, aby skonfigurować laboratorium.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Wdrażanie środowisk deweloperskich z usługą Azure DevTest Labs
1. **Tworzenie laboratorium** 
   
    Laboratoria stanowią punkt początkowy w usłudze Azure DevTest Labs. Po utworzeniu laboratorium, można wykonywać zadania, takie jak dodawanie użytkowników (deweloperzy) do laboratorium, ustawienia zasad w celu kontrolowania kosztów, definiowanie obrazów maszyn wirtualnych, które można szybko tworzyć i nie tylko.  
   
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
   | [Konfigurowanie obrazów z portalu Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Dowiedz się, jak można umieścić na liście dozwolonych obrazów portalu Azure Marketplace, co do wyboru dostępne obrazy dla deweloperów.|
   | [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) |Tworzenie niestandardowego obrazu, instalując wstępnie oprogramowanie, czego potrzebujesz, dzięki czemu deweloperzy mogą szybko tworzyć przy użyciu niestandardowego obrazu maszyny Wirtualnej.|
   | [Dowiedz się więcej o fabrycznie obrazu](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Obejrzyj film wideo, w którym opisano sposób konfigurowania i używania fabrykę obrazu.|

3. **Tworzenie szablonów wielokrotnego użytku na komputerach deweloperów** 
   
    Formuły w usłudze Azure DevTest Labs znajduje się lista domyślnych wartości właściwości, używany do tworzenia maszyny Wirtualnej. Aby utworzyć formułę w środowisku laboratoryjnym, pobrania obrazu, rozmiar maszyny Wirtualnej (kombinację procesora CPU i pamięci RAM) i sieci wirtualnej. Każdy deweloper można wyświetlić formuły w laboratorium i użyć go do utworzenia maszyny Wirtualnej. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Zarządzanie formułami laboratorium do tworzenia maszyn wirtualnych](devtest-lab-manage-formulas.md) |Dowiedz się, jak można utworzyć formułę, wybierając obrazu, rozmiar maszyny Wirtualnej (kombinację procesora CPU i pamięci RAM) i sieci wirtualnej.|

4. **Tworzenie artefaktów, aby umożliwić elastyczne dostosowywania maszyny Wirtualnej**

   Artefakty są używane do wdrażania i konfigurowania aplikacji po zaaprowizowaniu maszyny Wirtualnej. Artefaktami mogą być:

   - Narzędzia, które chcesz zainstalować na maszynie Wirtualnej — np. agenci, narzędzie Fiddler i Visual Studio.
   - Akcje, które chcesz uruchomić na maszynie Wirtualnej — takimi jak klonowanie repozytorium.
   - Aplikacje, które chcesz przetestować.

   Wiele artefaktów są już dostępne out-of--box. Jeśli chcesz, aby spełniał określone wymagania większym stopniu, można utworzyć własne niestandardowe artefakty.

   Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie niestandardowych artefaktów dla maszyn wirtualnych usługi DevTest Labs](devtest-lab-artifact-author.md) |Tworzenie niestandardowych artefaktów dla maszyn wirtualnych w środowisku laboratoryjnym.|
   | [Dodawanie repozytorium Git do przechowywania niestandardowych artefaktów i szablonów usługi Azure Resource Manager do użycia w usłudze Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Dowiedz się, jak przechowywać swoje niestandardowe artefakty w własne prywatnym repozytorium Git.|

5. **Kontrolowanie kosztów**
   
    Usługa Azure DevTest Labs umożliwia ustawienie zasad w środowisku laboratoryjnym, aby określić maksymalną liczbę maszyn wirtualnych, które mogą być tworzone przez dewelopera w środowisku laboratoryjnym. 
   
    Jeśli Twój zespół deweloperów zawiera zestaw pracy harmonogramu i chcesz zatrzymania wszystkich maszyn wirtualnych o określonej porze dnia, a następnie automatycznie ponownie uruchomić je następnego dnia, można z łatwością osiągnąć, ustawiając zasady automatycznego zamykania i automatyczne uruchamianie w środowisku laboratoryjnym. 
   
    Na koniec po zakończeniu tworzenia aplikacji, możesz usunąć wszystkich maszyn wirtualnych jednocześnie, uruchamiając skrypt programu PowerShell jednym. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) |Kontrolowanie kosztów przez ustawienie zasad w środowisku laboratoryjnym. |
   | [Usuń wszystkie laboratorium maszyn wirtualnych przy użyciu skryptu programu PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Po zakończeniu tworzenia, należy usunąć wszystkie laboratoriów w ramach jednej operacji.|

1. **Dodawanie sieci wirtualnej do maszyny Wirtualnej** 
   
    DevTest Labs tworzy nową sieć wirtualną (VNET), po każdym utworzeniu laboratorium. Skonfigurowanie własnej sieci wirtualnej — np. przy użyciu usługi ExpressRoute lub sieci VPN typu lokacja lokacja — aby była dostępna podczas tworzenia maszyn wirtualnych można dodać tej sieci Wirtualnej do ustawień sieci wirtualnej w środowisku laboratoryjnym.

    Ponadto jest dostępna, które zostaną dołączone maszyny Wirtualnej do domeny po utworzeniu maszyny Wirtualnej artefakt przyłączanie do domeny usługi Azure Active Directory. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs](devtest-lab-configure-vnet.md) |Dowiedz się, jak skonfigurować sieci wirtualnej w usłudze Azure DevTest Labs przy użyciu witryny Azure portal.|

6. **Udostępnianie laboratorium Każdy deweloper**
   
    Laboratoria są bezpośrednio dostępne przy użyciu łącza, które możesz udostępniać deweloperom. Nie nawet muszą mieć konto platformy Azure, tak długo, jak długo mają [konta Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Deweloperzy nie może wyświetlić maszyny wirtualne utworzone przez innych programistów.  
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Dodawanie projektanta do laboratorium Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Użyj witryny Azure portal, aby dodać deweloperów do środowiska laboratoryjnego.|
   | [Dodawanie deweloperów do laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Aby zautomatyzować Dodawanie deweloperom laboratorium za pomocą programu PowerShell. |
   | [Utwórz link pozwalający laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Dowiedz się, jak deweloperzy można uzyskać dostęp do laboratorium za pośrednictwem hiperłącze.|

7. **Automatyzacja tworzenia laboratorium dla więcej zespołów** 
   
    Można zautomatyzować tworzenie laboratorium, w tym ustawienia niestandardowe, tworząc szablon usługi Resource Manager i użycia w celu ponownego tworzenia laboratoriów identyczne. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium przy użyciu szablonu usługi Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Tworzenie laboratoriów w usłudze Azure DevTest Labs przy użyciu szablonów usługi Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

