---
title: Konwertowanie modelu
description: Szybki start, który pokazuje kroki konwersji dla modelu niestandardowego.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b1bf2c365229a418870250a78a733f9870fb5bb0
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892380"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Szybki start: konwertowanie modelu do renderowania

W [przewodnikach Szybki start: Renderowanie modelu za pomocą unity](render-model.md), można dowiedzieć się, jak używać projektu próbki Unity do renderowania wbudowanego modelu. W tym przewodniku pokazano, jak przekonwertować własne modele.

Omawiane tematy:

> [!div class="checklist"]
>
> * Konfigurowanie konta magazynu obiektów blob platformy Azure dla danych wejściowych i wyjściowych
> * Przekazywanie i konwertowanie modelu 3D do użytku z renderowaniem zdalnym platformy Azure
> * Dołącz przekonwertowany model 3D w aplikacji do renderowania

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [szybki start: Renderowanie modelu za pomocą unity](render-model.md)
* Instalowanie programu Azure PowerShell [(dokumentacja)](https://docs.microsoft.com/powershell/azure/)
  * Otwieranie programu PowerShell z prawami administratora
  * Uruchomić:`Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Omówienie

Moduł renderowania na serwerze nie może działać bezpośrednio z formatami modelu źródłowego, takimi jak FBX lub GLTF. Zamiast tego wymaga modelu, aby być w zastrzeżonym formacie binarnym.
Usługa konwersji zużywa modele z magazynu obiektów blob platformy Azure i zapisuje przekonwertowane modele z powrotem do dostarczonego kontenera magazynu obiektów blob platformy Azure.

Potrzebne elementy:

* Subskrypcja platformy Azure
* Konto "StorageV2" w twojej subskrypcji
* Kontener magazynu obiektów blob dla modelu wejściowego
* Kontener magazynu obiektów blob dla danych wyjściowych
* Model do konwersji, zobacz [przykładowe modele](../samples/sample-model.md)
  * Zobacz listę [obsługiwanych formatów źródłowych](../how-tos/conversion/model-conversion.md#supported-source-formats)
  * Aby użyć przykładowego skryptu konwersji, upewnij się, że przygotowujesz folder wejściowy zawierający model i wszystkie zależności zewnętrzne (takie jak tekstury zewnętrzne lub geometria)

## <a name="azure-setup"></a>Konfiguracja platformy Azure

Jeśli nie masz jeszcze konta, [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/)przejdź do , kliknij na opcję darmowe konto i postępuj zgodnie z instrukcjami.

Po uzyskaniu konta platformy Azure [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home)przejdź do pliku .

### <a name="storage-account-creation"></a>Tworzenie konta magazynu

Aby utworzyć magazyn obiektów blob, najpierw potrzebujesz konta magazynu.
Aby go utworzyć, kliknij przycisk "Utwórz zasób":

![Azure — dodawanie zasobu](media/azure-add-a-resource.png)

Na nowym ekranie wybierz **pozycję Magazyn** po lewej stronie, a następnie konto Magazynu — **obiekt blob, plik, tabela, kolejka** z następnej kolumny:

![Azure — dodawanie magazynu](media/azure-add-storage.png)

Kliknięcie tego przycisku spowoduje wyświetlenie następującego ekranu z właściwościami przechowywania do wypełnienia:

![Instalator platformy Azure](media/azure-setup1.png)

Wypełnij formularz w następujący sposób:

* Utwórz nową grupę zasobów z łącza poniżej pola rozwijanego i nazwij tę **ARR_Tutorial**
* W przypadku **nazwy konta magazynowania**wprowadź tutaj unikatową nazwę. **Ta nazwa musi być unikatowa globalnie**, w przeciwnym razie pojawi się monit informujący, że nazwa jest gotowa. W zakresie tego szybkiego startu, nazywamy go **arrtutorialstorage**. W związku z tym należy zastąpić go nazwą dla każdego wystąpienia w tym przewodniku Szybki start.
* Wybierz **lokalizację** blisko ciebie. Najlepiej użyć tej samej lokalizacji, co do konfigurowania renderowania w innym przewodniku Szybki start.
* **Wydajność ustawiona** na standardową
* **Rodzaj konta** ustawiony na StorageV2 (ogólnego przeznaczenia v2)
* **Replikacja ustawiona** na magazyn geograficzny dostępu do odczytu (RA-GRS)
* **Warstwa dostępu** ustawiona na Gorąca

Żadna z właściwości w innych kartach nie musi zostać zmieniona, więc możesz kontynuować **"Przejrzyj + utwórz",** a następnie wykonaj kroki, aby ukończyć konfigurację.

Witryna sieci Web informuje teraz o postępie wdrożenia i raportuje po pewnym czasie "Twoje wdrożenie zostało zakończone". Kliknij przycisk **"Przejdź do zasobu", aby** uzyskać następujące kroki:

![Kompletne tworzenie usługi Azure Storage](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Tworzenie magazynu obiektów blob

Następnie potrzebujemy dwóch kontenerów obiektów blob, jednego dla danych wejściowych i jednego dla danych wyjściowych.

Z przycisku **"Przejdź do zasobu"** powyżej, można przejść do strony z panelu po lewej stronie, który zawiera menu listy. Na tej liście w kategorii **"Usługa blob"** kliknij przycisk **"Kontenery":**

![Azure — dodawanie kontenerów](./media/azure-add-containers.png)

Naciśnij przycisk **"+ Kontener",** aby utworzyć **wejściowy** kontener magazynu obiektów blob.
Podczas tworzenia należy użyć następujących ustawień:
  
* Nazwa = arrinput
* Poziom dostępu publicznego = Prywatny

Po utworzeniu kontenera kliknij ponownie przycisk **+ Kontener** i powtórz z tymi ustawieniami dla kontenera **wyjściowego:**

* Nazwa = arroutput
* Poziom dostępu publicznego = Prywatny

Teraz powinny być dwa kontenery magazynu obiektów blob:

![Instalator magazynu obiektów blob](./media/blob-setup.png)

## <a name="run-the-conversion"></a>Uruchamianie konwersji

Aby ułatwić wywołanie usługi konwersji zasobów, udostępniamy skrypt narzędziowy. Znajduje się w folderze *Skrypty* i nazywa się **Conversion.ps1**.

W szczególności skrypt ten

1. przesyła wszystkie pliki w danym katalogu z dysku lokalnego do kontenera magazynu wejściowego
1. wywołuje [interfejs API REST konwersji zasobów,](../how-tos/conversion/conversion-rest-api.md) który pobiera dane z kontenera magazynu wejściowego i rozpoczyna konwersję, która zwróci identyfikator konwersji
1. sondowanie interfejsu API stanu konwersji z pobranym identyfikatorem konwersji, dopóki proces konwersji nie zakończy się powodzeniem lub niepowodzeniem
1. pobiera łącze do przekonwertowanego zasobu w magazynie wyjściowym

Skrypt odczytuje jego konfigurację z pliku *Scripts\arrconfig.json*. Otwórz ten plik JSON w edytorze tekstu.

```json
{
    "accountSettings": {
        "arrAccountId": "8*******-****-****-****-*********d7e",
        "arrAccountKey": "R***************************************l04=",
        "region": "<your-region>"
    },
    "renderingSessionSettings": {
        "vmSize": "standard",
        "maxLeaseTime": "1:00:00"
    },
    "assetConversionSettings": {
        "localAssetDirectoryPath": "D:\\tmp\\robot",
        "resourceGroup": "ARR_Tutorial",
        "storageAccountName": "arrexamplestorage",
        "blobInputContainerName": "arrinput",
        "inputFolderPath": "robotConversion",
        "inputAssetPath": "robot.fbx",
        "blobOutputContainerName": "arroutput",
        "outputFolderPath":"converted/robot",
        "outputAssetFileName": "robot.arrAsset"
    }
}
```

Konfiguracja w grupie **kontSettings** (identyfikator konta i klucz) powinna być wypełniona analogicznie do poświadczeń w [modelu Renderowanie modelu z programem Szybki start Unity](render-model.md).

Wewnątrz **grupy assetConversionSettings,** upewnij się, aby zmienić **resourceGroup**, **blobInputContainerName**i **blobOutputContainerName** jak widać powyżej.
Należy zauważyć, że wartość **arrtutorialstorage** musi zostać zastąpiona unikatową nazwą pobraną podczas tworzenia konta magazynu.

Zmień **localAssetDirectoryPath,** aby wskazać katalog na dysku, który zawiera model, który zamierzasz przekonwertować. Należy uważać, aby prawidłowo uciec\\ukośniki (" ") w\\\\ścieżce za pomocą podwójnych ukośnie (" ").

Wszystkie dane ze ścieżki podanej w **localAssetDirectoryPath** zostaną przekazane do kontenera obiektów **blobInputContainerName** obiektu blob pod ścieżką podprzewodniczą danych przez **inputFolderPath**. Tak więc w powyższej konfiguracji przykładowej\\zawartość\\katalogu "D: tmp robot" zostanie przesłana do kontenera obiektu blob "arrinput" konta magazynu "arrtutorialstorage" pod ścieżką "robotConversion". Istniejące już pliki zostaną zastąpione.

Zmień **dane wejścioweAssetPath** do ścieżki modelu, który ma zostać przekonwertowany - ścieżka jest względem localAssetDirectoryPath. Jako separator ścieżki należy\\użyć "/" zamiast " . Tak więc dla pliku "robot.fbx", który znajduje\\się\\bezpośrednio w "D: tmp robot" użyj "robot.fbx".

Po przekonwertowaniu modelu zostanie on zapisany z powrotem do kontenera magazynu podanego przez **obiekt blobOutputContainerName**. Podścieżka można określić, udostępniając opcjonalne **dane wyjścioweFolderPath**. W powyższym przykładzie wynikowy "robot.arrAsset" zostanie skopiowany do wyjściowego kontenera obiektów blob w obszarze "converted/robot".

Ustawienie konfiguracji **outputAssetFileName** określa nazwę przekonwertowanego zasobu - parametr jest opcjonalny, a wyjściowa nazwa pliku zostanie wydedukowana z nazwy pliku wejściowego w inny sposób. 

Otwórz program PowerShell, upewnij się, że zainstalowano *program Azure PowerShell,* jak wspomniano w [wymaganiach wstępnych.](#prerequisites) Następnie zaloguj się do subskrypcji za pomocą następującego polecenia i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> W przypadku, gdy organizacja ma więcej niż jedną subskrypcję, może być konieczne określenie argumentów Identyfikator subskrypcji i Dzierżawa. Szczegółowe informacje można znaleźć w [dokumentacji Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

Zmień katalog `azure-remote-rendering\Scripts` i uruchom skrypt konwersji:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Powinieneś zobaczyć coś ![takiego: Conversion.ps1](./media/successful-conversion.png)

Skrypt konwersji generuje identyfikator URI *sygnatury dostępu współdzielonego (SAS)* dla przekonwertowanego modelu. Teraz można skopiować ten identyfikator URI jako **nazwę modelu** do przykładowej aplikacji szybki start (zobacz [Szybki start: Renderowanie modelu za pomocą unity](render-model.md)).

![Zamień model w unity](./media/replace-model-in-unity.png)

 Próbka powinna teraz załadować i renderować model niestandardowy!

## <a name="optional-re-creating-a-sas-uri"></a>Opcjonalnie: Ponowne tworzenie identyfikatora URI sygnatury dostępu Współdzielonego

Identyfikator URI sygnatury dostępu Współdzielonego utworzony przez skrypt konwersji będzie prawidłowy tylko przez 24 godziny. Jednak po jego wygaśnięciu nie trzeba ponownie konwertować modelu. Zamiast tego można utworzyć nowy sygnatury dostępu Współdzielonego w portalu, zgodnie z opisem w następnych krokach:

1. Przejdź do witryny [Azure Portal](https://www.portal.azure.com).
1. Kliknij zasób konta ![ **magazynu:** Dostęp do podpisu](./media/portal-storage-accounts.png)
1. Na poniższym ekranie kliknij **Eksploratora magazynu** w lewym panelu i znajdź model wyjściowy (*.arrAsset* file) w kontenerze magazynu obiektów blob *arroutput.* Kliknij prawym przyciskiem myszy plik i wybierz polecenie ![Pobierz podpis dostępu **współdzielonego** z menu kontekstowego: Dostęp do podpisu](./media/portal-storage-explorer.png)
1. Zostanie otwarty nowy ekran, na którym można wybrać datę ważności. Naciśnij **klawisz Create**i skopiuj identyfikator URI wyświetlany w następnym oknie dialogowym. Ten nowy identyfikator URI zastępuje tymczasowy identyfikator URI utworzony przez skrypt.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz podstawy, zapoznaj się z naszymi samouczkami, aby zdobyć więcej dogłębnej wiedzy.

Jeśli chcesz poznać szczegóły konwersji modelu, zapoznaj się z [interfejsem API REST konwersji modelu](../how-tos/conversion/conversion-rest-api.md).

> [!div class="nextstepaction"]
> [Samouczek: Konfigurowanie projektu Unity od podstaw](../tutorials/unity/project-setup.md)
