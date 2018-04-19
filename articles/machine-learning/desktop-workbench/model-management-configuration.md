---
title: "Usługa Azure Machine Learning Model zarządzania instalacja i Konfiguracja | Dokumentacja firmy Microsoft"
description: "W tym dokumencie opisano kroki i pojęcia związane z w instalowaniu i konfigurowaniu modelu zarządzania w usłudze Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: 45ddd4dc6fb5559c020706e2784158b1319f9b52
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2018
---
# <a name="model-management-setup"></a>Model konfiguracji zarządzania

## <a name="overview"></a>Przegląd
Ten dokument stanowi wprowadzenie do wdrażania i zarządzania nimi komputerze uczenia modele jako usługi sieci web przy użyciu zarządzania modelu uczenia Maszynowego Azure. 

Za pomocą zarządzania modelu uczenia Maszynowego Azure można efektywnego wdrożenia i zarządzania nimi modeli uczenia maszynowego, które są tworzone przy użyciu wielu platform, na przykład SparkML, Keras, TensorFlow, kognitywnych zestaw narzędzi firmy Microsoft lub Python. 

Do końca tego dokumentu powinien mieć możliwość środowiska zarządzania modelu skonfigurowane i gotowe do wdrożenia z modeli uczenia maszynowego.

## <a name="what-you-need-to-get-started"></a>Wymagane do rozpoczęcia pracy
Aby uzyskać wykorzystanie tego przewodnika, właściciela do subskrypcji platformy Azure, którą można wdrożyć do modeli do powinna mieć.
Interfejsu wiersza polecenia jest wstępnie zainstalowane na Azure Machine Learning Workbench i na [Azure DSVMs](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Przy użyciu interfejsu wiersza polecenia
Aby używać interfejsów wiersza polecenia (CLIs) z Workbench, kliknij przycisk **pliku** -] **Otwórz interfejs wiersza polecenia**. 

W danych nauki maszyny wirtualnej, Połącz i Otwórz okno wiersza polecenia. Typ `az ml -h` Aby wyświetlić opcje. Aby uzyskać więcej informacji o poleceniach, Użyj flagi — Pomoc.

W innych systemach trzeba zainstalować CLIs.

### <a name="installing-or-updating-on-windows"></a>Instalowanie (lub aktualizowania) w systemie Windows

Instalowanie języka Python z https://www.python.org/. Upewnij się, że wybrano opcję zainstalowania pip.

Otwórz wiersz polecenia przy użyciu Uruchom jako Administrator i uruchom następujące polecenia:

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>Jeśli masz starszą wersję, odinstaluj go najpierw za pomocą następującego polecenia:
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Instalowanie (lub aktualizowania) w systemie Linux
Uruchom następujące polecenie w wierszu polecenia, a następnie postępuj zgodnie z monitami:

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

Po zakończeniu instalacji uruchom następujące polecenie:

```bash
sudo /opt/microsoft/azureml/initial_setup.sh
```

>[!NOTE]
>Wyloguj się i zaloguj się ponownie w sesji SSH, aby zmiany zaczęły obowiązywać.
>Poprzednie polecenia umożliwia zaktualizowanie starszej wersji CLIs na DSVM.
>

## <a name="deploying-your-model"></a>Model wdrażania
CLIs umożliwia wdrażanie modeli jako usługi sieci web. Usługi sieci web można wdrożyć lokalnie lub w klastrze.

Uruchom program z lokalnego wdrożenia, zweryfikuj, czy model i kod działają, następnie wdrożyć do klastra w środowisku produkcyjnym skali.

Aby rozpocząć, należy skonfigurować środowisko wdrażania. Konfigurowanie środowiska jest zadanie w czasie. Po zakończeniu instalacji można ponownie użyć środowiska w przypadku kolejnych wdrożeń. Zobacz sekcję poniżej, aby uzyskać więcej szczegółów.

