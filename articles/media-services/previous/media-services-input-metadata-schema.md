---
title: Schematu wejściowych metadanych usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Temat zawiera omówienie schematu wejściowych metadanych usługi Azure Media Services.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d72848e2-4b65-4c84-94bc-e2a90a6e7f47
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: 9edfdee82a289d3b966510281305dc45a451c35f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788942"
---
# <a name="input-metadata"></a>Dane wejściowe metadanych
Zadania kodowania jest skojarzony z zasób wejściowy (lub zasoby), na którym chcesz wykonać kilka zadań kodowania.  Po zakończeniu zadania jest generowany zawartości wyjściowej.  Elementu zawartości wyjściowej zawiera wideo, audio, miniatur, manifest itp. Elementu zawartości wyjściowej zawiera także plik o metadane dotyczące zasobu wejściowego. Nazwa pliku XML metadanych ma następujący format: &lt;asset_id&gt;_metadata.xml (na przykład 41114ad3-eb5e - 4c d 57 8 92-5354e2b7d4a4_metadata.xml), gdzie &lt;asset_id&gt; jest wartością IDśrodkaTrwałego zasobów wejściowych.  

Jeśli chcesz sprawdzić jego pliku metadanych, możesz utworzyć **SAS** lokalizator i pobierania plików na komputerze lokalnym. Przykład można znaleźć na tworzenie lokalizatora SAS i pobranie pliku [przy użyciu rozszerzenia SDK .NET usługi Media Services](media-services-dotnet-get-started.md).  

W tym artykule opisano elementy i typy schematu XML, na którym wejściowych metada (&lt;asset_id&gt;_metadata.xml) opiera się.  Uzyskać informacji o pliku, zawierającego metadane dotyczące zawartości wyjściowej, zobacz [metadanych dane wyjściowe](media-services-output-metadata-schema.md).  

