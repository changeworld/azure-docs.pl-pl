---
title: Importowanie danych z pliku do usługi Azure Machine Learning Studio | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przekazać pliku danych szkoleniowych z dysku twardego do usługi Azure Machine Learning Studio. Spowoduje to utworzenie modułu zestawu danych w obszarze roboczym.
keywords: Importowanie danych, formatu danych, typów danych, źródeł danych, dane szkoleniowe
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 70e159e7b7b2b5934cc584e9eb2e511d2b0ce0db
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346214"
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-machine-learning-studio"></a>Importowanie danych szkoleniowych z pliku na dysku twardym w usłudze Machine Learning Studio

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
