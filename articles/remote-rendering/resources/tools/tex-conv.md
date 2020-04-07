---
title: TexConv - Narzędzie do konwersji tekstur
description: Instrukcja obsługi narzędzia wiersza polecenia TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680026"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - Narzędzie do konwersji tekstur

TexConv to narzędzie wiersza polecenia do przetwarzania tekstur z typowych formatów wejściowych, takich jak PNG, TGA, JPEG i DDS, w zoptymalizowanych formatach do zużycia środowiska uruchomieniowego.
Podczas gdy najczęstszym scenariuszem jest `A.xxx` przekonwertowanie `B.yyy`pojedynczego pliku wejściowego na zoptymalizowany format, narzędzie ma wiele dodatkowych opcji do zaawansowanych zastosowań.

## <a name="command-line-help"></a>Pomoc wiersza polecenia

Uruchamianie programu TexConv.exe z parametrem `--help` spowoduje wyświetlenie wszystkich dostępnych opcji. Ponadto TexConv drukuje używane opcje podczas wykonywania, aby pomóc zrozumieć, co robi. Szczegółowe informacje można znaleźć w tym pliku wyjściowym.

## <a name="general-usage"></a>Ogólne użycie

TexConv zawsze tworzy dokładnie jeden plik **wyjściowy.** Może używać wielu plików **wejściowych** do montażu danych wyjściowych z. Do złożenia potrzebne jest również **mapowanie kanału**, które informuje, który kanał *(Czerwony, Zielony, Niebieski* lub *Alfa),* aby wziąć z którego pliku wejściowego i przenieść go do którego kanału obrazu wyjściowego.

