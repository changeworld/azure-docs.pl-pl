---
title: Tworzenie i wdrażanie modeli wykrywania obiektów przy użyciu usługi Azure Machine Learning pakietu dla przetwarzania obrazów.
description: Informacje o sposobie tworzenia, uczenia, testowania i wdrażania modelu przetwarzania obrazów obiektu wykrywania przy użyciu pakietu Learning maszyny platformy Azure do przetwarzania obrazów.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ms.openlocfilehash: 65bcb92b8e97b09ca961aa02cd5891419c2cddb2
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580654"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Tworzenie i wdrażanie modeli wykrywania obiektów przy użyciu usługi Azure Machine Learning

Ten artykuł zawiera informacje o sposobie użycia **Azure Machine Learning pakiet dla przetwarzania obrazów** do szkolenia, Testuj i wdrażaj [szybciej R CNN](https://arxiv.org/abs/1506.01497) model wykrywania obiektów. 

Dużą liczbę problemów w domenie przetwarzania komputera będzie możliwe przy użyciu wykrywania obiektu. Te problemy obejmują budowania modeli, których odnaleźć zmienną liczbę obiekty na obrazie. 

Podczas kompilowania i wdrażania tego modelu, z tym pakietem, możesz przejść przez następujące kroki:
1.  Tworzenie zestawu danych
2.  Definicja modelu sieci Neuronowej (DNN)
3.  Szkolenie modelu
4.  Ocena i wizualizacji
5.  Wdrażanie usługi sieci Web
6.  Usługi testowania obciążenia w sieci Web

W tym przykładzie TensorFlow jest używany jako platforma do uczenia głębokiego, szkolenia jest wykonywana lokalnie na komputerze GPU oparte takie jak [głębokiego uczenia maszyna wirtualna do nauki o danych](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), a wdrożenie używa interfejsu wiersza polecenia platformy Azure ML Operacjonalizacji.

Zapoznaj się z [pakietu dokumentację referencyjną](https://aka.ms/aml-packages/vision) uzyskać szczegółową dokumentację dla każdego modułu i klasy.

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Następujące konta i aplikacji, musisz skonfigurować i zainstalować:
   - Konto Eksperymentowanie w usłudze Azure Machine Learning 
   - Konto Zarządzanie modelami w usłudze Azure Machine Learning
   - Zainstalowana aplikacja Azure Machine Learning Workbench

   Jeśli te trzy nie zostały jeszcze utworzone lub zainstalowany, postępuj zgodnie z [instalacji usługi Azure Machine Learning Przewodnik Szybki Start i aplikacja Workbench](../service/quickstart-installation.md) artykułu. 

1. Musi być zainstalowany pakiet Learning maszyny platformy Azure do przetwarzania obrazów. Dowiedz się, jak [zainstalować ten pakiet w tym miejscu](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Przykładowe dane i notesu

### <a name="get-the-jupyter-notebook"></a>Pobieranie notesu programu Jupyter

Pobieranie notesu do uruchomienia przykładu opisane w tym miejscu samodzielnie.

> [!div class="nextstepaction"]
> [Pobieranie notesu programu Jupyter](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Ładowanie danych przykładowych

Dla tej wersji demonstracyjnej zestaw danych elementów połówce wewnątrz lodówki zostanie podany, składający się z obrazów 30 i 8 klasy (eggBox, joghurt, ketchup, grzyba, musztarda, orange, typu "Squash" i woda). Dla każdego obrazu jpg ma adnotację — plik xml o podobnej nazwie. 

Na poniższej ilustracji przedstawiono strukturę folderu zalecane. 

![Struktura folderów](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>Adnotacji obrazu

Obiekt, który lokalizacji są wymagane do nauczanie i ocena detektora obiektu z adnotacjami. [LabelImg](https://tzutalin.github.io/labelImg) to narzędzie adnotacji "open source", którego można dodawać adnotacje do obrazów. LabelImg zapisuje plik xml dla obrazu w formacie LZO Pascal, który może zostać odczytany przez ten pakiet. 


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.evaluation import DetectionEvaluation
from cvtk.evaluation.evaluation_utils import graph_error_counts
from cvtk.utils import detection_utils

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

from matplotlib import pyplot as plt
# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>Tworzenie zestawu danych

Tworzenie zestawu danych CVTK, który składa się z zestawu obrazów przy użyciu ich odpowiednich otaczający adnotacji pole. W tym przykładzie obrazów lodówki, które znajdują się w "... folder/sample_data/foods/szkolenia"są używane. Obsługiwane są tylko obrazy JPEG.


```python
image_folder = "detection/sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![PNG](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>Zdefiniuj model

W tym przykładzie jest używany model szybciej CNN R. Podczas definiowania modelu można podać różnych parametrów. Znaczenie tych parametrów, jak również parametry używane do trenowania (patrz następny rozdział) można znaleźć w dokumentacji interfejsu API albo CVTK lub na [Tensorflow obiektu wykrywania witryny sieci Web](https://github.com/tensorflow/models/tree/master/research/object_detection). Więcej informacji na temat modelu szybciej CNN języka R znajduje się w temacie [ten link](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details). Ten model opiera się na szybkie R-CNN i więcej informacji na ten temat można znaleźć [tutaj](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details).


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>Uczenie modelu

Model COCO skonfigurowanych pod kątem szybciej R-CNN z ResNet50 służy jako punktu wyjścia do szkolenia. 

To w opracowywaniu detektora, liczbę kroków szkolenia w kodzie ustawiono 350, tak, aby szkolenia przebiega szybciej (około 5 minut przy użyciu procesora GPU). W praktyce należy ustawić co najmniej 10 razy liczbę obrazów na zestaw szkoleniowy.

W tym przykładzie liczba kroków szkolenia wykrywacz ustawiono 350 szybkiego szkolenia. Jednak w praktyce regułą jest równa kroki co najmniej 10 razy liczba obrazów w zestawie szkolenia.

Są dwa parametry klucza do trenowania:
- Liczba kroków do nauczenia modelu, reprezentowany przez num_seps argument. Każdy krok przygotowuje modelu z minibatch rozmiar partii, w jedną.
- Uczenie kursów, które można ustawić, initial_learning_rate

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

Narzędzia TensorBoard można wizualizować postępy szkolenia. Zdarzenia aplikacji TensorBoard znajdują się w folderze określonym przez atrybut train_dir obiekt modelu. Aby wyświetlić narzędzia TensorBoard, wykonaj następujące kroki:
1. Skopiuj wydruku, który rozpoczyna się od "narzędzia tensorboard — logdir" do wiersza polecenia i uruchom go. 
2. Skopiuj adres URL zwracany z wiersza polecenia, do przeglądarki sieci web, aby wyświetlić narzędzia TensorBoard. 

Narzędzia TensorBoard powinien wyglądać jak poniższy zrzut ekranu. Może potrwać kilka minut w przypadku folderu szkolenia wypełniona. Dlatego nie są wyświetlane w aplikacji TensorBoard się poprawnie pierwszego czasu spróbuj powtórzyć kroki 1 i 2.  

![narzędzia tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>Ocenianie modelu

Metoda "Oceń" jest używana do oceny modelu. Ta funkcja wymaga obiektu ObjectDetectionDataset jako dane wejściowe. Ocena zestawu danych mogą być tworzone przy użyciu taką samą funkcję jak konto używane dla zestawu danych szkoleniowych. Obsługiwane metryki jest średnią precyzję, zgodnie z definicją [wyzwanie LZO PASCAL](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf).  


```python
image_folder = "detection/sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

Wyniki oceny można drukowane w postaci czystego.


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

Podobnie można obliczyć dokładności modelu na zestaw szkoleniowy. Pomaga to upewnić się, szkolenie zbieżne doskonałym rozwiązaniem. Dokładność na zestaw szkoleniowy po pomyślnym szkolenia często jest bliskie 100%.

Można również wyświetlać wyniki oceny z narzędzia TensorBoard, z uwzględnieniem wartości mAP i obrazów przy użyciu przewidywane obwiedni. Skopiuj wydruku z następujący kod do okna wiersza polecenia, aby uruchomić klienta narzędzia TensorBoard. Wartość portu 8008 jest tu używany w celu uniknięcia konfliktów z wartością domyślną 6006, którego używano do wyświetlania stanu w szkolenia.


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>Ocena obrazu

Po zakończeniu wykonywania uczonego modelu funkcji "wynik" obiekt modelu można oceniać nowe obrazy. Zwrócone wyniki mogą być wizualizowane za pomocą funkcji "wizualizacja". 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = "./scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![PNG](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>Zapisz model

Uczony model można zapisywane na dysku i ładowane do pamięci, jak pokazano w poniższych przykładach kodu.


```python
save_model_path = "./frozen_model/faster_rcnn.model"
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>Zapisane model do oceniania obciążenia

Aby użyć zapisanych modelu, należy załadować modelu do pamięci przy użyciu funkcji "obciążenie". Wystarczy raz załadować. 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

Po załadowaniu modelu może służyć do oceniania obrazu lub listy obrazów. Dla pojedynczego obrazu słownika jest zwracany za pomocą kluczy, takie jak "detection_boxes", "detection_scores" i "num_detections". Jeśli dane wejściowe są listę obrazów, zwracana jest lista słownika przy użyciu jednego słownika odpowiadający jednego obrazu. 


```python
detections_dict = my_detector_loaded.score(image_path)
```

Wykryte obiekty z oceny powyżej 0,5, takich jak etykiety, wyniki i współrzędne mogą być drukowane.


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

Podobnie jak zwizualizować wyniki przed.


```python
path_save = "./scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![PNG](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

## <a name="operationalization-deploy-and-consume"></a>Operacjonalizacja: Wdrażanie i korzystanie

Operacjonalizacja jest proces publikowania modeli i kodu jako usługi sieci web i użycia tych usług w celu uzyskania wyników biznesowych. 

Gdy model jest uczony, można wdrożyć modelu jako usługi sieci web do użycia przy użyciu [interfejsu wiersza polecenia usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Można wdrożyć swoje modele do komputera lokalnego lub klastra Azure Container Service (ACS). Korzystających z usługi ACS, można ręcznie skalować usługę sieci web lub użyć funkcji skalowania automatycznego.

**Zaloguj się przy użyciu wiersza polecenia platformy Azure**

Za pomocą [Azure](https://azure.microsoft.com/) konto z prawidłową subskrypcją, zaloguj się przy użyciu następującego polecenia interfejsu wiersza polecenia:
<br>`az login`

+ Aby przełączyć się do innej subskrypcji platformy Azure, użyj polecenia:
<br>`az account set --subscription [your subscription name]`

+ Aby wyświetlić bieżące konto zarządzania modelami, użyj polecenia:
  <br>`az ml account modelmanagement show`

**Utwórz i Ustaw środowisko wdrażania klastra**

Należy ustawić środowiska wdrażania na raz. Jeśli nie masz jeszcze, służą do konfigurowania środowiska wdrażania za pomocą [w instrukcjach](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Aby wyświetlić środowiska aktywne wdrożenie, użyj następującego polecenia interfejsu wiersza polecenia:
<br>`az ml env show`
   
Przykładowe polecenie wiersza polecenia platformy Azure, aby utworzyć i ustawić środowisko wdrażania

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Zarządzanie usługami sieci web i wdrożenia

Następujące funkcje interfejsu API może służyć do wdrażania modeli jako usług sieci web, zarządzać tymi usługami sieci web i zarządzanie wdrożeniami.

|Zadanie|Interfejs API|
|----|----|
|Tworzenie obiektu wdrożenia|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Wdrażanie usługi sieci web|`deploy_obj.deploy()`|
|Obraz oceny|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Usuń usługę sieci web|`deploy_obj.delete()`|
|Zbuduj obraz docker bez usługi sieci web|`deploy_obj.build_docker_image()`|
|Lista istniejącego wdrożenia|`AMLDeployment.list_deployment()`|
|Usuń, jeśli istnieje usługa o nazwie wdrożenia|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**Dokumentacja interfejsu API:** zapoznaj się z [pakietu dokumentację referencyjną](https://aka.ms/aml-packages/vision) uzyskać szczegółową dokumentację dla każdego modułu i klasy.

**Dokumentacja interfejsu wiersza polecenia:** bardziej zaawansowane operacje związane z wdrożeniem, można znaleźć [dokumentacja interfejsu wiersza polecenia zarządzania modelami](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Zarządzanie wdrażaniem w witrynie Azure portal**: mogą śledzić i zarządzanie wdrożeniami w [witryny Azure portal](https://ms.portal.azure.com/). W witrynie Azure portal należy znaleźć strony swojego konta Zarządzanie modelami usługi Machine Learning przy użyciu jego nazwy. Następnie przejdź do strony konto zarządzania modelami > Zarządzanie modelami w usłudze > usługi.

```python
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Korzystanie z usługi sieci web

Po utworzeniu usługi sieci Web użytkownik otrzymuje obrazów za pomocą wdrożonej usługi sieci Web. Istnieje kilka opcji:

   - Można bezpośrednio ocena Usługa sieci Web za pomocą obiektu wdrożenia przy użyciu: deploy_obj.score_image(image_path_or_url) 
   - Lub za pomocą adresu url punktu końcowego usługi i klucza usługi, (Brak w przypadku lokalnego wdrożenia): AMLDeployment.score_existing_service_with_image (image_path_or_url service_endpoint_url, service_key = None)
   - Formularz Twoich żądań http bezpośrednio do oceniania punktu końcowego usługi sieci Web (dla użytkowników zaawansowanych).

### <a name="score-with-existing-deployment-object"></a>Ocena z istniejącego obiektu wdrożenia
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Ocena za pomocą adresu url punktu końcowego usługi i klucz usługi
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>Ocena bezpośrednio punkt końcowy z żądania http
Poniżej przedstawiono niektóre przykładowy kod w celu utworzenia żądania http bezpośrednio w języku Python. Możesz zrobić to w innych językach programowania.


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request paramters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>Wyniki analizy serializacji z usługi sieci Web
Wynik z usługi sieci web jest w ciąg json, który może zostać przeanalizowany.


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "./scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi Azure Machine Learning pakietu dla przetwarzania obrazów w następujących artykułach:

+ Odczyt [pakietów — Przegląd i Dowiedz się, jak je zainstalować](https://aka.ms/aml-packages/vision).

+ Zapoznaj się z [dokumentację referencyjną](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) dla tego pakietu.

+ Dowiedz się więcej o [inne pakiety języka Python dla usługi Azure Machine Learning](reference-python-package-overview.md).
