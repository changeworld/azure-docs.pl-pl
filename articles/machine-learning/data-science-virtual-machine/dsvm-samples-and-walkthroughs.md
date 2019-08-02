---
title: Przykłady i wskazówki dla maszynami wirtualnymi analizy danych — Azure | Dokumentacja firmy Microsoft
description: Poznaj przykłady i wskazówki, które pokazują, jak wykonywać typowe zadania i scenariusze z maszyny wirtualnej do nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: vijetaj
ms.openlocfilehash: 57ebefb11d23c5ea18d5859ca494944e6a845e04
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565311"
---
# <a name="samples-on-data-science-virtual-machines"></a>Przykłady na maszynach wirtualnych do nauki o danych

Maszyn wirtualnych do nauki o danych zawiera wyczerpujący zbiór przykładowy kod. Przykładowy kod jest w formie notesów Jupyter i skrypty w językach takich jak Python i R. 
> [!NOTE]
> Aby uzyskać więcej informacji na temat sposobu uruchamiania aplikacji Jupyter notebooks na maszynach wirtualnych do nauki o danych, zobacz [Jupyter dostępu](#access-jupyter) sekcji.

## <a name="quick-reference-of-samples"></a>Krótki przewodnik próbek
| Przykłady kategorii | Opis | Lokalizacje |
| ------------- | ------------- | ------------- |
| Język R  | Przykłady w języku R opisano scenariusze, takie jak sposób nawiązywania połączeń z magazynami danych w chmurze platformy Azure. Mogą również wyjaśniają, jak porównania języka R typu open source i R. firmy Microsoft I ich wyjaśniają, jak operacjonalizować modele na Microsoft R Server lub SQL Server. <br/> [Język R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Język Python  | Przykłady w języku Python opis scenariuszy, takich jak łączenie z magazynami danych w chmurze platformy Azure i pracy z usługą Azure Machine Learning.  <br/> [Język Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Języka Julia  | Przykład w Julia, która szczegółowo określa najważniejsze wykreślania i głębokiego uczenia na platformie Julia. Od Julii danych wyjaśniono również wywoływania C i Python. <br/> [Języka Julia](#julia-language) |<br/> W systemie Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> W systemie Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Tworzenie usługi machine learning i głębokiego uczenia modeli za pomocą usługi Machine Learning. Wdrażanie modeli w dowolnym miejscu. Użyj zautomatyzowanej uczenia maszynowego i strojenia hiperparametrycznego inteligentnego. Zarządzanie modelami i rozproszonego szkolenia także używać. <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notesy PyTorch  | Przykładów, które korzystają z sieci neuronowych opartych na protokole PyTorch do uczenia głębokiego. Notesy w zakresie od początkujących do zaawansowanych scenariuszy.  <br/> [Notesy PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Przykłady różnych sieci neuronowych i technik zaimplementowany przy użyciu struktury TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Zestaw narzędzi usług Microsoft Cognitive <br/>   | Opublikowane przez zespół zestawu narzędzi Cognitive Toolkit w firmie Microsoft przykłady do uczenia głębokiego.  <br/> [Zestaw narzędzi cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> W systemie Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| caffe2 | Przykładów, które korzystają z sieci neuronowych opartych na protokole caffe2 do uczenia głębokiego. Kilka notesów zapoznaj się użytkowników przy użyciu caffe2 oraz sposób ich efektywnego wykorzystania. Przykłady obejmują tworzenie zestawu danych i wstępnego przetwarzania obrazu. Te aktualizacje obejmują również regresji i sposobu używania wstępnie szkolone modele. <br/> [caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Przykłady oparta na środowisku Python, używających H2O problemów rzeczywistych scenariuszy. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Język SparkML  | Przykłady, które korzystają z funkcji narzędzi Biblioteka MLLib platformy Spark za pośrednictwem pySpark i MMLSpark — Microsoft Machine Learning dla platformy Apache Spark, Apache Spark 2.x.  <br/> [Język SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standardowa usługi machine learning próbek w XGBoost do scenariuszy, takich jak klasyfikacji i regresji. <br/> [XGBoost](#xgboost) | <br/>W systemie Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Dostęp do aplikacji Jupyter 

Aby uzyskać dostęp do aplikacji Jupyter, wybierz pozycję `Jupyter` ikony w menu pulpitu lub aplikacji. Możesz również uzyskać dostęp do programu Jupyter na wersje systemu Linux maszyny wirtualnej do analizy danych. Możesz uzyskać dostęp zdalnie za pomocą przeglądarki sieci web, odwiedzając stronę `https://<Full Domain Name or IP Address of the DSVM>:8000` w systemie Ubuntu.

Aby dodać wyjątki i udostępnić Jupyter dostęp za pośrednictwem przeglądarki, zobacz poniższy zrzut ekranu.


![Włączenie wyjątku Jupyter](./media/ubuntu-jupyter-exception.png)


Zaloguj się przy użyciu tego samego hasła podczas logowania do sieci dla maszyn wirtualnych do nauki o danych.
<br/>

**Macierzysty Jupyter**
<br/>![Macierzysty Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Język R 
<br/>![Przykłady języka R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Język Python
<br/>![Przykłady w języku Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Języka Julia 
<br/>![Przykłady Julia](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Przykłady AzurekML](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Przykłady PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Przykłady TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Przykłady CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>caffe2 
<br/>![Przykłady caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Przykłady H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Przykłady SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Przykłady XGBoost](./media/xgboost-samples.png)<br/>

