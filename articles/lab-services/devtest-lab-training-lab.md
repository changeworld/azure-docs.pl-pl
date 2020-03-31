---
title: Korzystanie z laboratoriów DevTest w usłudze Azure do szkolenia | Dokumenty firmy Microsoft
description: Ten artykuł zawiera szczegółowe kroki, które można wykonać, aby skonfigurować laboratorium do szkolenia w laboratorium devtest platformy Azure.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 8f0a930d6e3c04548ade71f6d4e3294114eb60a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759981"
---
# <a name="use-azure-devtest-labs-for-training"></a>Korzystanie z laboratoriów DevTest w usłudze Azure do szkolenia
Usługi Azure DevTest Labs może służyć do implementowania wielu kluczowych scenariuszy oprócz dev/test. Jednym z tych scenariuszy jest utworzenie laboratorium do szkolenia. Usługa Azure DevTest Labs umożliwia utworzenie laboratorium, w którym można podać niestandardowe szablony, których każdy stażysta może używać do tworzenia identycznych i odizolowanych środowisk do szkolenia. Można zastosować zasady, aby upewnić się, że środowiska szkoleniowe są dostępne dla każdego stażysty tylko wtedy, gdy ich potrzebują i zawierają wystarczającą ilość zasobów — takich jak maszyny wirtualne — wymaganych do szkolenia. Na koniec możesz łatwo udostępnić laboratorium stażystom, do którego mogą uzyskać dostęp za pomocą jednego kliknięcia.

![Korzystanie z laboratoriów DevTest do szkolenia](./media/devtest-lab-training-lab/devtest-lab-training.png)

Laboratorium usługi Azure DevTest spełnia następujące wymagania, które są wymagane do prowadzenia szkoleń w dowolnym środowisku wirtualnym: 

* Stażyści nie widzą maszyn wirtualnych utworzonych przez innych stażystów
* Każda maszyna treningowa powinna być identyczna
* Stażyści mogą szybko zapewniać swoje środowisko szkoleniowe
* Kontroluj koszty, zapewniając, że stażyści nie mogą uzyskać większej liczby maszyn wirtualnych niż potrzebują do szkolenia, a także zamykać maszyny wirtualne, gdy ich nie używają
* Łatwe udostępnianie laboratorium szkoleniowego każdemu stażyście
* Ponowne wykorzystanie laboratorium szkoleniowego

W tym artykule dowiesz się o różnych funkcjach programu Azure DevTest Labs, które mogą służyć do spełnienia wcześniej opisanych wymagań szkoleniowych i szczegółowych kroków, które można wykonać, aby skonfigurować laboratorium do szkolenia.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Wdrażanie szkoleń za pomocą usługi Azure DevTest Labs
1. **Tworzenie laboratorium** 
   
    Laboratoria są punktem wyjścia w laboratorium devtest platformy Azure. Po utworzeniu laboratorium można wykonywać zadania, takie jak dodawanie użytkowników (stażystów) do laboratorium, ustawianie zasad kontroli kosztów, definiowanie obrazów maszyn wirtualnych, które można tworzyć szybko i nie tylko.   
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium w usłudze Azure DevTest Labs](devtest-lab-create-lab.md) |Dowiedz się, jak utworzyć laboratorium w laboratorium usługi Azure DevTest Labs w witrynie Azure portal. |
2. **Tworzenie maszyn wirtualnych szkoleniowych w ciągu kilku minut przy użyciu gotowych obrazów z rynku i obrazów niestandardowych** 
   
    Możesz wybrać gotowe obrazy z szerokiej gamy obrazów w portalu Azure Marketplace i udostępnić je uczestnikom szkolenia w laboratorium. Jeśli gotowe obrazy nie spełniają twoich wymagań, możesz utworzyć obraz niestandardowy, tworząc maszynę wirtualną laboratorium przy użyciu gotowego obrazu z portalu Azure Marketplace, instalując całe oprogramowanie potrzebne do szkolenia i zapisując maszynę wirtualną jako obraz niestandardowy w laboratorium. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie obrazów z witryny Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Dowiedz się, jak umieszczać obrazy w portalu Azure Marketplace na białej liście; udostępnienie do wyboru tylko obrazów, które chcesz uzyskać do szkolenia. |
   | [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) |Utwórz obraz niestandardowy, wstępnie instalując oprogramowanie potrzebne do szkolenia, aby uczestnicy szkolenia mogli szybko utworzyć maszynę wirtualną przy użyciu niestandardowego obrazu. |
