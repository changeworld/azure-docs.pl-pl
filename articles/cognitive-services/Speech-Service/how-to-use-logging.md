---
title: Rejestrowanie SDK mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak włączyć rejestrowanie w SDK mowy (C++, C#, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805794"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Włączanie rejestrowania w sdku mowy

Rejestrowanie do pliku jest opcjonalną funkcją dla SDK mowy. Podczas rejestrowania rozwoju zawiera dodatkowe informacje i diagnostyki z podstawowych składników speech SDK. Można ją włączyć, ustawiając `Speech_LogFilename` właściwość obiektu konfiguracji mowy na lokalizację i nazwę pliku dziennika. Rejestrowanie zostanie aktywowane globalnie po utworzeniu aparatu rozpoznawania z tej konfiguracji i nie można go później wyłączyć. Nie można zmienić nazwy pliku dziennika podczas uruchomionej sesji rejestrowania.

> [!NOTE]
> Rejestrowanie jest dostępne od momentu, gdy pakiet SDK mowy w wersji 1.4.0 jest dostępny we wszystkich obsługiwanych językach programowania SDK mowy, z wyjątkiem języka JavaScript.

## <a name="sample"></a>Sample

Nazwa pliku dziennika jest określona w obiekcie konfiguracji. Biorąc `SpeechConfig` za przykład i zakładając, że utworzono wystąpienie o nazwie: `config`

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

Aparat rozpoznawania można utworzyć z obiektu konfiguracyjnego. Umożliwi to rejestrowanie dla wszystkich aparatów rozpoznawania.

> [!NOTE]
> Jeśli utworzysz `SpeechSynthesizer` z obiektu konfiguracyjnego, nie włączy rejestrowania. Jeśli rejestrowanie jest włączone, otrzymasz również diagnostykę `SpeechSynthesizer`z pliku .

## <a name="create-a-log-file-on-different-platforms"></a>Tworzenie pliku dziennika na różnych platformach

W systemie Windows lub Linux plik dziennika może znajdować się w dowolnej ścieżce, dla którego użytkownik ma uprawnienia do zapisu. Uprawnienia do zapisu do lokalizacji systemu plików w innych systemach operacyjnych mogą być domyślnie ograniczone lub ograniczone.

### <a name="universal-windows-platform-uwp"></a>Platforma uniwersalna systemu Windows (UWP)

Aplikacje platformy uniwersalnej systemu Wizjerskiego muszą być umieszczane pliki dziennika w jednej z lokalizacji danych aplikacji (lokalne, roamingowe lub tymczasowe). Plik dziennika można utworzyć w lokalnym folderze aplikacji:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Więcej informacji o uprawnieniach dostępu do plików dla aplikacji platformy uniwersalnej systemu Uniwersalnego jest dostępnych [tutaj](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Plik dziennika można zapisać w magazynie wewnętrznym, magazynie zewnętrznym lub katalogu pamięci podręcznej. Pliki utworzone w pamięci wewnętrznej lub katalogu pamięci podręcznej są prywatne dla aplikacji. Zaleca się utworzenie pliku dziennika w magazynie zewnętrznym.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Powyższy kod zapisze plik dziennika w magazynie zewnętrznym w katalogu głównym katalogu specyficznego dla aplikacji. Użytkownik może uzyskać dostęp do pliku za `Android/data/ApplicationName/logfile.txt`pomocą menedżera plików (zwykle w ). Plik zostanie usunięty po odinstalowaniu aplikacji.

Należy również poprosić o `WRITE_EXTERNAL_STORAGE` uprawnienia w pliku manifestu:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Więcej informacji na temat przechowywania danych i plików dla aplikacji na Androida jest dostępnych [tutaj](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Dostępne są tylko katalogi wewnątrz piaskownicy aplikacji. Pliki można tworzyć w katalogach dokumentów, biblioteki i katalogów tymczasowych. Pliki w katalogu dokumentów mogą być udostępniane użytkownikowi. Poniższy fragment kodu pokazuje utworzenie pliku dziennika w katalogu dokumentu aplikacji:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Aby uzyskać dostęp do utworzonego pliku, `Info.plist` dodaj poniższe właściwości do listy właściwości aplikacji:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Więcej informacji o systemie plików iOS jest dostępnych [tutaj](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
