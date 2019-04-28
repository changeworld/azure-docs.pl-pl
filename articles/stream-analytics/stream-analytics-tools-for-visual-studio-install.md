---
title: Konfigurowanie usługi Azure Stream Analytics tools for Visual Studio
description: W tym artykule opisano wymagania dotyczące instalacji i konfiguracji narzędzia Azure Stream Analytics dla programu Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 673f4935dce28b30c10e6abf4c7d22e00c1dd73a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60762239"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instalowanie narzędzi Azure Stream Analytics dla programu Visual Studio
Narzędzia usługi Azure Stream Analytics obsługują programy Visual Studio 2017, 2015 i 2013. W tym artykule opisano sposób instalowania i odinstalowywania narzędzia.

Aby uzyskać więcej informacji na temat korzystania z narzędzi, zobacz [usługi Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Instalowanie
### <a name="recommended-visual-studio-2019-and-2017"></a>Zalecane: Visual Studio 2019 and 2017
* Pobierz [Visual Studio 2019 r (w wersji Preview 2 lub nowszego) i Visual Studio 2017 (15.3 lub nowszej)](https://www.visualstudio.com/). Obsługiwane są wersje Enterprise (Ultimate/Premium), Professional i Community. Wersja Express nie jest obsługiwana. Program Visual Studio 2017 dla komputerów Mac nie jest obsługiwane. 
* Narzędzia Stream Analytics tools są częścią **programowanie na platformie Azure** i **przechowywanie i przetwarzanie danych** obciążenia w programie Visual Studio 2017. Włącz jedno z tych dwóch obciążeń w ramach instalacji programu Visual Studio.

Włącz **przechowywanie i przetwarzanie danych** obciążenia, jak pokazano:

![Wybrano obciążeniu przechowywania i przetwarzania danych](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Włącz **programowanie na platformie Azure** obciążenia, jak pokazano:

![Wybrano obciążenie programistyczne platformy Azure](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)

* W menu Narzędzia wybierz **rozszerzenia i aktualizacje**. Znajdź usługę Azure Data Lake i Stream Analytics tools zainstalowanych rozszerzeń i kliknij przycisk **aktualizacji** można zainstalować najnowsze rozszerzenia. 

![Rozszerzenia programu Visual Studio i aktualizacje](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-extensions-updates.png)

### <a name="visual-studio-2015-2013"></a>Visual Studio 2015, 2013
* Zainstaluj program Visual Studio 2015 lub Visual Studio 2013 Update 4. Obsługiwane są wersje Enterprise (Ultimate/Premium), Professional i Community. Wersja Express nie jest obsługiwana. 
* Instalowanie zestawu Microsoft Azure SDK dla platformy .NET w wersji 2.7.1 lub nowszej za pomocą [Instalatora platformy sieci Web](https://www.microsoft.com/web/downloads/platform.aspx).
* Zainstaluj [usługi Azure Stream Analytics tools for Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Aktualizacja

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 and 2017
Nowe przypomnienie wersji wyświetlane powiadomienia programu Visual Studio.

![Visual Studio, nowe przypomnienie wersji](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-new-version-reminder-vs-tools.png)

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 i 2013
Zainstalowane narzędzia Stream Analytics dla programu Visual Studio sprawdzaj automatycznie nowych wersji. Postępuj zgodnie z instrukcjami w oknie podręcznym, aby zainstalować najnowszą wersję. 


## <a name="uninstall"></a>Dezinstalacja

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 and 2017
Kliknij dwukrotnie Instalatora programu Visual Studio i wybierz **Modyfikuj**. Wyczyść **Azure Data Lake i Stream Analytics Tools** pola wyboru z poziomu **przechowywanie i przetwarzanie danych** obciążenia lub **programowanie na platformie Azure** obciążenia.

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 i 2013
Przejdź do panelu sterowania, a następnie odinstaluj **Microsoft Azure Data Lake i Stream Analytics tools for Visual Studio**.





