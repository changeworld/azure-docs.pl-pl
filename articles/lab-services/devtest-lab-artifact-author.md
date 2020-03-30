---
title: Tworzenie niestandardowych artefaktów dla maszyny wirtualnej DevTest Labs | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć własne artefakty do użycia w laboratoriach DevTest.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 69b83590fb9b25c68d231b732b985ba633bb6884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66399196"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Tworzenie niestandardowych artefaktów dla maszyny wirtualnej DevTest Labs

Obejrzyj poniższy klip wideo, aby zapoznać się z krokami opisanymi w tym artykule:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Omówienie
Artefakty *artifacts* można użyć do wdrożenia i skonfigurowania aplikacji po aprowizowaniu maszyny Wirtualnej. Artefakt składa się z pliku definicji artefaktu i innych plików skryptów, które są przechowywane w folderze w repozytorium Git. Pliki definicji artefaktów składają się z JSON i wyrażeń, których można użyć do określenia, co chcesz zainstalować na maszynie Wirtualnej. Na przykład można zdefiniować nazwę artefaktu, polecenie do uruchomienia i parametry, które są dostępne po uruchomieniu polecenia. Nazwy można odwoływać się do innych plików skryptów w pliku definicji artefaktu.

## <a name="artifact-definition-file-format"></a>Format pliku definicji artefaktu
W poniższym przykładzie przedstawiono sekcje, które składają się na podstawową strukturę pliku definicji:

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Nazwa elementu | Wymagana? | Opis |
| --- | --- | --- |
| $schema |Nie |Lokalizacja pliku schematu JSON. Plik schematu JSON może pomóc w przetestowaniu ważności pliku definicji. |
| title |Tak |Nazwa artefaktu wyświetlanego w laboratorium. |
| description |Tak |Opis artefaktu wyświetlanego w laboratorium. |
| ikonuri |Nie |Identyfikator URI ikony wyświetlanej w laboratorium. |
| targetOsType |Tak |System operacyjny maszyny Wirtualnej, w którym jest zainstalowany artefakt. Obsługiwane opcje to Windows i Linux. |
| parameters |Nie |Wartości, które są podane, gdy polecenie instalacji artefaktu jest uruchamiane na komputerze. Pomaga to dostosować artefakt. |
| runCommand (uruchamianie) |Tak |Polecenie instalacji artefaktu, które jest wykonywane na maszynie wirtualnej. |

### <a name="artifact-parameters"></a>Parametry artefaktu
W sekcji parametrów pliku definicji określ, które wartości użytkownik może wprowadzić podczas instalowania artefaktu. Można odwołać się do tych wartości w poleceniu instalacji artefaktu.

Aby zdefiniować parametry, należy użyć następującej struktury:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| Nazwa elementu | Wymagana? | Opis |
| --- | --- | --- |
| type |Tak |Typ wartości parametru. Zobacz poniższą listę dozwolonych typów. |
| displayName |Tak |Nazwa parametru, który jest wyświetlany użytkownikowi w laboratorium. |
| description |Tak |Opis parametru, który jest wyświetlany w laboratorium. |

Dozwolone typy to:

* (dowolny prawidłowy ciąg JSON)
* int (dowolna prawidłowa ćwolna ważność JSON)
* bool (dowolny prawidłowy JSON Boolean)
* tablica (dowolna prawidłowa tablica JSON)

