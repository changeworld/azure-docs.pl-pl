---
title: Znane problemy i rozwiązywanie problemów dla usługi Azure Machine Learning
description: Pobierz listę znanych problemów, obejścia problemu i rozwiązywania problemów
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 27e73bc75c5f04190bad3dab49c1d46782982a18
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034145"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Znane problemy i rozwiązywania problemów z usługi Azure Machine Learning
 
Ten artykuł ułatwia znajdowanie i poprawić błędy lub błędów napotkanych podczas korzystania z usługi Azure Machine Learning. 


## <a name="databricks-and-azure-machine-learning"></a>Databricks i Azure Machine Learning

**Zalecenie klastra usługi Databricks:** 

Tworzenie klastra usługi Azure Databricks jako v4.x za pomocą języka Python 3. Firma Microsoft zaleca klastra wysokiej współbieżności.
 
**Zestaw SDK AML błąd instalacji w usłudze Databricks podczas instalowania dodatkowych pakietów** niektórych pakietów, takich jak `psutil upgrade libs`, mogą powodować konflikty. Aby uniknąć błędów instalacji, należy zainstalować pakiety zamrożenia wersji lib. Ten problem jest powiązane z usługi Databricks i nie dotyczą AML zestawu SDK. Przykład:
```
pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
```


## <a name="gather-diagnostics-information"></a>Zbierz informacje diagnostyczne
Czasami może być przydatne Jeśli podasz informacje diagnostyczne podczas pytania o pomoc. Oto, miejsca zamieszkania pliki dziennika:

## <a name="resource-quotas"></a>Limity przydziałów zasobów

Dowiedz się więcej o [limity przydziałów zasobów](how-to-manage-quotas.md) można napotkać podczas pracy z usługą Azure Machine Learning.

## <a name="get-more-support"></a>Uzyskaj więcej obsługę

Możesz przesyłać żądania pomocy technicznej i poproś pomoc techniczną, fora i inne. [Dowiedz się więcej...](support-for-aml-services.md)
