---
title: 'Szybki start: konfiguracja platformy Java (Windows, Linux, macOS) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik służy do konfigurowania platformy do korzystania z oprogramowania Java (Windows, Linux, macOS) za pomocą zestawu SDK usługi mowy.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 7147f0d13c88c1d2e17e81a360a5aee55ee760ed
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78926026"
---
W tym przewodniku pokazano, jak zainstalować [pakiet SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) dla 64-bitowego środowiska Java 8 JRE. Jeśli chcesz, aby nazwa pakietu zaczęła się samodzielnie, java SDK nie jest dostępny w centralnym repozytorium Maven. Niezależnie od tego, czy `pom.xml` używasz gradle, czy pliku zależności, musisz dodać `https://csspeechstorage.blob.core.windows.net/maven/` niestandardowe repozytorium wskazujące (zobacz poniżej nazwę pakietu).

> [!NOTE]
> Aby uzyskać informacje dotyczące zestawu Speech Devices SDK oraz urządzenia Roobo, zobacz [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- Pakiet Java Speech SDK jest dostępny dla tych systemów operacyjnych:
  - Windows: tylko 64-bitowy
  - Mac: macOS X w wersji 10.13 lub nowszej
  - Linux: 64-bitowy tylko na Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8

## <a name="prerequisites"></a>Wymagania wstępne

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [zestaw JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (wymaga java już zainstalowany)
- Obsługiwane platformy Linux będą wymagały`libssl` zainstalowania niektórych bibliotek `libasound2` (do obsługi warstwy bezpiecznych gniazd i do obsługi dźwięku). Zapoznaj się z poniższą dystrybucją, aby uzyskać polecenia potrzebne do zainstalowania poprawnych wersji tych bibliotek.

  - Na Ubuntu uruchom następujące polecenia, aby zainstalować wymagane pakiety:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - W Debianie 9 uruchom następujące polecenia, aby zainstalować wymagane pakiety:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - W programie RHEL/CentOS 8 uruchom następujące polecenia, aby zainstalować wymagane pakiety:

        ```sh
        sudo yum update
        sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
        ```

> [!NOTE]
> Na RHEL/CentOS 8 postępuj zgodnie z instrukcjami [dotyczącymi konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- W systemie Windows potrzebujesz [programu Microsoft Visual C++ redystrybucyjny dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) dla swojej platformy. Należy pamiętać, że zainstalowanie tego po raz pierwszy może wymagać ponownego uruchomienia systemu Windows przed kontynuowaniem tego przewodnika.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Tworzenie projektu eclipse i instalowanie pakietu SDK mowy

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]