3. **Tworzenie szablonów wielokrotnego użytku dla maszyn szkoleniowych** 
   
    Formuła w laboratorium devtest azure to lista wartości właściwości domyślnych używanych do tworzenia maszyny Wirtualnej. Formułę można utworzyć w laboratorium, wybierając obraz, rozmiar maszyny Wirtualnej (kombinację procesora CPU i pamięci RAM) oraz sieć wirtualną. Każdy stażysta może zobaczyć formułę w laboratorium i użyć jej do utworzenia maszyny Wirtualnej. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Zarządzanie formułami DevTest Labs w celu tworzenia maszyn wirtualnych](devtest-lab-manage-formulas.md) |Dowiedz się, jak utworzyć formułę, podnosząc obraz, rozmiar maszyny Wirtualnej (kombinacja procesora i pamięci RAM) oraz sieć wirtualną. |
4. **Kontrola kosztów**
   
    Usługi Azure DevTest Labs umożliwia ustawienie zasad w laboratorium, aby określić maksymalną liczbę maszyn wirtualnych, które mogą być tworzone przez stażystę w laboratorium. 
   
    Jeśli prowadzisz wielodniowe szkolenie i chcesz zatrzymać wszystkie maszyny wirtualne o określonej porze dnia, a następnie automatycznie ponownie uruchomić je następnego dnia, można łatwo to osiągnąć, ustawiając zasady automatycznego zamykania i automatycznego uruchamiania w laboratorium. 
   
    Na koniec po zakończeniu szkolenia można usunąć wszystkie maszyny wirtualne naraz, uruchamiając jeden skrypt programu PowerShell. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) |Kontroluj koszty, ustawiając zasady w laboratorium. |
   | [Usuwanie wszystkich maszyn wirtualnych w laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Usuń wszystkie laboratoria w jednej operacji po zakończeniu szkolenia. |
5. **Udostępnianie laboratorium każdemu stażyście**
   
    Dostęp do laboratoriów można uzyskać bezpośrednio za pomocą łącza, który udostępniasz swoim uczestnikom. Stażyści nie muszą nawet mieć konta platformy Azure, o ile mają [konto Microsoft.](devtest-lab-faq.md#what-is-a-microsoft-account) Stażyści nie widzą maszyn wirtualnych utworzonych przez innych stażystów.  
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Dodawanie stażysty do laboratorium w laboratorium usługi Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Użyj witryny Azure Portal, aby dodać uczestników szkolenia do laboratorium szkoleniowego. |
   | [Dodawanie uczestników szkolenia do laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Program PowerShell służy do automatyzacji dodawania uczestników szkolenia do laboratorium szkoleniowego. |
   | [Uzyskaj link do laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Dowiedz się, jak dostęp do laboratorium można uzyskać bezpośrednio za pośrednictwem hiperłącza. |
6. **Ponownie i ponownie wynauż laboratorium** 
   
    Tworzenie laboratorium, w tym ustawienia niestandardowe, można zautomatyzować, tworząc szablon Menedżera zasobów i używając go do tworzenia identycznych laboratoriów wielokrotnie. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium przy użyciu szablonu Menedżera zasobów](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Tworzenie laboratoriów w laboratoriach devtest platformy Azure przy użyciu szablonów Menedżera zasobów. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

