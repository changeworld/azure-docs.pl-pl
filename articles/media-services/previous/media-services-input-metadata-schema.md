---
title: Schemat danych wejściowych metadanych w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Temat zawiera omówienie schematu metadanych wejściowych usługi Azure Media Services.
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
ms.openlocfilehash: fa4487b07f130947ac5da2a5dbae6776b06acbe7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463774"
---
# <a name="input-metadata"></a>Metadane wejściowe 

Zadania kodowania jest skojarzony z wejściowego elementu (lub zasoby), w której chcesz wykonać kilka zadań kodowania.  Po zakończeniu zadania elementu zawartości wyjściowej jest generowany.  Elementu zawartości wyjściowej zawiera film wideo, audio, miniatury, manifest itp. Elementu zawartości wyjściowej zawiera także plik o metadane dotyczące zasobu danych wejściowych. Nazwa pliku XML metadanych ma następujący format: &lt;asset_id&gt;_metadata.xml (na przykład 41114ad3-eb5e - 4c, d 57 8 92-5354e2b7d4a4_metadata.xml), gdzie &lt;asset_id&gt; jest wartością AssetId zasób wejściowy.  

Usługi Media Services nie skanuje uprzedzając zdarzenie wejściowe zasoby do generowania metadanych. Metadane wejściowe zostanie wygenerowany tylko jako artefakt, gdy dane wejściowe, zasób jest przetwarzana w ramach zadania. Dlatego ten artefakt jest zapisywany w danych wyjściowych zasobów. Różne narzędzia są używane do generowania metadanych dla zasobów danych wejściowych i wyjściowych zasoby. Dlatego metadanych wejściowych ma schemat nieco inne niż metadanych danych wyjściowych.

Jeśli chcesz sprawdzić plik metadanych, możesz utworzyć **sygnatury dostępu Współdzielonego** lokalizatora i pobierania plików na komputerze lokalnym. Przykład można znaleźć na temat sposobu tworzenia lokalizatora SAS następuje i Pobierz plik [przy użyciu rozszerzenia SDK .NET usługi Media Services](media-services-dotnet-get-started.md).  

W tym artykule omówiono elementów i typów schematu XML, na którym metada danych wejściowych (&lt;asset_id&gt;_metadata.xml) opiera się.  Aby uzyskać informacje o pliku który zawiera metadane dotyczące elementu zawartości wyjściowej, zobacz [zwróci metadane z](media-services-output-metadata-schema.md).  

