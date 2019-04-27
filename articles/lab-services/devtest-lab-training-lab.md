---
title: Korzystanie z usługi Azure DevTest Labs potrzeby szkolenia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure DevTest Labs na potrzeby scenariuszy szkoleniowych.
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
ms.openlocfilehash: 0a7ce1640636c6fba246584d098043a91990b9a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622846"
---
# <a name="use-azure-devtest-labs-for-training"></a>Korzystanie z usługi Azure DevTest Labs potrzeby szkolenia
Usługa Azure DevTest Labs może służyć do implementowania wielu kluczowych scenariuszy, oprócz tworzenia i testowania. Jest jednym z tych scenariuszy konfigurowania laboratorium na potrzeby szkolenia. Usługa Azure DevTest Labs umożliwia tworzenie laboratorium, w którym mogą udostępniać szablony niestandardowe, które każdy szkoleniowych można użyć do utworzenia identyczne i wyizolowanego środowiska do trenowania. Można zastosować zasad w celu zapewnienia środowiska szkoleń są dostępne dla każdego szkoleniowych, tylko wtedy, gdy są potrzebne i zawiera za mało zasobów — takich jak virtual machines — wymagana na szkolenia. Na koniec laboratorium mogą łatwo udostępniać za pomocą Zaleć, które mogą oni uzyskać dostęp za pomocą jednego kliknięcia.

![Korzystanie DevTest Labs na potrzeby szkolenia](./media/devtest-lab-training-lab/devtest-lab-training.png)

Usługa Azure DevTest Labs, spełnia następujące wymagania, które są wymagane do prowadzenia szkoleń w dowolnym środowisku wirtualnym: 

* Maszyny wirtualne utworzone przez innych Zaleć Zaleć nie są widoczne.
* Do każdej maszyny szkolenia powinny być identyczne
* Zaleć można szybko inicjować obsługę środowisk szkolenia
* Kontrolowanie kosztów przez zapewnienie, że Zaleć nie można pobrać więcej maszyn wirtualnych, niż jest to wymagane dla szkoleń, a także zamykania maszyn wirtualnych, jeśli ich nie używają
* Łatwe udostępnianie laboratorium szkolenia każda szkoleniowych
* Ponowne użycie laboratorium szkolenia ponownego

W tym artykule poznasz różne funkcje usługi Azure DevTest Labs, których można użyć w celu spełnienia wymagań dotyczących szkoleń opisany wcześniej i szczegółowy opis kroków, które można wykonać, aby skonfigurować laboratorium na potrzeby szkolenia.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementowanie szkolenie przy użyciu usługi Azure DevTest Labs
1. **Tworzenie laboratorium** 
   
    Laboratoria stanowią punkt początkowy w usłudze Azure DevTest Labs. Po utworzeniu laboratorium, można wykonywać zadania takie jak dodawanie użytkowników (Zaleć) do laboratorium, ustawienia zasad w celu kontrolowania kosztów, zdefiniuj obrazów maszyn wirtualnych, które mogą szybko tworzyć i nie tylko.   
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium w usłudze Azure DevTest Labs](devtest-lab-create-lab.md) |Dowiedz się, jak tworzenie laboratorium w usłudze Azure DevTest Labs w witrynie Azure portal. |
2. **Tworzenie maszyn wirtualnych szkolenia w ciągu kilku minut przy użyciu gotowych do użycia w portalu marketplace, obrazy i obrazy niestandardowe** 
   
    Można wybrać gotowych do użycia obrazy z szerokiej gamy obrazów w portalu Azure Marketplace i udostępnić je Zaleć w środowisku laboratoryjnym. Jeśli gotowych do użycia obrazy nie spełniają Twoich wymagań, możesz utworzyć niestandardowy obraz, od utworzenia laboratorium na maszynę Wirtualną przy użyciu gotowych do użycia obrazu z witryny Azure Marketplace, instalowania oprogramowania potrzebnych na szkolenie i zapiszesz maszynę Wirtualną jako obraz niestandardowy w środowisku laboratoryjnym. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie obrazów z portalu Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Dowiedz się, jak można umieścić na liście dozwolonych obrazów portalu Azure Marketplace; co do wyboru dostępne tylko obrazy mają szkolenia. |
   | [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) |Tworzenie niestandardowego obrazu, instalując wstępnie oprogramowanie, potrzebnych szkolenia, dzięki czemu Zaleć można szybko utworzyć Maszynę wirtualną przy użyciu niestandardowego obrazu. |
