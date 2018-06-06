---
title: Skompiluj i Wdróż model wykrywania obiektów przy użyciu usługi Azure Machine Learning pakietu dla komputera wizji.
description: Informacje o sposobie tworzenia, uczenia, testowania i wdrażania modelu wykrywania obiektu wizji komputera przy użyciu pakietu Learning Azure maszyny do widzenia komputera.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ms.openlocfilehash: 62cc37d8c462d0fc1831de7b50a85738d6e63a17
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34728021"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Tworzenie i wdrażanie modeli wykrywania obiektów z usługi Azure Machine Learning

W tym artykule, Dowiedz się, jak używać **Azure maszyny Learning pakiet dla komputera wizji** do uczenia, testowania i wdrażania [szybciej CNN R](https://arxiv.org/abs/1506.01497) model wykrywania obiektów. 

Wiele problemów w domenie wizji komputera będzie możliwe przy użyciu obiektu wykrywania. Te problemy obejmują budowania modeli, które zmienną liczbę obiektów w obrazie. 

Podczas tworzenia i wdrażania tego modelu z tym pakietem, można przejść przez następujące kroki:
1.  Tworzenie zestawu danych
2.  Definicja modelu sieci Neuronowej głębokość (DNN)
3.  Szkolenie modelu
4.  Ocena i wizualizacji
5.  Wdrażanie usługi sieci Web
6.  Usługa sieci Web testów obciążenia

W tym przykładzie TensorFlow służy jako platforma głębokie uczenia, szkolenia odbywa się lokalnie na komputerze GPU zasilane takich jak [głębokie uczenia wirtualna nauki danych](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), i instalacja używa interfejsu wiersza polecenia Azure ML Operationalization.

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
> [Pobierz notesu Jupyter](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Ładowanie danych przykładowych

Dla tego pokazu spożywczych elementów wewnątrz lodówki zestawu danych jest dostępne, składające się z 30 obrazów i 8 klasy (eggBox, joghurt ketchup, grzyba, musztarda, orange, typu "Squash" i wody). Dla każdego obrazu jpg Brak adnotacji-pliku xml o podobnej nazwie. 

Na poniższej ilustracji przedstawiono struktury folderów zalecane. 

![Struktura folderów](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>Adnotacji obrazu

Adnotacji obiektu lokalizacji są wymagane do nauczanie i Ewaluacja detektora obiektu. [LabelImg](https://tzutalin.github.io/labelImg) jest narzędzia adnotacji typu open source, który może służyć do adnotacji obrazów. LabelImg zapisuje plik xml na obraz w formacie Pascal LZO, który może zostać odczytany przez ten pakiet. 

## <a name="storage-context"></a>Kontekst magazynu
Kontekst magazynu służy do określenia, gdzie są przechowywane różne pliki wyjściowe, takich jak pliki modelu DNN. Aby uzyskać więcej informacji, zobacz [dokumentacji StorageContext](https://docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-ml-py-latest). Zwykle zawartość magazynu nie trzeba ustawić jawnie. Jednak aby uniknąć limit rozmiaru projektu Workbench 25 MB, należy ustawić katalogu danych wyjściowych, aby wskazać lokalizację poza projektem AML ("... /.. /.. /.. / cvtk_output "). Upewnij się, można usunąć katalogu "cvtk_output", gdy nie jest już potrzebne.


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.utils import detection_utils
from matplotlib import pyplot as plt

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Initialize the context object
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)

# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>Tworzenie zestawu danych

Utwórz CVTK zestawu danych, który obejmuje utworzenie zestawu obrazów, przy użyciu ich odpowiednich ograniczający pole adnotacji. W tym przykładzie obrazy lodówce, które zostały opublikowane w "... folder/sample_data/żywności/szkolenia"są używane. Obsługiwane są tylko obrazy JPEG.


```python
image_folder = "../sample_data/foods/train"
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


## <a name="define-a-model"></a>Zdefiniuj modelu

W tym przykładzie jest używany model szybciej CNN R. Różnych parametrów można podać podczas definiowania tego modelu. Znaczenie tych parametrów, a także parametry używane na potrzeby uczenia (zobacz następną sekcję) znajduje się dokumentacja interfejsu API albo CVTK lub na [Tensorflow obiektu wykrywania witryny sieci Web](https://github.com/tensorflow/models/tree/master/research/object_detection). Więcej informacji na temat modelu szybciej R CNN można znaleźć w folderze [to łącze](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details). Ten model jest oparty na szybkie R-CNN i można go znaleźć więcej informacji na temat jej [tutaj](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details).


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>Uczenie modelu

Model uczenia COCO szybciej R-CNN o ResNet50 służy jako punkt początkowy do szkolenia. 

W celu przeszkolenia detektora, liczbę czynności szkolenia w kodzie ustawiono 350, tak, aby szkolenia przebiega szybciej (~ 5 minut z procesora GPU). W praktyce ustaw ją na co najmniej 10 razy liczba obrazów w zestawu szkoleniowego.

W tym przykładzie liczbę czynności szkolenia detektora wynosi 350 szybkiego szkolenia. Jednak w praktyce regułą jest ustalenie kroki do co najmniej 10 razy liczbę obrazów w zestawu szkoleniowego.

Są dwa parametry klucza do trenowania:
- Liczba kroków do nauczenia modelu reprezentowanego przez num_seps argument. Każdy krok przygotowuje modelu o minibatch rozmiar partii w jeden.
- Learning obliczenia, które mogą być ustawiane przez initial_learning_rate

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
    

TensorBoard umożliwia wizualizowanie postępu szkolenia. Zdarzenia TensorBoard znajdują się w folderze określonym przez atrybut train_dir obiektu modelu. Aby wyświetlić TensorBoard, wykonaj następujące kroki:
1. Skopiuj wydruku, który rozpoczyna się od "tensorboard--logdir" do wiersza polecenia i uruchom go. 
2. Skopiuj adres URL zwrócone z wiersza polecenia do przeglądarki sieci web, aby wyświetlić TensorBoard. 

TensorBoard powinien wyglądać Poniższy zrzut ekranu. Trwa kilka chwil folderu szkolenia do wypełnienia. Dlatego TensorBoard nie są wyświetlane się poprawnie pierwszego czasu spróbuj powtórzyć kroki 1 i 2.  

![tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>Ocenianie modelu

Metoda "oceny" służy do ewaluacji modelu. Ta funkcja wymaga obiektu ObjectDetectionDataset jako danych wejściowych. Zestaw danych oceny mogą być tworzone przy użyciu taką samą funkcję jak używane dla zestawu danych szkoleniowych. Obsługiwane Metryka to średnia dokładność, zgodnie z definicją [Challenge LZO PASCAL](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf).  


```python
image_folder = "../sample_data/foods/test"
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
    

Wyniki oceny można wydrukowane w formacie czyste.


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
    

Podobnie można obliczyć dokładności modelu na zestaw szkoleniowy. Dzięki temu, upewnij się, że szkolenie zbieżność dobrym rozwiązaniem. Dokładność na zestaw szkoleniowy po pomyślnym szkolenia często jest bliski 100%.

Wyniki oceny można również wyświetlać z TensorBoard, w tym wartości mAP i obrazy z przewidywane obwiedni. W oknie wiersza polecenia, aby uruchomić klienta TensorBoard, skopiuj wydruku z następującym kodem. W tym miejscu wartość portu 8008 jest używany w celu uniknięcia konfliktów z wartością domyślną 6006, był używany podczas wyświetlania stanu szkolenia.


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>Wynik obrazu

Po zakończeniu wykonywania trenowanego modelu obiektu modelu "wynik" funkcja może służyć do wyniku nowych obrazów. Zwrócone wyniki można wizualizowane za pomocą funkcji "wizualizacji". 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = out_root_path + "/scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![PNG](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>Zapisz model

Trenowanego modelu można zapisywane na dysku i załadowana do pamięci, jak pokazano w poniższych przykładach kodu.


```python
save_model_path = out_root_path + "/frozen_model/faster_rcnn.model" # Please save your model to outside of your AML workbench project folder because of the size limit of AML project
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>Załaduj zapisany model wyników.

Aby użyć zapisanych modelu, należy załadować modelu do pamięci za pomocą funkcji "obciążenie". Wystarczy raz załadować. 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

Po załadowaniu modelu może służyć do wyniku obrazu lub listy obrazów. Dla pojedynczego obrazu słownik jest zwracana z kluczami, takie jak "detection_boxes", "detection_scores" i "num_detections". Jeśli dane wejściowe są listy obrazów, zwracana jest lista słownika z jednego słownika odpowiadający jednego obrazu. 


```python
detections_dict = my_detector_loaded.score(image_path)
```

Wykryte obiekty z wyników powyżej 0,5, łącznie z etykiet, wyniki i współrzędnych można drukować.


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
    

Wizualizacja wyników tak samo jak przed.


```python
path_save = out_root_path + "/scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![PNG](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

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

Po utworzeniu usługi sieci Web można wynik obrazów za pomocą wdrożonej usługi sieci Web. Istnieje kilka opcji:

   - Może bezpośrednio wynik usługi webservice z obiektem wdrożenia z: deploy_obj.score_image(image_path_or_url) 
   - Lub za pomocą adresu url punktu końcowego usługi i klucza usługi, (Brak w przypadku lokalnego wdrożenia): AMLDeployment.score_existing_service_with_image (image_path_or_url service_endpoint_url, service_key = None)
   - Formularz żądania http bezpośrednio do wyniku punkt końcowy usługi sieci Web (dla użytkowników zaawansowanych).

### <a name="score-with-existing-deployment-object"></a>Wynik z istniejącego obiektu wdrożenia
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

### <a name="score-with-service-endpoint-url-and-service-key"></a>Wynik z adresu url punktu końcowego usługi i klucz usługi
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

### <a name="score-endpoint-with-http-request-directly"></a>Wynik końcowy z bezpośrednio żądania http
Poniżej przedstawiono niektóre przykładowy kod do utworzenia żądania http bezpośrednio w języku Python. Możesz zrobić to w innych językach programowania.


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

### <a name="parse-serialized-result-from-webservice"></a>W wyniku analizy serializacji Usługa sieci Web
Wynik z usługi sieci web jest w ciągu json, który może zostać przeanalizowany.


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
path_save = "../../../cvtk_output/scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o Azure Machine Learning pakietu wizji komputerze w następujących artykułach:

+ Odczyt [pakietu Przegląd i Dowiedz się, jak zainstalować ją](https://aka.ms/aml-packages/vision).

+ Eksploruj [odwołania dokumentacji](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) dla tego pakietu.

+ Dowiedz się więcej o [inne pakiety języka Python dla usługi Azure Machine Learning](reference-python-package-overview.md).
