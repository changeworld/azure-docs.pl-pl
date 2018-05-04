---
title: Dostosowanie obrazu kontener używany do wdrażania modeli uczenia Maszynowego Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób dostosowywania obrazu kontener dla modeli uczenia maszynowego Azure
services: machine-learning
author: tedway
ms.author: tedway, raymondl
manager: mwinkle
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ms.openlocfilehash: f56b651c40187e42361ac12f0cbf4e509385e0d2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Dostosowanie obrazu kontenera używana dla modeli uczenia Maszynowego Azure

W tym artykule opisano sposób dostosowywania obrazu kontener dla modeli uczenia maszynowego Azure.  Azure ML Workbench używa kontenerów machine learning modele wdrażania. Modele są wdrażane wraz z ich zależności i uczenie Maszynowe Azure tworzy obraz z modelu, zależności i skojarzone pliki.

## <a name="how-to-customize-the-docker-image"></a>Sposobu dostosowywania obrazu Docker
Dostosowanie obrazu Docker, która wdraża uczenie Maszynowe Azure przy użyciu:

1. A `dependencies.yml` plik: Zarządzanie zależności, które mogą zostać zainstalowane z [PyPi]( https://pypi.python.org/pypi), można użyć `conda_dependencies.yml` plik z projektu Workbench lub Utwórz swój własny. Jest to metoda zaleca się instalowanie zależności Python, które mogą zostać zainstalowane narzędzia pip.

   Przykład polecenia interfejsu wiersza polecenia:
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
        
2. Plik kroki Docker: przy użyciu tej opcji, możesz dostosować wdrożonego obrazu, instalując zależności, których nie można zainstalować z PyPi. 

   Plik powinny obejmować kroki instalacji Docker, takich jak plik DockerFile. Poniższe polecenia są dozwolone w pliku: 

    URUCHOM ENV, ARG, ETYKIETY, UDOSTĘPNIANIE

   Przykład polecenia interfejsu wiersza polecenia:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Obraz, manifestu i usługi poleceniach flagi plik docker.

   Przykładowy plik kroki Docker:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> Obraz podstawowy dla kontenerów uczenie Maszynowe Azure Ubuntu i nie można zmienić. Określ inny obraz podstawowy, zostaną zignorowane.

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy został dostosowany obraz kontenera, można wdrożyć klaster do użytku na dużą skalę.  Aby uzyskać więcej informacji na temat konfigurowania klastra dla wdrożenia usługi sieci web, zobacz [konfiguracji zarządzania modelu](deployment-setup-configuration.md). 
