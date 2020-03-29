---
title: Zapisywanie i rozpowszechnianie obrazów w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule przedstawiono kroki, aby zapisać obrazy niestandardowe z już utworzonych maszyn wirtualnych (VM) w laboratorium usługi Azure DevTest.
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
ms.openlocfilehash: e5bc8e5041bfe6d95e3ff1a93bb3338ccead5bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759435"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Zapisywanie obrazów niestandardowych i ich dystrybucja do wielu laboratoriów
W tym artykule przedstawiono kroki, aby zapisać obrazy niestandardowe z już utworzonych maszyn wirtualnych (maszyny wirtualne). Obejmuje również sposób dystrybucji tych obrazów niestandardowych do innych laboratoriów DevTest w organizacji.

## <a name="prerequisites"></a>Wymagania wstępne
Następujące elementy powinny być już na miejscu:

- Laboratorium fabryki obrazów w laboratorium devtest azure.
- Projekt DevOps platformy Azure, który jest używany do automatyzacji fabryki obrazu.
- Lokalizacja kodu źródłowego zawierająca skrypty i konfigurację (w naszym przykładzie w tym samym projekcie DevOps, o którym mowa w poprzednim kroku).
- Definicja kompilacji do organizowania zadań programu Azure Powershell.

W razie potrzeby wykonaj kroki opisane w [obszarze Uruchom fabrykę obrazów z usługi Azure DevOps,](image-factory-set-up-devops-lab.md) aby utworzyć lub skonfigurować te elementy. 

## <a name="save-vms-as-generalized-vhds"></a>Zapisywanie maszyn wirtualnych jako uogólnionych vhdów
Zapisz istniejące maszyny wirtualne jako uogólnione wirtualne.  Istnieje przykładowy skrypt programu PowerShell, aby zapisać istniejące maszyny wirtualne jako uogólnione wirtualne. Aby go użyć, najpierw dodaj inne zadanie **programu Azure Powershell** do definicji kompilacji, jak pokazano na poniższej ilustracji:

![Krok Dodaj program Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Gdy masz nowe zadanie na liście, wybierz element, abyśmy mogli wypełnić wszystkie szczegóły, jak pokazano na poniższej ilustracji: 

![Ustawienia programu PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Uogólnione i wyspecjalizowane obrazy niestandardowe
W [witrynie Azure portal](https://portal.azure.com), podczas tworzenia obrazu niestandardowego z maszyny wirtualnej, można wybrać, aby mieć uogólniony lub wyspecjalizowany obraz niestandardowy.

- **Wyspecjalizowany obraz niestandardowy:** Sysprep/Deprovision NIE był uruchamiany na maszynie. Oznacza to, że obraz jest dokładną kopią dysku systemu operacyjnego na istniejącej maszynie wirtualnej (migawka).  Te same pliki, aplikacje, konta użytkowników, nazwa komputera i tak dalej, są obecne, gdy tworzymy nowy komputer z tego niestandardowego obrazu.
- **Uogólniony obraz niestandardowy:** Sysprep/Deprovision został uruchomiony na maszynie.  Po uruchomieniu tego procesu usuwa konta użytkowników, usuwa nazwę komputera, usuwa gałęzie rejestru użytkownika itp., w celu uogólniania obrazu, dzięki czemu można go dostosować podczas tworzenia innej maszyny wirtualnej.  Po uogólnianiu maszyny wirtualnej (przez uruchomienie sysprep), proces niszczy bieżącą maszynę wirtualną — nie będzie już działać.

Skrypt do przyciągania obrazów niestandardowych w fabryce obrazów zapisze dyski VD dla wszystkich maszyn wirtualnych utworzonych w poprzednim kroku (zidentyfikowanych na podstawie tagu na zasobie na platformie Azure).

## <a name="update-configuration-for-distributing-images"></a>Aktualizacja konfiguracji dystrybucji obrazów
Następnym krokiem w procesie jest wypchnięcie obrazów niestandardowych z laboratorium fabryki obrazów do innych laboratoriów, które ich potrzebują. Podstawową częścią tego procesu jest plik konfiguracyjny **labs.json.** Ten plik można znaleźć w folderze **Konfiguracja** dołączonym do fabryki obrazów.

Istnieją dwie kluczowe rzeczy wymienione w pliku konfiguracyjnym labs.json:

- Unikatowa identyfikacja określonego laboratorium docelowego przy użyciu identyfikatora subskrypcji i nazwy laboratorium.
- Określony zestaw obrazów, które powinny być wypychane do laboratorium jako ścieżki względne do katalogu głównego konfiguracji. Można również określić cały folder (aby uzyskać wszystkie obrazy w tym folderze).

Oto przykład labs.json plik z dwóch laboratoriów wymienionych. W takim przypadku są dystrybucji obrazów do dwóch różnych laboratoriach.

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
Korzystając z tych samych kroków, które zostały wcześniej widoczne w tym artykule, dodaj dodatkowe zadanie kompilacji **programu Azure Powershell** do definicji kompilacji. Wypełnij szczegóły, jak pokazano na poniższej ilustracji: 

![Tworzenie zadania dystrybucji obrazów](./media/save-distribute-custom-images/second-build-task-powershell.png)

Parametry są następujące:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

To zadanie wykonuje wszystkie niestandardowe obrazy obecne w fabryce obrazów i wypycha je do wszystkich laboratoriów zdefiniowanych w pliku Labs.json.

## <a name="queue-the-build"></a>Kolejka kompilacji
Po zakończeniu zadania kompilacji dystrybucji, kolejka do nowej kompilacji, aby upewnić się, że wszystko działa. Po pomyślnym zakończeniu kompilacji nowe obrazy niestandardowe pojawią się w laboratorium docelowym, które zostało wprowadzone do pliku konfiguracyjnego Labs.json.

## <a name="next-steps"></a>Następne kroki
W następnym artykule z serii można zaktualizować fabrykę obrazów za pomocą zasad przechowywania i czynności oczyszczania: [Ustaw zasady przechowywania i uruchom skrypty oczyszczania](image-factory-set-retention-policy-cleanup.md).
