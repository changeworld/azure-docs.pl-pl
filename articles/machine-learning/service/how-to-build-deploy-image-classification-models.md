---
title: Tworzenie i wdrażanie obrazu klasyfikacji modelu przy użyciu usługi Azure Machine Learning pakietu dla komputera wizji.
description: Informacje o sposobie tworzenia, uczenia, testowania i wdrażania modelu klasyfikacji obrazu wizji komputera przy użyciu pakietu Learning Azure maszyny do widzenia komputera.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: 2c988f8651d0ae9a8662b502ca2ba2dbabb2defe
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116055"
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Tworzenie i wdrażanie modeli klasyfikacji obrazu przy użyciu usługi Azure Machine Learning

W tym artykule, Dowiedz się, jak używać **Azure maszyny Learning pakiet dla komputera wizji** (AMLPCV) do uczenia, testowania i wdrażania modelu klasyfikacji obrazu. 

Wiele problemów w domenie wizji komputera będzie możliwe za pomocą funkcji klasyfikacji obrazu. Te problemy obejmują budowania modeli, które odpowiedzi na pytania takie jak:
+ _OBIEKT znajduje się w obrazu? Na przykład "kot", "samochód", "Wyślij" i tak dalej_
+ _Jakie klasy ważności choroby oczu jest evinced przez ten pacjenta retinal skanowania?_

Podczas tworzenia i wdrażania tego modelu z AMLPCV, można przejść przez następujące kroki:
1. Tworzenie zestawu danych
2. Obraz wizualizacji i adnotacji
3. Rozszerzeniu obrazu
4. Definicja modelu sieci Neuronowej głębokość (DNN)
5. Klasyfikator szkolenia
6. Ocena i wizualizacji
7. Wdrażanie usługi sieci Web
8. Usługa sieci Web testów obciążenia

[CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) służy jako platforma głębokie learning szkolenia jest wykonywana lokalnie na maszynie GPU zasilane takich jak ([głębokie uczenia wirtualna nauki danych](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)), i instalacja używa interfejsu wiersza polecenia Azure ML Operationalization.

