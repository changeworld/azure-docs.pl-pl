---
title: Usługi Azure Machine Learning Model zarządzania instalacja i Konfiguracja | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano kroki i pojęcia zaangażowane w instalowaniu i konfigurowaniu Zarządzanie modelami w usłudze Azure Machine Learning.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 12/6/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 84068376f35bc4df6672cffcc0ac3438b12edbb5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979679"
---
# <a name="model-management-setup"></a>Model konfiguracji zarządzania

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Ten dokument ułatwia rozpoczęcie pracy z używaniem Zarządzanie modelami w usłudze Azure ML na potrzeby wdrażania i zarządzania nimi modeli jako usług sieci web usługi machine learning. 

Za pomocą zarządzania modelami w usłudze Azure ML, mogą efektywnie wdrażanie i zarządzanie modeli uczenia maszynowego, które zostały utworzone przy użyciu wielu struktur, w tym SparkML, Keras, TensorFlow, Microsoft Cognitive Toolkit lub Python. 

Do końca tego dokumentu powinny mieć możliwość środowiska zarządzania modelami, skonfigurowane i gotowe do wdrażania modeli uczenia maszynowego.

## <a name="what-you-need-to-get-started"></a>Wymagane do rozpoczęcia pracy
Aby maksymalnie wykorzystać możliwości tego przewodnika, należy uprawień dostęp do subskrypcji platformy Azure lub grupy zasobów, którą można wdrożyć swoje modele, aby.
Interfejs wiersza polecenia jest wstępnie zainstalowany w aplikacji Azure Machine Learning Workbench i w [maszyny Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Przy użyciu interfejsu wiersza polecenia
Aby używać interfejsów wiersza polecenia (interfejsów wiersza polecenia) z aplikacji Workbench, kliknij przycisk **pliku** -> **Otwórz wiersz polecenia**. 

Na maszyny wirtualnej analizy danych, łączenie i Otwórz okno wiersza polecenia. Typ `az ml -h` Aby wyświetlić opcje. Aby uzyskać więcej informacji na temat poleceń, Użyj flagi help.

W innych systemach trzeba zainstalować interfejsów wiersza polecenia.

>[!NOTE]
> W notesie Jupyter na DSVM systemu Linux dostępne wiersza polecenia platformy Azure i interfejsu wiersza polecenia platformy Azure ML następujący format polecenia.  **Te informacje dotyczą notesu programu Jupyter w systemie Linux maszyny wirtualnej DSVM specjalnie**.  Polecenia zapewniają dostęp do bieżącego języka Python jądra w notesie (np. conda `py35` środowiska)
>```
>import sys
>! {sys.executable} -m azure.cli login
>! {sys.executable} -m azure.cli ml -h
>```

### <a name="installing-or-updating-on-windows"></a>Instalowanie (lub aktualizowanie) na Windows

Zainstaluj język Python z https://www.python.org/. Upewnij się, że wybrano SE nainstalovat pip.

Otwórz wiersz polecenia przy użyciu Uruchom jako Administrator i uruchom następujące polecenia:

```cmd
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="installing-or-updating-on-linux"></a>Instalowanie (lub aktualizowania) w systemie Linux
Uruchom następujące polecenie w wierszu polecenia, a następnie postępuj zgodnie z instrukcjami:

```bash
sudo -i
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="configuring-docker-on-linux"></a>Konfigurowanie platformy Docker w systemie Linux
Aby skonfigurować platformy Docker w systemie Linux do użycia przez użytkowników innych niż root, postępuj zgodnie z instrukcjami w tym miejscu: [poinstalacyjne kroki dla systemu Linux](https://docs.docker.com/engine/installation/linux/linux-postinstall/)

>[!NOTE]
> W systemie Linux maszyny wirtualnej DSVM uruchomieniem poniższego skryptu, aby poprawnie skonfigurować platformy Docker. **Pamiętaj, aby wylogować się i zaloguj się ponownie po uruchomieniu skryptu.**
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>Wdrażanie modelu
Interfejs wiersza polecenia umożliwia wdrażanie modeli jako usług sieci web. Usługi sieci web można wdrażać lokalnie lub w klastrze.

Uruchom program z lokalnym wdrożeniem, zweryfikuj, że model i kod pracować, to wdrożenie w klastrze skalowania w środowisku produkcyjnym.

Aby rozpocząć, musisz skonfigurować swoje środowisko wdrażania. Konfiguracja środowiska jest zadanie w czasie. Po zakończeniu instalacji można użyć ponownie środowisko w przypadku kolejnych wdrożeń. Zobacz następujące sekcji, aby uzyskać więcej szczegółów.

Po zakończeniu instalacji środowiska:
- Monit o logowanie do platformy Azure. Aby się zarejestrować, należy użyć przeglądarki sieci web o otwarcie strony https://aka.ms/devicelogin i wprowadzenie udostępnionego kodu do uwierzytelniania.
- Podczas procesu uwierzytelniania zostanie wyświetlony monit o konta do uwierzytelniania za pomocą. Ważne: Wybierz konto, które ma prawidłową subskrypcję platformy Azure i wystarczające uprawnienia do tworzenia zasobów w ramach konta. Gdy logowanie zostanie zakończone, zobaczy informacji o subskrypcji i zostanie wyświetlony monit, czy chcesz kontynuować z wybranym kontem.

### <a name="environment-setup"></a>Konfigurowanie środowiska
Aby rozpocząć proces instalacji, musisz zarejestrować kilku dostawców środowiska, wprowadzając następujące polecenia:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### <a name="local-deployment"></a>Wdrożenie lokalne
Aby wdrożyć i przetestować usługę sieci web na komputerze lokalnym, należy skonfigurować środowisku lokalnym za pomocą następującego polecenia. Nazwa grupy zasobów jest opcjonalne.

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Lokalnego wdrożenia usługi internetowej, należy zainstalować platformę Docker na komputerze lokalnym. 
>

Polecenie Ustawienia lokalnego środowiska umożliwia utworzenie następujących zasobów w ramach subskrypcji:
- Zasób grupy (Jeśli nie zostanie podana, czy podana nazwa nie istnieje)
- Konto magazynu
- Rejestr Azure Container Registry (ACR)
- Konto usługi Application insights

Gdy Instalator zakończy się pomyślnie, należy ustawić środowisko ma być używany, używając następującego polecenia:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Wdrażanie klastra
Użyj wdrożenia klastra na potrzeby scenariuszy produkcyjnych w dużej skali. Konfiguruje klaster usługi ACS z rozwiązaniem Kubernetes jako orkiestrator. Klastra ACS może być skalowana w celu obsługi większej przepływności dla wywołania usługi sieci web.

Aby wdrożyć usługę sieci web w środowisku produkcyjnym, należy najpierw skonfigurować środowisko, używając następującego polecenia:

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

Polecenie instalacji środowiska klastra umożliwia utworzenie następujących zasobów w ramach subskrypcji:
- Zasób grupy (Jeśli nie zostanie podana, czy podana nazwa nie istnieje)
- Konto magazynu
- Rejestr Azure Container Registry (ACR)
- Wdrożenie rozwiązania Kubernetes w klastrze usługi Azure Container Service (ACS)
- Konto usługi Application insights

>[!IMPORTANT]
> Aby można było pomyślnie utworzyć środowisko klastra, należy mieć prawa dostępu współautora w subskrypcji platformy Azure lub grupy zasobów.

Szybko są tworzone grupy zasobów, konto magazynu oraz usługi ACR. Wdrażanie usług ACS może potrwać do 20 minut. 

Aby sprawdzić stan aprowizacji klastra bieżące, użyj następującego polecenia:

```azurecli
az ml env show -n [environment name] -g [resource group]
```

Po zakończeniu instalacji należy ustawić środowisko, które ma być używany dla tego wdrożenia. Użyj następującego polecenia:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Po utworzeniu środowiska w przypadku kolejnych wdrożeń, wystarczy użyj polecenia set powyżej, aby użyć go ponownie.
>

### <a name="create-a-model-management-account"></a>Tworzenie konta zarządzania modelami
Konto zarządzania modelami jest wymagane na potrzeby wdrażania modeli. Należy zrobić to raz na subskrypcję i ponownie używać tego samego konta w wielu wdrożeniach.

Aby utworzyć nowe konto, użyj następującego polecenia:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Aby użyć istniejącego konta, użyj następującego polecenia:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

W wyniku tego procesu środowisko jest gotowe i konta zarządzania modelami został utworzony w celu zapewnienia funkcje niezbędne do zarządzania i wdrażania modeli uczenia maszynowego (zobacz [Zarządzanie modelami w usłudze Azure Machine Learning](model-management-overview.md) dla Przegląd).

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać instrukcje dotyczące wdrażania usług sieci web do uruchomienia na komputerze lokalnym lub w klastrze w dalszym ciągu na [wdrażania modelu uczenia maszynowego w formie usługi internetowej](model-management-service-deploy.md).
* Wypróbuj jedną z wielu przykładów w galerii.