> [!NOTE]
> Można znaleźć [kodu schematu](media-services-input-metadata-schema.md#code) [przykład XML](media-services-input-metadata-schema.md#xml) na końcu tego artykułu.  
> 
> 

## <a name="AssetFiles"></a> Element AssetFiles (element główny)
Zawiera kolekcję [elementu AssetFile](media-services-input-metadata-schema.md#AssetFile)s dla zadania kodowania.  

Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

| Name (Nazwa) | Opis |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs = maxOccurs "1" = "unbounded" |Element podrzędny pojedynczego. Aby uzyskać więcej informacji, zobacz [AssetFile elementu](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a> AssetFile element
 Zawiera atrybuty i elementy, które opisują pliku zasobów.  

 Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Nazwa**<br /><br /> Wymagane |**xs:String** |Nazwa pliku zasobów. |
| **Rozmiar**<br /><br /> Wymagane |**xs:Long** |Rozmiar pliku elementu zawartości w bajtach. |
| **Czas trwania**<br /><br /> Wymagane |**DURATION** |Czas trwania wstecz odtwarzania zawartości. Przykład: Czas trwania = "PT25M37.757S". |
| **NumberOfStreams**<br /><br /> Wymagane |**xs:int** |Liczba strumieni w pliku zasobów. |
| **FormatNames**<br /><br /> Wymagane |**xs: ciąg** |Format nazwy. |
| **FormatVerboseNames**<br /><br /> Wymagane |**xs: ciąg** |Pełne nazwy formatu. |
| **Czas rozpoczęcia** |**DURATION** |Godzina rozpoczęcia zawartości. Przykład: Wartość StartTime = "PT2.669S". |
| **OverallBitRate** |**xs: int** |Średnia szybkość transmisji bitów pliku zasobów w KB/s. |

> [!NOTE]
> Następujące elementy podrzędne cztery musi znajdować się w określonej kolejności.  
> 
> 

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Programy**<br /><br /> minOccurs="0" | |Kolekcja wszystkich [element programy](media-services-input-metadata-schema.md#Programs) gdy plik zasobów jest w formacie MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Każdy plik zasobów fizycznych może zawierać zero lub więcej ścieżek wideo przeplatana do formatu odpowiedniego kontenera. Ten element zawiera zbiór wszystkich [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) które są częścią pliku zasobów. |
| **AudioTracks**<br /><br /> minOccurs="0" | |Każdy plik zasobów fizycznych może zawierać zero lub więcej ścieżek audio przeplatana do formatu odpowiedniego kontenera. Ten element zawiera zbiór wszystkich [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) które są częścią pliku zasobów. |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Reprezentowane jako ciągi key\value metadanych pliku zasobów. Na przykład:<br /><br /> **&lt;Metadane klucz = wartość "język" = "eng" /&gt;** |

## <a name="TrackType"></a> TrackType
Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Identyfikator**<br /><br /> Wymagane |**xs:int** |Liczony od zera indeks tej ścieżki audio i wideo.<br /><br /> Nie jest stosowanie TrackID jako plik MP4. |
| **Koder-dekoder** |**xs:String** |Parametry kodera-dekodera wideo Śledź. |
| **CodecLongName** |**xs: ciąg** |Śledź audio i wideo koder-dekoder długa nazwa. |
| **Podstawy czasu**<br /><br /> Wymagane |**xs:String** |Podstawa czasu. Przykład: Podstawy czasu = "1/48000" |
| **NumberOfFrames** |**xs:int** |Liczba ramek (istnieje dla ścieżki wideo). |
| **Czas rozpoczęcia** |**xs: czas trwania** |Godzina rozpoczęcia śledzenia. Przykład: Wartość StartTime = "PT2.669S" |
| **Czas trwania** |**DURATION** |Śledzenie czasu trwania. Przykład: Czas trwania = "PTSampleFormat M37.757S". |

> [!NOTE]
> Następujące elementy podrzędne dwóch musi znajdować się w określonej kolejności.  
> 
> 

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Dyspozycji**<br /><br /> minOccurs = maxOccurs "0" = "1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Zawiera informacje o prezentacji (na przykład czy określonej ścieżki audio jest dla osób niedowidzących). |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Ogólny klucza i wartości ciągów, które może służyć do przechowywania różne informacje. Na przykład klucz = "język", a wartość = "eng". |

## <a name="AudioTrackType"></a> AudioTrackType (dziedziczy TrackType)
 **AudioTrackType** jest typem złożonym globalny, który dziedziczy z [TrackType](media-services-input-metadata-schema.md#TrackType).  

 Typ reprezentuje określonej ścieżki audio w pliku zasobów.  

 Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **SampleFormat** |**xs:String** |Przykładowy format. |
| **ChannelLayout** |**xs: ciąg** |Układ kanału. |
| **Kanały**<br /><br /> Wymagane |**xs:int** |Liczba (0 lub więcej) audio kanałów. |
| **SamplingRate**<br /><br /> Wymagane |**xs:int** |Częstotliwość próbkowania audio w próbek na sekundę lub Hz. |
| **Szybkość transmisji bitów** |**xs:int** |Średnia audio szybkość transmisji bitów w bitów na sekundę, obliczoną z pliku zasobów. Tylko podstawowe strumienia ładunku jest liczony i koszty opakowania jest niedostępna w tej liczby. |
| **BitsPerSample** |**xs:int** |Bity na przykład dla formatu wFormatTag typu. |

## <a name="VideoTrackType"></a> VideoTrackType (dziedziczy TrackType)
**VideoTrackType** jest typem złożonym globalny, który dziedziczy z [TrackType](media-services-input-metadata-schema.md#TrackType).  

Typ reprezentuje określonej ścieżki wideo w pliku zasobów.  

Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **FourCC**<br /><br /> Wymagane |**xs:String** |Kodera-dekodera wideo FourCC kodu. |
| **Profil** |**xs: ciąg** |Ścieżka wideo profil. |
| **Poziom** |**xs: ciąg** |Poziom śledzenia wideo. |
| **PixelFormat** |**xs: ciąg** |Ścieżka wideo format piksela. |
| **Szerokość**<br /><br /> Wymagane |**xs:int** |Zakodowany szerokości wideo w pikselach. |
| **Wysokość**<br /><br /> Wymagane |**xs:int** |Zakodowany wideo wysokość w pikselach. |
| **DisplayAspectRatioNumerator**<br /><br /> Wymagane |**xs: double** |Licznik współczynnik proporcji wideo. |
| **DisplayAspectRatioDenominator**<br /><br /> Wymagane |**xs:Double** |Denominator współczynnik proporcji wideo. |
| **DisplayAspectRatioDenominator**<br /><br /> Wymagane |**xs: double** |Przykładowe wideo licznik współczynnik proporcji. |
| **SampleAspectRatioNumerator** |**xs: double** |Przykładowe wideo licznik współczynnik proporcji. |
| **SampleAspectRatioNumerator** |**xs:Double** |Denominator współczynnik proporcji próbki wideo. |
| **Szybkość klatek**<br /><br /> Wymagane |**xs:decimal** |Mierzy szybkość odtwarzania wideo w formacie .3f. |
| **Szybkość transmisji bitów** |**xs:int** |Średnia wideo szybkość transmisji bitów w kilobitów na sekundę, obliczoną z pliku zasobów. Tylko podstawowe strumienia ładunku jest liczony i koszty opakowania nie jest dołączony. |
| **MaxGOPBitrate** |**xs: int** |Maksymalna liczba GOP średnia szybkość transmisji bitów dla tej ścieżki wideo, w kilobitów na sekundę. |
| **HasBFrames** |**xs:int** |Ścieżka wideo liczbę ramek B. |

## <a name="MetadataType"></a> MetadataType
**MetadataType** jest typem złożonym globalne informacje metadanych z pliku zasobów jako ciągi klucza i wartości. Na przykład klucz = "język", a wartość = "eng".  

Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Klucz**<br /><br /> Wymagane |**xs:String** |Klucz w parę klucza i wartości. |
| **value**<br /><br /> Wymagane |**xs:String** |Wartość w parę klucza i wartości. |

## <a name="ProgramType"></a> ProgramType
**ProgramType** jest typem złożonym globalnego zawiera opis programu.  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **ProgramId**<br /><br /> Wymagane |**xs:int** |Identyfikator programu |
| **NumberOfPrograms**<br /><br /> Wymagane |**xs:int** |Liczba programów. |
| **PmtPid**<br /><br /> Wymagane |**xs:int** |Program mapy tabele (PMTs) zawierają informacje o programach.  Aby uzyskać więcej informacji, zobacz [rata](http://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Wymagane |**xs: int** |Używany przez dekodera. Aby uzyskać więcej informacji, zobacz [PCR](http://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: długa** |Sygnatura czasowa rozpoczęcia prezentacji. |
| **EndPTS** |**xs: długa** |Końcowa sygnatura czasowa prezentacji. |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** jest typem złożonym globalny, który opisuje strumienia.  

Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Domyślne**<br /><br /> Wymagane |**xs: int** |Ten atrybut na 1, aby wskazać, że jest to domyślny sposób ich prezentacji. |
| **Dub**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na 1, aby wskazać, że jest to uaktywniane prezentacji. |
| **Oryginał**<br /><br /> Wymagane |**xs: int** |Ten atrybut na 1, aby wskazać, że jest to prezentacji. |
| **Komentarz**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na wartość 1, aby wskazać, że ta ścieżka zawiera komentarz. |
| **Tekst**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na wartość 1, aby wskazać, że ta ścieżka zawiera tekst. |
| **Karaoke**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na wartość 1, aby wskazać, że ta pozycja reprezentuje ścieżkę karaoke (muzyką w tle, nie vocals). |
| **Wymuszone**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na 1, aby wskazać, że jest to wymuszone prezentacji. |
| **HearingImpaired**<br /><br /> Wymagane |**xs:int** |Ten atrybut na 1, aby wskazać, że ta ścieżka jest ds naruszone. |
| **VisualImpaired**<br /><br /> Wymagane |**xs:int** |Ten atrybut jest ustawiony na 1, aby wskazać, że ta ścieżka jest dla niedowidzących. |
| **CleanEffects**<br /><br /> Wymagane |**xs: int** |Wartość tego atrybutu 1 wskazuje, że ta ścieżka ma wpływ czyste. |
| **AttachedPic**<br /><br /> Wymagane |**xs: int** |Ten atrybut jest ustawiony na 1, aby wskazać, że ta ścieżka ma obrazów. |

## <a name="Programs"></a> Element programy
Element otoki zawierający wiele **Program** elementów.  

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Dla plików zasobów, które są w formacie MPEG-TS zawiera informacje dotyczące programów w pliku zasobów. |

## <a name="VideoTracks"></a> VideoTracks element
 Element otoki zawierający wiele **VideoTrack** elementów.  

 Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (dziedziczy TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Zawiera informacje dotyczące ścieżki wideo w pliku zasobów. |

## <a name="AudioTracks"></a> AudioTracks element
 Element otoki zawierający wiele **AudioTrack** elementów.  

 Zobacz przykład XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (dziedziczy TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Zawiera informacje dotyczące ścieżki audio w pliku zasobów. |

## <a name="code"></a> Kod schematu
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
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


## <a name="xml"></a> Przykład pliku XML
Poniżej przedstawiono przykładowy plik metadanych danych wejściowych.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
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

