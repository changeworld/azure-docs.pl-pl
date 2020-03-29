---
title: Schemat metadanych wejściowych usługi Azure Media Services | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie schematu metadanych wejściowych usługi Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a81d6edfd887dc935a53742b7bc1492651c9bda5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887123"
---
# <a name="input-metadata"></a>Metadane wejściowe 

Zadanie kodowania jest skojarzone z zasobem wejściowym (lub zasobami), na którym chcesz wykonać niektóre zadania kodowania.  Po zakończeniu zadania jest produkowany zasób wyjściowy.  Zasób wyjściowy zawiera wideo, audio, miniatury, manifest itp. Zasób wyjściowy zawiera również plik z metadanymi dotyczącymi zasobu wejściowego. Nazwa pliku XML metadanych ma następujący &lt;format:&gt;asset_id _metadata.xml (na przykład 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), gdzie &lt;asset_id&gt; jest wartością AssetId zasobu wejściowego.  

Usługi Media Services nie skanują z wyprzedzeniem zasobów wejściowych w celu wygenerowania metadanych. Wejściowe metadane są generowane tylko jako artefakt, gdy wejściowy zasób jest przetwarzany w zadaniu. W związku z tym ten artefakt jest zapisywany do wyjściowego zasobu. Różne narzędzia są używane do generowania metadanych dla zasobów wejściowych i zasobów wyjściowych. W związku z tym metadane wejściowe ma nieco inny schemat niż metadane wyjściowe.

Jeśli chcesz sprawdzić plik metadanych, możesz **SAS** utworzyć lokalizator sygnatury dostępu Współdzielonego i pobrać plik na komputer lokalny. Można znaleźć przykład tworzenia lokalizatora sygnatury dostępu Współdzielonego i pobierania pliku [za pomocą rozszerzeń SDK usługi Media Services .NET](media-services-dotnet-get-started.md).  

W tym artykule omówiono elementy i typy schematu XML, na którym opiera się metada wejściowa (&lt;asset_id&gt;_metadata.xml).  Aby uzyskać informacje o pliku zawierającym metadane dotyczące zasobu wyjściowego, zobacz [Metadane wyjściowe](media-services-output-metadata-schema.md).  

