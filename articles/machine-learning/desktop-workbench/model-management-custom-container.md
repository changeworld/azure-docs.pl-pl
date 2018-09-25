---
title: Dostosowanie obrazu kontenera, używany do wdrażania modeli uczenia Maszynowego platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób dostosowywania obrazu kontenera na potrzeby modeli usługi Azure Machine Learning
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: mldocs, raymondl
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: d4cfb7067510ec06df3319035dee5e2195cb2f9d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997543"
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Dostosowanie obrazu kontenera, używany dla modeli uczenia Maszynowego Azure

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



W tym artykule opisano sposób dostosowywania obrazu kontenera na potrzeby modeli usługi Azure Machine Learning.  Usługa Azure ML Workbench używa kontenerów do wdrażania modeli uczenia maszynowego. Modele są wdrażane wraz z ich zależnościami i uczenie Maszynowe Azure tworzy obraz z modelu, zależności i skojarzone pliki.

## <a name="how-to-customize-the-docker-image"></a>Jak dostosować obraz platformy Docker
Dostosowanie obrazu platformy Docker, wdrażającą uczenie Maszynowe Azure przy użyciu:

1. A `dependencies.yml` pliku: Zarządzanie zależnościami, które są możliwe do zainstalowania z [PyPi]( https://pypi.python.org/pypi), możesz użyć `conda_dependencies.yml` plik z projektu aplikacji Workbench, lub Utwórz swoje własne. To podejście zaleca się instalowania zależności języka Python, które są możliwe do zainstalowania narzędzia pip.

   Przykład interfejsu wiersza polecenia:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   Przykładowy plik conda_dependencies: 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. Docker kroki pliku: użycie tej opcji, możesz dostosować wdrożonym obrazie, instalując zależności, których nie można zainstalować z PyPi. 

   Plik powinien zawierać kroki instalacji platformy Docker, takich jak plik DockerFile. Następujące polecenia są dozwolone w pliku: 

    URUCHOM ENV, ARG, ETYKIETY, UDOSTĘPNIANIE

   Przykład interfejsu wiersza polecenia:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Obraz, Manifest i usługa poleceniach akceptowana jest wartość flagi pliku platformy docker.

   Przykładowy plik kroki platformy Docker:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> Obraz podstawowy dla kontenerów z usługi uczenie Maszynowe Azure jest Ubuntu i nie można jej zmienić. Jeśli określisz inny obraz podstawowy, zostaną zignorowane.

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy został dostosowany obraz kontenera, można wdrożyć klaster do użycia na dużą skalę.  Aby uzyskać więcej informacji na temat konfigurowania klastra dla wdrożenia usługi internetowej, zobacz [konfiguracji zarządzania w modelu](deployment-setup-configuration.md). 
