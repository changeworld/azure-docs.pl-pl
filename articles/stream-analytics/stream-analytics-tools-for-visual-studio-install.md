---
title: Instrukcje instalacji usługi analiza strumienia Azure tools dla programu Visual Studio | Dokumentacja firmy Microsoft
description: Instrukcje instalacji usługi analiza strumienia Azure tools dla programu Visual Studio
keywords: Visual studio
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: fe1f2158439b2c04e8232eb6fe3c9faf79641ffe
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Instrukcje instalacji usługi Stream Analytics tools dla programu Visual Studio
Azure Stream Analytics tools są teraz obsługiwane w Visual Studio 2017 r. 2015 i 2013. W tym dokumencie wprowadzeniu sposobu instalowania i odinstalowywania narzędzia.

Dowiedz się, jak używać [Stream Analytics tools dla Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Instalowanie
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Pobierz [programu Visual Studio 2017 (15.3 lub nowszej)](https://www.visualstudio.com/). Enterprise (Ultimate/Premium), Professional i społeczności wersje są obsługiwane. Express edition nie jest obsługiwane. 
* Narzędzia do analizy strumienia są częścią **Azure programowanie** i **magazynu danych i przetwarzania** obciążenia w programie Visual Studio 2017 r. Włącz albo jeden z tych dwóch obciążeń w ramach instalacji programu Visual Studio.

Włącz **magazynu danych i przetwarzania** obciążenia, jak pokazano:

![Obciążenie przechowywania i przetwarzania danych](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

Włącz **Azure programowanie** obciążenia, jak pokazano:

![Programowanie Azure obciążenia](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Zainstaluj program Visual Studio 2015 lub Visual Studio 2013 Update 4. Enterprise (Ultimate/Premium), Professional i społeczności wersje są obsługiwane. Express edition nie jest obsługiwane. 
* Zainstaluj zestaw Microsoft Azure SDK dla platformy .NET w wersji 2.7.1 lub nowszej przy użyciu [Instalatora platformy sieci Web](http://www.microsoft.com/web/downloads/platform.aspx).
* Zainstaluj [analiza strumienia Azure tools dla Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Aktualizacja

### <a name="visual-studio-2017"></a>Visual Studio 2017
Monit wersji zostaną wyświetlone powiadomienia programu Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Analiza strumienia narzędzia Visual Studio sprawdzał nowe wersje. Postępuj zgodnie z instrukcjami w wyskakującym oknie, aby zainstalować najnowszą wersję. 


## <a name="uninstall"></a>Dezinstalacja

### <a name="visual-studio-2017"></a>Visual Studio 2017
Kliknij dwukrotnie pozycję Instalator programu Visual Studio i wybierz **Modyfikuj**. Wyczyść **usługi Azure Data Lake i narzędzia do analizy strumienia** pole wyboru przy użyciu dowolnego **magazynu danych i przetwarzania** obciążenia lub **Azure programowanie** obciążenia.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Przejdź do panelu sterowania, a następnie odinstaluj **Microsoft Azure Data Lake i Stream Analytics tools dla Visual Studio**.





