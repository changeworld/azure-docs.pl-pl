---
title: Tworzenie niestandardowych artefaktów dla swojej maszyny wirtualnej w usłudze DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć własne artefakty do użycia z usługą Azure DevTest Labs.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399196"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Tworzenie niestandardowych artefaktów dla swojej maszyny wirtualnej w usłudze DevTest Labs

Obejrzyj poniższy klip wideo z omówieniem kroków opisanych w tym artykule:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Przegląd
Możesz użyć *artefaktów* wdrażanie i konfigurowanie aplikacji po zainicjowaniu obsługi administracyjnej maszyny Wirtualnej. Artefakt składa się z plikiem definicji artefaktów i inne pliki skryptów, które są przechowywane w folderze w repozytorium Git. Pliki definicji artefaktu składają się z kodu JSON i wyrażeń, które służą do określania, co chcesz zainstalować na maszynie Wirtualnej. Na przykład można zdefiniować nazwę artefakt, polecenie do uruchomienia i parametry, które są dostępne, gdy polecenie jest wykonywane. Mogą odwoływać się do innych plików skryptu w pliku definicji artefaktu według nazwy.

## <a name="artifact-definition-file-format"></a>Format pliku definicji artefaktu
Poniższy przykład przedstawia sekcje, które tworzą podstawowa struktura pliku definicji:

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
| $schema |Nie |Lokalizacja pliku schematu JSON. Plik schematu JSON może pomóc można sprawdzić poprawność pliku definicji. |
| title |Tak |Nazwa artefaktu wyświetlane w środowisku laboratoryjnym. |
| description |Tak |Opis artefaktu wyświetlane w środowisku laboratoryjnym. |
| iconUri |Nie |Identyfikator URI ikona wyświetlana w środowisku laboratoryjnym. |
| targetOsType |Tak |System operacyjny maszyny wirtualnej, w którym zainstalowano artefaktu. Obsługiwane opcje to Windows i Linux. |
| parameters |Nie |Wartości, które znajdują się po uruchomieniu polecenia install artefaktów na maszynie. Dzięki temu można dostosować swoje artefaktu. |
| runCommand |Yes |Artefakt zainstalować polecenia, który jest wykonywany na maszynie Wirtualnej. |

### <a name="artifact-parameters"></a>Parametry artefaktu
W sekcji parametrów w pliku definicji należy określić wartości, które użytkownik może wprowadzić podczas instalacjo artefaktu. Mogą odwoływać się do tych wartości w poleceniu instalacji artefaktu.

Aby zdefiniować parametry, należy użyć następującej strukturze:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| Nazwa elementu | Wymagana? | Opis |
| --- | --- | --- |
| type |Tak |Typ wartości parametru. Przejrzyj następującą listę dozwolonych typów. |
| displayName |Tak |Nazwa parametru, który jest wyświetlany użytkownikowi w środowisku laboratoryjnym. |
| description |Tak |Opis parametru, który jest wyświetlany w środowisku laboratoryjnym. |

Dozwolone typy to:

* ciąg (dowolny prawidłowy ciąg JSON)
* int (Dowolna prawidłowa JSON liczba całkowita)
* wartość logiczna (wszystkie prawidłowe logiczna JSON)
* Tablica (wszystkie prawidłową tablicą JSON)

