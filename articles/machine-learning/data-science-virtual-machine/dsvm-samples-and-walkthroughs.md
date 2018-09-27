---
title: Przykłady i wskazówki dla maszyny wirtualnej analizy danych — Azure | Dokumentacja firmy Microsoft
description: Przykłady i wskazówki dla maszyny wirtualnej do nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: a1f15b805d2f27152d9ba85608ce0dc1d1aac21e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392572"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Przykłady na maszynach wirtualnych do nauki o danych (DSVM)

Maszyny zawiera wyczerpujący zbiór przykładowy kod zarówno w formie notesów programu Jupyter, a także skrypty w językach takich jak Python i R.    
> [!NOTE]
> Zapoznaj się [Jupyter dostępu](#access-jupyter) sekcji, aby uzyskać więcej informacji na temat uruchamiania aplikacji Jupyter Notebooks na maszyny wirtualnej DSVM.

## <a name="quick-reference-of-samples"></a>Krótki przewodnik próbek
| Przykłady kategorii | Opis | Lokalizacje |
| ------------- | ------------- | ------------- |
| **R** języka  | Przykłady w **R** wyjaśniające, scenariusze, takie jak łączenie z danymi w chmurze platformy Azure są przechowywane, porównywanie Open Source R i Microsoft R & modele operacjonalizacji oprogramowania Microsoft R Server lub SQL Server. <br/> [Zrzut ekranu](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** języka  | Przykłady w **Python** wyjaśniające, scenariusze, takie jak łączenie z magazynami danych w chmurze platformy Azure i Praca z **usługi Azure Machine Learning**.  <br/> [Zrzut ekranu](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Julia** języka  | Próbkowanie **Julia** , szczegółowo Plotting w Julia, głębokiego uczenia się w Julia, wywoływania C i Python z Julia itp. <br/> [Zrzut ekranu](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **Usługi Azure Machine Learning** usługi Azure ml  | Tworzenie, uczenie Maszynowe i głębokiego uczenia modeli za pomocą **usługi Azure Machine Learning** Service i wdrażanie modeli w dowolnym miejscu. Wykorzystaj możliwości, takie jak automatyczne ML inteligentne parametr hyper dostosowywanie i zarządzanie modelami rozproszonego szkolenia. <br/> [Zrzut ekranu](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| **PyTorch** notesów  | Głębokie uczenie przy użyciu przykładów **PyTorch** na podstawie sieci neuronowych. Istnieją różne notesów, począwszy od początkujących do zaawansowanych scenariuszy.  <br/> [Zrzut ekranu](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| **TensorFlow**  | Wiele różnych neuronowych przykłady sieci i implementowany przy użyciu technik **TensorFlow** framework. <br/> [Zrzut ekranu](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive Toolkit)  | Opublikowane przez zespół zestawu narzędzi Cognitive Toolkit w firmie Microsoft przykłady do uczenia głębokiego.  <br/> [Zrzut ekranu](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **caffe2** | Głębokie uczenie przy użyciu przykładów **caffe2** na podstawie sieci neuronowych. Istnieje kilka notesów przeznaczone do zapoznania się użytkowników przy użyciu caffe2 i jak z niej korzystać, wraz z przykładami, takich jak obraz, stosując przetwarzanie wstępne tworzenie zestawu danych, regresji i za pomocą wstępnie przeszkolonych modeli. <br/> [Zrzut ekranu](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| **H2O**   | Przykłady oparta na środowisku Python, wykorzystując **H2O** wiele problemów rzeczywistych scenariuszy. <br/> [Zrzut ekranu](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** języka  | Przykład wykorzystania funkcji i możliwości platforma Spark **MLlib** toolkit za pośrednictwem **pySpark** i **MMLSpark — Microsoft Machine Learning dla platformy Apache Spark** na **Platformy Apache Spark 2.x**.  <br/> [Zrzut ekranu](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| **XGBoost** | Standardowa usługi Machine Learning z przykładami w **XGBoost** do scenariuszy, takich jak klasyfikacji, regresji itp. <br/> [Zrzut ekranu](#xgboost) | <br/>**Windows**:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Dostęp do aplikacji Jupyter 

Dostęp do aplikacji Jupyter, klikając `Jupyter` ikony w menu pulpitu lub aplikacji. Można również przejść Jupyter na systemie Linux wersje maszyny DSVM zdalnie z poziomu przeglądarki sieci web, odwiedzając **`https://<Full Domain Name or IP Address of the DSVM>:8000`** w systemie Ubuntu.

Zobacz zrzut ekranu, aby dodać wyjątek i włączyć dostęp do aplikacji Jupyter za pośrednictwem przeglądarki.


![Włączenie wyjątku Jupyter](./media/ubuntu-jupyter-exception.png)


Zaloguj się przy użyciu tego samego hasła podczas logowania do sieci dla maszyny DSVM.
<br/>

**Strona główna programu Jupyter**
<br/>![Strona główna programu Jupyter](./media/jupyter-home.png)<br/>

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

