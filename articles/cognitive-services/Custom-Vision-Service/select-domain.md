---
title: Wybieranie domeny dla projektu Custom Vision — Wizja komputerowa
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak wybrać domenę dla projektu w usłudze Niestandardowej wizji.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1569b6081adad4cae0855f9adfb4e14e910bf819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899452"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Wybieranie domeny dla projektu usługi Custom Vision

W bloku ustawień dla projektu usługi Custom Vision możesz wybrać domenę dla swojego projektu. Wybierz domenę, która jest najbliższa scenariuszowi.

## <a name="image-classification"></a>Klasyfikacja obrazów

|Domain|Przeznaczenie|
|---|---|
|__Ogólny__| Zoptymalizowany pod kątem szerokiego zakresu zadań klasyfikacji obrazów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę rodzajową.|
|__Żywności__|Zoptymalizowany pod kątem zdjęć potraw, jak można je zobaczyć w menu restauracji. Jeśli chcesz sklasyfikować zdjęcia poszczególnych owoców lub warzyw, użyj domeny Żywność.|
|__Zabytki__|Zoptymalizowany pod kątem rozpoznawalnych punktów orientacyjnych, zarówno naturalnych, jak i sztucznych. Ta domena działa najlepiej, gdy punkt orientacyjny jest wyraźnie widoczny na zdjęciu. Ta domena działa nawet wtedy, gdy punkt orientacyjny jest lekko zasłonięty przez ludzi przed nim.|
|__Sprzedaż detaliczna__|Zoptymalizowane pod kątem obrazów, które znajdują się w katalogu zakupów lub witrynie zakupów. Jeśli chcesz, aby wysoka precyzja klasyfikacji między sukienki, spodnie i koszule, należy użyć tej domeny.|
|__Domeny kompaktowe__| Zoptymalizowany pod kątem ograniczeń klasyfikacji w czasie rzeczywistym na urządzeniach brzegowych.|

## <a name="object-detection"></a>Wykrywanie obiektów

|Domain|Przeznaczenie|
|---|---|
|__Ogólne__| Zoptymalizowany pod kątem szerokiego zakresu zadań wykrywania obiektów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę rodzajową.|
|__Logo__|Zoptymalizowany pod kątem znajdowania logo marki na obrazach.|
|__Produkty na półkach__|Zoptymalizowany do wykrywania i klasyfikowania produktów na półkach.|
|__Domeny kompaktowe__| Zoptymalizowane pod kątem ograniczeń wykrywania obiektów w czasie rzeczywistym na urządzeniach brzegowych.|

## <a name="compact-domains"></a>Domeny kompaktowe

Modele generowane przez domeny kompaktowe można eksportować do uruchamiania lokalnie. Wydajność modelu zależy od wybranej domeny. W poniższej tabeli informujemy o rozmiarze modelu i czasie wnioskowania \[na\]procesorze Intel Desktop i procesorze graficznym NVidia GPU 1 . 

> [!NOTE]
> Liczby te nie obejmują wstępnego przetwarzania i postprzetworzenia czasu.

|Zadanie|Domain|Rozmiar modelu|Czas wnioskowania procesora|Czas wnioskowania gpu|
|---|---|---|---|---|
|Klasyfikacja|General (compact) (Ogólne (kompaktowe))|5 MB|13 ms|5 ms.|
|Wykrywanie obiektów|General (compact) (Ogólne (kompaktowe))|45 MB|35 ms|5 ms.|
|Wykrywanie obiektów|Ogólne (kompaktowe) [S1]|14 MB|27 ms.|7 ms|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision AI Dev Kit)

Po wybraniu domeny kompaktowej dostępna jest dodatkowa opcja "Możliwości eksportu" umożliwiająca rozróżnienie między "Platformami podstawowymi" a "Zestawem deweloperów Wizjonera AI".

W obszarze _Możliwości eksportu_ dwie opcje to:

- Podstawowe platformy (Tensorflow, CoreML, ONNX itp.)
- Vision AI Dev Kit.

Gdy _wybrano zestaw wizyjnych AI Dev Kit,_ _ogólne,_ _punkt orientacyjny_i _handel detaliczny,_ ale nie dla klasyfikacji obrazów, podczas gdy zarówno _ogólne (kompaktowe),_ jak i _ogólne (kompaktowe) [S1]_ są dostępne do wykrywania obiektów. _Food_

>[!NOTE]
>__Domena ogólna (kompaktowa)__ do wykrywania obiektów wymaga specjalnej logiki postprocessingu. Aby uzyskać szczegółowe informacje, zobacz przykładowy skrypt w wyeksportowanym pakiecie zip. Jeśli potrzebujesz modelu bez logiki postprocessing, użyj __ogólne (kompaktowe) [S1]__.

>[!IMPORTANT]
>Nie ma żadnej gwarancji, że eksportowane modele dają dokładnie taki sam wynik jak interfejs API przewidywania w chmurze. Niewielka różnica w uruchomionej platformie lub implementacji przetwarzania wstępnego może spowodować większą różnicę w wyjściach modelu. Szczegółowe informacje na temat logiki przetwarzania wstępnego można znaleźć w [tym dokumencie](python-tutorial.md).

\[1\] Procesor Intel Xeon E5-2690 i NVIDIA Tesla M60