## <a name="secrets-as-secure-strings"></a>Wpisy tajne jako bezpieczne ciągi
Deklaruj wpisy tajne jako bezpieczne ciągi. Oto składnia deklarowania parametru bezpiecznego ciągu `parameters` w sekcji pliku **artifactfile.json:**

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Dla polecenia instalacji artefaktu uruchom skrypt programu PowerShell, który przyjmuje bezpieczny ciąg utworzony za pomocą polecenia ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Pełny przykład artifactfile.json i artifact.ps1 (skrypt programu PowerShell) można znaleźć [w tym przykładzie w usłudze GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

Innym ważnym punktem, na który należy zwrócić uwagę, nie jest rejestrowanie wpisów tajnych w konsoli, ponieważ dane wyjściowe są przechwytywane do debugowania użytkownika. 

## <a name="artifact-expressions-and-functions"></a>Wyrażenia i funkcje artefaktów
Do skonstruowania polecenia instalacji artefaktu można użyć wyrażeń i funkcji.
Wyrażenia są łączone z nawiasami ([ i ]) i są oceniane po zainstalowaniu artefaktu. Wyrażenia mogą być wyświetlane w dowolnym miejscu wartości ciągu JSON. Wyrażenia zawsze zwracają inną wartość JSON. Jeśli trzeba użyć ciągu literału rozpoczynającego się od nawiasu ([), należy użyć dwóch nawiasów ([[).
Zazwyczaj używa się wyrażeń z funkcjami do konstruowania wartości. Podobnie jak w języku JavaScript, wywołania funkcji są formatowane jako **functionName(arg1, arg2, arg3)**.

Na poniższej liście przedstawiono typowe funkcje:

* **parameters(parameterName)**: Zwraca wartość parametru, która jest podana podczas uruchamiania polecenia artefaktu.
* **concat(arg1, arg2, arg3,..... )**: Łączy wiele wartości ciągu. Ta funkcja może przyjmować różne argumenty.

W poniższym przykładzie pokazano, jak używać wyrażeń i funkcji do konstruowania wartości:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Tworzenie niestandardowego artefaktu

1. Zainstaluj edytor JSON. Do pracy z plikami definicji artefaktów potrzebny jest edytor JSON. Zalecamy użycie [programu Visual Studio Code](https://code.visualstudio.com/), który jest dostępny dla systemów Windows, Linux i OS X.
2. Pobierz przykładowy plik definicji artifactfile.json. Sprawdź artefakty utworzone przez zespół DevTest Labs w naszym [repozytorium GitHub.](https://github.com/Azure/azure-devtestlab) Stworzyliśmy bogatą bibliotekę artefaktów, które pomogą Ci tworzyć własne artefakty. Pobierz plik definicji artefaktu i wprowadzać w nim zmiany, aby utworzyć własne artefakty.
3. Skorzystaj z IntelliSense. Użyj IntelliSense, aby wyświetlić prawidłowe elementy, których można użyć do skonstruowania pliku definicji artefaktu. Można również zobaczyć różne opcje dla wartości elementu. Na przykład podczas edytowania **targetOsType** element IntelliSense pokazuje dwie opcje, dla systemu Windows lub Linux.
4. Przechowuj artefakt w [publicznym repozytorium Git dla DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) lub [własnego repozytorium Git.](devtest-lab-add-artifact-repo.md) W publicznym repozytorium można wyświetlać artefakty udostępnione przez inne osoby, których można używać bezpośrednio lub dostosowywać je do własnych potrzeb.
   
   1. Utwórz osobny katalog dla każdego artefaktu. Nazwa katalogu powinna być taka sama jak nazwa artefaktu.
   2. Przechowuj plik definicji artefaktu (artifactfile.json) w utworzonym katalogu.
   3. Przechowuj skrypty, do których odwołuje się polecenie instalacji artefaktu.
      
      Oto przykład tego, jak może wyglądać folder artefaktów:
      
      ![Przykład folderu artefaktów](./media/devtest-lab-artifact-author/git-repo.png)
5. Jeśli używasz własnego repozytorium do przechowywania artefaktów, dodaj repozytorium do laboratorium, wykonując instrukcje w artykule: [Dodaj repozytorium Git dla artefaktów i szablonów](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Pokrewne artykuły:
* [Jak zdiagnozować błędy artefaktów w DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Dołączanie maszyny Wirtualnej do istniejącej domeny usługi Active Directory przy użyciu szablonu Menedżera zasobów w laboratoriach DevTest](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać repozytorium artefaktów Git do laboratorium.](devtest-lab-add-artifact-repo.md)
