---
title: Tworzenie testu analizatora internetowego przy użyciu interfejsu wiersza polecenia | Dokumenty firmy Microsoft
description: W tym artykule dowiesz się, jak utworzyć pierwszy test analizatora internetowego.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: d474442086e2a114f26df279ab2682cd7628a5f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74184280"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Tworzenie testu analizatora internetowego przy użyciu interfejsu wiersza polecenia (wersja zapoznawcza)

Istnieją dwa sposoby tworzenia zasobu analizatora internetowego — przy użyciu [witryny Azure portal](internet-analyzer-create-test-portal.md) lub przy użyciu interfejsu wiersza polecenia. Ta sekcja ułatwia tworzenie nowego zasobu analizatora internetu platformy Azure przy użyciu naszego środowiska interfejsu wiersza polecenia. 


> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Publiczna wersja zapoznawcza jest dostępna do użycia globalnie; jednak przechowywanie danych jest ograniczona do *us West 2* podczas podglądu.

## <a name="object-model"></a>Model obiektu
Interfejsu wiersza polecenia analizatora internetowego udostępnia następujące typy zasobów:
* **Testy** — test porównuje wydajność użytkownika końcowego dwóch punktów końcowych internetu (A i B) w czasie.
* **Profile** — testy są tworzone w ramach profilu analizatora internetowego. Profile umożliwiają grupowanie powiązanych testów; jeden profil może zawierać jeden lub więcej testów.
* **Wstępnie skonfigurowane punkty końcowe** — skonfigurowaliśmy punkty końcowe z różnymi konfiguracjami (regiony, technologie przyspieszania itp.). Można użyć dowolnego z tych wstępnie skonfigurowanych punktów końcowych w testach.
* **Karty wyników** — karta wyników zawiera szybkie i miarodajne podsumowania wyników pomiarów. Zapoznaj się [z tłumaczeniem karty wyników](internet-analyzer-scorecard.md).
* **Szeregi czasowe** — szereg czasu pokazuje, jak metryka zmienia się wraz z czasem.

## <a name="profile-and-test-creation"></a>Tworzenie profilu i testu
1. Uzyskaj dostęp w wersji zapoznawczej analizatora internetowego, [Azure Internet Analyzer FAQ](internet-analyzer-faq.md)wykonując instrukcje **dotyczące uczestniczenia w wersji zapoznawczej?**
2. [Zainstaluj narzędzie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
3. Uruchom `login` polecenie, aby rozpocząć sesję interfejsu wiersza polecenia:
    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia można otworzyć domyślną przeglądarkę, zrobi to i załadować stronę logowania platformy Azure.
    W przeciwnym razie otwórz https://aka.ms/devicelogin stronę przeglądarki i wprowadź kod autoryzacji wyświetlany w terminalu.

4. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

5. Wybierz identyfikator subskrypcji, któremu udzielono dostępu do publicznej wersji zapoznawczej analizatora internetowego.

    Po zalogowaniu się zobaczysz listę subskrypcji skojarzonych z twoim kontem platformy Azure. Informacje o `isDefault: true` subskrypcji to aktualnie aktywowana subskrypcja po zalogowaniu. Aby wybrać inną [subskrypcję,](https://docs.microsoft.com/cli/azure/account#az-account-set) użyj polecenia az account set z identyfikatorem subskrypcji, aby przełączyć się na. Aby uzyskać więcej informacji na temat wyboru subskrypcji, zobacz [Używanie wielu subskrypcji platformy Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).

    Istnieją sposoby logowania nieinterakcyjnego, które opisano szczegółowo w artykule [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

6. **[Opcjonalnie]** Utwórz nową grupę zasobów platformy Azure:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Zainstaluj rozszerzenie analizatora internetowego interfejsu wiersza polecenia platformy Azure:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Utwórz nowy profil analizatora internetowego:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Wyświetl listę wszystkich wstępnie skonfigurowanych punktów końcowych dostępnych dla nowo utworzonego profilu:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Utwórz nowy test w nowo utworzonym profilu InternetAnalyzer:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    Powyższe polecenie zakłada, `www.contoso.com` `www.microsoft.com` że oba i są hostowanie obrazu jednego piksela[(trans.gif](https://fpc.msedge.net/apc/trans.gif)) w ścieżkach niestandardowych. Jeśli ścieżka obiektu nie jest wyraźnie określony, Analizator internetowy będzie domyślnie używany `/apc/trans.gif` jako ścieżka obiektu, czyli gdzie wstępnie skonfigurowane punkty końcowe są hostowane obrazu jednego piksela. Należy również zauważyć, że schemat (https/http) nie musi być określony; Analizator internetowy obsługuje tylko punkty końcowe HTTPS, więc zakłada się, że protokół HTTPS.

11. Nowy test powinien pojawić się pod profilem analizatora internetowego:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Przykładowe dane wyjściowe:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Aby rozpocząć generowanie pomiarów, plik JavaScript wskazany przez **skrypt testowyFileUri** musi być osadzony w aplikacji sieci Web. Szczegółowe instrukcje można znaleźć na stronie [Osażony klient analizatora internetowego.](internet-analyzer-embed-client.md)

13. Postęp testu można monitorować, śledząc jego wartość "statusu":
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Wyniki testu można sprawdzić, generując dla niego terminy lub karty wyników:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Następne kroki

* Przejrzyj [odwołanie do interfejsu wiersza polecenia analizatora internetowego, aby](https://docs.microsoft.com/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest) uzyskać pełną listę obsługiwanych poleceń i przykładów użycia.
* Przeczytaj często zadawane pytania [dotyczące analizatora internetowego](internet-analyzer-faq.md).
* Dowiedz się więcej o osadzaniu [klienta analizatora internetowego](internet-analyzer-embed-client.md) i tworzeniu [niestandardowego punktu końcowego](internet-analyzer-custom-endpoint.md). 