Po zakończeniu instalacji środowiska:
- Zostanie wyświetlony monit logowanie do platformy Azure. Do logowania, aby otworzyć https://aka.ms/devicelogin strony i wprowadź kod podany w celu uwierzytelnienia korzystanie z przeglądarki internetowej.
- W procesie uwierzytelniania zostanie wyświetlony monit o konta do uwierzytelniania. Ważne: Należy wybrać konto, które ma ważnej subskrypcji platformy Azure i wystarczających uprawnień do utworzenia zasobów na koncie. - zobaczy podczas logowania zostanie zakończone, informacji o subskrypcji i zostanie wyświetlony monit, czy chcesz kontynuować z wybrane konto.

### <a name="environment-setup"></a>Konfigurowanie środowiska
Aby rozpocząć proces instalacji, należy zarejestrować dostawcę środowiska, wprowadzając następujące polecenie:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>Wdrożenia lokalnego
Aby wdrożyć i przetestować usługi sieci web na komputerze lokalnym, należy skonfigurować środowisko lokalne za pomocą następującego polecenia:

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Wdrożenie usługi lokalnej sieci web wymaga użytkownika do zainstalowania platformy Docker na komputerze lokalnym. 
>

Polecenie Ustawienia lokalnego środowiska tworzy następujące zasoby w Twojej subskrypcji:
- Grupa zasobów (Jeśli nie zostanie podana)
- Konto magazynu
- Rejestru kontenera platformy Azure (ACR)
- Application Insights

Po pomyślnym zakończeniu instalacji, należy ustawić środowiska do użycia przy użyciu następującego polecenia:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Wdrożenie klastra
Użyj wdrożenia klastra dla scenariuszy wysokiej skali w środowisku produkcyjnym. Konfiguruje klaster usług ACS z Kubernetes jako orchestrator. Aby obsłużyć większą przepływność dla wywołania usługi sieci web, może być skalowana klastrem usługi ACS.

Aby wdrożyć usługi sieci web w środowisku produkcyjnym, należy najpierw skonfigurować ze środowiskiem za pomocą następującego polecenia:

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

Polecenie Ustawienia środowiska klastra tworzy następujące zasoby w Twojej subskrypcji:
- Grupa zasobów (Jeśli nie zostanie podana)
- Konto magazynu
- Rejestru kontenera platformy Azure (ACR)
- Wdrożenie Kubernetes w klastrze usługi kontenera platformy Azure (ACS)
- Application Insights

Grupy zasobów konta magazynu i ACR są tworzone szybko. Wdrażanie usług ACS może zająć maksymalnie 20 minut. 

Po zakończeniu instalacji należy ustawić w środowisku można użyć dla tego wdrożenia. Użyj następującego polecenia:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Po utworzeniu środowiska w przypadku kolejnych wdrożeń, wystarczy użyj polecenia set powyżej, aby użyć go ponownie.
>

>[!NOTE] 
>Aby utworzyć punkt końcowy HTTPS, Określ certyfikat SSL, podczas tworzenia klastra przy użyciu nazwa certyfikatu i--cert pem opcje az ml env Instalatora. To ustawienie klaster do obsługi żądań HTTPS, zabezpieczone przy użyciu podanego certyfikatu. Po zakończeniu instalacji należy utworzyć rekord CNAME DNS, który wskazuje nazwę FQDN klastra.

### <a name="create-an-account"></a>Tworzenie konta usługi
Konto jest wymagane do wdrażania modeli. Należy wykonać to jeden raz dla każdego konta, a można ponownie użyć tego samego konta w przypadku wielu wdrożeń.

Aby utworzyć nowe konto, użyj następującego polecenia:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Aby użyć istniejącego konta, użyj następującego polecenia:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>Wdróż model
Teraz można przystąpić do wdrażania modelu zapisane w postaci usługi sieci web. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

### <a name="next-steps"></a>Następne kroki
Wypróbuj jedną z wielu próbek w galerii.
