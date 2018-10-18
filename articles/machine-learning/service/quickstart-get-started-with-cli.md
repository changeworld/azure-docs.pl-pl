---
title: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu rozszerzenia interfejsu wiersza polecenia | Microsoft Docs
description: W tym przewodniku Szybki start dowiesz się, jak rozpocząć pracę z usługą Azure Machine Learning przy użyciu rozszerzenia interfejsu wiersza polecenia usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
author: rastala
ms.author: roastala
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 856f9629e97f8cf7cf811e7d591cbcad6067f47a
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237164"
---
# <a name="quickstart-get-started-with-azure-machine-learning-using-the-cli-extension"></a>Szybki start: rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu rozszerzenia interfejsu wiersza polecenia

W tym przewodniku Szybki start użyjesz rozszerzenia interfejsu wiersza polecenia uczenia maszynowego, aby rozpocząć pracę z [usługą Azure Machine Learning](overview-what-is-azure-ml.md) (wersja zapoznawcza).

Korzystając z interfejsu wiersza polecenia, dowiesz się, jak wykonywać następujące czynności:

1. Tworzenie obszaru roboczego w subskrypcji platformy Azure. Obszar roboczy jest używany przez jednego lub większą liczbę użytkowników do przechowywania ich zasobów obliczeniowych, modeli, wdrożeń i historii uruchamiania w chmurze.
1. Dołączanie projektu do swojego obszaru roboczego.   Projekt jest lokalnym folderem, który zawiera skrypty i pliki konfiguracji potrzebne do rozwiązania problemu uczenia maszynowego.  
1. Uruchamianie w projekcie skryptu w języku Python, który rejestruje niektóre wartości w wielu iteracjach.
1. Wyświetlanie zarejestrowanych wartości w historii uruchamiania obszaru roboczego.

> [!NOTE]
> Dla Twojej wygody następujące zasoby platformy Azure są automatycznie dodawane do obszaru roboczego, jeśli są dostępne w regionie: [rejestr kontenerów](https://azure.microsoft.com/services/container-registry/), [magazyn](https://azure.microsoft.com/services/storage/), [usługa Application Insights](https://azure.microsoft.com/services/application-insights/) i [magazyn kluczy](https://azure.microsoft.com/services/key-vault/).

Utworzone zasoby mogą być używane jako wstępnie wymagane składniki w innych samouczkach usługi Azure Machine Learning i artykułach z instrukcjami.

Ten interfejs wiersza polecenia został utworzony na podstawie opartego na języku Python <a href="http://aka.ms/aml-sdk" target="_blank">zestawu SDK</a> dla usługi Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wykonywania kroków tego samouczka Szybki start upewnij się, że zostały spełnione następujące wymagania wstępne:

+ Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ Zainstalowane środowisko [Python 3.5 lub nowsze](https://www.python.org/).
+ [Zainstalowany interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="install-the-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia

Na komputerze otwórz edytor wiersza polecenia i zainstaluj [rozszerzenie uczenia maszynowego dla interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md).  Instalacja może potrwać kilka minut.

```azurecli
az extension add azureml-sdk
```

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

[!INCLUDE [aml-install-sdk](../../../includes/aml-install-sdk.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Korzystając z interfejsu wiersza polecenia platformy Azure, zaloguj się do platformy Azure, określ subskrypcję i utwórz grupę zasobów.

W oknie wiersza polecenia zaloguj się przy użyciu polecenia `az login` interfejsu wiersza polecenia platformy Azure. Postępuj zgodnie ze wskazówkami dotyczącymi logowania interakcyjnego:
    
   ```azurecli
   az login
   ```

Wyświetl listę dostępnych subskrypcji platformy Azure i określ tę, której chcesz użyć:
   ```azurecli
   az account list --output table
   az account set --subscription <your-subscription-id>
   az account show
   ```
   gdzie \<your-subscription-id\> to wartość identyfikatora subskrypcji do użycia. Nie uwzględniaj nawiasów.

Utwórz grupę zasobów do przechowywania obszaru roboczego.
W tym przewodniku Szybki start przyjęto następujące założenia:
   + Nazwa grupy zasobów to `docs-aml`.
   + Region to `eastus2`. 

   ```azurecli
   az group create -n docs-aml -l eastus2
   ```

## <a name="create-a-workspace-and-a-project-folder"></a>Tworzenie obszaru roboczego i folderu projektu

W oknie wiersza polecenia utwórz Obszar roboczy usługi Azure Machine Learning Service w ramach grupy zasobów.


   W tym przewodniku Szybki start przyjęto następujące założenia:
   + Nazwa obszaru roboczego to `docs-ws`.
   + Nazwa grupy zasobów to `docs-aml`.

   ```azurecli
   az ml workspace create -n docs-ws -g docs-aml
   ```

W oknie wiersza polecenia utwórz folder na komputerze lokalnym do przechowywania projektu usługi Azure Machine Learning.

   ```
   mkdir docs-prj
   cd docs-prj
   ```

## <a name="create-a-python-script"></a>Tworzenie skryptu w języku Python

[!INCLUDE [aml-create-script-pi](../../../includes/aml-create-script-pi.md)]

## <a name="run-the-script"></a>Uruchamianie skryptu

Dołącz folder do obszaru roboczego jako projekt. Argument `--history` określa nazwę pliku historii uruchamiania, który przechwytuje metryk podczas każdego przebiegu.

   ```azurecli
   az ml project attach --history my_history -w docs-ws -g docs-aml
   ```

Uruchom skrypt na komputerze lokalnym.

   ```azurecli
   az ml run submit -c local pi.py
   ```

   To polecenie uruchamia kod i w konsoli zwraca link internetowy. Skopiuj ten link i wklej go w przeglądarce internetowej.

W przeglądarce internetowej odwiedź podany adres URL. W portalu internetowym zostaną wyświetlone wyniki przebiegu. Możesz przeanalizować wyniki tego przebiegu lub poprzednich przebiegów, jeśli takie istnieją.

Pulpit nawigacyjny portalu jest obsługiwany tylko w przeglądarkach Edge, Chrome i Firefox.

   ![wyświetlanie historii](./media/quickstart-get-started/web-results.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki
Utworzono zasoby niezbędne do rozpoczęcia eksperymentowania z modelami i ich wdrażania. Został również utworzony projekt, uruchomiony skrypt i zbadana historia uruchamiania tego skryptu.

Aby poznać szczegółowo środowisko przepływu pracy, wykonaj czynności opisane w samouczku dotyczącym tworzenia, szkolenia i wdrażania modelu w usłudze Azure Machine Learning.

> [!div class="nextstepaction"]
> [Samouczek: tworzenie, szkolenie i wdrażanie](tutorial-train-models-with-aml.md)