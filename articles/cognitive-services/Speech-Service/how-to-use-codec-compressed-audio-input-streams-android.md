---
title: Skompresowane audio kodera-dekoder strumienia przy użyciu zestawu Speech SDK w systemie Android
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przesyłać strumieniowo skompresowane audio do usługi mowy przy użyciu zestawu Speech SDK w systemie Android.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 1539ca9aa18892f617f278e67c8b6141f5f6d880
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331131"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>Instrukcje: korzystanie z kodera-dekoder kompresji audio przy użyciu zestawu Speech SDK w systemie Android

Interfejs API **strumienia danych wejściowych audio** zestawu Speech SDK umożliwia przesyłanie strumieniowo skompresowanego dźwięku do usługi mowy przy użyciu PullStream lub PushStream.

> [!IMPORTANT]
> Przesyłane strumieniowo skompresowane audio wejściowe jest obecnie obsługiwane dla [ C++systemów, C#i Java w systemie Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8)](how-to-use-codec-compressed-audio-input-streams.md). Jest ona również obsługiwana w przypadku języka Java w systemach Android i [C na platformie iOS](how-to-use-codec-compressed-audio-input-streams-ios.md) .
> Wymagany jest pakiet Speech SDK w wersji 1.7.0 lub nowszej.

W przypadku WAV/PCM zapoznaj się z dokumentacją mowy linii głównej. Na zewnątrz WAV/PCM obsługiwane są następujące skompresowane formaty danych wejściowych kodera-dekoder:

- MP3
- OPUS/OGG
- FLAC
- ALAW w kontenerze WAV
- MULAW w kontenerze WAV

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>Wymagania wstępne dotyczące korzystania z kodera skompresowanych danych wejściowych audio w systemie Android

Koder-dekoder skompresowany audio jest implementowany przy użyciu [GStreamer](https://gstreamer.freedesktop.org). Ze względów licencjonowania GStreamer pliki binarne nie są kompilowane z zestawem SDK. Musisz użyć wstępnie skompilowanych plików binarnych dla systemu Android. Aby pobrać wstępnie skompilowane biblioteki, zobacz [Instalowanie aplikacji dla systemu Android](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c).

`libgstreamer_android.so` jest wymagana. Upewnij się, że wtyczki GStreamer są połączone w `libgstreamer_android.so`.

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

Poniżej przedstawiono przykładowy `Android.mk` i plik `Application.mk`. Wykonaj następujące kroki, aby utworzyć obiekt współużytkowany GStreamer: `libgstreamer_android.so`.

```make
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
LOCAL_LDLIBS := -llog
include $(BUILD_SHARED_LIBRARY)


ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
GSTREAMER_EXTRA_LIBS      := -liconv
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```make
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

`libgstreamer_android.so` można skompilować przy użyciu następującego polecenia w systemie Ubuntu 16,04 lub 18,04. Następujące wiersze polecenia zostały przetestowane tylko dla [GStreamer Android Version 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) z [systemem Android NDK b16b.](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip)

```sh
# assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

Gdy obiekt współużytkowany (libgstreamer_android. so) jest skompilowany, Deweloper aplikacji musi umieścić obiekt współużytkowany w aplikacji systemu Android, aby można go było załadować przy użyciu zestawu Speech SDK.

## <a name="example-code-using-codec-compressed-audio-input"></a>Przykładowy kod przy użyciu kodera skompresowanego sygnału audio

Aby przesłać strumieniowo w skompresowanym formacie audio do usługi mowy, Utwórz `PullAudioInputStream` lub `PushAudioInputStream`. Następnie utwórz `AudioConfig` z wystąpienia klasy Stream, określając format kompresji strumienia.

Załóżmy, że masz klasę strumienia wejściowego o nazwie `myPullStream` i używają OPUS/OGG. Twój kod może wyglądać następująco:

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = AudioStreamFormat.getCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig = AudioConfig.fromStreamInput(myPullStream, audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get()

String text = result.getText();
```

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Zobacz jak rozpoznać mowę w języku Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
