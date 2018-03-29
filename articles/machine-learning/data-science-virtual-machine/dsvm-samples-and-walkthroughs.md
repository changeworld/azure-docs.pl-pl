---
title: Przykłady i wskazówki dla danych nauki maszyny wirtualnej - Azure | Dokumentacja firmy Microsoft
description: Przykłady i wskazówki dla maszyny wirtualnej analizy danych.
keywords: narzędzia do analizy danych, maszyny wirtualnej analizy danych, narzędzia do analizy danych, nauki danych systemu linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 6c5b5c8c9dbb03a2189eb5f1539499ace55e7dfc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Przykłady na maszynach wirtualnych nauki danych (DSVM)

DSVMs mają załączone przykłady pełni działał w poziomie w formie notesów Jupyter i te, które nie są oparte na Jupyter. Dostęp do aplikacji Jupyter, klikając `Jupyter` ikony w menu aplikacji lub pulpitu.  
> [!NOTE]
> Zapoznaj się [Jupyter dostępu](#access-jupyter) sekcji, aby umożliwić notesów Jupyter na Twojej DSVM.

## <a name="quick-reference-of-samples"></a>Krótki przewodnik próbek
| Przykłady kategorii | Opis | Lokalizacje |
| ------------- | ------------- | ------------- |
| **R** języka  | Przykłady w **R** wyjaśniający scenariusze, takie jak łączenie z danymi w chmurze Azure przechowuje porównanie Otwórz R źródła i Microsoft R & modele operacyjnych Microsoft R Server lub SQL Server. <br/> [Zrzut ekranu](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** języka  | Przykłady w **Python** wyjaśniający scenariuszy, takich jak nawiązać połączenie z magazynów danych w chmurze platformy Azure i Praca z **usługi Azure Machine Learning**.  <br/> [Zrzut ekranu](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Julia** języka  | Przykładowe w **Julia** który szczegółów Plotting w Julia, głębokie uczenie w Julia, wywoływanie C i języka Python z Julia itp. <br/> [Zrzut ekranu](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (Kognitywnych zestaw narzędzi firmy Microsoft)  | Bezpośrednie uczenia przykłady publikowane przez zespół kognitywnych zestawu narzędzi firmy Microsoft.  <br/> [Zrzut ekranu](#cntk) | <br/>**Windows**:<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **MXNet** notesów  | Głębokość uczenia przykłady wykorzystania **MXNet** na podstawie sieci neuronowej. Istnieją różne notesów od dla początkujących użytkowników zaawansowanych scenariuszy.  <br/> [Zrzut ekranu](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Uczenie maszynowe Azure** uczenie maszynowe Azure  | Interakcja z **usługi Azure Machine Learning** Studio i tworzenie punktów końcowych usługi sieci web z lokalnie przeszkolone modele, oceniania przepływów pracy opartych na chmurze. <br/> [Zrzut ekranu](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **caffe2** | Głębokość uczenia przykłady wykorzystania **caffe2** na podstawie sieci neuronowej. Istnieje kilka notesów umożliwiającą zapoznanie użytkowników z caffe2 i jak z niego korzystać w rezultacie wraz z przykładami takich jak obraz przetwarzanie wstępne utworzenie zestawu danych, regresji, a za pomocą wstępnie uczony modeli. <br/> [Zrzut ekranu](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | Na podstawie języka Python próbki, przy użyciu **H2O** wiele problemów rzeczywistych scenariuszy. <br/> [Zrzut ekranu](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** języka  | Przykład wykorzystania funkcji i możliwości platforma Spark **MLlib** toolkit za pośrednictwem **pySpark 2.0** na **Apache Spark 2.0**.  <br/> [Zrzut ekranu](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| **MMLSpark** języka  | Wiele przykładów przy użyciu **MMLSpark - Microsoft Machine Learning platformy Apache Spark**, która to platforma, która udostępnia szereg głębokie uczenie i narzędzia analizy danych dla **Apache Spark**. <br/> [Zrzut ekranu](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | Wiele próbek sieci Neuronowej i metody implementowane za pomocą różnych **TensorFlow** framework. <br/> [Zrzut ekranu](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | Przykłady standardowej uczenia maszynowego w **XGBoost** w scenariuszach, takich jak klasyfikacji, regresji itp. <br/> [Zrzut ekranu](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Dostęp do aplikacji Jupyter 

Odwiedź stronę główną Jupyter, przechodząc do **`https://localhost:9999`** w systemie Windows lub **`https://localhost:8000`** na Ubuntu.


### <a name="enabling-jupyter-access-from-browser"></a>Włączanie dostępu Jupyter z przeglądarki

**Windows DSVM**

Uruchom **`Jupyter SetPassword`** ze skrótu na pulpicie i wykonaj przetworzyć wiersza do zestawu/resetowania hasła dla Jupyter i uruchamiania Jupyter. 
<br/>![Włącz wyjątek Jupyter](./media/jupyter-setpassword.png)<br/>
Po procesie Jupyter została pomyślnie uruchomiona na maszynie Wirtualnej, odwiedzając jest dostępne główną Jupyter **`https://localhost:9999`** w przeglądarce. Zrzut ekranu, aby dodać wyjątek i włączyć Jupyter dostęp za pośrednictwem przeglądarki w temacie
<br/>![Włącz wyjątek Jupyter](./media/windows-jupyter-exception.png)<br/>
Zaloguj się przy użyciu nowego hasła, które zostały ustawione.
<br/>
**Linux DSVM**

Dostępne główną Jupyter na maszynie Wirtualnej, odwiedzając **`https://localhost:8000`** w przeglądarce. Zobacz zrzut ekranu, aby dodać wyjątek i włączyć Jupyter dostęp za pośrednictwem przeglądarki.
<br/>![Włącz wyjątek Jupyter](./media/ubuntu-jupyter-exception.png)<br/>
Zaloguj się przy użyciu tego samego hasła jak logowanie do DSVM.
<br/>

**Strona główna Jupyter**
<br/>![Strona główna Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Języka R 
<br/>![R — przykłady](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Język Python
<br/>![Przykłady dla języka Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia języka 
<br/>![Przykłady Julia](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK 
<br/>![Przykłady CNTK](./media/cntk-samples2.png)<br/>
<br/>![Przykłady CNTK](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXNet
<br/>![Przykłady MXnet](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Przykłady AzurekML](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>caffe2 
<br/>![Przykłady caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Przykłady H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Przykłady SparkML](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Przykłady TensorFlow](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Przykłady XGBoost](./media/xgboost-samples.png)<br/>