Najbardziej prosta linia poleceń jest taka:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`określa plik wyjściowy i format
- `-in0`określa pierwszy obraz wejściowy
- `-rgba`mówi, że obraz wyjściowy powinien korzystać ze wszystkich czterech kanałów i że powinny być one wykonane 1:1 z obrazu wejściowego

## <a name="multiple-input-files"></a>Wiele plików wejściowych

Aby zmontować dane wyjściowe z wielu plików `-in` wejściowych, należy określić każdy plik wejściowy przy użyciu opcji z rosnącą liczbą:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Podczas składania mapy sześcianu z tekstur 2D `-bottom`można `-front` `-back` również `-px` `-nx`użyć `-py` `-right` `-ny`, `-pz` `-nz`, `-left` `-top`, , , , lub , , , , .

Aby zamapować te dane wejściowe na plik wyjściowy, potrzebne jest odpowiednie mapowanie kanału.

## <a name="channel-mappings"></a>Mapowania kanałów

Opcje mapowania kanałów określają, z którego wejścia należy wypełnić podane kanały wyjściowe. Można określić dane wejściowe dla każdego kanału indywidualnie w ten sposób:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Tutaj kanały RGB wyjścia zostaną wypełnione przy użyciu pierwszego obrazu wejściowego, ale czerwony i niebieski zostaną zamienione. Kanał alfa danych wyjściowych zostanie wypełniony wartościami z czerwonego kanału drugiego obrazu wejściowego.

Określenie mapowania dla każdego kanału oddzielnie daje największą elastyczność. Dla wygody to samo można napisać za pomocą operatorów "swizzling":

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Kanały wyjściowe

Dostępne są następujące opcje mapowania kanałów:

- `-r`, `-g` `-b`, `-a` , : Określają one przypisania pojedynczego kanału
- `-rg`: Określ przypisania kanałów na czerwono i zielono.
- `-rgb`: Określ przypisania kanałów na czerwono, zielono-zielone i niebieskie.
- `-rgba`: Określa wszystkie cztery przypisania kanałów.

Wymieniając tylko kanał R, RG lub RGB, nakazuje TexConv utworzenie pliku wyjściowego z tylko 1, 2 lub 3 kanałami.

### <a name="input-swizzling"></a>Swizzling danych wejściowych

Przy podaniu tekstury wejściowej, który kanał wyjściowy powinien wypełnić, można przesiąklić wejście:

- `-rgba in0`jest równoznaczne z`-rgba in0.rgba`
- `-rgba in0.bgra`będzie swizzle kanałów wejściowych
- `-rgb in0.rrr`powiela czerwony kanał na wszystkie kanały

Można również wypełnić kanały z czarnym lub białym:

- `-rgb in0 -a white`stworzy 4-kanałową teksturę wyjściową, ale ustawi alfa na w pełni nieprzezroczystą
- `-rg black -b white`stworzy całkowicie niebieską teksturę

## <a name="common-options"></a>Typowe opcje

Najciekawsze opcje są wymienione poniżej. Więcej opcji są `TexConv --help`wymienione przez .

### <a name="output-type"></a>Typ wyjścia

- `-type 2D`: Wyjście będzie zwykłym obrazem 2D.
- `-type Cubemap`: Dane wyjściowe będą obrazem mapy modułowej. Obsługiwane tylko pliki wyjściowe DDS. Po określeniu tej wartości można zmontować mapę modułu z 6 zwykłych obrazów wejściowych 2D.

### <a name="image-compression"></a>Kompresja obrazu

- `-compression none`: Obraz wyjściowy zostanie nieskompresowany.
- `-compression medium`: Jeśli jest obsługiwany, obraz wyjściowy będzie używał kompresji bez utraty zbyt wysokiej jakości.
- `-compression high`: Jeśli jest obsługiwany, obraz wyjściowy użyje kompresji i jakości poświęcenia na rzecz mniejszego pliku.

### <a name="mipmaps"></a>Mipmapy

Domyślnie TexConv generuje mipmaps, gdy format wyjściowy obsługuje go.

- `-mipmaps none`: Mipmaps nie zostanie wygenerowany.
- `-mipmaps Linear`: Jeśli jest obsługiwana, mipmaps zostanie wygenerowany przy użyciu filtru pudełkowego.

### <a name="usage-srgb--gamma-correction"></a>Użycie (korekcja sRGB / gamma)

Opcja `-usage` określa cel danych wyjściowych i w ten sposób informuje TexConv, czy zastosować korektę gamma do plików wejściowych i wyjściowych. Użycie dotyczy tylko kanałów RGB. Kanał alfa jest zawsze uważany za zawierający wartości liniowe. Jeśli użycie nie zostanie określone, tryb "auto" spróbuje wykryć użycie z formatu i nazwy pliku pierwszego obrazu wejściowego. Na przykład jedno- i dwukanałowe formaty wyjściowe są zawsze liniowe. Sprawdź dane wyjściowe, aby zobaczyć, jaką decyzję podjął TexConv.

- `-usage Linear`: Obraz wyjściowy zawiera wartości, które nie reprezentują kolorów. Jest to zazwyczaj w przypadku tekstur metalicznych i chropowatości, a także wszelkiego rodzaju masek.

- `-usage Color`: Obraz wyjściowy reprezentuje kolor, taki jak mapy rozproszone/albedo. Flaga sRGB zostanie ustawiona w wyjściowym nagłówku DDS.

- `-usage HDR`: Plik wyjściowy powinien używać więcej niż 8 bitów na piksel do kodowania. W związku z tym wszystkie wartości są przechowywane w przestrzeni liniowej. W przypadku tekstur HDR nie ma znaczenia, czy dane reprezentują kolor, czy inne dane.

- `-usage NormalMap`: Obraz wyjściowy reprezentuje normalną mapę stycznej przestrzeni. Wartości zostaną znormalizowane, a obliczenia mipmap zostaną nieznacznie zoptymalizowane.

- `-usage NormalMap_Inverted`: Wyjście jest normalną mapą stycznej przestrzeni z Y skierowanym w przeciwnym kierunku niż wejście.

### <a name="image-rescaling"></a>Ponowne skalowanie obrazu

- `-minRes 64`: Określa minimalną rozdzielczość wyjścia. Jeśli obraz wejściowy jest mniejszy, zostanie on przeskalowycony.
- `-maxRes 1024`: Określa maksymalną rozdzielczość wyjścia. Jeśli obraz wejściowy jest większy, zostanie zmniejszona.
- `-downscale 1`: Jeśli jest większa niż 0, obrazy wejściowe zostaną zmniejszone o połowę w rozdzielczości N razy. Użyj tego, aby zastosować ogólną redukcję jakości.

## <a name="examples"></a>Przykłady

### <a name="convert-a-color-texture"></a>Konwertowanie tekstury kolorów

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Konwertowanie normalnej mapy

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Tworzenie mapy modułu HDR

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

Doskonałym źródłem map kostek HDR jest [hdrihaven.com.](https://hdrihaven.com/hdris/)

### <a name="bake-multiple-images-into-one"></a>Piec wiele obrazów w jednym

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Wyodrębnianie pojedynczego kanału

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
