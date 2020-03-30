---
title: Testowanie pliku definicji interfejsu użytkownika
description: W tym artykule opisano sposób testowania środowiska użytkownika do tworzenia aplikacji zarządzanej platformy Azure za pośrednictwem portalu.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: e2d075a58872f9337c7d1faa642a48047e2f9ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250179"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testowanie interfejsu portalu dla aplikacji zarządzanych platformy Azure

Po [utworzeniu pliku createUiDefinition.json](create-uidefinition-overview.md) dla zarządzanej aplikacji należy przetestować środowisko użytkownika. Aby uprościć testowanie, należy użyć środowiska piaskownicy, które ładuje plik w portalu. Nie trzeba faktycznie wdrożyć aplikację zarządzana. Piaskownica przedstawia interfejs użytkownika w bieżącym, pełnoekranowym interfejsie portalu. Można też użyć skryptu do testowania interfejsu. Oba podejścia są pokazane w tym artykule. Piaskownica jest zalecanym sposobem wyświetlania podglądu interfejsu.

## <a name="prerequisites"></a>Wymagania wstępne

* Plik **createUiDefinition.json.** Jeśli nie masz tego pliku, skopiuj [przykładowy plik](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="use-sandbox"></a>Korzystanie z piaskownicy

1. Otwórz pole [izoluj definicji interfejsu użytkownika](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Pokaż piaskownicę](./media/test-createuidefinition/show-sandbox.png)

1. Zastąp pustą definicję zawartością pliku createUiDefinition.json. Wybierz **opcję Podgląd**.

   ![Wybierz podgląd](./media/test-createuidefinition/select-preview.png)

1. Zostanie wyświetlony utworzony formularz. Można przejść przez środowisko użytkownika i wypełnić wartości.

   ![Pokaż formularz](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli formularz nie jest wyświetlany po **wybraniu opcji Podgląd,** może wystąpić błąd składni. Poszukaj czerwonego wskaźnika na prawym pasku przewijania i przejdź do niego.

![Pokaż błąd składni](./media/test-createuidefinition/show-syntax-error.png)

Jeśli formularz nie jest wyświetlany, a zamiast tego zostanie wyświetlone ikona chmury z kroplą łzy, formularz ma błąd, taki jak brak właściwości. Otwórz narzędzia web developer w przeglądarce. **Konsola** wyświetla ważne komunikaty dotyczące interfejsu.

![Pokaż błąd](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Użyj skryptu testowego

Aby przetestować interfejs w portalu, skopiuj jeden z następujących skryptów do komputera lokalnego:

* [Skrypt ładowania bocznego programu PowerShell — moduł Az](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Skrypt ładowania po stronie programu PowerShell — moduł platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skrypt ładowania po stronie interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Aby wyświetlić plik interfejsu w portalu, uruchom pobrany skrypt. Skrypt tworzy konto magazynu w ramach subskrypcji platformy Azure i przekazuje plik createUiDefinition.json do konta magazynu. Konto magazynu jest tworzone przy pierwszym uruchomieniu skryptu lub w przypadku usunięcia konta magazynu. Jeśli konto magazynu już istnieje w subskrypcji platformy Azure, skrypt ponownie go używa. Skrypt otwiera portal i ładuje plik z konta magazynu.

Podaj lokalizację konta magazynu i określ folder, w którym znajduje się plik createUiDefinition.json.

W przypadku programu PowerShell użyj polecenia:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Jeśli plik createUiDefinition.json znajduje się w tym samym folderze co skrypt i utworzono już konto magazynu, nie trzeba podawać tych parametrów.

W przypadku programu PowerShell użyj polecenia:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```bash
./sideload-createuidef.sh
```

Skrypt otworzy nową kartę w przeglądarce. Wyświetla portal z interfejsem do tworzenia aplikacji zarządzanej.

Podaj wartości pól. Po zakończeniu zobaczysz wartości, które są przekazywane do szablonu, które można znaleźć w konsoli narzędzi programistycznych przeglądarki.

![Pokaż wartości](./media/test-createuidefinition/show-json.png)

Tych wartości można użyć jako pliku parametrów do testowania szablonu wdrożenia.

Jeśli portal zawiesza się na ekranie podsumowania, może wystąpić błąd w sekcji danych wyjściowych. Na przykład może mieć odwołanie do formantu, który nie istnieje. Jeśli parametr w danych wyjściowych jest pusty, parametr może odwoływać się do właściwości, która nie istnieje. Na przykład odwołanie do formantu jest prawidłowe, ale odwołanie do właściwości jest nieprawidłowe.

## <a name="test-your-solution-files"></a>Testowanie plików rozwiązania

Teraz, po zweryfikowaniu interfejsu portalu działa zgodnie z oczekiwaniami, nadszedł czas, aby sprawdzić, czy plik createUiDefinition jest poprawnie zintegrowany z plikiem mainTemplate.json. Można uruchomić test skryptu sprawdzania poprawności, aby przetestować zawartość plików rozwiązania, w tym pliku createUiDefinition. Skrypt sprawdza poprawność składni JSON, sprawdza wyrażenia wyrażenia regularne w polach tekstowych i upewnia się, że wartości wyjściowe interfejsu portalu są zgodne z parametrami szablonu. Aby uzyskać informacje dotyczące uruchamiania tego skryptu, zobacz [Uruchamianie statycznych sprawdzania poprawności szablonów](https://github.com/Azure/azure-quickstart-templates/tree/master/test).

## <a name="next-steps"></a>Następne kroki

Po sprawdzeniu poprawności interfejsu portalu dowiedz się więcej o [udostępnianiu aplikacji zarządzanej platformy Azure w portalu Marketplace](publish-marketplace-app.md).
