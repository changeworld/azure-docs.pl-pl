---
title: Importuj dane z pliku
titleSuffix: Azure Machine Learning Studio
description: Dowiedz się, jak można przekazać pliku danych szkoleniowych z dysku twardego do usługi Azure Machine Learning Studio. Spowoduje to utworzenie modułu zestawu danych w obszarze roboczym.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: fe09eabe399af2d3ddc776843f39742774e6ea3c
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495891"
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-azure-machine-learning-studio"></a>Importowanie danych szkoleniowych z pliku na dysku twardym do usługi Azure Machine Learning Studio

Dowiedz się, jak przekazać plik danych z dysku twardego do użycia jako dane szkoleniowe w usłudze Azure Machine Learning Studio. Przez zaimportowanie pliku danych, masz modułu zestawu danych, gotowe do użycia w obszarze roboczym.

## <a name="steps-to-import-data-from-a-local-file"></a>Kroki, aby zaimportować dane z pliku lokalnego
Aby zaimportować dane z lokalnego dysku twardego, wykonaj następujące czynności:

1. Kliknij przycisk **+ nowy** u dołu okna Machine Learning Studio.
2. Wybierz **DATASET** i **z pliku lokalnego**.
3. W **przekazać nowy zestaw danych** okno dialogowe, przejdź do pliku, który chcesz przekazać
4. Wprowadź nazwę, identyfikator typu danych i opcjonalnie wprowadź opis. Opis jest zalecane — umożliwia zarejestrowanie dowolnej właściwości o dane, które należy pamiętać, korzystając z danych w przyszłości.
5. Pole wyboru **to nowa wersja istniejący zestaw danych** pozwala zaktualizować istniejący zestaw danych przy użyciu nowych danych. Kliknij to pole wyboru, a następnie wprowadź nazwę istniejący zestaw danych.

![Przekaż nowy zestaw danych](./media/import-data-from-local-file/upload-dataset.png)

Podczas przekazywania zobaczysz komunikat, że plik jest przekazywany. Przekaż czas zależy od rozmiaru danych i prędkości połączenia z usługą. Jeśli wiesz, że plik będzie zająć dużo czasu, można wykonywać inne czynności, w usłudze Machine Learning Studio, podczas oczekiwania. Jednak zamknięcie przeglądarki powoduje, że przekazywanie danych nie powiedzie się.

## <a name="dataset-module-is-ready-for-use"></a>Moduł zestaw danych jest gotowa do użycia
Po przekazaniu danych są przechowywane w module zestawu danych i jest dostępny dla każdego doświadczenia w obszarze roboczym.

Podczas edycji eksperymentu, możesz znaleźć zestawy danych zostały utworzone w **Moje zestawy danych** w obszarze **zapisane zestawów danych** liście palety modułów. Możesz przeciągać i upuszczać zestawu danych do obszaru roboczego eksperymentu, gdy użytkownik chce używać zestawu danych do dalszej analizy i uczenia maszynowego.
