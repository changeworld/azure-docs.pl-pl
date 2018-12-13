---
title: Dostosowywanie modelu osoby w Video Indexer — platformy Azure
titlesuffix: Azure Media Services
description: Ten artykuł zawiera omówienie modelu osoby w Video Indexer i sposobami ich dostosowywania.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285025"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Dostosowywanie modelu osoby w indeksatora wideo


Usługa Video Indexer obsługuje wykrywanie twarzy oraz rozpoznawanie osobistości zawartości wideo. Funkcja rozpoznawania osobistości obejmuje około 1 000 000 twarze na podstawie źródła najczęściej żądanych danych, takich jak ZAUFANI Wikipedia i górnym specjaliści LinkedIn. Wykryto twarzy, które nie są rozpoznawane przez funkcję rozpoznawania osobistości; jednak są pozostawione bez nazwy. Po przekazywania filmu wideo do indeksatora wideo i uzyskuj wyniki z powrotem, może Przejdź wstecz i nazwij twarzy, które nie zostały rozpoznane. Gdy etykiety twarz o nazwie twarzy i nazwy poproś o dodanie Cię do swojego konta osoby modelu. Indeksator wideo następnie rozpozna twarzach przyszłych filmów wideo i ostatnich filmów wideo.

Video Indexer witryny sieci Web lub interfejsu API umożliwia edytowanie twarzy, które zostały wykryte w filmie wideo na swoim koncie, zgodnie z opisem w następujących tematach:

- [Dostosuj model osoby za pomocą interfejsów API](customize-person-model-with-api.md)
- [Dostosuj model osoby, witryny sieci Web](customize-person-model-with-website.md)
