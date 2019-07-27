---
title: Dostępność scenariusza — usługa mowy
titleSuffix: Azure Cognitive Services
description: Odwołanie dla regionów usługi mowy.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: 94fd415909e86a43916ee2f510732a6a6d9c5ed3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552947"
---
# <a name="scenario-availability"></a>Dostępność scenariusza

Zestaw SDK usługi mowy zawiera wiele scenariuszy obejmujących różne języki programowania i środowiska.  Nie wszystkie scenariusze są obecnie dostępne we wszystkich językach programowania lub w wszystkich środowiskach.  Poniżej przedstawiono dostępność każdego scenariusza.

- **Rozpoznawanie mowy (SR), lista fraz, intencje, tłumaczenia i kontenery lokalne**
  - Wszystkie języki programowania/środowiska, w których znajduje się łącze do strzałki <img src="media/index/link.jpg" height="15" width="15"></img> w [tabeli szybkiego](https://aka.ms/csspeech)startu.
- **Zamiana tekstu na mowę (TTS)**
  - C++/Windows & Linux
  - C#/Windows & platformy UWP & Unity
  - Interfejs API REST usługi TTS może być używany w każdej innej sytuacji.
- **Wake Word (słowo kluczowe Spotter/KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
  - Funkcja Wake Word (Spottere Keywords/KWS) może działać z dowolnym typem mikrofonu, oficjalną obsługą KWS, ale jest obecnie ograniczona do tablic mikrofonu znajdujących się na sprzęcie platformy Azure urządzenia Kinect DK lub w zestawie SDK urządzeń mowy
- **Głos — pierwszy asystent wirtualny**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (Speech Devices SDK)
- **Transkrypcja konwersacji**
  - C++/Windows & Linux
  - C#(Framework & .NET Core)/Windows & platformy UWP & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
- **Transkrypcja centrum wywołania**
  - Interfejs API REST i może być używany w dowolnej sytuacji
- **Koder-dekoder skompresowanego sygnału audio**
  - C++/Linux
  - C#/Linux
  - Java/Linux & Android
