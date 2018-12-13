---
title: Usługi Azure Machine Learning Model zarządzania instalacja i Konfiguracja | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano kroki i pojęcia zaangażowane w instalowaniu i konfigurowaniu Zarządzanie modelami w usłudze Azure Machine Learning.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ROBOTS: NOINDEX
ms.openlocfilehash: d10f61e6276b1ce0a0237e3cf2e35a68edf4a0cf
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257584"
---
# <a name="model-management-setup"></a>Model konfiguracji zarządzania

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



## <a name="overview"></a>Przegląd
Ten dokument ułatwia rozpoczęcie pracy z używaniem Zarządzanie modelami w usłudze Azure ML na potrzeby wdrażania i zarządzania nimi modeli jako usług sieci web usługi machine learning. 

Za pomocą zarządzania modelami w usłudze Azure ML, mogą efektywnie wdrażanie i zarządzanie modeli uczenia maszynowego, które zostały utworzone przy użyciu wielu struktur, w tym SparkML, Keras, TensorFlow, Microsoft Cognitive Toolkit lub Python. 

Do końca tego dokumentu powinny mieć możliwość środowiska zarządzania modelami, skonfigurowane i gotowe do wdrażania modeli uczenia maszynowego.

## <a name="what-you-need-to-get-started"></a>Wymagane do rozpoczęcia pracy
Aby maksymalnie wykorzystać możliwości tego przewodnika, należy mieć dostęp właściciela do subskrypcji platformy Azure, którą można wdrożyć swoje modele, aby.
Interfejs wiersza polecenia jest wstępnie zainstalowany w aplikacji Azure Machine Learning Workbench i w [maszyny Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Przy użyciu interfejsu wiersza polecenia
Aby używać interfejsów wiersza polecenia (interfejsów wiersza polecenia) z aplikacji Workbench, kliknij przycisk **pliku** -] **Otwórz interfejs wiersza polecenia**. 

Na maszyny wirtualnej analizy danych, łączenie i Otwórz okno wiersza polecenia. Typ `az ml -h` Aby wyświetlić opcje. Aby uzyskać więcej informacji na temat poleceń, Użyj flagi help.

W innych systemach trzeba zainstalować interfejsów wiersza polecenia.

### <a name="installing-or-updating-on-windows"></a>Instalowanie (lub aktualizowanie) na Windows

Zainstaluj język Python z https://www.python.org/. Upewnij się, że wybrano SE nainstalovat pip.

Otwórz wiersz polecenia przy użyciu Uruchom jako Administrator i uruchom następujące polecenia:

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>Jeśli masz wcześniejszą wersję, odinstaluj go najpierw za pomocą następującego polecenia:
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Instalowanie (lub aktualizowania) w systemie Linux
Uruchom następujące polecenie w wierszu polecenia, a następnie postępuj zgodnie z instrukcjami:

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
>Wyloguj się i zaloguj się ponownie do swojej sesji SSH, aby zmiany zaczęły obowiązywać.
>Poprzednie polecenia można użyć, aby zaktualizować starszą wersję interfejsów wiersza polecenia na maszyny DSVM.
>

## <a name="deploying-your-model"></a>Wdrażanie modelu
Interfejsów wiersza polecenia umożliwia wdrażanie modeli jako usług sieci web. Usługi sieci web można wdrażać lokalnie lub w klastrze.

Uruchom program z lokalnym wdrożeniem, zweryfikuj, że model i kod pracować, to wdrożenie w klastrze skalowania w środowisku produkcyjnym.

Aby rozpocząć, musisz skonfigurować swoje środowisko wdrażania. Konfiguracja środowiska jest zadanie w czasie. Po zakończeniu instalacji można użyć ponownie środowisko w przypadku kolejnych wdrożeń. Zobacz następujące sekcji, aby uzyskać więcej szczegółów.

