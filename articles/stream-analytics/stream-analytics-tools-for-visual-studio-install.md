---
title: Konfigurowanie narzędzi usługi Azure Stream Analytics dla programu Visual Studio
description: W tym artykule opisano wymagania dotyczące instalacji i konfigurowanie narzędzi usługi Azure Stream Analytics dla programu Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354363"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instalowanie narzędzi usługi Azure Stream Analytics dla programu Visual Studio

Visual Studio 2019 i Visual Studio 2017 obsługują narzędzia Azure Data Lake i Stream Analytics Tools. W tym artykule opisano sposób instalowania i odinstalowywania narzędzi.

Aby uzyskać więcej informacji na temat korzystania z narzędzi, zobacz [Szybki start: Tworzenie zadania usługi Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Zainstaluj

Narzędzia obsługują wersje Visual Studio Enterprise (Ultimate/Premium), Professional i Community. Express edition i Visual Studio dla komputerów Mac nie obsługują ich.

Firma Microsoft zaleca Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Instalacja dla programu Visual Studio 2019 i 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Narzędzia Azure Data Lake i Stream Analytics są częścią obciążeń deweloperskich i **przetwarzających dane** **platformy Azure.** Włącz jedno z tych dwóch obciążeń podczas instalacji. Jeśli program Visual Studio jest już zainstalowany, wybierz pozycję **Narzędzia** > **pobierz narzędzia i funkcje,** aby dodać obciążenia.

Pobierz [program Visual Studio 2019 (wersja zapoznawcza 2 lub wyższa) lub Visual Studio 2017 (15.3 lub powyżej)](https://www.visualstudio.com/) i postępuj zgodnie z instrukcjami do zainstalowania.

Wybierz obciążenie **magazynowaniem i przetwarzaniem danych,** jak pokazano na rysunku:

![Wybrano obciążenie związane z przechowywaniem i przetwarzaniem danych](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Wybierz obciążenie **deweloperów platformy Azure,** jak pokazano na rysunku:

![Wybrano obciążenie deweloperów platformy Azure](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

Po dodaniu obciążenia zaktualizuj narzędzia. Ta procedura odnosi się do programu Visual Studio 2019:

1. Wybierz **opcję Rozszerzenia Zarządzaj** > **rozszerzeniami**.

1. W **obszarze Zarządzanie rozszerzeniami**wybierz pozycję **Aktualizacje** i wybierz pozycję **Azure Data Lake i Stream Analytics Tools**.

1. Wybierz **pozycję Aktualizuj,** aby zainstalować najnowsze rozszerzenie.

![Rozszerzenia i aktualizacje programu Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Instalowanie dla programu Visual Studio 2015 i 2013<a name="visual-studio-2015-2013"></a>

Narzędzia obsługują wersje Visual Studio Enterprise (Ultimate/Premium), Professional i Community. Wersja express nie obsługuje ich.

* Zainstaluj program Visual Studio 2015 lub Visual Studio 2013 Update 4.
* Zainstaluj zestaw Microsoft Azure SDK dla platformy .NET w wersji 2.7.1 lub nowszej przy użyciu [instalatora platformy sieci Web](https://www.microsoft.com/web/downloads/platform.aspx).
* Zainstaluj [narzędzie Microsoft Azure Data Lake i Stream Analytics Tools dla programu Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Aktualizacji<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

W przypadku programu Visual Studio 2019 i programu Visual Studio 2017 jako powiadomienie programu Visual Studio jest wyświetlane jako powiadomienie o nowej wersji.

W przypadku programów Visual Studio 2015 i Visual Studio 2013 narzędzia automatycznie sprawdzają dostępność nowych wersji. Postępuj zgodnie z instrukcjami, aby zainstalować najnowszą wersję.

## <a name="uninstall"></a>Dezinstalacja

Można odinstalować usługi Azure Data Lake i narzędzia do analizy strumienia. W przypadku programu Visual Studio 2019 lub Visual Studio 2017 wybierz pozycję **Narzędzia** > **Pobierz narzędzia i funkcje**. W **obszarze Modyfikowanie** **odznaczanie narzędzia Azure Data Lake i Stream Analytics Tools**. Pojawia się w ramach **obciążenia magazynu danych i przetwarzania** lub obciążenia **dewelopera platformy Azure.**

Aby odinstalować program Visual Studio 2015 lub Visual Studio 2013, przejdź do programu**Programy i funkcje** **Panelu sterowania** > . Odinstaluj **narzędzie Microsoft Azure Data Lake i Stream Analytics Tools dla programu Visual Studio**.
