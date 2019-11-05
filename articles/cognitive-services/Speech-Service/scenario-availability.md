---
title: Dostępność scenariusza — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dokumentacja dotycząca regionów usługi mowy.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: 6ec31df7cef8391728eae7845f64f55bb1c6466a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491339"
---
# <a name="scenario-availability"></a>Dostępność scenariusza

Zestaw SDK usługi mowy zawiera wiele scenariuszy obejmujących różne języki programowania i środowiska.  Nie wszystkie scenariusze są obecnie dostępne we wszystkich językach programowania lub w wszystkich środowiskach.  Poniżej przedstawiono dostępność każdego scenariusza.

- **Rozpoznawanie mowy (SR), lista fraz, intencje, tłumaczenia i kontenery lokalne**
  - Wszystkie języki programowania/środowiska, w których znajduje się łącze do strzałki <img src="media/index/link.jpg" height="15" width="15"></img> w [tabeli szybkiego](https://aka.ms/csspeech)startu.
- **Zamiana tekstu na mowę (TTS)**
  - C++/Windows & Linux
  - C#/Windows & platformy UWP & Unity
  - Java (JRE i Android)
  - Python
  - Swift
  - Obiektowy C
  - Interfejs API REST usługi TTS może być używany w każdej innej sytuacji.
- **Wykrywania trendów słowa kluczowego (KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
  - Funkcja wykrywania trendów słowa kluczowego (KWS) może działać z dowolnym typem mikrofonu, oficjalną obsługą KWS, ale jest obecnie ograniczona do tablic mikrofonów znajdujących się na sprzęcie platformy Azure urządzenia Kinect DK lub zestawu Speech Devices SDK
- **Asystenci głosu**
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
  - Java/Linux, Android i iOS