Po zakończeniu instalacji środowiska:
- Monit o logowanie do platformy Azure. Aby się zarejestrować, należy użyć przeglądarki sieci web o otwarcie strony https://aka.ms/devicelogin i wprowadzenie udostępnionego kodu do uwierzytelniania.
- Podczas procesu uwierzytelniania zostanie wyświetlony monit o konta do uwierzytelniania za pomocą. Ważne: Wybierz prawidłową subskrypcję platformy Azure i wystarczające uprawnienia do tworzenia zasobów na koncie. konto — gdy logowanie zostanie zakończone, zobaczy informacji o subskrypcji i zostanie wyświetlony monit, czy chcesz kontynuować z wybranego konta .

### <a name="environment-setup"></a>Konfigurowanie środowiska
Aby rozpocząć proces instalacji, należy zarejestrować dostawcę środowiska, wprowadzając następujące polecenie:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>Wdrożenie lokalne
Aby wdrożyć i przetestować usługę sieci web na komputerze lokalnym, należy skonfigurować środowisku lokalnym za pomocą następującego polecenia:

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Wymaga lokalnego wdrożenia usługi internetowej usługi zainstalować platformę Docker na komputerze lokalnym. 
>

Polecenie Ustawienia lokalnego środowiska umożliwia utworzenie następujących zasobów w ramach subskrypcji:
- Grupa zasobów (Jeśli nie zostanie podana)
- Konto magazynu
- Rejestr Azure Container Registry (ACR)
- Usługa Application insights

Gdy Instalator zakończy się pomyślnie, należy ustawić środowisko ma być używany, używając następującego polecenia:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Wdrażanie klastra
Użyj wdrożenia klastra na potrzeby scenariuszy produkcyjnych w dużej skali. Konfiguruje klaster usługi ACS z rozwiązaniem Kubernetes jako orkiestrator. Klastra ACS może być skalowana w celu obsługi większej przepływności dla wywołania usługi sieci web.

Aby wdrożyć usługę sieci web w środowisku produkcyjnym, należy najpierw skonfigurować środowisko, używając następującego polecenia:

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

Polecenie instalacji środowiska klastra umożliwia utworzenie następujących zasobów w ramach subskrypcji:
- Grupa zasobów (Jeśli nie zostanie podana)
- Konto magazynu
- Rejestr Azure Container Registry (ACR)
- Wdrożenie rozwiązania Kubernetes w klastrze usługi Azure Container Service (ACS)
- Usługa Application insights

Szybko są tworzone grupy zasobów, konto magazynu oraz usługi ACR. Wdrażanie usług ACS może potrwać do 20 minut. 

Po zakończeniu instalacji należy ustawić środowisko, które ma być używany dla tego wdrożenia. Użyj następującego polecenia:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Po utworzeniu środowiska w przypadku kolejnych wdrożeń, wystarczy użyj polecenia set powyżej, aby użyć go ponownie.
>

>[!NOTE] 
>Aby utworzyć punkt końcowy HTTPS, Określ certyfikat SSL, podczas tworzenia klastra przy użyciu nazwa certyfikatu i--pem certyfikatu opcje az ml env Instalatora. Spowoduje to utworzenie klastra do obsługi żądań przy użyciu protokołu https, zabezpieczone przy użyciu podanych certyfikatów. Po zakończeniu instalacji należy utworzyć rekord CNAME DNS, który wskazuje nazwę FQDN klastra.

### <a name="create-an-account"></a>Utwórz konto
Konto usługi jest wymagane na potrzeby wdrażania modeli. Należy zrobić to raz dla danego konta i ponownie używać tego samego konta w wielu wdrożeniach.

Aby utworzyć nowe konto, użyj następującego polecenia:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Aby użyć istniejącego konta, użyj następującego polecenia:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>Wdrażanie modelu
Teraz można przystąpić do wdrażania modelu zapisane w postaci usługi sieci web. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

### <a name="next-steps"></a>Następne kroki
Wypróbuj jedną z wielu przykładów w galerii.
