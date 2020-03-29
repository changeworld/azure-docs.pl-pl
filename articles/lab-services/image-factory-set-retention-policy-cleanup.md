---
title: Konfigurowanie zasad przechowywania w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady przechowywania, wyczyścić fabrykę i wycofać stare obrazy z DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759418"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Konfigurowanie zasad przechowywania w laboratoriach devtest platformy Azure
W tym artykule opisano ustawianie zasad przechowywania, czyszczenie fabryki i wycofywanie starych obrazów ze wszystkich innych laboratoriów DevTest w organizacji. 

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że postępowałeś zgodnie z tymi artykułami przed kontynuowaniem:

- [Tworzenie fabryki obrazów](image-factory-create.md)
- [Uruchamianie fabryki obrazów z usługi Azure DevOps](image-factory-set-up-devops-lab.md)
- [Zapisywanie obrazów niestandardowych i ich dystrybucja do wielu laboratoriów](image-factory-save-distribute-custom-images.md)

Następujące elementy powinny być już na miejscu:

- Laboratorium fabryki obrazów w laboratorium DevTest Labs platformy Azure
- Co najmniej jeden docelowy program Azure DevTest Labs, w którym fabryka będzie dystrybuować złote obrazy
- Projekt DevOps platformy Azure używany do automatyzacji fabryki obrazów.
- Lokalizacja kodu źródłowego zawierająca skrypty i konfigurację (w naszym przykładzie w tym samym projekcie DevOps użytym powyżej)
- Definicja kompilacji do organizowania zadań programu Azure Powershell
 
## <a name="setting-the-retention-policy"></a>Ustawianie zasad przechowywania
Przed skonfigurowaniem kroków oczyszczania należy określić liczbę historycznych obrazów, które mają być zachowane w laboratoriach DevTest. Po wykonaniu [artykułu Uruchom fabrykę obrazów z usługi Azure DevOps,](image-factory-set-up-devops-lab.md) skonfigurowano różne zmienne kompilacji. Jednym z nich był **ImageRetention**. Ustaw tę zmienną na `1`, co oznacza, że DevTest Labs nie będzie obsługiwać historii obrazów niestandardowych. Dostępne będą tylko najnowsze obrazy rozproszone. Jeśli ta zmienna `2`zostanie zmieniona na , zostanie zachowany najnowszy obraz rozproszony plus poprzedni. Tę wartość można ustawić, aby zdefiniować liczbę obrazów historycznych, które mają być zachowane w laboratoriach DevTest.

## <a name="cleaning-up-the-factory"></a>Czyszczenie fabryki
Pierwszym krokiem w czyszczeniu fabryki jest usunięcie złotych maszyn wirtualnych obrazu z fabryki obrazów. Istnieje skrypt do wykonania tego zadania, podobnie jak nasze poprzednie skrypty. Pierwszym krokiem jest dodanie innego zadania **programu Azure Powershell** do definicji kompilacji, jak pokazano na poniższej ilustracji:

![Krok programu PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Po wprowadzeniu nowego zadania na liście zaznacz element i wypełnij wszystkie szczegóły, jak pokazano na poniższej ilustracji:

![Oczyszczanie starych obrazów zadania programu PowerShell](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Parametry skryptu `-DevTestLabName $(devTestLabName)`to: .

## <a name="retire-old-images"></a>Wycofywanie starych obrazów 
To zadanie usuwa wszystkie stare obrazy, zachowując tylko historię odpowiadającą zmiennej **kompilacji ImageRetention.** Dodaj dodatkowe zadanie **kompilacji programu Azure Powershell** do naszej definicji kompilacji. Po dodaniu zaznacz zadanie i wypełnij szczegóły, jak pokazano na poniższej ilustracji: 

![Wycofywanie starych obrazów zadaniem programu PowerShell](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Parametry skryptu to:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Kolejka kompilacji
Po zakończeniu definicji kompilacji, kolejka do nowej kompilacji, aby upewnić się, że wszystko działa. Po pomyślnym zakończeniu kompilacji nowe obrazy niestandardowe są wyświetlane w laboratorium docelowym, a jeśli sprawdzisz laboratorium fabryki obrazów, nie zostanie wyświetlone żadne aprowizacji maszyn wirtualnych. Ponadto w kolejce do dalszych kompilacji, zobaczysz zadania oczyszczania wycofywania starych obrazów niestandardowych z DevTest Labs zgodnie z wartością przechowywania ustawioną w zmiennych kompilacji.

> [!NOTE]
> Jeśli wykonano potok kompilacji na końcu ostatniego artykułu z serii, ręcznie usuń maszyny wirtualne, które zostały utworzone w laboratorium fabryki obrazu przed kolejkowania nowej kompilacji.  Ręczny krok oczyszczania jest potrzebny tylko wtedy, gdy wszystko ustawimy i zweryfikujemy działanie.



## <a name="summary"></a>Podsumowanie
Teraz masz uruchomione fabryki obrazów, które można generować i rozpowszechniać niestandardowe obrazy do laboratoriów na żądanie. W tym momencie jest to tylko kwestia prawidłowego skonfigurowania obrazów i zidentyfikowania laboratoriów docelowych. Jak wspomniano w poprzednim artykule, plik **Labs.json** znajdujący się w folderze **Konfiguracja** określa, które obrazy powinny być udostępniane w każdym z laboratoriów docelowych. Podczas dodawania innych Laboratoriów DevTest do organizacji, wystarczy dodać wpis w Labs.json dla nowego laboratorium.

Dodawanie nowego obrazu do fabryki jest również proste. Jeśli chcesz dołączyć nowy obraz w fabryce, otwórz [witrynę Azure portal](https://portal.azure.com), przejdź do fabryki DevTest Labs, wybierz przycisk, aby dodać maszynę wirtualną i wybierz żądany obraz i artefakty. Zamiast wybierać przycisk **Utwórz,** aby utworzyć nową maszynę wirtualną, wybierz pozycję **Wyświetl szablon usługi Azure Resource Manager**" i zapisz szablon jako plik .json gdzieś w folderze **GoldenImages** w repozytorium. Przy następnym uruchomieniu fabryki obrazów zostanie utworzony obraz niestandardowy.


## <a name="next-steps"></a>Następne kroki
1. [Zaplanuj kompilację/wydanie,](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) aby okresowo uruchamiać fabrykę obrazów. Regularnie odświeża obrazy generowane fabrycznie.
2. Zrób więcej złotych obrazów dla swojej fabryki. Można również rozważyć [utworzenie artefaktów](devtest-lab-artifact-author.md) do skryptu dodatkowe elementy zadań konfiguracji maszyny Wirtualnej i uwzględnić artefakty w obrazach fabrycznych.
4. Utwórz [oddzielną kompilację/wersję,](/azure/devops/pipelines/overview?view=azure-devops-2019) aby uruchomić skrypt **DistributeImages** oddzielnie. Ten skrypt można uruchomić po wprowadzeniu zmian w pliku Labs.json i skopiowaniu obrazów do laboratoriów docelowych bez konieczności ponownego tworzenia wszystkich obrazów.

