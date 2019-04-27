---
title: Rejestrowanie zestawu SDK mowy — usługi mowy
titleSuffix: Azure Cognitive Services
description: Włącz rejestrowanie w zestawie SDK rozpoznawania mowy.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 75eaea22c4809eda78e54514961d13113b4a5f3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60696855"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Włącz rejestrowanie w zestawie SDK rozpoznawania mowy

Rejestrowanie do pliku jest opcjonalna funkcja zestawu SDK rozpoznawania mowy. Podczas tworzenia rejestrowanie udostępnia dodatkowe informacje i informacji diagnostycznych z zestawu SDK Speeck podstawowe składniki. Można ją włączyć, ustawiając właściwość `Speech_LogFilename` na obiekt konfiguracji rozpoznawania mowy, lokalizację i nazwę pliku dziennika. Rejestrowanie zostanie aktywowany globalnie po utworzeniu aparat rozpoznawania od tej konfiguracji i nie można wyłączyć później. Nie można zmienić nazwę pliku dziennika podczas uruchamiania rejestrowania sesji.

> [!NOTE]
> Rejestrowanie jest dostępny w wszystkich SDK mowy obsługiwanych języków programowania, z wyjątkiem języka JavaScript.

## <a name="sample"></a>Sample

Nazwa pliku dziennika jest określony dla obiektu konfiguracji. Biorąc `SpeechConfig` jako przykład i przy założeniu, że masz utworzonego wystąpienia o nazwie `config`:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Aparat rozpoznawania można utworzyć obiektu konfiguracji. Spowoduje to włączenie rejestrowania dla wszystkich aparatów rozpoznawania gestów.

> [!NOTE]
> Jeśli tworzysz `SpeechSynthesizer` z obiektu konfiguracji będzie nie włączać rejestrowania. Jeśli jednak włączono rejestrowania, otrzymasz także Diagnostyka z `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Tworzy plik dziennika na różnych platformach

Windows lub Linux plik dziennika może być w dowolnej ścieżce, do których użytkownik ma uprawnienia do zapisu dla. Uprawnienia do zapisu do lokalizacji systemu plików w innych systemach operacyjnych może być ograniczony lub domyślnie ograniczony.

### <a name="universal-windows-platform-uwp"></a>Platforma uniwersalna systemu Windows

Aplikacje platformy uniwersalnej systemu Windows muszą być umieszcza pliki dziennika w lokalizacji danych aplikacji (lokalnego, mobilnych lub tymczasowe). Plik dziennika można utworzyć w taki sposób, w folderze lokalnym aplikacji:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Więcej o dostępie do pliku uprawnień dla aplikacji platformy uniwersalnej systemu Windows jest dostępna [tutaj](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Plik dziennika można zapisać w pamięci wewnętrznej, magazynu zewnętrznego lub katalog pamięci podręcznej. Pliki utworzone w pamięci wewnętrznej lub katalog pamięci podręcznej są prywatne do aplikacji. Zaleca się, aby utworzyć plik dziennika na magazynie zewnętrznym.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Powyższy kod zostanie zapisane w pliku dziennika magazynu zewnętrznego w katalogu głównym katalogiem specyficzne dla aplikacji. Użytkownik może uzyskać dostępu do pliku za pomocą Menedżera plików (zwykle w `Android/data/ApplicationName/logfile.txt`). Plik jest usuwany po odinstalowaniu aplikacji.

Należy również żądać `WRITE_EXTERNAL_STORAGE` uprawnień w pliku manifestu:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Więcej o danych i pliku magazynu dla aplikacji systemu Android dostępnej [tutaj](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Dostępne są tylko katalogi w piaskownicy aplikacji. Pliki mogą być tworzone w dokumentach, biblioteki i katalogów plików tymczasowych. Pliki w katalogu dokumenty można udostępnione przez użytkownika. Poniższy fragment kodu przedstawia utworzenie pliku dziennika w katalogu dokumentów aplikacji:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Aby uzyskać dostęp do utworzonego pliku, Dodaj poniższe właściwości do `Info.plist` listy właściwości aplikacji:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Więcej o iOS systemu plików jest dostępna [tutaj](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)

