---
title: Testowanie definicji interfejsu użytkownika usługi Azure Managed Applications | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób testowania środowiska użytkownika do tworzenia aplikacji zarządzanych platformy Azure za pośrednictwem portalu.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/10/2018
ms.author: tomfitz
ms.openlocfilehash: 2c313538e297c5781b48fcfe9d0d5390f94c97f5
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043844"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Testowanie interfejsu portalu platformy Azure dla aplikacji zarządzanej
Po [tworzenia pliku createUiDefinition.json](create-uidefinition-overview.md) dla aplikacji zarządzanych platformy Azure, musisz przetestowanie środowiska użytkownika. Aby uprościć testowanie, należy użyć skryptu, który ładuje plik w portalu. Nie potrzebujesz rzeczywiście wdrożyć Twoją zarządzaną aplikacją.

## <a name="prerequisites"></a>Wymagania wstępne

* A **createUiDefinition.json** pliku. Jeśli nie masz tego pliku, skopiuj [przykładowy plik](https://github.com/Azure/azure-quickstart-templates/blob/master/test/template-validation-tests/sample-template/createUIDefinition.json) i zapisać go lokalnie.

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="download-test-script"></a>Pobierz skrypt testu

Aby przetestować interfejs użytkownika w portalu, skopiuj jeden z poniższych skryptów na komputer lokalny:

* [Skrypt programu PowerShell ładowane bezpośrednio](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skrypt interfejsu wiersza polecenia platformy Azure w ładowane bezpośrednio](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Uruchom skrypt

Aby wyświetlić plik interfejsu w portalu, uruchom pobranego skryptu. Skrypt tworzy konto magazynu w ramach subskrypcji platformy Azure, a następnie przekazuje plik createUiDefinition.json do konta magazynu. Następnie otwiera portalu i ładuje plik z konta magazynu.

Podaj lokalizację dla konta magazynu, a następnie określ folder, który zawiera plik createUiDefinition.json. Musisz podać czas lokalizacji pierwszego konta magazynu, można uruchomić skryptu lub jeśli konto magazynu zostało usunięte.

W przypadku programu PowerShell użyj polecenia:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory <path-to-folder-with-createuidefinition>
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a <path-to-folder-with-createuidefinition>
```

## <a name="test-your-interface"></a>Testowanie interfejsu użytkownika

Skrypt zostanie otwarty w nowej karcie w przeglądarce. Wyświetla portalu przy użyciu interfejsu do tworzenia aplikacji zarządzanej.

![Wyświetl portal](./media/test-createuidefinition/view-portal.png)

Przed rozpoczęciem wypełniania pól, należy otworzyć narzędzia Web Developer Tools w przeglądarce. **Konsoli** ważne komunikaty dotyczące interfejsu.

![Wybierz pozycję Konsola](./media/test-createuidefinition/select-console.png)

Jeśli Twoja definicja interfejsu zawiera błąd, zostanie wyświetlony opis w konsoli.

![Pokaż błąd](./media/test-createuidefinition/show-error.png)

Podaj wartości dla pól. Po zakończeniu przekonasz się wartości, które są przekazywane do szablonu.

![Pokaż wartości](./media/test-createuidefinition/show-json.png)

## <a name="test-your-solution-files"></a>Testowanie pliki rozwiązania

Teraz, gdy masz pewność, że portal interfejsu działa zgodnie z oczekiwaniami, nadszedł czas na zweryfikować, że plik createUiDefinition prawidłowo jest zintegrowany z pliku mainTemplate.json. Można uruchomić skrypt do weryfikowania testami zawartości pliki rozwiązania, dołączając plik createUiDefinition. Skrypt sprawdza poprawność ze składnią pliku JSON, sprawdza, czy wyrażenie regularne wyrażenia pól tekstowych i upewnia się, że wartości danych wyjściowych w interfejsie portalu odpowiadać parametrom szablonu. Aby uzyskać informacje na temat uruchamiania tego skryptu, zobacz [Uruchom sprawdzanie poprawności statycznego dla szablonów](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Kolejne kroki

Po upewnieniu się, portalu interfejsu, więcej informacji na temat tworzenia swojej [Azure zarządzanych aplikacji, które są dostępne w portalu Marketplace](publish-marketplace-app.md).
