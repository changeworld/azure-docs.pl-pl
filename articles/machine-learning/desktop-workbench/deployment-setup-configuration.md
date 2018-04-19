---
title: Usługa Azure Machine Learning Model zarządzania instalacja i Konfiguracja | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano kroki i pojęcia związane z w instalowaniu i konfigurowaniu modelu zarządzania w usłudze Azure Machine Learning.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/6/2017
ms.openlocfilehash: 0859031ac26b061861aa51dce1093f2fe4350935
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="model-management-setup"></a>Model konfiguracji zarządzania

Ten dokument stanowi wprowadzenie do wdrażania i zarządzania nimi komputerze uczenia modele jako usługi sieci web przy użyciu zarządzania modelu uczenia Maszynowego Azure. 

Za pomocą zarządzania modelu uczenia Maszynowego Azure można efektywnego wdrożenia i zarządzania nimi modeli uczenia maszynowego, które są tworzone przy użyciu wielu platform, na przykład SparkML, Keras, TensorFlow, kognitywnych zestaw narzędzi firmy Microsoft lub Python. 

Do końca tego dokumentu powinien mieć możliwość środowiska zarządzania modelu skonfigurowane i gotowe do wdrożenia z modeli uczenia maszynowego.

## <a name="what-you-need-to-get-started"></a>Wymagane do rozpoczęcia pracy
Lepsze wykorzystanie tego przewodnika, ma współautora dostępu do subskrypcji platformy Azure lub grupy zasobów, które można wdrożyć do modeli.
Interfejsu wiersza polecenia jest wstępnie zainstalowane na Azure Machine Learning Workbench i na [Azure DSVMs](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Przy użyciu interfejsu wiersza polecenia
Aby używać interfejsów wiersza polecenia (CLIs) z Workbench, kliknij przycisk **pliku** -> **Otwórz okno wiersza polecenia**. 

W danych nauki maszyny wirtualnej, Połącz i Otwórz okno wiersza polecenia. Typ `az ml -h` Aby wyświetlić opcje. Aby uzyskać więcej informacji o poleceniach, Użyj flagi — Pomoc.

W innych systemach trzeba zainstalować CLIs.

### <a name="installing-or-updating-on-windows"></a>Instalowanie (lub aktualizowania) w systemie Windows

Zainstaluj środowisko Python z https://www.python.org/. Upewnij się, że wybrano opcję zainstalowania pip.

Otwórz wiersz polecenia przy użyciu Uruchom jako Administrator i uruchom następujące polecenia:

```cmd
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="installing-or-updating-on-linux"></a>Instalowanie (lub aktualizowania) w systemie Linux
Uruchom następujące polecenie w wierszu polecenia, a następnie postępuj zgodnie z monitami:

```bash
sudo -i
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="configuring-docker-on-linux"></a>Konfigurowanie Docker w systemie Linux
Aby skonfigurować Docker w systemie Linux na potrzeby używania przez użytkowników innych niż root, postępuj zgodnie z instrukcjami w tym miejscu: [czynności poinstalacyjne dla systemu Linux](https://docs.docker.com/engine/installation/linux/linux-postinstall/)

>[!NOTE]
> Na DSVM Linux można uruchomić skryptu poniżej, aby poprawnie skonfigurować Docker. **Pamiętaj, aby się wylogować i zalogować się ponownie po uruchomieniu skryptu.**
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>Model wdrażania
Użyj interfejsu wiersza polecenia, aby wdrożyć modele jako usługi sieci web. Usługi sieci web można wdrożyć lokalnie lub w klastrze.

Uruchom program z lokalnego wdrożenia, zweryfikuj, czy model i kod działają, następnie wdrożyć do klastra w środowisku produkcyjnym skali.

Aby rozpocząć, należy skonfigurować środowisko wdrażania. Konfigurowanie środowiska jest zadanie w czasie. Po zakończeniu instalacji można ponownie użyć środowiska w przypadku kolejnych wdrożeń. Zobacz sekcję poniżej, aby uzyskać więcej szczegółów.

Po zakończeniu instalacji środowiska:
- Zostanie wyświetlony monit logowanie do platformy Azure. Do logowania, użyj przeglądarki sieci web, aby otworzyć stronę https://aka.ms/devicelogin i wprowadź kod podany w celu uwierzytelnienia.
- W procesie uwierzytelniania zostanie wyświetlony monit o konta do uwierzytelniania. Ważne: Wybierz konto, które ma ważnej subskrypcji platformy Azure i wystarczających uprawnień do utworzenia zasobów w ramach konta. Podczas logowania zostanie zakończone, zobaczy informacji o subskrypcji i zostanie wyświetlony monit, czy chcesz kontynuować z wybranym kontem.

### <a name="environment-setup"></a>Konfigurowanie środowiska
Aby rozpocząć proces instalacji, należy zarejestrować kilku dostawców środowiska, wprowadzając następujące polecenia:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### <a name="local-deployment"></a>Wdrożenia lokalnego
Aby wdrożyć i przetestować usługi sieci web na komputerze lokalnym, należy skonfigurować środowisko lokalne za pomocą następującego polecenia. Nazwa grupy zasobów jest opcjonalna.

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Wdrożenie usługi lokalnej sieci web wymaga zainstalowania platformy Docker na komputerze lokalnym. 
>

Polecenie Ustawienia lokalnego środowiska tworzy następujące zasoby w Twojej subskrypcji:
- Grupa zasobów (Jeśli nie zostanie podana, czy podana nazwa nie istnieje)
- Konto magazynu
- Rejestru kontenera platformy Azure (ACR)
- Konto usługi insights aplikacji

Po pomyślnym zakończeniu instalacji, należy ustawić środowiska do użycia przy użyciu następującego polecenia:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Wdrożenie klastra
Użyj wdrożenia klastra dla scenariuszy wysokiej skali w środowisku produkcyjnym. Konfiguruje klaster usług ACS z Kubernetes jako orchestrator. Aby obsłużyć większą przepływność dla wywołania usługi sieci web, może być skalowana klastrem usługi ACS.

Aby wdrożyć usługi sieci web w środowisku produkcyjnym, należy najpierw skonfigurować ze środowiskiem za pomocą następującego polecenia:

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

Polecenie Ustawienia środowiska klastra tworzy następujące zasoby w Twojej subskrypcji:
- Grupa zasobów (Jeśli nie zostanie podana, czy podana nazwa nie istnieje)
- Konto magazynu
- Rejestru kontenera platformy Azure (ACR)
- Wdrożenie Kubernetes w klastrze usługi kontenera platformy Azure (ACS)
- Konto usługi insights aplikacji

>[!IMPORTANT]
> Aby można było pomyślnie utworzyć środowisko klastra, zostanie musi być współautora dostęp do subskrypcji platformy Azure lub grupy zasobów.

Grupy zasobów konta magazynu i ACR są tworzone szybko. Wdrażanie usług ACS może zająć maksymalnie 20 minut. 

Aby sprawdzić stan inicjowania obsługi klastra trwającą, użyj następującego polecenia:

```azurecli
az ml env show -n [environment name] -g [resource group]
```

Po zakończeniu instalacji należy ustawić w środowisku można użyć dla tego wdrożenia. Użyj następującego polecenia:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Po utworzeniu środowiska w przypadku kolejnych wdrożeń, wystarczy użyj polecenia set powyżej, aby użyć go ponownie.
>

### <a name="create-a-model-management-account"></a>Utwórz konto zarządzania modelu
Konto zarządzania model jest wymagany podczas wdrażania modeli. Należy wykonać to raz na subskrypcję, a można ponownie użyć tego samego konta w przypadku wielu wdrożeń.

Aby utworzyć nowe konto, użyj następującego polecenia:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Aby użyć istniejącego konta, użyj następującego polecenia:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

W wyniku tego procesu środowisko jest gotowe, a konto zarządzania model został utworzony w celu udostępnia funkcje niezbędne do zarządzania i wdrażania modeli uczenia maszynowego (zobacz [Azure Machine Learning Model zarządzania](model-management-overview.md) dla Przegląd).

## <a name="next-steps"></a>Kolejne kroki

* Dla instrukcji na temat wdrażania usług sieci web do uruchomienia na komputerze lokalnym lub w klastrze w dalszym ciągu na [wdrażania modelu uczenia maszynowego, usługa sieci Web](model-management-service-deploy.md).
* Wypróbuj jedną z wielu próbek w galerii.
