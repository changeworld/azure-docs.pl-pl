---
title: Korzystanie z laboratoriów testowych usługi Azure DevTest dla środowisk testowych maszyn wirtualnych i paas | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać usługi Azure DevTest Labs dla scenariuszy środowiska testowego maszyny Wirtualnej i PaaS.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60623186"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Korzystanie z laboratoriów testowych usługi Azure DevTest dla środowisk testowych maszyn wirtualnych i paas

Usługi Azure DevTest Labs możesz używać do wdrażania wielu kluczowych scenariuszy, ale podstawowy scenariusz polega na używaniu usługi DevTest Labs do hostowania maszyn dla testerów. 

W tym scenariuszu usługa DevTest Labs zapewnia następujące korzyści:

- Testerzy mogą przetestować najnowszą wersję swoich aplikacji dzięki możliwości szybkiej aprowizacji środowisk systemów Windows i Linux przy użyciu szablonów wielokrotnego użytku i artefaktów.
- Testerzy mogą skalować w górę swoje testowane obciążenie, aprowizując wielu agentów testowych.
- Administratorzy mogą kontrolować koszty, zapewniając, że:
  - Testerzy nie mogą uzyskać więcej maszyn wirtualnych, niż potrzebują.
  - Maszyny wirtualne są zamykane, gdy nie są używane.

![Korzystanie z laboratoriów DevTest do szkolenia](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

W tym artykule dowiesz się o różnych funkcjach programu Azure DevTest Labs używanych do spełnienia wymagań testera i szczegółowych krokach, które należy wykonać w celu skonfigurowania laboratorium.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Wdrażanie środowisk testowych za pomocą usługi Azure DevTest Labs
1. **Tworzenie laboratorium** 
   
    Laboratoria są punktem wyjścia w laboratorium devtest platformy Azure. Po utworzeniu laboratorium można wykonywać zadania, takie jak dodawanie użytkowników (testerów) do laboratorium, ustawianie zasad kontroli kosztów, definiowanie obrazów maszyn wirtualnych, które można tworzyć szybko i więcej.  
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium w usłudze Azure DevTest Labs](devtest-lab-create-lab.md) |Dowiedz się, jak utworzyć laboratorium w laboratorium usługi Azure DevTest Labs w witrynie Azure portal. |
2. **Tworzenie maszyn wirtualnych w ciągu kilku minut przy użyciu gotowych obrazów z portalu Marketplace i obrazów niestandardowych** 
   
    Możesz wybrać gotowe obrazy z szerokiej gamy obrazów w portalu Azure Marketplace i udostępnić je w laboratorium. Jeśli gotowe obrazy nie spełniają twoich wymagań, możesz utworzyć obraz niestandardowy, tworząc maszynę wirtualną laboratorium przy użyciu gotowego obrazu z witryny Azure Marketplace, instalując całe potrzebne oprogramowanie i zapisując maszynę wirtualną jako obraz niestandardowy w laboratorium.

    Jeśli będziesz używać obrazów niestandardowych, rozważ użycie fabryki obrazów do tworzenia i rozpowszechniania obrazów. Fabryka obrazów to rozwiązanie konfiguracji jako kodu, które regularnie tworzy i dystrybuuje skonfigurowane obrazy automatycznie. Pozwala to zaoszczędzić czas wymagany do ręcznego skonfigurowania systemu po utworzeniu maszyny Wirtualnej z podstawowym systemem operacyjnym.
  
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie obrazów z witryny Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Dowiedz się, jak umieszczać obrazy w portalu Azure Marketplace na białej liście, udostępniając do wyboru tylko te obrazy, które chcesz uzyskać dla testerów.|
   | [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) |Utwórz obraz niestandardowy, wstępnie instalując potrzebne oprogramowanie, aby testerzy mogli szybko utworzyć maszynę wirtualną przy użyciu niestandardowego obrazu.|
   | [Dowiedz się więcej o fabryce obrazów](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Obejrzyj film, w którego opisano sposób konfigurowania fabryki obrazów i korzystania z niej.|

3. **Tworzenie szablonów wielokrotnego użytku dla maszyn testowych** 
   
    Formuła w laboratorium devtest azure to lista wartości właściwości domyślnych używanych do tworzenia maszyny Wirtualnej. Formułę można utworzyć w laboratorium, wybierając obraz, rozmiar maszyny Wirtualnej (kombinację procesora CPU i pamięci RAM) oraz sieć wirtualną. Każdy tester może zobaczyć formułę w laboratorium i użyć jej do utworzenia maszyny Wirtualnej. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Zarządzanie formułami DevTest Labs w celu tworzenia maszyn wirtualnych](devtest-lab-manage-formulas.md) |Dowiedz się, jak utworzyć formułę, podnosząc obraz, rozmiar maszyny Wirtualnej (kombinacja procesora i pamięci RAM) oraz sieć wirtualną.|

