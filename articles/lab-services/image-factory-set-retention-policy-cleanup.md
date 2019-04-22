---
title: Tworzenie fabryki danych obrazu w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć fabrykę danych obrazu niestandardowego w usłudze Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 48412b3006a462fcc9c77219f42fb41d08f2df61
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59490743"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Utwórz fabrykę obrazu niestandardowego w usłudze Azure DevTest Labs
W tym artykule opisano ustawienia zasad przechowywania, czyszczenie fabryki i wycofywana starych obrazów z wszystkich innych usłudze DevTest Labs w organizacji. 

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że przed kontynuacją zostały wykonane następujące artykuły:

- [Utwórz fabrykę obrazu](image-factory-create.md)
- [Uruchom fabrykę obrazu z DevOps platformy Azure](image-factory-set-up-devops-lab.md)
- [Zapisywanie niestandardowych obrazów i dystrybucji do wielu labs](image-factory-save-distribute-custom-images.md)

Następujące elementy już powinny być stosowane:

- Laboratorium na potrzeby fabrycznie obrazu w usłudze Azure DevTest Labs
- Co najmniej jeden docelowy usługi Azure DevTest Labs, gdzie fabryka będziesz dystrybuować złoty obrazów
- Projekt DevOps platformy Azure używane do automatyzowania fabrycznie obrazu.
- Lokalizacji kodu źródłowego, zawierający skrypty i konfiguracji (w tym przykładzie w tym samym projekcie DevOps powyżej)
- Definicję kompilacji do koordynowania zadań programu Azure Powershell
 
## <a name="setting-the-retention-policy"></a>Ustawianie zasad przechowywania
Przed skonfigurowaniem czynności czyszczenia należy zdefiniować liczbę historycznych obrazy mają być zachowane w usłudze DevTest Labs. Po wykonaniu [Uruchom fabrykę obrazu z DevOps platformy Azure](image-factory-set-up-devops-lab.md) artykułu, możesz skonfigurować różne Tworzenie zmiennych. Jeden z nich był **ImageRetention**. Ta zmienna jest ustawiona `1`, co oznacza, że usługa DevTest Labs nie będzie prowadzić historię obrazów niestandardowych. Tylko najnowsza wersja rozproszonych obrazy będą dostępne. Jeśli zmienisz tej zmiennej `2`, najnowsze rozproszonych obrazu, a także poprzednie zostaną zachowane. Można ustawić tę wartość, aby zdefiniować liczbę historycznych obrazy, które chcesz zachować w Twojej usłudze DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Czyszczenie fabryka
Pierwszym krokiem podczas czyszczenia fabryka jest usunięcie złotego obrazu maszyny wirtualne z fabryki obrazu. Brak skrypt, aby wykonać to zadanie, podobnie jak nasze wcześniejsze skrypty. Pierwszym krokiem jest dodanie innej **programu Azure Powershell** zadań do definicji kompilacji, jak pokazano na poniższej ilustracji:

![Krok programu PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Po utworzeniu nowego zadania na liście, wybierz element, a następnie wypełnij wszystkie szczegóły, jak pokazano na poniższej ilustracji:

![Stare zadania programu PowerShell obrazy oczyszczania](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Parametry skryptu: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Wycofywanie starego obrazów 
To zadanie powoduje usunięcie starych obrazów, w zachowanie tylko historii pasujący **ImageRetention** Tworzenie zmiennej. Dodaj dodatkowy **programu Azure Powershell** kompilacji zadanie, aby nasza definicja kompilacji. Po dodaniu, wybierz zadanie, a następnie wypełnij szczegóły, jak pokazano na poniższej ilustracji: 

![Wycofywanie starego zadań programu PowerShell obrazy](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Parametry skryptu są następujące: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Dodaj kompilację do kolejki
Teraz, że zostały wykonane definicji kompilacji, Dodaj do kolejki kompilację nowy, aby upewnić się, że wszystko działa. Po zakończeniu kompilacji pomyślnie nowe niestandardowe obrazy będą wyświetlane w laboratorium docelowego, a jeśli sprawdzić laboratorium fabrycznie obrazu, zostanie wyświetlony bez aprowizowanych maszyn wirtualnych. Ponadto jeśli tworząc dodatkowe kompilacje kolejki, zobaczysz zadania oczyszczania, wycofywanie się starych obrazów niestandardowych w usłudze DevTest Labs zgodnie do przechowywania wartości w zmiennych kompilacji.

> [!NOTE]
> Jeśli potok kompilacji na końcu ostatniego artykuł został wykonany w tej serii, należy ręcznie usunąć maszyn wirtualnych, które zostały utworzone w laboratorium fabryki obraz przed kolejkowania nowej kompilacji.  Krok ręczne czyszczenie jest potrzebny tylko wtedy, gdy skonfigurujemy wszystko i sprawdź, czy działa.



## <a name="summary"></a>Podsumowanie
Masz teraz uruchomione fabryki obrazu, która może wygenerować i dystrybuowanie obrazów niestandardowych do laboratoriów na żądanie. Ten punkt, jego kwestia pobierania obrazów prawidłowo skonfigurowane i identyfikowanie labs docelowego. Zgodnie z opisem w poprzednim artykule **Labs.json** plik znajdujący się w Twojej **konfiguracji** folder określa obrazy, które ma zostać udostępniona w każdym z laboratoriów docelowego. W miarę dodawania innych DevTest Labs dla Twojej organizacji, należy po prostu dodaj odpowiedni wpis w Labs.json dla nowego laboratorium.

Dodawanie nowego obrazu z fabryką również jest proste. Jeśli chcesz dołączyć nowy obraz w fabryce otworzysz [witryny Azure portal](https://portal.azure.com), przejdź do usługi fabryki DevTest Labs, wybierz przycisk aby dodać Maszynę wirtualną, a następnie wybierz obraz z witryny marketplace żądaną i artefaktów. Zamiast zaznaczania **Utwórz** przycisk, aby utworzyć nową maszynę Wirtualną, zaznacz **widok usługi Azure Resource Manager szablon**"i zapisać szablon w formacie JSON gdzieś w ramach **GoldenImages** folderu w repozytorium. Przy następnym uruchomieniu fabryką obrazu zostanie utworzony obraz niestandardowy.


## <a name="next-steps"></a>Kolejne kroki
1. [Planowanie kompilacji/wydania](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) okresowe uruchamianie fabrycznie obrazu. Odświeża jej generowane fabryki obrazów w regularnych odstępach czasu.
2. Wprowadź więcej obrazów złoty fabryki. Można również rozważyć [tworzenie artefaktów](devtest-lab-artifact-author.md) skryptu dodatkowe fragmenty zadania konfiguracji maszyny Wirtualnej i zawierać artefakty do obrazów fabryki.
4. Tworzenie [oddzielić kompilacji/wydania](/azure/devops/pipelines/overview?view=azure-devops-2019) do uruchomienia **DistributeImages** skrypt oddzielnie. Ten skrypt można uruchomić, gdy dokonać zmian Labs.json i pobieranie obrazów skopiowane do laboratoriów docelowej bez konieczności ponownego tworzenia wszystkie obrazy ponownie.

