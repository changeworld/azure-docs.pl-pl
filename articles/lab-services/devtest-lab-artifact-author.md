---
title: Tworzenie niestandardowych artefaktów dla maszyny wirtualnej DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć własne artefakty do korzystania z usługi Azure DevTest Labs.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 268b9af7835c51d78812b35aff5aaac585961b01
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787626"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Tworzenie niestandardowych artefaktów dla maszyny wirtualnej DevTest Labs

Obejrzyj film następujące omówienie kroków opisanych w tym artykule:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>Przegląd
Można użyć *artefakty* do wdrażania i konfigurowania aplikacji po udostępnić maszynie Wirtualnej. Artefakt składa się z plikiem definicji artefaktów i innych plików skryptów, które są przechowywane w folderze w repozytorium Git. Pliki definicji artefaktów składają się z kodu JSON i wyrażeń używanych do określania, jakie ma zostać zainstalowany na maszynie Wirtualnej. Na przykład można zdefiniować nazwę artefaktu, polecenie do uruchomienia i parametrów, które są dostępne po uruchomieniu polecenia. Według nazwy mogą odwoływać się do innych plików skryptów w pliku definicji artefaktów.

## <a name="artifact-definition-file-format"></a>Format pliku definicji artefaktów
W poniższym przykładzie przedstawiono sekcje, które tworzą podstawowej struktury plików definicji:

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
| $schema |Nie |Lokalizacja pliku schematu JSON. Plik schematu JSON może pomóc w testowania ważności pliku definicji. |
| tytuł |Yes |Nazwa artefaktu wyświetlany w laboratorium. |
| description |Yes |Opis artefaktu wyświetlany w laboratorium. |
| Identyfikator iconUri |Nie |Identyfikator URI ikony wyświetlane w laboratorium. |
| targetOsType |Yes |System operacyjny maszyny wirtualnej, w którym zainstalowano artefaktu. Obsługiwane opcje to system Windows i Linux. |
| parameters |Nie |Wartości, które znajdują się po uruchomieniu polecenia install artefaktów na maszynie. Dzięki temu można dostosować z artefaktów. |
| UruchomPolecenie |Yes |Artefaktu zainstalować polecenia, która jest wykonywana na maszynie Wirtualnej. |

### <a name="artifact-parameters"></a>Parametry artefaktów
W sekcji Parametry pliku definicji określić wartości, które użytkownik może wprowadzić podczas instalacji artefaktu. Mogą odwoływać się do tych wartości w poleceniu instalacji artefaktu.

Aby określić parametry, należy użyć następującej struktury:

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Nazwa elementu | Wymagana? | Opis |
| --- | --- | --- |
| type |Yes |Typ wartości parametru. Przejrzyj następującą listę dozwolonych typów. |
| Nazwa wyświetlana |Yes |Nazwa parametru, który jest wyświetlany użytkownikowi w laboratorium. | |
| description |Yes |Opis parametru, który jest wyświetlany w laboratorium. |

Dozwolone typy to:

* ciąg (dowolny prawidłowy ciąg JSON)
* int (wszystkie prawidłowe JSON liczba całkowita)
* wartość logiczna (wszystkie prawidłowe JSON wartość logiczna)
* Tablica (nieprawidłowa tablicy JSON)

## <a name="artifact-expressions-and-functions"></a>Artefaktu wyrażeń i funkcji
Można używać wyrażeń i funkcji do tworzenia artefaktu polecenie instalacji.
Wyrażenia są ujęte w nawiasy kwadratowe ([i]) i są oceniane po zainstalowaniu artefaktu. Wyrażenia mogą występować w dowolnym miejscu w wartości ciągu JSON. Wyrażenia zawsze zwracają inną wartość JSON. Aby użyć literału ciągu, rozpoczynająca się od nawiasu ([), należy użyć dwóch nawiasy kwadratowe ([[).
Zazwyczaj można za pomocą wyrażenia funkcji do skonstruowania wartość. Podobnie jak w języku JavaScript, wywołania funkcji są sformatowane jako **functionName (arg1, arg2, arg3)**.

Poniższa lista zawiera typowe funkcje:

* **parameters(parameterName)**: zwraca wartość parametru jest dostępne po uruchomieniu polecenia artefaktu.
* **concat (arg1, arg2, arg3,...)** : Łączy wiele wartości ciągu. Ta funkcja może zająć wiele argumentów.

Poniższy przykład przedstawia sposób używać wyrażeń i funkcji do tworzenia wartości:

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Tworzenie niestandardowych artefaktów

1. Zainstaluj edytor JSON. Należy edytora JSON, aby pracować z plikami definicji artefaktów. Firma Microsoft zaleca używanie [Visual Studio Code](https://code.visualstudio.com/), która jest dostępna dla systemu Windows, Linux i OS X.
2. Pobierz plik przykładowy artifactfile.json definicji. Zapoznaj się z artefakty utworzone przez zespół DevTest Labs w naszym [repozytorium GitHub](https://github.com/Azure/azure-devtestlab). Utworzono rozbudowane biblioteki artefaktów, które mogą pomóc tworzyć własne artefaktów. Pobierz plik definicji artefaktów i wprowadzania zmian do tworzenia własnych artefaktów.
3. Upewnij się, użyj IntelliSense. Użyj funkcji IntelliSense, aby wyświetlić prawidłowe elementy, których można użyć do utworzenia pliku definicji artefaktów. Możesz również sprawdzić różnych opcji dla wartości elementu. Na przykład podczas edytowania **targetOsType** elementu IntelliSense przedstawiono dwa wybory dla systemu Windows lub Linux.
4. Przechowywanie artefaktów w [repozytorium Git](devtest-lab-add-artifact-repo.md).
   
   1. Utwórz oddzielne katalog dla każdego artefaktu. Nazwa katalogu powinna być taka sama jak nazwa artefaktu.
   2. Przechowuj plik definicji artefaktów (artifactfile.json) w katalogu, który został utworzony.
   3. Przechowywania skryptów, które są przywoływane z polecenia install artefaktu.
      
      Oto przykład może wyglądać folderu artefaktu:
      
      ![Przykład folderu artefaktów](./media/devtest-lab-artifact-author/git-repo.png)
5. Dodaj repozytorium artefaktów do laboratorium. Zobacz [dodać repozytorium Git artefakty i szablony](devtest-lab-add-artifact-repo.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Jak diagnozować awarie artefaktów w usłudze DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Dołącz Maszynę wirtualną do istniejącej domeny usługi Active Directory przy użyciu szablonu usługi Resource Manager w usłudze DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Dodaj repozytorium artefaktów Git do laboratorium](devtest-lab-add-artifact-repo.md).

