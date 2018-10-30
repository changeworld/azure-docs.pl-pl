---
title: 'Samouczek: tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK dla języka Java'
titlesuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, prześlij obrazy, wyszkol projekt i wykonaj przewidywanie przy użyciu domyślnego punktu końcowego.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: e302fc580d9c83d269f0deedd051a3ea23bd274e
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957218"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Samouczek: tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK dla języka Java

Dowiedz się, jak utworzyć projekt klasyfikacji obrazów za pomocą usługi Custom Vision Service przy użyciu języka Java. Po jego utworzeniu możesz dodać tagi, przesłać obrazy, wyszkolić projekt, uzyskać adres URL domyślnego punktu końcowego przewidywania projektu i użyć go do programowego przetestowania obrazu. Ta przykładowa aplikacja open source może posłużyć jako szablon do utworzenia własnej aplikacji przy użyciu interfejsu API Custom Vision.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstalowany zestaw JDK 7 lub 8.
- Zainstalowane narzędzia Maven.

## <a name="get-the-training-and-prediction-keys"></a>Uzyskanie kluczy szkoleniowego i predykcyjnego

Aby uzyskać klucze używane w tym przykładzie, przejdź na [stronę Custom Vision](https://customvision.ai) i wybierz __ikonę koła zębatego__ w prawym górnym rogu. W sekcji __Accounts__ (Konta) skopiuj wartości z pól __Training Key__ (Klucz szkoleniowy) i __Prediction Key__ (Klucz predykcyjny).

![Obraz interfejsu użytkownika do uzyskiwania kluczy](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Zainstalowanie pakietu SDK Custom Vision Service

Pakiet SDK Custom Vision można zainstalować z centralnego repozytorium Maven:
* [Pakiet SDK do szkolenia](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Pakiet SDK do przewidywania](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>Zrozumienie kodu

Cały projekt, razem z obrazami, jest dostępny w [repozytorium przykładów Custom Vision Azure dla języka Java ](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). 

Otwórz projekt `Vision/CustomVision` w swoim ulubionym zintegrowanym środowisku projektowym Java. 

W tej aplikacji do utworzenia nowego projektu o nazwie __Przykładowy projekt Java__ zostanie użyty pobrany wcześniej klucz szkoleniowy. Następnie zostaną przesłane obrazy do szkolenia i testowania klasyfikatora. Klasyfikator określa, czy drzewo jest __Choiną__ czy __Wiśnią japońską__.

Poniższe fragmenty kodu implementują podstawową funkcjonalność tej przykładowej aplikacji:

## <a name="create-a-custom-vision-service-project"></a>Utworzenie projektu Custom Vision Service

> [!IMPORTANT]
> Ustaw `trainingApiKey` na wartość uzyskanego wcześniej klucza szkoleniowego.

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>Dodanie tagów do projektu

```java
// create hemlock tag
Tag hemlockTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Hemlock")
    .execute();

// create cherry tag
Tag cherryTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Japanese Cherry")
    .execute();
```

## <a name="upload-images-to-the-project"></a>Przesłanie obrazów do projektu

Przykład jest skonfigurowany w taki sposób, aby zawierał obrazy w pakiecie końcowym. Obrazy są odczytywane z sekcji zasobów pliku jar i przesyłane do usługi.

```java
System.out.println("Adding images...");
for (int i = 1; i <= 10; i++) {
    String fileName = "hemlock_" + i + ".jpg";
    byte[] contents = GetImage("/Hemlock", fileName);
    AddImageToProject(trainer, project, fileName, contents, hemlockTag.id(), null);
}

for (int i = 1; i <= 10; i++) {
    String fileName = "japanese_cherry_" + i + ".jpg";
    byte[] contents = GetImage("/Japanese Cherry", fileName);
    AddImageToProject(trainer, project, fileName, contents, cherryTag.id(), null);
}
```

W poprzednim fragmencie kodu używane były dwie funkcje pomocnicze pobierające obrazy jako strumienie zasobów i przesyłające je do usługi.

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag)
{
    System.out.println("Adding image: " + fileName);

    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    batch = batch.withTagIds(Collections.singletonList(tag));

    trainer.createImagesFromFiles(project.id(), batch);
}

private static byte[] GetImage(String folder, String fileName)
{
    try {
        return ByteStreams.toByteArray(CustomVisionSamples.class.getResourceAsStream(folder + "/" + fileName));
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    return null;
}
```

## <a name="train-the-project"></a>Szkolenie projektu

Utworzenie pierwszej iteracji w projekcie i oznaczenie jej jako domyślnej. 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());

while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(1000);
    iteration = trainer.getIteration(project.id(), iteration.id());
}

System.out.println("Training Status: "+ iteration.status());
trainer.updateIteration(project.id(), iteration.id(), iteration.withIsDefault(true));
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Uzyskanie domyślnego punktu końcowego do przewidywania i użycie go

> [!IMPORTANT]
> Ustaw `predictionApiKey` na wartość uzyskanego wcześniej klucza do przewidywania.

```java
final String predictionApiKey = "insert your prediction key here";
PredictionEndpoint predictClient = CustomVisionPredictionManager.authenticate(predictionApiKey);

// Use below for predictions from a url
// String url = "some url";
// ImagePrediction results = predictor.predictions().predictImage()
//                         .withProjectId(project.id())
//                         .withUrl(url)
//                         .execute();

// load test image
byte[] testImage = GetImage("/Test", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%%", prediction.tagName(), prediction.probability() * 100.0f));
}
```

## <a name="run-the-example"></a>Uruchomienie przykładu

Kompilacja i uruchomienie rozwiązania przy użyciu narzędzia Maven:

```
mvn compile exec:java
```

Dane wyjściowe aplikacji będą podobne do poniższego przykładu:

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```