[Kod schematu przykład](media-services-input-metadata-schema.md#code) [XML](media-services-input-metadata-schema.md#xml) na końcu tego artykułu.  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a>AssetFiles element (element główny)
Zawiera kolekcję [AssetFile element](media-services-input-metadata-schema.md#AssetFile)s dla zadania kodowania.  

Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

| Nazwa | Opis |
| --- | --- |
| **Plik aktywów**<br /><br /> minOccurs="1" maxOccurs="bez ograniczeń" |Pojedynczy element podrzędny. Aby uzyskać więcej informacji, zobacz [AssetFile element](media-services-input-metadata-schema.md#AssetFile). |

## <a name="assetfile-element"></a><a name="AssetFile"></a>Element Pliku aktywów
 Zawiera atrybuty i elementy opisujące plik zasobu.  

 Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Nazwa**<br /><br /> Wymagany |**xs:ciąg znaków** |Nazwa pliku zasobu. |
| **Rozmiar**<br /><br /> Wymagany |**xs:long** |Rozmiar pliku zasobu w bajtach. |
| **Czas trwania**<br /><br /> Wymagany |**xs:czas trwania** |Czas odtwarzania zawartości. Przykład: Czas trwania="PT25M37.757S". |
| **LiczbaOfStreams**<br /><br /> Wymagany |**xs:int** |Liczba strumieni w pliku zasobu. |
| **Nazwy formatów**<br /><br /> Wymagany |**xs: ciąg znaków** |Formatowanie nazw. |
| **FormatVerboseNames**<br /><br /> Wymagany |**xs: ciąg znaków** |Formatowanie nazw pełnych. |
| **Starttime** |**xs:czas trwania** |Czas rozpoczęcia zawartości. Przykład: StartTime="PT2.669S". |
| **Wskaźnik BitRate** |**xs: int** |Średnia szybkość transmisji bitów pliku zasobu w kbps. |

> [!NOTE]
> Następujące cztery elementy podrzędne muszą pojawić się w sekwencji.  
> 
> 

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Programy**<br /><br /> minOccurs="0" | |Kolekcja wszystkich [programów elementu,](media-services-input-metadata-schema.md#Programs) gdy plik zasobu jest w formacie MPEG-TS. |
| **VideoTracks (Śledzenie wideo)**<br /><br /> minOccurs="0" | |Każdy plik zasobu fizycznego może zawierać zero lub więcej ścieżek wideo przeplatane do odpowiedniego formatu kontenera. Ten element zawiera kolekcję wszystkich [VideoTracks,](media-services-input-metadata-schema.md#VideoTracks) które są częścią pliku zasobu. |
| **AudioTracks (AudioTracks)**<br /><br /> minOccurs="0" | |Każdy plik zasobu fizycznego może zawierać zero lub więcej ścieżek audio przeplatane do odpowiedniego formatu kontenera. Ten element zawiera kolekcję wszystkich [AudioTracks,](media-services-input-metadata-schema.md#AudioTracks) które są częścią pliku zasobu. |
| **Metadane**<br /><br /> minOccurs="0" maxOccurs="bez ograniczeń" |[Typ metadanych](media-services-input-metadata-schema.md#MetadataType) |Metadane pliku zasobów reprezentowane jako ciągi klucza wartości. Przykład:<br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a>Typ utworu
Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Identyfikator**<br /><br /> Wymagany |**xs:int** |Indeks zerowy tej ścieżki audio lub wideo.<br /><br /> Nie jest to koniecznie, że TrackID, jak używany w pliku MP4. |
| **Kodek** |**xs:ciąg znaków** |Ciąg kodeka ścieżki wideo. |
| **KodekDługoname** |**xs: ciąg znaków** |Nazwa kodeka ścieżki audio lub wideo. |
| **Baza czasu**<br /><br /> Wymagany |**xs:ciąg znaków** |Podstawa czasu. Przykład: TimeBase="1/48000" |
| **Liczba Ramek** |**xs:int** |Liczba klatek (w przypadku ścieżek wideo). |
| **Starttime** |**xs: czas trwania** |Śledź czas rozpoczęcia. Przykład: StartTime="PT2.669S" |
| **Czas trwania** |**xs:czas trwania** |Śledzenie czasu trwania. Przykład: duration="PTSampleFormat M37.757s". |

> [!NOTE]
> Następujące dwa elementy podrzędne muszą pojawić się w sekwencji.  
> 
> 

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Dyspozycji**<br /><br /> minOccurs="0" maxOccurs="1" |[Typ rozkładu strumienia](media-services-input-metadata-schema.md#StreamDispositionType) |Zawiera informacje o prezentacji (na przykład, czy dana ścieżka dźwiękowa jest dla widzów niedowidzących). |
| **Metadane**<br /><br /> minOccurs="0" maxOccurs="bez ograniczeń" |[Typ metadanych](media-services-input-metadata-schema.md#MetadataType) |Ogólne ciągi klucza/wartości, które mogą być używane do przechowywania różnych informacji. Na przykład key="language" i value="eng". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a>AudioTrackType (dziedziczy z TrackType)
 **AudioTrackType** to globalny typ złożony, który dziedziczy z [TrackType](media-services-input-metadata-schema.md#TrackType).  

 Typ reprezentuje określoną ścieżkę audio w pliku zasobu.  

 Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Przykładformat** |**xs:ciąg znaków** |Przykładowy format. |
| **Układ kanału** |**xs: ciąg znaków** |Układ kanału. |
| **Kanały**<br /><br /> Wymagany |**xs:int** |Liczba (0 lub więcej) kanałów audio. |
| **SamplingRate (Szybkość próbkowania)**<br /><br /> Wymagany |**xs:int** |Częstotliwość próbkowania dźwięku w próbkach/s lub Hz. |
| **Bitrate** |**xs:int** |Średnia szybkość transmisji bitów audio w bitach na sekundę, obliczona na podstawie pliku zasobu. Tylko ładunek strumienia elementarnego jest liczony, a obciążenie związane z pakowaniem nie jest uwzględniane w tej liczbie. |
| **BitsPerSample (BitsPerSample)** |**xs:int** |Bity na próbkę dla typu formatu wFormatTag. |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a>VideoTrackType (dziedziczy z TrackType)
**VideoTrackType** to globalny typ złożony, który dziedziczy z [TrackType](media-services-input-metadata-schema.md#TrackType).  

Typ reprezentuje określoną ścieżkę wideo w pliku zasobu.  

Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Fourcc**<br /><br /> Wymagany |**xs:ciąg znaków** |Kodek wideo Kodek FourCC. |
| **Profil** |**xs: ciąg znaków** |Profil utworu wideo. |
| **Poziom** |**xs: ciąg znaków** |Poziom ścieżki wideo. |
| **Pixelformat** |**xs: ciąg znaków** |Format pikseli ścieżki wideo. |
| **impulsów**<br /><br /> Wymagany |**xs:int** |Zakodowana szerokość wideo w pikselach. |
| **Wysokość**<br /><br /> Wymagany |**xs:int** |Zakodowana wysokość wideo w pikselach. |
| **DisplayAspectRatioNumerator**<br /><br /> Wymagany |**xs: podwójne** |Licznik proporcji ekranu wideo. |
| **DisplayAspectRatioDenominator**<br /><br /> Wymagany |**xs:double** |Mianownik proporcji ekranu wideo. |
| **DisplayAspectRatioDenominator**<br /><br /> Wymagany |**xs: podwójne** |Licznik współczynnika proporcji próbki wideo. |
| **SampleAspectRatioNumerator** |**xs: podwójne** |Licznik współczynnika proporcji próbki wideo. |
| **SampleAspectRatioNumerator** |**xs:double** |Mianownik współczynnika proporcji próbki wideo. |
| **Ilość klatek**<br /><br /> Wymagany |**xs:dziesiętny** |Zmierzona liczba klatek na sekundę wideo w formacie .3f. |
| **Bitrate** |**xs:int** |Średnia szybkość transmisji bitów wideo w kilobitach na sekundę, obliczona na podstawie pliku zasobu. Tylko ładunek strumienia elementarnego jest liczony, a obciążenie związane z pakowaniem nie jest uwzględniane. |
| **Maksymalna liczba bitów** |**xs: int** |Maksymalna średnia szybkość transmisji bitów GOP dla tego utworu wideo, w kilobitach na sekundę. |
| **Ramki HasBFrame** |**xs:int** |Liczba klatek B ścieżki wideo. |

## <a name="metadatatype"></a><a name="MetadataType"></a>Typ metadanych
**MetadataType** to globalny typ złożony, który opisuje metadane pliku zasobu jako ciągi klucza/wartości. Na przykład key="language" i value="eng".  

Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **key**<br /><br /> Wymagany |**xs:ciąg znaków** |Klucz w parze klucz/wartość. |
| **value**<br /><br /> Wymagany |**xs:ciąg znaków** |Wartość w parze klucz/wartość. |

## <a name="programtype"></a><a name="ProgramType"></a>Typ programu
**ProgramType** jest globalnym typem złożonym opisującym program.  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **ProgramId**<br /><br /> Wymagany |**xs:int** |Identyfikator programu |
| **Programy NumberOf**<br /><br /> Wymagany |**xs:int** |Liczba programów. |
| **PmtPid ( PmtPid )**<br /><br /> Wymagany |**xs:int** |Tabele map programów (PMT) zawierają informacje o programach.  Aby uzyskać więcej informacji, zobacz [PMt](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid ( pcrpid )**<br /><br /> Wymagany |**xs: int** |Używany przez dekoder. Aby uzyskać więcej informacji, zobacz [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: długi** |Uruchamianie sygnatury czasowej prezentacji. |
| **EndPTS** |**xs: długi** |Zakończenie sygnatury czasowej prezentacji. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a>Typ rozkładu strumienia
**StreamDispositionType** jest globalnym typem złożonym, który opisuje strumień.  

Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Domyślny**<br /><br /> Wymagany |**xs: int** |Ustaw ten atrybut na 1, aby wskazać, że jest to domyślna prezentacja. |
| **Dub**<br /><br /> Wymagany |**xs:int** |Ustaw ten atrybut na 1, aby wskazać, że jest to prezentacja podona. |
| **Oryginał**<br /><br /> Wymagany |**xs: int** |Ustaw ten atrybut na 1, aby wskazać, że jest to oryginalna prezentacja. |
| **Komentarz**<br /><br /> Wymagany |**xs:int** |Ustaw ten atrybut na 1, aby wskazać, że ta ścieżka zawiera komentarz. |
| **Teksty**<br /><br /> Wymagany |**xs:int** |Ustaw ten atrybut na 1, aby wskazać, że utwór zawiera teksty piosenek. |
| **Karaoke**<br /><br /> Wymagany |**xs:int** |Ustaw ten atrybut na 1, aby wskazać, że reprezentuje utwór karaoke (muzyka w tle, brak wokalu). |
| **Zmuszona**<br /><br /> Wymagany |**xs:int** |Ustaw ten atrybut na 1, aby wskazać, że jest to wymuszona prezentacja. |
| **SłyszeniePowłożony**<br /><br /> Wymagany |**xs:int** |Ustaw ten atrybut na 1, aby wskazać, że ta ścieżka jest dla osób, które są niedosłyszące. |
| **VisualImpaired (Własnomiony)**<br /><br /> Wymagany |**xs:int** |Ustaw ten atrybut na 1, aby wskazać, że ta ścieżka jest dla osób niedowidzących. |
| **Czyste efekty**<br /><br /> Wymagany |**xs: int** |Ustaw ten atrybut na 1, aby wskazać, że ta ścieżka ma czyste efekty. |
| **AttachedPic**<br /><br /> Wymagany |**xs: int** |Ustaw ten atrybut na 1, aby wskazać, że ta ścieżka ma obrazy. |

## <a name="programs-element"></a><a name="Programs"></a>Element Programy
Element otoki, w którym trzyma się wielu elementów **programu.**  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="bez ograniczeń" |[Typ programu](media-services-input-metadata-schema.md#ProgramType) |W przypadku plików zasobów w formacie MPEG-TS zawiera informacje o programach w pliku zasobu. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a>Element VideoTracks
 Element otoki z wieloma elementami **VideoTrack.**  

 Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Śledzenie wideo**<br /><br /> minOccurs="0" maxOccurs="bez ograniczeń" |[VideoTrackType (dziedziczy z TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Zawiera informacje o ścieżkach wideo w pliku zasobu. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a>Element AudioTracks
 Element otoki przechowujące wiele elementów **AudioTrack.**  

 Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>elementy
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **AudioTrack (AudioTrack)**<br /><br /> minOccurs="0" maxOccurs="bez ograniczeń" |[AudioTrackType (dziedziczy z TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Zawiera informacje o ścieżkach audio w pliku zasobu. |

## <a name="schema-code"></a><a name="code"></a>Kod schematu
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               elementFormDefault="qualified">  

      <xs:complexType name="MetadataType">  
        <xs:attribute name="key"   type="xs:string" use="required"/>  
        <xs:attribute name="value" type="xs:string" use="required"/>  
      </xs:complexType>  

      <xs:complexType name="ProgramType">  
        <xs:attribute name="ProgramId" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Program Id</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Number of programs</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PmtPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pmt pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PcrPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pcr pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="StartPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>start pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="EndPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>end pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="StreamDispositionType">  
        <xs:attribute name="Default"          type="xs:int" use="required" />  
        <xs:attribute name="Dub"              type="xs:int" use="required" />  
        <xs:attribute name="Original"         type="xs:int" use="required" />  
        <xs:attribute name="Comment"          type="xs:int" use="required" />  
        <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
        <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
        <xs:attribute name="Forced"           type="xs:int" use="required" />  
        <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
        <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
        <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
        <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
      </xs:complexType>  

      <xs:complexType name="TrackType" abstract="true">  
        <xs:sequence>  
          <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
          <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
        </xs:sequence>  
        <xs:attribute name="Id" use="required">  
          <xs:annotation>  
            <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Codec" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec string</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="CodecLongName" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec long name</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="TimeBase"  type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfFrames">  
          <xs:annotation>  
            <xs:documentation>number of frames</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="StartTime" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Duration" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="VideoTrackType">  
        <xs:annotation>  
          <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="FourCC" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>video codec FourCC code</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Profile" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>profile</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Level" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>level</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="PixelFormat" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>Video track's pixel format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Width" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video width in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Height" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video height in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioNumerator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioDenominator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="FrameRate" use="required">  
              <xs:annotation>  
                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:decimal">  
                  <xs:minInclusive value="0"/>  
                  <xs:fractionDigits value="3"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="MaxGOPBitrate">  
              <xs:annotation>  
                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="HasBFrames" type="xs:int">  
              <xs:annotation>  
                <xs:documentation>video track number of B frames</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:complexType name="AudioTrackType">  
        <xs:annotation>  
          <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="SampleFormat"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>sample format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="ChannelLayout"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>channel layout</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Channels" use="required">  
              <xs:annotation>  
                <xs:documentation>number of audio channels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SamplingRate" use="required">  
              <xs:annotation>  
                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="BitsPerSample">  
              <xs:annotation>  
                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Programs" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" type="xs:duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
                  <xs:annotation>  
                    <xs:documentation>number of streams in asset file</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatNames" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format verbose names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="StartTime" type="xs:duration">  
                  <xs:annotation>  
                    <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="OverallBitRate">  
                  <xs:annotation>  
                    <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:int">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  


## <a name="xml-example"></a><a name="xml"></a>Przykład XML
Poniżej przedstawiono przykład pliku metadanych wejściowych.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
      <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
        <VideoTracks>  
          <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
          </VideoTrack>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
          </AudioTrack>  
        </AudioTracks>  
        <Metadata key="major_brand" value="mp42" />  
        <Metadata key="minor_version" value="0" />  
        <Metadata key="compatible_brands" value="mp42mp41" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
        <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

