---
title: Testowanie definicji interfejsu użytkownika usługi Azure Managed Applications | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób testowania środowiska użytkownika do tworzenia aplikacji zarządzanych platformy Azure za pośrednictwem portalu.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 99ca319910be2cb20214172826eb40361abe72f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257585"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testowanie interfejsu portalu usługi Azure Managed Applications

Po [tworzenia pliku createUiDefinition.json](create-uidefinition-overview.md) dla zarządzanych aplikacji, należy przetestować środowisko użytkownika. Aby uprościć testowanie, należy użyć środowiska piaskownicy, która ładuje plik w portalu. Nie potrzebujesz rzeczywiście wdrożyć Twoją zarządzaną aplikacją. Piaskownica przedstawia interfejsu użytkownika w środowisku portalu aktualnych i pełnego ekranu. Alternatywnie można użyć skryptu programu PowerShell dla testowania interfejsu, ale używa widoku starszej wersji portalu. Oba podejścia są wyświetlane w tym artykule. Piaskownica to zalecany sposób nad wersją zapoznawczą interfejsu.

## <a name="prerequisites"></a>Wymagania wstępne

* A **createUiDefinition.json** pliku. Jeśli nie masz tego pliku, skopiuj [przykładowy plik](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="use-sandbox"></a>Użyj ustawienia piaskownica

1. Otwórz [tworzenie piaskownicy definicji interfejsu użytkownika](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Pokaż piaskownicy](./media/test-createuidefinition/show-sandbox.png)

1. Zastąp zawartość pliku createUiDefinition.json pustą definicję. Wybierz **Podgląd**.

   ![Wybierz ikonę Podgląd](./media/test-createuidefinition/select-preview.png)

1. Zostanie wyświetlony formularz, który został utworzony. Można przejść przez środowisko użytkownika i podaj wartości.

   ![Pokaż formularz](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie jest wyświetlany po wybraniu formularza **(wersja zapoznawcza)** , może być błąd składni. Wyszukaj czerwony wskaźnika na pasku przewijania w prawo i przejście do niej.

![Pokaż błąd składni](./media/test-createuidefinition/show-syntax-error.png)

Jeśli formularz nie jest wyświetlany, a zamiast tego będzie widoczna ikona chmury z listy zakończenia, formularz ma błąd, np. Brak właściwości. Otwórz narzędzia dla deweloperów sieci Web w przeglądarce. **Konsoli** ważne komunikaty dotyczące interfejsu.

![Pokaż błąd](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Użyj skryptu testu

Aby przetestować interfejs użytkownika w portalu, skopiuj jeden z poniższych skryptów na komputer lokalny:

* [Skrypt programu PowerShell ładowane bezpośrednio](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skrypt interfejsu wiersza polecenia platformy Azure w ładowane bezpośrednio](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Aby wyświetlić plik interfejsu w portalu, uruchom pobranego skryptu. Skrypt tworzy konto magazynu w ramach subskrypcji platformy Azure, a następnie przekazuje plik createUiDefinition.json do konta magazynu. Konto magazynu jest tworzone podczas pierwszego uruchomienia skryptu, lub jeśli konto magazynu zostało usunięte. Jeśli konto magazynu już istnieje w subskrypcji platformy Azure, skrypt ponownie go używa. Skrypt spowoduje otwarcie portalu i ładuje plik z konta magazynu.

Podaj lokalizację dla konta magazynu, a następnie określ folder, który zawiera plik createUiDefinition.json.

W przypadku programu PowerShell użyj polecenia:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Jeśli plik createUiDefinition.json znajduje się w tym samym folderze co skrypt, a masz już utworzone konto magazynu, nie trzeba podać te parametry.

W przypadku programu PowerShell użyj polecenia:

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
./sideload-createuidef.sh
```

Skrypt zostanie otwarty w nowej karcie w przeglądarce. Wyświetla portalu przy użyciu interfejsu do tworzenia aplikacji zarządzanej.

![Wyświetl portal](./media/test-createuidefinition/view-portal.png)

Podaj wartości dla pól. Po zakończeniu przekonasz się wartości, które są przekazywane do szablonu.

![Pokaż wartości](./media/test-createuidefinition/show-json.png)

Te wartości można użyć jako plik parametrów do testowania wdrożenia szablonu.

Jeśli portalu zawiesza się na ekranie Podsumowanie, może być błąd w sekcji danych wyjściowych. Na przykład użytkownik może mieć odwołuje się do formant, który nie istnieje. Jeśli parametr w danych wyjściowych jest pusta, parametr może odwoływać się do właściwości, która nie istnieje. Na przykład odwołanie do formantu jest prawidłowy, ale odwołania do właściwości jest nieprawidłowa.

## <a name="test-your-solution-files"></a>Testowanie pliki rozwiązania

Teraz, gdy masz pewność, że portal interfejsu działa zgodnie z oczekiwaniami, nadszedł czas na zweryfikować, że plik createUiDefinition prawidłowo jest zintegrowany z pliku mainTemplate.json. Można uruchomić skrypt do weryfikowania testami zawartości pliki rozwiązania, dołączając plik createUiDefinition. Skrypt sprawdza poprawność ze składnią pliku JSON, sprawdza, czy wyrażenie regularne wyrażenia pól tekstowych i upewnia się, że wartości danych wyjściowych w interfejsie portalu odpowiadać parametrom szablonu. Aby uzyskać informacje na temat uruchamiania tego skryptu, zobacz [Uruchom sprawdzanie poprawności statycznego dla szablonów](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Kolejne kroki

Po upewnieniu się, portalu interfejsu, więcej informacji na temat tworzenia swojej [Azure zarządzanych aplikacji, które są dostępne w portalu Marketplace](publish-marketplace-app.md).
