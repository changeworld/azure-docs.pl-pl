---
title: 'Szybki start: Tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK dla środowiska Node.js'
titlesuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, przekaż obrazy, wytrenuj projekt i wykonaj przewidywanie przy użyciu zestawu Node.js SDK.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 8ec0ae435b9b3818f742531bfaf728f36957ecbf
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67143985"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-nodejs-sdk"></a>Szybki start: Tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision Node.js SDK

Ten artykuł zawiera informacje i przykładowy kod, dzięki którym można łatwiej rozpocząć tworzenie modelu klasyfikacji obrazów za pomocą zestawu Custom Vision SDK i środowiska Node.js. Po jego utworzeniu możesz można dodać tagi, przekazywać obrazy, szkolenie projektu, uzyskać adres URL punktu końcowego opublikowanej prognozowania projektu i używać punktu końcowego programowo testować obrazu. Użyj tego przykładu jako szablonu do utworzenia własnej aplikacji Node.js. Jeśli chcesz przejść przez proces tworzenia i używania modelu klasyfikacji _bez_ kodu, zobacz zamiast tego [wskazówki dotyczące przeglądarki](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstalowana wersja platformy [Node.js 8](https://www.nodejs.org/en/download/) lub nowsza.
- Zainstalowane narzędzie [npm](https://www.npmjs.com/).

## <a name="install-the-custom-vision-sdk"></a>Instalowanie zestawu Custom Vision SDK

Aby zainstalować zestaw Custom Vision Service SDK dla środowiska Node.js, uruchom następujące polecenie w programie PowerShell:

```powershell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [node-get-images](includes/node-get-images.md)]

## <a name="add-the-code"></a>Dodawanie kodu

Utwórz nowy plik o nazwie *sample.js* w preferowanym katalogu projektu.

### <a name="create-the-custom-vision-service-project"></a>Tworzenie projektu Custom Vision Service

Dodaj następujący kod do skryptu, aby utworzyć nowy projekt Custom Vision Service. Wstaw klucze subskrypcji w odpowiednich definicjach.

```javascript
const util = require('util');
const fs = require('fs');
const TrainingApiClient = require("azure-cognitiveservices-customvision-training");
const PredictionApiClient = require("azure-cognitiveservices-customvision-prediction");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://southcentralus.api.cognitive.microsoft.com"

const publishIterationName = "classifyModel";

const trainer = new TrainingApiClient(trainingKey, endPoint);

(async () => {
    console.log("Creating project...");
    const sampleProject = await trainer.createProject("Sample Project")
```

### <a name="create-tags-in-the-project"></a>Tworzenie tagów w projekcie

Aby utworzyć tagi klasyfikacji dla projektu, dodaj następujący kod na końcu pliku *sample.js*:

```javascript
    const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
    const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

### <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

Aby dodać przykładowe obrazy do projektu, po utworzeniu tagów wstaw następujący kod. Ten kod przekazuje każdy obraz z odpowiednim tagiem. Należy wprowadzić ścieżkę do bazowego pliku obrazu, zależnie od tego, gdzie został pobrany projekt z przykładami dotyczącymi zestawu Cognitive Services Node.js SDK.

> [!NOTE]
> Należy zmienić element *sampleDataRoot* na ścieżkę do obrazów, zależnie od tego, gdzie został wcześniej pobrany projekt z przykładami dotyczącymi zestawu Cognitive Services Node.js SDK.

```javascript
    console.log("Adding images...");
    let fileUploadPromises = [];

    const hemlockDir = `${sampleDataRoot}/Hemlock`;
    const hemlockFiles = fs.readdirSync(hemlockDir);
    hemlockFiles.forEach(file => {
        fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${hemlockDir}/${file}`), { tagIds: [hemlockTag.id] }));
    });

    const cherryDir = `${sampleDataRoot}/Japanese Cherry`;
    const japaneseCherryFiles = fs.readdirSync(cherryDir);
    japaneseCherryFiles.forEach(file => {
        fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${cherryDir}/${file}`), { tagIds: [cherryTag.id] }));
    });

    await Promise.all(fileUploadPromises);
```

### <a name="train-the-classifier-and-publish"></a>Klasyfikator uczenie i publikowanie

Ten kod tworzy pierwszą iteracją w projekcie, a następnie publikuje tej iteracji do endpoint prognoz. Nazwa nadana opublikowanych iteracji może służyć do wysyłania żądań do prognozowania. Iteracji nie jest dostępna w punkcie końcowym prognozowania, dopóki zostanie opublikowany.

```javascript
    console.log("Training...");
    let trainingIteration = await trainer.trainProject(sampleProject.id);

    // Wait for training to complete
    console.log("Training started...");
    while (trainingIteration.status == "Training") {
        console.log("Training status: " + trainingIteration.status);
        await setTimeoutPromise(1000, null);
        trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
    }
    console.log("Training status: " + trainingIteration.status);
    
    // Publish the iteration to the end point
    await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>I korzystaj z opublikowanych iteracji w punkcie końcowym prognoz

Aby wysłać obraz do punktu końcowego przewidywania i uzyskać przewidywanie, dodaj na końcu pliku następujący kod:

```javascript
    const predictor = new PredictionApiClient(predictionKey, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_image.jpg`);

    const results = await predictor.classifyImage(sampleProject.id, publishIterationName, testFile);

    // Step 6. Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}%`);
    });
})()
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom plik *sample.js*.

```powershell
node sample.js
```

Dane wyjściowe aplikacji powinny być podobne do poniższego tekstu:

```
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

Możesz następnie sprawdzić, czy obraz testowy (znajdujący się w folderze **<bazowy_adres_url_obrazów>/Images/Test/** ) został odpowiednio otagowany. Możesz też wrócić do [witryny internetowej Custom Vision](https://customvision.ai) i wyświetlić bieżący stan nowo utworzonego projektu.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz wiesz już, jak wykonać każdy krok procesu klasyfikacji obrazów przy użyciu kodu. W tym przykładzie jest wykonywana jedna iteracja szkolenia, ale często trzeba szkolić i testować model wiele razy, aby zwiększyć jego dokładność.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)