3. **Tworzenie szablonów wielokrotnego użycia dla maszyn szkolenia** 
   
    Formuły w usłudze Azure DevTest Labs znajduje się lista domyślnych wartości właściwości, używany do tworzenia maszyny Wirtualnej. Aby utworzyć formułę w środowisku laboratoryjnym, pobrania obrazu, rozmiar maszyny Wirtualnej (kombinację procesora CPU i pamięci RAM) i sieci wirtualnej. Każdy szkoleniowych można wyświetlić formuły w laboratorium i użyć go do utworzenia maszyny Wirtualnej. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Zarządzanie formułami laboratorium do tworzenia maszyn wirtualnych](devtest-lab-manage-formulas.md) |Dowiedz się, jak można utworzyć formułę, wybierając obrazu, rozmiar maszyny Wirtualnej (kombinację procesora CPU i pamięci RAM) i sieci wirtualnej. |
4. **Kontrolowanie kosztów**
   
    Usługa Azure DevTest Labs umożliwia ustawienie zasad w środowisku laboratoryjnym, aby określić maksymalną liczbę maszyn wirtualnych, które mogą być tworzone przez szkoleniowych w środowisku laboratoryjnym. 
   
    Jeśli przeprowadzamy wielodniowe szkoleniowych i do zatrzymania wszystkich maszyn wirtualnych o określonej porze dnia, a następnie automatycznie uruchom je ponownie następnego dnia, możesz łatwo to zrobić przez ustawienie automatycznego zamykania i automatycznego uruchamiania zasad w środowisku laboratoryjnym. 
   
    Na koniec po zakończeniu szkolenia można usunąć wszystkich maszyn wirtualnych jednocześnie, uruchamiając skrypt programu PowerShell jednym. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) |Kontrolowanie kosztów przez ustawienie zasad w środowisku laboratoryjnym. |
   | [Usuń wszystkie laboratorium maszyn wirtualnych przy użyciu skryptu programu PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Po zakończeniu szkolenia, należy usunąć wszystkie laboratoriów w ramach jednej operacji. |
5. **Udostępnianie laboratorium każdego szkoleniowych**
   
    Laboratoria są bezpośrednio dostępne przy użyciu łącza, które możesz udostępniać swoje Zaleć. Twoje Zaleć nie nawet muszą mieć konto platformy Azure, tak długo, jak długo mają [konta Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Maszyny wirtualne utworzone przez innych Zaleć Zaleć nie są widoczne.  
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Dodawanie szkoleniowych do laboratorium Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Użyj witryny Azure portal, aby dodać Zaleć do środowiska laboratoryjnego szkolenia. |
   | [Dodaj Zaleć do laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Aby zautomatyzować Dodawanie Zaleć do środowiska laboratoryjnego szkolenie za pomocą programu PowerShell. |
   | [Utwórz link pozwalający laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Dowiedz się, jak laboratorium są bezpośrednio dostępne hiperłącze. |
6. **Ponowne użycie laboratorium ponownego** 
   
    Można zautomatyzować tworzenie laboratorium, w tym ustawienia niestandardowe, tworząc szablon usługi Resource Manager i użycia w celu ponownego tworzenia laboratoriów identyczne. 
   
    Dowiedz się więcej, klikając łącza w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium przy użyciu szablonu usługi Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Tworzenie laboratoriów w usłudze Azure DevTest Labs przy użyciu szablonów usługi Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