3. **Tworzenie środowisk testowych wielu maszyn wirtualnych** 
   
    Za pomocą szablonów usługi Azure Resource Manager można zdefiniować infrastrukturę i konfigurację rozwiązania platformy Azure i wielokrotnie wdrażać wiele testowych maszyn wirtualnych w spójnym stanie.

    Zasoby usługi Azure PaaS mogą być aprowistyczne w środowisku za pomocą szablonu Menedżera zasobów i wyświetlane w śledzeniu kosztów. Automatyczne zamykanie maszyny Wirtualnej nie ma jednak zastosowania do zasobów PaaS.

    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md) |Dowiedz się, jak wdrożyć wiele maszyn wirtualnych w spójnym stanie dla środowiska testowego.|

4. **Tworzenie artefaktów w celu umożliwienia elastycznego dostosowywania maszyn wirtualnych**

   Artefakty są używane do wdrażania i konfigurowania aplikacji po zainicjowaniu obsługi administracyjnej maszyny Wirtualnej. Artefakty mogą być:

   - Narzędzia, które chcesz zainstalować na maszynie Wirtualnej — takie jak agenci, Fiddler i Visual Studio.
   - Akcje, które chcesz uruchomić na maszynie Wirtualnej — takie jak klonowanie repozytorium.
   - Aplikacje, które chcesz przetestować.

   Wiele artefaktów jest już dostępnych po wyjęciu z pudełka. Jeśli jednak chcesz uzyskać więcej dostosowywania do konkretnych potrzeb, możesz utworzyć własne artefakty niestandardowe.

   Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie niestandardowych artefaktów dla maszyny Wirtualnej DevTest Labs](devtest-lab-artifact-author.md) |Tworzenie własnych artefaktów niestandardowych dla maszyn wirtualnych w laboratorium.|
   | [Dodawanie repozytorium Git do przechowywania artefaktów niestandardowych i szablonów usługi Azure Resource Manager do użycia w laboratoriach usługi Azure DevTest](devtest-lab-add-artifact-repo.md) |Dowiedz się, jak przechowywać niestandardowe artefakty we własnym prywatnym repozytorium Git.|

5. **Kontrola kosztów**
   
    Usługi Azure DevTest Labs umożliwia ustawienie zasad w laboratorium, aby określić maksymalną liczbę maszyn wirtualnych, które mogą być tworzone przez testera w laboratorium. 
   
    Jeśli zespół testowy ma ustawiony harmonogram pracy i chcesz zatrzymać wszystkie maszyny wirtualne o określonej porze dnia, a następnie automatycznie ponownie uruchomić je następnego dnia, można łatwo to osiągnąć, ustawiając zasady automatycznego zamykania i automatycznego uruchamiania w laboratorium. 
   
    Na koniec po zakończeniu tworzenia aplikacji można usunąć wszystkie maszyny wirtualne naraz, uruchamiając jeden skrypt programu PowerShell. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) |Kontroluj koszty, ustawiając zasady w laboratorium. |
   | [Usuwanie wszystkich maszyn wirtualnych w laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Usuń wszystkie laboratoria w jednej operacji po zakończeniu testowania.|

1. **Dodawanie sieci wirtualnej do laboratorium** 
   
    DevTest Labs tworzy nową sieć wirtualną (VNET) za każdym razem, gdy tworzone jest laboratorium. Jeśli skonfigurowano własną sieć wirtualną — na przykład za pomocą usługi ExpressRoute lub sieci VPN lokacji do lokacji — można dodać tę sieć wirtualną do ustawień sieci wirtualnej w laboratorium, tak aby była dostępna podczas tworzenia maszyn wirtualnych.

    Ponadto istnieje artefakt sprzężenia domeny usługi Azure Active Directory, który łączy maszynę wirtualną z domeną podczas tworzenia maszyny wirtualnej. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie sieci wirtualnej w laboratorium devtest platformy Azure](devtest-lab-configure-vnet.md) |Dowiedz się, jak skonfigurować sieć wirtualną w laboratorium devtest azure przy użyciu witryny Azure portal.|

6. **Udostępnianie laboratorium każdemu testerowi**
   
    Labs można uzyskać bezpośrednio za pomocą łącza, które można udostępnić testerom. Nie muszą nawet mieć konta platformy Azure, o ile mają [konto Microsoft.](devtest-lab-faq.md#what-is-a-microsoft-account) Testerzy nie widzą maszyn wirtualnych utworzonych przez innych testerów.  
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Dodawanie testera do laboratorium w laboratorium usługi Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Użyj witryny Azure portal, aby dodać testerów do laboratorium.|
   | [Dodawanie testerów do laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Użyj programu PowerShell, aby zautomatyzować dodawanie testerów do laboratorium. |
   | [Uzyskaj link do laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Dowiedz się, jak testerzy mogą bezpośrednio uzyskiwać dostęp do laboratorium za pośrednictwem hiperłącza.|

7. **Automatyzacja tworzenia laboratorium dla większej liczby zespołów** 
   
    Tworzenie laboratorium, w tym ustawienia niestandardowe, można zautomatyzować, tworząc szablon Menedżera zasobów i używając go do tworzenia identycznych laboratoriów wielokrotnie. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium przy użyciu szablonu Menedżera zasobów](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Tworzenie laboratoriów w laboratoriach devtest platformy Azure przy użyciu szablonów Menedżera zasobów. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