## <a name="secrets-as-secure-strings"></a>Wpisy tajne jako bezpieczny ciąg
Zadeklaruj wpisów tajnych jako bezpieczny ciąg. Oto Składnia deklaracji parametru bezpieczny ciąg, w ramach `parameters` części **artifactfile.json** pliku:

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Polecenie instalacji artefaktu, uruchom skrypt programu PowerShell, który przyjmuje bezpieczny ciąg utworzony za pomocą polecenia ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Artifactfile.json kompletny przykład i artifact.ps1 (skrypt programu PowerShell) można znaleźć [tego przykładu w usłudze GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

Jest innym ważnym punktem należy pamiętać, aby nie rejestrować wpisy tajne w konsoli jako dane wyjściowe są przechwytywane do debugowania użytkownika. 

## <a name="artifact-expressions-and-functions"></a>Artefakt wyrażeń i funkcji
Można używać wyrażeń i funkcji do konstruowania artefaktu zainstalować polecenia.
Wyrażenia są ujęte w nawiasach kwadratowych ([i]), które są oceniane, po zainstalowaniu artefaktu. Wyrażenia może występować w dowolnym miejscu w wartości ciągu JSON. Wyrażenia zawsze zwraca inną wartość JSON. Aby użyć literału ciągu, rozpoczynająca się od nawiasu ([), należy użyć dwóch nawiasy kwadratowe ([[).
Zazwyczaj należy użyć wyrażeń z usługą functions do utworzenia wartości. Tak samo, jak w języku JavaScript, wywołania funkcji są sformatowane jako **functionName (arg1 arg2, arg3)** .

Na poniższej liście przedstawiono typowe funkcje:

* **parameters(parameterName)** : Zwraca wartość parametru, który znajduje się po uruchomieniu polecenia artefaktu.
* **concat (arg1, arg2 arg3...)** : To połączenie wielu wartości ciągów. Ta funkcja może przybierać różne argumentów.

Poniższy przykład pokazuje, jak używać wyrażeń i funkcji do tworzenia wartości:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Tworzenie niestandardowego artefaktu

1. Instalowanie edytora JSON. Potrzebujesz edytora JSON do pracy z plikami definicji artefaktu. Firma Microsoft zaleca używanie [programu Visual Studio Code](https://code.visualstudio.com/), który jest dostępny dla Windows, Linux i OS X.
2. Pobierz przykładowy plik definicji artifactfile.json. Zapoznaj się z artefaktów utworzonych przez zespół usługi DevTest Labs w naszym [repozytorium GitHub](https://github.com/Azure/azure-devtestlab). Utworzyliśmy Bogata biblioteka artefaktów, które mogą pomóc Ci tworzenie własnych artefaktów. Pobierz plik definicji artefaktu i zmodyfikować go do tworzenia własnych artefaktów.
3. Korzystaj z funkcji IntelliSense. Użyj funkcji IntelliSense, aby wyświetlić prawidłowe elementy, które można używać do tworzenia pliku definicji artefaktu. Widać też różne opcje dla wartości elementu. Na przykład podczas edytowania **targetOsType** elementu, IntelliSense pokazuje dwie opcje dla Windows lub Linux.
4. Store artefaktu w [publicznego repozytorium Git na potrzeby usługi DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) lub [repozytorium Git](devtest-lab-add-artifact-repo.md). W publicznym repozytorium można wyświetlić artefaktów udostępnione przez innych użytkowników można używać bezpośrednio lub dostosować je do swoich potrzeb.
   
   1. Należy utworzyć oddzielny katalog dla każdego artefaktu. Nazwa katalogu powinna być taka sama jak nazwa artefaktu.
   2. Store plik definicji artefaktu (artifactfile.json) w katalogu, który został utworzony.
   3. Store skrypty, które są wywoływane z polecenia install artefaktu.
      
      Poniżej przedstawiono przykładowy wygląd folderu artefaktów:
      
      ![Przykładowy folder artefaktu](./media/devtest-lab-artifact-author/git-repo.png)
5. Jeśli używasz własnego repozytorium do przechowywania artefaktów, Dodaj repozytorium do laboratorium, wykonując instrukcje przedstawione w artykule: [Dodawanie repozytorium Git dla artefaktów i szablonów](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Pokrewne artykuły:
* [Jak diagnozowanie błędów artefaktów w usłudze DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Dołączanie maszyny Wirtualnej do istniejącej domeny usługi Active Directory przy użyciu szablonu usługi Resource Manager w usłudze DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Dodawanie repozytorium artefaktów usługi Git do laboratorium](devtest-lab-add-artifact-repo.md).
