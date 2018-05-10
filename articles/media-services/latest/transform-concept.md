---
title: Przekształca i zadania w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Korzystając z usługi Media Services, musisz utworzyć transformacji dla opisu zasady lub wymagania dotyczące przetwarzania plików wideo. Ten artykuł zawiera omówienie przekształcenie jest i jak z niego korzystać.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d256d87548d54951cb77beffb88bba26a1a3de49
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="transforms-and-jobs"></a>Transformacje i zadania

## <a name="overview"></a>Przegląd 

Najnowszą wersję programu Azure Media Services REST API (v3) wprowadzono nowy zasób szablonem przepływu pracy do kodowania i/lub analizowania wideo o nazwie **przekształcenie**. **Przekształca** może służyć do konfiguracji typowych zadań związanych z kodowania lub analying wideo. Każdy **przekształcenie** opisuje proste przepływu pracy zadań przetwarzania plików wideo lub audio. 

**Przekształcenie** obiekt jest przepisu i **zadania** rzeczywistego żądania do usługi Azure Media Services do zastosowania, który jest **przekształcenie** do danej wejściowej zawartości wideo lub audio. **Zadania** określa informacje, takie jak lokalizacja wejściowego pliku wideo oraz lokalizację danych wyjściowych. Można określić lokalizację wideo użycia: adresy URL HTTP (s), adresy URL SAS lub ścieżka do plików znajdujących się lokalnie lub w magazynie obiektów Blob Azure. Można mieć maksymalnie 100 przekształcenia na koncie usługi Azure Media Services i przesyłania zadań w tych transformacji. Można następnie subskrybować zdarzenia, takie jak zmiany stanu zadania, za pomocą powiadomień, które łączyć bezpośrednio z systemu powiadomień Azure zdarzeń siatki. 

Ponieważ ten interfejs API jest wymuszany przez Menedżera zasobów Azure, można użyć szablonów usługi Resource Manager do tworzenia i wdrażania przekształcenia na koncie usługi Media Services. Kontrola dostępu oparta na rolach można również ustawić na poziomie zasobów, w tym interfejsie API, co umożliwia zablokowanie dostępu do określonych zasobów, takich jak transformacji.

## <a name="typical-workflow-and-example"></a>Typowy przepływ pracy i przykład

1. Utwórz transformacji 
2. Przesyłanie zadań w tej transformacji. 
3. Lista transformacji 
4. Usuń transformacji, jeśli nie zamierzasz używać w przyszłości ten "przepisu". 

Załóżmy, że chcesz wyodrębnić pierwszej ramki pliki wideo jako obraz miniatury — przedstawiono kroki, które można wykonać: 

1. Zdefiniuj reguły przetwarzania — na przykład, należy użyć pierwszej ramki wideo jako miniatury 
2. Następnie dla każdego wideo, które jako dane wejściowe do usługi, można sprawdzić usługi: 
    1. Gdzie można znaleźć wideo, a 
    2. Miejsca do zapisywania danych wyjściowych — na przykład obraz miniatury 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przesyłanie strumieniowe plików wideo](stream-files-dotnet-quickstart.md)
