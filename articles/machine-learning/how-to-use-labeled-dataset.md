---
title: Tworzenie i eksplorowanie zestawów danych za pomocą etykiet
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak eksportować etykiety danych z projektów etykietowania usługi Azure Machine Learning i używać ich do zadań uczenia maszynowego.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549491"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Tworzenie i eksplorowanie zestawu danych usługi Azure Machine Learning za pomocą etykiet

W tym artykule dowiesz się, jak wyeksportować etykiety danych z projektu etykietowania danych usługi Azure Machine Learning i załadować je do popularnych formatów, takich jak pandas dataframe do eksploracji danych lub zestaw danych Torchvision do transformacji obrazu. 

## <a name="what-are-datasets-with-labels"></a>Co to są zestawy danych z etykietami 

Zestawy danych usługi Azure Machine Learning z etykietami są [zestawami kartotekowymi](how-to-create-register-datasets.md#dataset-types) z właściwością etykiety, będziemy odwoływać się do nich jako zestawy danych etykiet. Te określone typy Zestawów danych tabelaryczne są tworzone tylko jako dane wyjściowe projektów etykietowania danych usługi Azure Machine Learning. Utwórz projekt etykietowania danych za pomocą [tych kroków](how-to-create-labeling-projects.md). Uczenie maszynowe obsługuje projekty etykietowania danych do klasyfikacji obrazów, wieloznakowe lub wieloklasowe oraz identyfikację obiektów wraz z ograniczonymi polami.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree) przed rozpoczęciem.
* Zestaw [SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub dostęp do studia usługi Azure Machine [Learning](https://ml.azure.com/).
    * Instalowanie pakietu [zestawu danych azure-contrib](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)
* Obszar roboczy uczenia maszynowego. Zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md).
* Dostęp do projektu etykietowania danych usługi Azure Machine Learning. Jeśli nie masz projektu etykietowania, utwórz go za pomocą [tych kroków](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Eksportowanie etykiet danych 

Po zakończeniu projektu etykietowania danych można wyeksportować dane etykiety z projektu etykietowania. W ten sposób można przechwycić odwołanie do danych i ich etykiet i wyeksportować je w [formacie COCO](http://cocodataset.org/#format-data) lub jako zestaw danych usługi Azure Machine Learning. Użyj przycisku **Eksportuj** na stronie **Szczegóły projektu** projektu etykietowania.

### <a name="coco"></a>Coco 

 Plik COCO jest tworzony w domyślnym magazynie obiektów blob obszaru roboczego usługi Azure Machine Learning w folderze w *ramach export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Zestaw danych usługi Azure Machine Learning

Można uzyskać dostęp do wyeksportowanego zestawu danych usługi Azure Machine Learning w sekcji **Zestawy danych** w studio usługi Azure Machine Learning. Strona **Szczegóły** zestawu danych zawiera również przykładowy kod dostępu do etykiet z języka Python.

![Wyeksportowany zestaw danych](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Eksplorowanie oznaczonych zestawów danych

Załaduj oznaczone zestawy danych do zestawu danych pandas dataframe lub Torchvision, aby wykorzystać popularne biblioteki typu open source do eksploracji danych, a pyTorch udostępniał biblioteki do przekształcania i szkolenia obrazów.

### <a name="pandas-dataframe"></a>Pandas dataframe

Można załadować oznaczone zestawy danych do pandas [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) dataframe `azureml-contrib-dataset` z metodą z klasy. Zainstaluj klasę za pomocą następującego polecenia powłoki: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Obszar nazw azureml.contrib zmienia się często, ponieważ pracujemy nad ulepszeniem usługi. W związku z tym wszystko w tej przestrzeni nazw należy uznać za wersję zapoznawczą i nie jest w pełni obsługiwane przez firmę Microsoft.

Oferujemy następujące opcje obsługi plików dla strumieni plików podczas konwersji na ramkę danych pandas.
* Pobierz: pobierz pliki danych na ścieżkę lokalną.
* Montaż: Zamontuj pliki danych w punkcie instalacji. Instalacja działa tylko dla obliczeń opartych na systemie Linux, w tym maszyny wirtualnej usługi Azure Machine Learning i przetwarzania usługi Azure Machine Learning Compute.

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Zestawy danych torchvision

Zestawy danych z etykietami można załadować do zestawu danych Torchvision za `azureml-contrib-dataset` pomocą metody [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) również z klasy. Aby korzystać z tej metody, musisz mieć zainstalowany [PyTorch.](https://pytorch.org/) 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Następne kroki

* Zobacz [zestaw danych z notesem etykiet,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) aby uzyskać pełną próbkę szkolenia.
