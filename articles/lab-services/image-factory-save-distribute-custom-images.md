---
title: Zapisz i dystrybuowanie obrazów w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622685"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Zapisywanie obrazów niestandardowych i ich dystrybucja do wielu laboratoriów
Ten artykuł omawia zawiera kroki, aby zapisać obrazy niestandardowe z już utworzonych maszyn wirtualnych (VM). Omówiono również sposób dystrybucji tych obrazów niestandardowych innych użytkownik usługi DevTest Labs w organizacji.

## <a name="prerequisites"></a>Wymagania wstępne
Następujące elementy już powinny być stosowane:

- Laboratorium na potrzeby fabrycznie obrazu w usłudze Azure DevTest Labs.
- Projekt DevOps platformy Azure używanej do zautomatyzowania fabrycznie obrazu.
- Lokalizacji kodu źródłowego, zawierający skrypty i konfiguracji (w tym przykładzie w tym samym projekcie metodyki DevOps, opisane w poprzednim kroku).
- Tworzenie definicji do koordynowania zadań programu Azure Powershell.

Jeśli to konieczne, wykonaj czynności opisane w [Uruchom fabrykę obrazu z DevOps platformy Azure](image-factory-set-up-devops-lab.md) utworzyć lub skonfigurować te elementy. 

## <a name="save-vms-as-generalized-vhds"></a>Zapisz maszyny wirtualne jako uogólniony wirtualne dyski twarde
Zapisz istniejących maszyn wirtualnych jako uogólniony wirtualnych dysków twardych.  Brak przykładowy skrypt programu PowerShell, aby zapisać istniejących maszyn wirtualnych jako uogólniony wirtualnych dysków twardych. Aby go użyć, najpierw Dodaj kolejny **programu Azure Powershell** zadań do definicji kompilacji, jak pokazano na poniższej ilustracji:

![Dodaj krok programu Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Po utworzeniu nowego zadania na liście, wybierz element, aby firma Microsoft może wypełnić wszystkie szczegóły, jak pokazano na poniższej ilustracji: 

![Ustawienia programu PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Uogólnione a wyspecjalizowane obrazy niestandardowe
W [witryny Azure portal](https://portal.azure.com), podczas tworzenia obrazu niestandardowego z maszyny wirtualnej, można wybrać uogólnionego lub wyspecjalizowanego obrazu niestandardowego.

- **Wyspecjalizowany obraz niestandardowy:** Narzędzie Sysprep/anulowania aprowizacji nie została uruchomiona na maszynie. Oznacza to, że obraz jest dokładną kopię dysku systemu operacyjnego na istniejącej maszynie wirtualnej (Migawka).  Tych samych plików, aplikacji, konta użytkowników, nazwy komputera i tak dalej występują wszystkie podczas tworzenia nowej maszyny za pomocą tego niestandardowego obrazu.
- **Uogólnionego obrazu niestandardowego:** Narzędzie Sysprep/anulowania aprowizacji zostało uruchomione na maszynie.  Po uruchomieniu tego procesu go usuwa konta użytkowników, usuwa nazwę komputera, usuwa gałęzi rejestru użytkownika, itp., z celem uogólnianie obrazu, dzięki czemu można dostosowywać, tworząc inną maszynę wirtualną.  Uogólnienie maszyny wirtualnej (przez uruchomienie narzędzia sysprep), proces niszczy bieżącej maszyny wirtualnej — przestanie być funkcjonalności.

Skrypt przyciągania obrazów niestandardowych w fabryce obraz zostanie zapisany wirtualne dyski twarde na wszystkie maszyny wirtualne utworzone w poprzednim kroku (zidentyfikowane na podstawie tagu zasobu na platformie Azure).

## <a name="update-configuration-for-distributing-images"></a>Zaktualizuj konfigurację do dystrybucji obrazów
Następnym krokiem w procesie jest wypychanie obrazów niestandardowych z laboratorium fabrycznie obrazu się do innych laboratoria, które ich potrzebują. Jest główną częścią tego procesu **labs.json** pliku konfiguracji. Można znaleźć tego pliku w **konfiguracji** folderu znajdującego się w fabryce obrazu.

Istnieją dwa kluczowych zagadnień wymienionych w pliku konfiguracyjnym labs.json:

- Unikatowa identyfikacja laboratorium określonego miejsca docelowego przy użyciu Identyfikatora subskrypcji i nazwę laboratorium.
- Określony zestaw obrazów, które powinny zostać przeniesiony do laboratorium jako ścieżki względne do głównej konfiguracji. Można określić całego folderu (na przykład aby pobrać wszystkie obrazy w tym folderze) zbyt.

Oto przykładowy plik labs.json z dwa laboratoria na liście. W tym przypadku jest rozpowszechniana obrazów do dwóch różnych laboratoriów.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Tworzenie zadania kompilacji
Za pomocą tych samych kroków, jak już wspomniano wcześniej w tym artykule dodać kolejny **programu Azure Powershell** zadanie kompilacji dla Ciebie definicji kompilacji. Wypełnij szczegóły, jak pokazano na poniższej ilustracji: 

![Tworzenie zadań do dystrybucji obrazów](./media/save-distribute-custom-images/second-build-task-powershell.png)

Dostępne są następujące parametry: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

To zadanie pobiera wszelkich niestandardowych obrazów, istnieje w fabryce obrazu i wypychanie ich do dowolnej labs zdefiniowane w pliku Labs.json.

## <a name="queue-the-build"></a>Dodaj kompilację do kolejki
Po zakończeniu zadań kompilacji dystrybucji w kolejce kompilację nowy, aby upewnić się, że wszystko działa. Po pomyślnym zakończeniu kompilacji nowe obrazy niestandardowe będą widoczne w środowisku laboratoryjnym w miejsce docelowe, wprowadzonego w pliku konfiguracji Labs.json.

## <a name="next-steps"></a>Kolejne kroki
W następnym artykule z tej serii aktualizacji za pomocą zasad i oczyszczania kroki przechowywania fabrycznie obrazu: [Ustawić zasady przechowywania i uruchamiania skryptów czyszczenia](image-factory-set-retention-policy-cleanup.md).
