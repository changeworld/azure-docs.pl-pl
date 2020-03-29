---
title: Przykładowe programy & wskazówki dotyczące uczenia maszynowego
titleSuffix: Azure Data Science Virtual Machine
description: Za pomocą tych przykładów i instruktajów dowiedz się, jak obsługiwać typowe zadania i scenariusze za pomocą maszyny wirtualnej nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900051"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Przykłady na maszynach wirtualnych do nauki o danych platformy Azure

Maszyny wirtualne do nauki o danych platformy Azure (DSVM) zawierają kompleksowy zestaw przykładowego kodu. Te przykłady obejmują notesy jupyter i skrypty w językach takich jak Python i R.
> [!NOTE]
> Aby uzyskać więcej informacji na temat uruchamiania notesów Jupyter na maszynach wirtualnych do nauki o danych, zobacz sekcję [Dostęp jupyter.](#access-jupyter)

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić te przykłady, musisz aprowizować maszynę wirtualną nauki o danych. Zobacz szybki start dla [Windows](./provision-vm.md) i [Ubuntu](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Dostępne przykłady
| Kategoria próbek | Opis | Lokalizacje |
| ------------- | ------------- | ------------- |
| Język R  | Przykłady ilustrują scenariusze, takie jak łączenie się z magazynami danych w chmurze opartych na platformie Azure i jak porównać usługi Open source R i Microsoft Machine Learning Server. Wyjaśniają również, jak operacjonalizacji modeli na microsoft machine learning server i SQL Server. <br/> [Język R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Język Python  | Przykłady wyjaśniają scenariusze, takie jak łączenie się z magazynami danych w chmurze opartych na platformie Azure i jak pracować z usługą Azure Machine Learning.  <br/> [Język Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Język Julia  | Zawiera szczegółowy opis kreślenia i uczenia głębokiego w Julii. Wyjaśnia również, jak wywołać C i Python z Julia. <br/> [Język Julia](#julia-language) |<br/> W systemie Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> W systemie Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Ilustruje sposób tworzenia modeli uczenia maszynowego i uczenia głębokiego za pomocą uczenia maszynowego. Wdrażaj modele w dowolnym miejscu. Korzystaj z automatycznego uczenia maszynowego i inteligentnego dostrajania hiperparametrycznego. Należy również korzystać z zarządzania modelami i szkoleń rozproszonych. <br/> [Uczenie maszynowe](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notebooki PyTorch  | Próbki uczenia głębokiego korzystające z sieci neuronowych opartych na PyTorch. Notebooki wahają się od początkujących do zaawansowanych scenariuszy.  <br/> [Notebooki PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Wiele przykładów sieci neuronowych i technik zaimplementowanych przy użyciu struktury TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Zestaw narzędzi usług Microsoft Cognitive <br/>   | Próbki uczenia głębokiego opublikowane przez zespół Cognitive Toolkit w firmie Microsoft.  <br/> [Zestaw narzędzi usług Cognitive](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> W systemie Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Próbki uczenia głębokiego korzystające z sieci neuronowych opartych na caffe2. Kilka notebooków zapozna użytkowników z Caffe2 i jak go skutecznie używać. Przykłady obejmują wstępne przetwarzanie obrazu i tworzenie zestawu danych. Obejmują one również regresji i jak używać wstępnie przeszkolonych modeli. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2o   | Przykłady oparte na języku Python, które używają H2O dla rzeczywistych scenariuszy problemów. <br/> [H2o](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Język SparkML  | Przykłady korzystające z funkcji zestawu narzędzi Apache Spark MLLib za pośrednictwem pySpark i MMLSpark: Microsoft Machine Learning for Apache Spark on Apache Spark 2.x.  <br/> [Język SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standardowe przykłady uczenia maszynowego w XGBoost dla scenariuszy, takich jak klasyfikacja i regresja. <br/> [XGBoost (XGBoost)](#xgboost) | <br/>W systemie Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Dostęp Jupyter 

Aby uzyskać dostęp do jupytera, wybierz ikonę **Jupyter** w menu pulpitu lub aplikacji. Można również uzyskać dostęp do Jupyter na linuxowej wersji DSVM. Aby uzyskać zdalny dostęp z `https://<Full Domain Name or IP Address of the DSVM>:8000` przeglądarki internetowej, przejdź do Ubuntu.

Aby dodać wyjątki i udostępnić dostęp Jupyter za pośrednictwem przeglądarki, skorzystaj z następujących wskazówek:


![Włącz wyjątek Jupyter](./media/ubuntu-jupyter-exception.png)


Zaloguj się przy użyciu tego samego hasła, którego używasz do logowania się do maszyny wirtualnej nauki o danych.
<br/>

**Jupyter domu**
<br/>![Jupyter domu](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Język R 
<br/>![Próbki R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Język Python
<br/>![Przykłady w języku Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Język Julia 
<br/>![Julia próbki](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Przykłady usługi Azure Machine Learning](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Próbki PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Próbki TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Próbki CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![próbki caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2o 
<br/>![Próbki H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML (Iskra ML) 
<br/>![Próbki sparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Próbki XGBoost](./media/xgboost-samples.png)<br/>