Zapoznaj się [pakietu dokumentacji](https://aka.ms/aml-packages/vision) szczegółowe odwołania dla każdego modułu i klasy.

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Następujące konta i aplikacji musi skonfigurować i zainstalować:
   - Konto Eksperymentowanie w usłudze Azure Machine Learning 
   - Konto Azure Machine Learning Model zarządzania
   - Zainstalowana aplikacja Azure Machine Learning Workbench

   Jeśli te trzy nie zostały jeszcze utworzony lub zainstalowany, należy wykonać [Szybki Start Azure Machine Learning i Workbench instalacji](../service/quickstart-installation.md) artykułu. 

1. Musi być zainstalowany pakiet Azure maszyny Learning wizji komputera. Dowiedz się, jak [zainstalować ten pakiet w tym miejscu](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Przykładowe dane i notesu

### <a name="get-the-jupyter-notebook"></a>Pobierz notesu Jupyter

Pobieranie Notes, aby uruchomić przykładowy opisu samodzielnie.

> [!div class="nextstepaction"]
> [Pobierz notesu Jupyter](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>Ładowanie danych przykładowych

W poniższym przykładzie użyto składające się z 63 obrazy zastawy stołowe zestawu danych. Obraz jest oznaczone jako należące do jednej z czterech różnych klas (kręgle Pucharze, sztućce, płytkę). Liczba obrazów w tym przykładzie jest mały, aby szybko mogą być wykonywane w tym przykładzie. W praktyce należy podać co najmniej 100 obrazów na klasy. Wszystkie obrazy znajdują się w folderze *"... /sample_data/imgs_recycling / "* w podkatalogach o nazwie"bowl","Pucharze","sztućce"i"tablicy".

![Azure Machine Learning zestawu danych.](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="storage-context"></a>Kontekst magazynu

Kontekst magazynu służy do określenia, gdzie będą przechowywane różne pliki wyjściowe, takich jak obrazy zwiększonej lub DNN plików modelu. Aby uzyskać więcej informacji na kontekstów magazynu, zobacz [dokumentacji StorageContext](https://review.docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-python&branch=smoke-test). 

Zwykle zawartość magazynu nie trzeba ustawić jawnie. Jednak aby uniknąć jego limit 25 MB rozmiar projektu powodowanego przez Azure Machine Learning Workbench, ustawić dane wyjściowe katalogu Azure pakietu Learning maszyny do widzenia komputera do lokalizacji poza projektu usługi Azure Machine Learning ("... /.. /.. /.. / cvtk_output "). Pamiętaj usunąć katalogu "cvtk_output", gdy nie jest już potrzebne.


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix, basic_plot
import matplotlib.pyplot as plt
%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Set storage context.
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)
```




    {
        "storage": {
            "outputs_path": "../../../cvtk_output",
            "persistent_path": "../../../cvtk_output",
            "temp_path": "../../../cvtk_output"
        }
    }



## <a name="create-a-dataset"></a>Tworzenie zestawu danych

Po zaimportowane zależności i Ustaw kontekst magazynu można utworzyć obiektu dataset.

Do utworzenia tego obiektu z usługi Azure Machine Learning pakietu dla komputera wizji, podaj katalog główny obrazów na dysku lokalnym. Ten katalog musi wykonaj tej samej struktury ogólne jako zastawy stołowe zestawu danych, czyli, zawierają podkatalogów z rzeczywistych obrazów:
- główny
    - Etykieta 1
    - Etykieta 2
    - Przyciski ...
    - n etykiety
  
Uczenie modelu klasyfikacji obrazu dla innego zestawu danych jest tak proste, jak zmienić ścieżkę katalogu głównego `dataset_location` poniższy kod, aby wskazywały na różnych obrazów.


```python
# Root image directory 
dataset_location = os.path.abspath(os.path.join(os.getcwd(), "../sample_data/imgs_recycling"))

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

Obiekt dataset udostępnia funkcję pobierania obrazów za pomocą [interfejsu API Bing obraz wyszukiwania](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/). 

Obsługiwane są dwa typy zapytań wyszukiwania: 
+ Zwykły tekst zapytania
+ Zapytania o adres URL obrazu

Należy podać te zapytania oraz etykiety klasy w pliku tekstowym zakodowane w formacie JSON. Na przykład:

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

Ponadto należy jawnie Utwórz obiekt kontekstu zawiera klucz interfejsu API Bing obraz wyszukiwania. Wymaga to subskrypcji interfejsu API Bing obraz wyszukiwania.

## <a name="visualize-and-annotate-images"></a>Wizualizuj i adnotacje obrazów

Można zwizualizować obrazów i poprawne etykiety w obiekcie dataset za pomocą następującego elementu widget. 

Jeśli wystąpi błąd "Nie wykryto Javascript widżet", uruchom to polecenie w celu jego rozwiązania:
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
from ui_utils.ui_annotation import AnnotationUI
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Azure Machine Learning zestawu danych.](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>Rozszerzyć obrazów

[ `augmentation` Modułu](https://docs.microsoft.com/en-us/python/api/cvtk.augmentation) udostępnia funkcje, aby rozszerzyć obiektu dataset za pomocą wszystkie przekształcenia opisanego w [imgaug](https://github.com/aleju/imgaug) biblioteki. Przekształcenia obrazu można grupować w pojedynczy potok, w tym przypadku wszystkie przekształcenia w potoku są stosowane jednocześnie każdego obrazu. 

Jeśli chcesz zastosować różne rozszerzeniu kroki oddzielnie lub w inny sposób, można zdefiniować wiele potoki i przekazać je do *augment_dataset* funkcji. Aby uzyskać dodatkowe informacje i przykłady rozszerzeniu obrazu, zobacz [dokumentacji imgaug](https://github.com/aleju/imgaug).

Dodawanie obrazów zwiększonej do zestawu szkoleniowego jest szczególnie przydatne w przypadku małych zestawów danych. Ponieważ proces szkolenia DNN jest wolniejszy ze względu na zwiększenie liczby obrazów szkolenia, zaleca się rozpocząć eksperymenty bez rozszerzeniu.


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>Zdefiniuj DNN modeli

Obsługiwane są następujące modele głębokie sieci neuronowe pretrained z tym pakietem: 
+ Resnet 18
+ Resnet 34
+ Resnet 50
+ Resnet 101
+ Resnet 152

Te DNNs może służyć jako klasyfikatora, albo featurizer. 

Więcej informacji na temat sieci można znaleźć [tutaj](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md), i jest podstawowe wprowadzenie do uczenia transferu [tutaj](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0).

Parametry klasyfikacji obrazu domyślnego dla tego pakietu są rozdzielczości pikseli 224 x 224 i DNN Resnet 18. Te parametry zostały wybrane do pracy oraz w różnych zadań. Dokładność często można zwiększyć, na przykład przez zwiększenie rozdzielczość obrazu do 500 x 500 pikseli i/lub wybierając bardziej modelu (Resnet 50). Jednak zmiana parametrów są dostępne w znaczący wzrost czasu szkolenia. Zapoznaj się z artykułem na [jak zwiększyć dokładność](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models).


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>Szkolenie klasyfikatora

Możesz wybrać jedną z następujących metod DNN wstępnie przeszkolone.

  - **Uszczegółowienie DNN**, który przygotowuje DNN do przeprowadzania klasyfikacji bezpośrednio. Podczas szkolenia DNN przebiega powoli, zwykle prowadzi do uzyskania najlepszych wyników ponieważ wagi wszystkich sieci można zwiększyć podczas uczenia, aby zapewnić najlepszą dokładności.

  - **DNN featurization**, które uruchamia DNN jako — jest uzyskanie wymiarów dolna reprezentację obrazu (512, 2048 lub 4096 pojawia się). Czy reprezentacja jest następnie używany jako dane wejściowe w celu przeszkolenia oddzielne klasyfikatora. Ponieważ DNN pozostaje niezmieniony, takie podejście jest znacznie szybciej porównywana Uszczegółowienie DNN jednak dokładność nie jest w dobrej. Niemniej jednak szkolenia zewnętrznych klasyfikatora, takich jak liniowej SVM (jak pokazano w poniższym kodzie) można podać silne linii bazowej i ułatwić zrozumienie możliwości problem.
  
TensorBoard umożliwia wizualizowanie postępu szkolenia. Aby aktywować TensorBoard:
1. Dodaj parametr `tensorboard_logdir=PATH` jak pokazano w poniższym kodzie
1. Uruchom klienta TensorBoard za pomocą polecenia `tensorboard --logdir=PATH` w nowej konsoli.
1. Otwórz przeglądarkę sieci web, zgodnie z zaleceniami TensorBoard, która domyślnie wynosi localhost:6006. 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![PNG](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>Oceń i wizualizacji modelu wydajności

Można ocenić wydajność uczonego modelu w zestawie danych niezależnych badań, za pomocą modułu oceny. Metryki oceny, który oblicza należą:
 
+ Dokładność (domyślnie w średnio klasy)
+ Oczyść krzywej
+ Krzywą ROC
+ Obszar w obszarze krzywej
+ Macierz pomyłek


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![PNG](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

from ui_utils.ui_results_viewer import ResultsUI
results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Azure Machine Learning zestawu danych.](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
from ui_utils.ui_precision_recall import PrecisionRecallUI
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Azure Machine Learning zestawu danych.](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

## <a name="operationalization-deploy-and-consume"></a>Operationalization: Wdrażanie i korzystanie

Operationalization jest proces publikowania modeli i kodu jako usługi sieci web i korzystania z tych usług w celu uzyskania wyników biznesowych. 

Po przygotowaniu modelu można wdrożyć ten model jako usługę sieci web do użycia przy użyciu [interfejsu wiersza polecenia Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Modeli można wdrożyć do komputera lokalnego lub klastra usługi kontenera platformy Azure (ACS). Korzystając z usług ACS, można ręcznie skalować usługi sieci web lub za pomocą funkcji skalowania automatycznego.

**Zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure**

Przy użyciu [Azure](https://azure.microsoft.com/) konto z prawidłową subskrypcją, zaloguj się za pomocą interfejsu wiersza polecenia następujące polecenie:
<br>`az login`

+ Aby przełączyć się do innej subskrypcji platformy Azure, użyj polecenia:
<br>`az account set --subscription [your subscription name]`

+ Aby wyświetlić bieżące konto zarządzania modelu, użyj polecenia:
  <br>`az ml account modelmanagement show`

**Tworzenie i ustawianie środowiska wdrażania klastra**

Należy ustawić środowiska wdrażania na raz. Jeśli nie masz jeszcze, konfigurowanie środowiska wdrażania za pomocą [tych instrukcji](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Aby wyświetlić środowiska active wdrażania, użyj następującego polecenia interfejsu wiersza polecenia:
<br>`az ml env show`
   
Przykład polecenia wiersza polecenia platformy Azure do utworzenia i ustawienia środowiska wdrażania

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Zarządzanie usługami sieci web i wdrożenia

Następujące interfejsy API umożliwia wdrażanie modeli jako usługi sieci web, zarządzanie tych usług sieci web i zarządzanie wdrożeniami.

|Zadanie|Interfejs API|
|----|----|
|Tworzenie obiektu wdrożenia|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Wdrażanie usługi sieci web|`deploy_obj.deploy()`|
|Wynik obrazu|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Usuń usługę sieci web|`deploy_obj.delete()`|
|Utwórz obraz docker bez usługi sieci web|`deploy_obj.build_docker_image()`|
|Lista istniejącego wdrożenia|`AMLDeployment.list_deployment()`|
|Usunięcie usługi istnieje z nazwą wdrożenia|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**Dokumentacja interfejsu API:** zapoznaj się [pakietu dokumentacji](https://aka.ms/aml-packages/vision) szczegółowe odwołania dla każdego modułu i klasy.

**Odwołanie do interfejsu wiersza polecenia:** bardziej zaawansowane operacje związane z wdrożeniem, można znaleźć w publikacji [model zarządzania odwołanie interfejsu wiersza polecenia](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Zarządzanie wdrażaniem w portalu Azure**: można śledzić i zarządzanie wdrożeniami w [portalu Azure](https://ms.portal.azure.com/). W portalu Azure Znajdź stronę konto Machine Learning Model zarządzania za pomocą jego nazwy. Następnie przejdź do strony konta Model zarządzania > Model zarządzania > usługi.


```python
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Korzystanie z usługi sieci web 

Po wdrożeniu modelu jako usługę sieci web, możesz wynik obrazów z usługą sieci web przy użyciu jednej z następujących metod:

- Wynik usługę sieci web bezpośrednio z przy użyciu obiektu wdrożenia `deploy_obj.score_image(image_path_or_url)`

- Za pomocą punktu końcowego adresu URL i usługi klucza usługi (Brak w przypadku lokalnego wdrożenia): `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- Formularz żądania HTTP bezpośrednio do wynik końcowy usługi sieci web. Ta opcja jest dla użytkowników zaawansowanych.

### <a name="score-with-existing-deployment-object"></a>Wynik z istniejącego obiektu wdrożenia

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added paramters. Add softmax to score.
print("Score image url with added paramters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParamters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParamters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Wynik z adresu url punktu końcowego usługi i klucz usługi

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>Wynik końcowy z bezpośrednio żądania http

Poniższy przykładowy kod tworzy żądanie HTTP bezpośrednio w języku Python. Jednak możesz zrobić to w innych językach programowania.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional paramters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>Wynik analizy serializowany z usługi sieci web

Dane wyjściowe z usługą sieci web jest ciągu JSON. Ten ciąg JSON z różnych klas modelu DNN można analizować.


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o Azure Machine Learning pakietu wizji komputerze w następujących artykułach:

+ Dowiedz się, jak [poprawić ten model](how-to-improve-accuracy-for-computer-vision-models.md).

+ Odczyt [pakietu Przegląd i Dowiedz się, jak zainstalować ją](https://aka.ms/aml-packages/vision).

+ Eksploruj [odwołania dokumentacji](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) dla tego pakietu.

+ Dowiedz się więcej o [inne pakiety języka Python dla usługi Azure Machine Learning](reference-python-package-overview.md).
