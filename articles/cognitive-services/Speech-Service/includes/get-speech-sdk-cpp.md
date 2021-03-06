---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 296dea2e92d494cb9feaeb9f0c942b6a7da57abb
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656515"
---
:::row:::
    :::column span="3":::
        C++ Speech SDK jest dostępny w systemach Windows, Linux i macOS. Aby uzyskać więcej informacji, zobacz <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.CognitiveServices.Speech <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>Pakiet NuGet języka C++

Zestawu SDK mowy języka C++ można zainstalować `Install-Package` w **Menedżerze pakietów** za pomocą następującego polecenia.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>Pliki binarne języka C++ i pliki nagłówkowe

Alternatywnie C++ Speech SDK można zainstalować z plików binarnych. Pobierz pakiet SDK jako <a href="https://aka.ms/csspeech/linuxbinary" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> pakiet .tar</a> i rozpakuj pliki w wybranym katalogu. Zawartość tego pakietu (które zawierają pliki nagłówkowe dla architektur docelowych x86 i x64) są uporządkowane w następujący sposób:

  | Ścieżka                   | Opis                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licencja                                              |
  | `ThirdPartyNotices.md` | Uwagi dotyczące innych firm                                  |
  | `include`              | Pliki nagłówkowe języka C++                                 |
  | `lib/x64`              | Natywna biblioteka x64 do łączenia z aplikacją |
  | `lib/x86`              | Natywna biblioteka x86 do łączenia z aplikacją |

  Aby utworzyć aplikację, skopiuj lub przenieś wymagane pliki binarne (i biblioteki) do środowiska programistycznego. Uwzględnij je zgodnie z wymaganiami w procesie kompilacji.

#### <a name="additional-resources"></a>Zasoby dodatkowe

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Windows, Linux i macOS Szybki start Kod źródłowy C++<span class="docon docon-navigate-external x-hidden-focus"></span></a>