Możesz znaleźć [kod schematu](media-services-input-metadata-schema.md#code) [XML — przykład](media-services-input-metadata-schema.md#xml) na końcu tego artykułu.  
 

## <a name="AssetFiles"></a> Element AssetFiles (element główny)
Zawiera kolekcję [elementu AssetFile](media-services-input-metadata-schema.md#AssetFile)s dla zadania kodowania.  

Zobacz przykład XML na końcu tego artykułu: [XML — przykład](media-services-input-metadata-schema.md#xml).  

| Name (Nazwa) | Opis |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs="1" maxOccurs="unbounded" |Jeden typ elementów podrzędnych. Aby uzyskać więcej informacji, zobacz [elementu AssetFile](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a> AssetFile element
 Zawiera atrybuty i elementy, które opisują pliku elementu zawartości.  

 Zobacz przykład XML na końcu tego artykułu: [XML — przykład](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Nazwa**<br /><br /> Wymagane |**xs:String** |Nazwa pliku zasobu. |
| **Rozmiar**<br /><br /> Wymagane |**xs:Long** |Rozmiar pliku elementu zawartości, w bajtach. |
| **Czas trwania**<br /><br /> Wymagane |**DURATION** |Czas trwania wstecz odtwarzania zawartości. Przykład: Duration="PT25M37.757S". |
| **NumberOfStreams**<br /><br /> Wymagane |**xs:int** |Liczba strumieni w pliku elementu zawartości. |
| **FormatNames**<br /><br /> Wymagane |**xs: ciąg** |Format nazwy. |
| **FormatVerboseNames**<br /><br /> Wymagane |**xs: ciąg** |Pełne nazwy formatu. |
| **Godzina rozpoczęcia** |**DURATION** |Godzina rozpoczęcia zawartości. Przykład: StartTime="PT2.669S". |
| **OverallBitRate** |**xs: int** |Średnia szybkość transmisji bitów pliku zasobów w KB/s. |

> [!NOTE]
> Następujących elementów podrzędnych cztery musi znajdować się w sekwencji.  
> 
> 

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Programy**<br /><br /> minOccurs="0" | |Kolekcja wszystkich [elementu programy](media-services-input-metadata-schema.md#Programs) gdy plik zasobów jest w formacie MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Każdy plik zasobów fizycznych może zawierać zero lub więcej ścieżek filmy wideo z przeplotem do formatu odpowiedniego kontenera. Ten element zawiera zbiór wszystkich [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) będących częścią pliku elementu zawartości. |
| **AudioTracks**<br /><br /> minOccurs="0" | |Każdy plik zasobów fizycznych może zawierać zero lub więcej ścieżki audio z przeplotem do formatu odpowiedniego kontenera. Ten element zawiera zbiór wszystkich [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) będących częścią pliku elementu zawartości. |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Metadane pliku elementu zawartości w postaci ciągów key\value. Na przykład:<br /><br /> **&lt;Metadane klucz = wartość "język" = "eng" /&gt;** |

## <a name="TrackType"></a> TrackType
Zobacz przykład XML na końcu tego artykułu: [XML — przykład](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Identyfikator**<br /><br /> Wymagane |**xs:int** |Liczony od zera indeks tej ścieżki audio lub wideo.<br /><br /> To nie jest koniecznie że TrackID jako używane w pliku MP4. |
| **Koder-dekoder** |**xs:String** |Parametry kodera-dekodera wideo śledzenie. |
| **CodecLongName** |**xs: ciąg** |Koder długa nazwa ścieżki audio lub wideo. |
| **TimeBase**<br /><br /> Wymagane |**xs:String** |Podstawa czasu. Przykład: TimeBase="1/48000" |
| **NumberOfFrames** |**xs:int** |Liczba ramek (stosowany w przypadku ścieżek wideo). |
| **Godzina rozpoczęcia** |**xs: czas trwania** |Godzina rozpoczęcia śledzenia. Przykład: StartTime="PT2.669S" |
| **Czas trwania** |**DURATION** |Śledzenie czasu trwania. Przykład: Duration="PTSampleFormat M37.757S". |

> [!NOTE]
> Następujących elementów podrzędnych dwóch musi znajdować się w sekwencji.  
> 
> 

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Dyspozycja**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Zawiera informacje o prezentacji (na przykład, czy określonej ścieżki audio jest przeznaczone dla osób niedowidzących). |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Ciągi ogólny klucz/wartość, które mogą służyć do przechowywania różnych informacji. Na przykład klucz = "język" i wartość = "eng". |

## <a name="AudioTrackType"></a> AudioTrackType (dziedziczy TrackType)
 **AudioTrackType** to globalny typ złożony, który dziedziczy z [TrackType](media-services-input-metadata-schema.md#TrackType).  

 Typ reprezentuje określonej ścieżki audio w pliku elementu zawartości.  

 Zobacz przykład XML na końcu tego artykułu: [XML — przykład](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **SampleFormat** |**xs:String** |Przykładowy format. |
| **ChannelLayout** |**xs: ciąg** |Układ kanału. |
| **Channels**<br /><br /> Wymagane |**xs:int** |Liczba (0 lub więcej) audio kanałów. |
| **SamplingRate**<br /><br /> Wymagane |**xs:int** |Częstotliwość próbkowania audio w prób. / s lub Hz. |
| **Szybkość transmisji bitów** |**xs:int** |Średnia audio szybkość transmisji bitów w bitach na sekundę obliczone z pliku zasobów. Tylko ładunek strumienia podstawowych są traktowane, a obciążenie pakowania jest niedostępna w tej liczby. |
| **BitsPerSample** |**xs:int** |Bity na przykład wFormatTag format typu. |

## <a name="VideoTrackType"></a> VideoTrackType (dziedziczy TrackType)
**VideoTrackType** to globalny typ złożony, który dziedziczy z [TrackType](media-services-input-metadata-schema.md#TrackType).  

Typ reprezentuje określonej ścieżki wideo, w pliku elementu zawartości.  

Zobacz przykład XML na końcu tego artykułu: [XML — przykład](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **FourCC**<br /><br /> Wymagane |**xs:String** |Kodera-dekodera wideo FourCC kodu. |
| **Profil** |**xs: ciąg** |Profil ścieżki wideo. |
| **Poziom** |**xs: ciąg** |Poziom śledzenia wideo. |
| **Element pixelFormat** |**xs: ciąg** |Format pikseli Ścieżka wideo. |
| **Szerokość**<br /><br /> Wymagane |**xs:int** |Kodowany w formacie wideo szerokość w pikselach. |
| **Wysokość**<br /><br /> Wymagane |**xs:int** |Kodowany w formacie wideo wysokość w pikselach. |
| **DisplayAspectRatioNumerator**<br /><br /> Wymagane |**xs: double** |Licznik współczynnik proporcji ekranu wideo. |
| **DisplayAspectRatioDenominator**<br /><br /> Wymagane |**xs:double** |Denominator współczynnik proporcji ekranu wideo. |
| **DisplayAspectRatioDenominator**<br /><br /> Wymagane |**xs: double** |Licznik współczynnik proporcji próbki wideo. |
| **SampleAspectRatioNumerator** |**xs: double** |Licznik współczynnik proporcji próbki wideo. |
| **SampleAspectRatioNumerator** |**xs:double** |Denominator współczynnik proporcji próbki wideo. |
| **Szybkość klatek**<br /><br /> Wymagane |**xs:decimal** |Mierzone szybkość odtwarzania wideo w formacie .3f. |
| **Szybkość transmisji bitów** |**xs:int** |Średnia wideo szybkość transmisji bitów w kilobitów na sekundę obliczone z pliku zasobów. Tylko ładunek strumienia podstawowych są traktowane, a obciążenie pakietu nie jest dołączony. |
| **MaxGOPBitrate** |**xs: int** |Maksymalna liczba GOP średnia szybkość transmisji bitów dla tej ścieżki wideo w kilobitów na sekundę. |
| **HasBFrames** |**xs:int** |Ścieżka wideo liczba ramek B. |

## <a name="MetadataType"></a> MetadataType
**MetadataType** jest globalne typ złożony, który opisuje metadane pliku zasobów jako ciągi klucz/wartość. Na przykład klucz = "język" i wartość = "eng".  

Zobacz przykład XML na końcu tego artykułu: [XML — przykład](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Klucz**<br /><br /> Wymagane |**xs:String** |Klucz w pary klucz/wartość. |
| **value**<br /><br /> Wymagane |**xs:String** |Wartość w pary klucz/wartość. |

## <a name="ProgramType"></a> ProgramType
**ProgramType** jest globalne typ złożony, który zawiera opis programu.  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **ProgramId**<br /><br /> Wymagane |**xs:int** |Identyfikator programu |
| **NumberOfPrograms**<br /><br /> Wymagane |**xs:int** |Liczba programów. |
| **PmtPid**<br /><br /> Wymagane |**xs:int** |Program Map tabele (PMTs) zawierają informacje na temat programów.  Aby uzyskać więcej informacji, zobacz [PMt](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Wymagane |**xs: int** |Używane przez dekoder. Aby uzyskać więcej informacji, zobacz [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: long** |Sygnatura czasowa rozpoczęcia prezentacji. |
| **EndPTS** |**xs: long** |Końcowa sygnatura czasowa prezentacji. |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** jest globalne typ złożony, który opisuje strumienia.  

Zobacz przykład XML na końcu tego artykułu: [XML — przykład](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Domyślne**<br /><br /> Wymagane |**xs: int** |Ten atrybut jest ustawiony na 1, aby wskazać, że jest to domyślny prezentacji. |
| **Dub**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na 1, aby wskazać, że jest to uaktywniane prezentacji. |
| **Oryginał**<br /><br /> Wymagane |**xs: int** |Ten atrybut jest ustawiony na 1, aby wskazać, że jest to prezentacji. |
| **Komentarz**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na 1, aby wskazać, że ta ścieżka zawiera komentarz. |
| **Teksty**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na 1, aby wskazać, że ta ścieżka zawiera tekst. |
| **Karaoke**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na 1, aby wskazać, że stanowi to śledzenie biegam (muzyki w tle, nie vocals). |
| **Wymuszone**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na 1, aby wskazać, że jest to wymuszone prezentacji. |
| **HearingImpaired**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na 1, aby wskazać, że ta ścieżka jest dla osób, które są niedosłyszących. |
| **VisualImpaired**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na 1, aby wskazać, że ta ścieżka jest niedowidzących. |
| **CleanEffects**<br /><br /> Wymagane |**xs: int** |Ten atrybut jest ustawiony na 1, aby wskazać, że ta ścieżka ma skutki czyste. |
| **AttachedPic**<br /><br /> Wymagane |**xs: int** |Ten atrybut jest ustawiony na 1, aby wskazać, że ta ścieżka ma obrazów. |

## <a name="Programs"></a> Element programy
Element otoki zawierający wiele **Program** elementów.  

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Pliki zasobów, które są w formacie MPEG-TS zawiera informacje na temat programów w pliku elementu zawartości. |

## <a name="VideoTracks"></a> VideoTracks element
 Element otoki zawierający wiele **VideoTrack** elementów.  

 Zobacz przykład XML na końcu tego artykułu: [XML — przykład](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (dziedziczy TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Zawiera informacje o wideo ścieżek w pliku elementu zawartości. |

## <a name="AudioTracks"></a> AudioTracks element
 Element otoki zawierający wiele **AudioTrack** elementów.  

 Zobacz przykład XML na końcu tego artykułu: [XML — przykład](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (dziedziczy TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Zawiera informacje dotyczące ścieżki audio w pliku elementu zawartości. |

## <a name="code"></a> Kod schematu
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


## <a name="xml"></a> XML — przykład
Oto przykładowy plik metadanych danych wejściowych.  

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

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

