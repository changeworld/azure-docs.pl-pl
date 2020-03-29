---
title: Schemat metadanych danych wyjściowych usługi Azure Media Services | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie schematu metadanych danych wyjściowych usługi Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 3f0c6b60e2be625d1f869c3eda4acb9dfd3c6e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74886816"
---
# <a name="output-metadata"></a>Metadane danych wyjściowych
## <a name="overview"></a>Omówienie
Zadanie kodowania jest skojarzone z zasobem wejściowym (lub zasobami), na którym chcesz wykonać niektóre zadania kodowania. Na przykład zakodowanie pliku MP4 do adaptacyjnych zestawów szybkości transmisji bitów H.264 MP4; utworzyć miniaturę; utworzyć nakładki. Po zakończeniu zadania jest produkowany zasób wyjściowy.  Zasób wyjściowy zawiera wideo, audio, miniatury itp. Zasób wyjściowy zawiera również plik z metadanymi dotyczącymi zasobu wyjściowego. Nazwa pliku XML metadanych ma następujący &lt;format:&gt;source_file_name _manifest.xml (na przykład BigBuckBunny_manifest.xml).  

Usługi Media Services nie skanują z wyprzedzeniem zasobów wejściowych w celu wygenerowania metadanych. Wejściowe metadane są generowane tylko jako artefakt, gdy wejściowy zasób jest przetwarzany w zadaniu. W związku z tym ten artefakt jest zapisywany do wyjściowego zasobu. Różne narzędzia są używane do generowania metadanych dla zasobów wejściowych i zasobów wyjściowych. W związku z tym metadane wejściowe ma nieco inny schemat niż metadane wyjściowe.

Jeśli chcesz sprawdzić plik metadanych, możesz **SAS** utworzyć lokalizator sygnatury dostępu Współdzielonego i pobrać plik na komputer lokalny.  

W tym artykule omówiono elementy i typy schematu XML, na którym opiera się metada wyjściowa (source_file_name&lt;&gt;_manifest.xml). Aby uzyskać informacje o pliku zawierającym metadane dotyczące zasobu wejściowego, zobacz Metadane wejściowe.  

Pełny kod schematu i przykład XML można znaleźć na końcu tego artykułu.  

## <a name="assetfiles-root-element"></a><a name="AssetFiles"></a>Element główny AssetFiles
Zbieranie wpisów AssetFile dla zadania kodowania.  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **Plik aktywów**<br/><br/> minOccurs="0" maxOccurs="1" |AssetFile element, który jest częścią AssetFiles kolekcji. |

## <a name="assetfile-element"></a><a name="AssetFile"></a>Element Pliku aktywów
Przykład XML można znaleźć w [przykładzie XML](#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Nazwa**<br/><br/> Wymagany |**xs:ciąg znaków** |Nazwa pliku zasobu multimedialnego. |
| **Rozmiar**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:long** |Rozmiar pliku zasobu w bajtach. |
| **Czas trwania**<br/><br/> Wymagany |**xs:czas trwania** |Czas odtwarzania zawartości. |

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **Źródeł** |Kolekcja plików multimedialnych wejściowych/źródłowych, który został przetworzony w celu wytworzenia tego pliku assetfile. Aby uzyskać więcej informacji, zobacz Element źródła. |
| **VideoTracks (Śledzenie wideo)**<br/><br/> minOccurs="0" maxOccurs="1" |Każdy fizyczny AssetFile może zawierać w nim zero lub więcej utworów wideo przeplatane do odpowiedniego formatu kontenera. Aby uzyskać więcej informacji, zobacz VideoTracks element. |
| **AudioTracks (AudioTracks)**<br/><br/> minOccurs="0" maxOccurs="1" |Każdy fizyczny AssetFile może zawierać w nim zero lub więcej ścieżek audio przeplatane do odpowiedniego formatu kontenera. Jest to zbiór wszystkich tych ścieżek audio. Aby uzyskać więcej informacji, zobacz AudioTracks element. |

## <a name="sources-element"></a><a name="Sources"></a>Element źródła
Kolekcja plików multimedialnych wejściowych/źródłowych, który został przetworzony w celu wytworzenia tego pliku assetfile.  

Przykład XML można znaleźć w [przykładzie XML](#xml).  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **Źródła**<br/><br/> minOccurs="1" maxOccurs="bez ograniczeń" |Plik wejściowy/źródłowy używany podczas generowania tego zasobu. Aby uzyskać więcej informacji, zobacz Element źródła. |

## <a name="source-element"></a><a name="Source"></a>Element źródłowy
Plik wejściowy/źródłowy używany podczas generowania tego zasobu.  

Przykład XML można znaleźć w [przykładzie XML](#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Nazwa**<br/><br/> Wymagany |**xs:ciąg znaków** |Nazwa pliku źródłowego. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a>Element VideoTracks
Każdy fizyczny AssetFile może zawierać w nim zero lub więcej utworów wideo przeplatane do odpowiedniego formatu kontenera. **VideoTracks** element reprezentuje kolekcję wszystkich ścieżek wideo.  

Przykład XML można znaleźć w [przykładzie XML](#xml).  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **Śledzenie wideo**<br/><br/> minOccurs="1" maxOccurs="bez ograniczeń" |Określona ścieżka wideo w nadrzędnym pliku assetfile. Aby uzyskać więcej informacji, zobacz VideoTrack element. |

## <a name="videotrack-element"></a><a name="VideoTrack"></a>Element VideoTrack
Określona ścieżka wideo w nadrzędnym pliku assetfile.  

Przykład XML można znaleźć w [przykładzie XML](#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Identyfikator**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:int** |Indeks zerowy tej ścieżki wideo. **Uwaga:**  Ten **identyfikator** niekoniecznie jest identyfikatorem trackid używanym w pliku MP4. |
| **Fourcc**<br/><br/> Wymagany |**xs:ciąg znaków** |Kodek wideo Kodek FourCC. |
| **Profil** |**xs:ciąg znaków** |profil H264 (dotyczy tylko kodeka H264). |
| **Poziom** |**xs:ciąg znaków** |poziom H264 (dotyczy tylko kodeka H264). |
| **impulsów**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:int** |Zakodowana szerokość wideo w pikselach. |
| **Wysokość**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:int** |Zakodowana wysokość wideo w pikselach. |
| **DisplayAspectRatioNumerator**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:double** |Licznik proporcji ekranu wideo. |
| **DisplayAspectRatioDenominator**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:double** |Mianownik proporcji ekranu wideo. |
| **Framerate**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:dziesiętny** |Zmierzona liczba klatek na sekundę wideo w formacie .3f. |
| **Ramka docelowa**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:dziesiętny** |Predefiniowana docelowa liczba klatek na sekundę wideo w formacie .3f. |
| **Bitrate**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:int** |Średnia szybkość transmisji bitów wideo w kilobitach na sekundę, obliczona na podstawie pliku zasobów. Zlicza tylko ładunek strumienia elementarnego i nie obejmuje narzutów opakowania. |
| **Docelowa szybkość**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:int** |Docelowa średnia szybkość transmisji bitów dla tej ścieżki wideo, zgodnie z żądaniem za pomocą ustawień predefiniowanych kodowania, w kilobitach na sekundę. |
| **Maksymalna liczba bitów**<br/><br/> minWłącznie ="0" |**xs:int** |Maksymalna średnia szybkość transmisji bitów GOP dla tego utworu wideo, w kilobitach na sekundę. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a>Element AudioTracks
Każdy fizyczny AssetFile może zawierać w nim zero lub więcej ścieżek audio przeplatane do odpowiedniego formatu kontenera. **AudioTracks** element reprezentuje kolekcję wszystkich tych ścieżek audio.  

Przykład XML można znaleźć w [przykładzie XML](#xml).  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **AudioTrack (AudioTrack)**<br/><br/> minOccurs="1" maxOccurs="bez ograniczeń" |Określona ścieżka audio w pliku nadrzędnym AssetFile. Aby uzyskać więcej informacji, zobacz AudioTrack element. |

## <a name="audiotrack-element"></a><a name="AudioTrack"></a>Element AudioTrack
Określona ścieżka audio w pliku nadrzędnym AssetFile.  

Przykład XML można znaleźć w [przykładzie XML](#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Identyfikator**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:int** |Indeks zerowy tej ścieżki dźwiękowej. **Uwaga:**  Niekoniecznie jest to TrackID używany w pliku MP4. |
| **Kodek** |**xs:ciąg znaków** |Ciąg kodeka ścieżki dźwiękowej. |
| **Koderweryjda** |**xs:ciąg znaków** |Opcjonalny ciąg wersji kodera, wymagany dla EAC3. |
| **Kanały**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:int** |Liczba kanałów audio. |
| **SamplingRate (Szybkość próbkowania)**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:int** |Częstotliwość próbkowania dźwięku w próbkach/s lub Hz. |
| **Bitrate**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:int** |Średnia szybkość transmisji bitów audio w bitach na sekundę, obliczona na podstawie pliku assetfile. Zlicza tylko ładunek strumienia elementarnego i nie obejmuje narzutów opakowania. |
| **BitsPerSample (BitsPerSample)**<br/><br/> minWłącznie ="0"<br/><br/> Wymagany |**xs:int** |Bity na próbkę dla typu formatu wFormatTag. |

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **LoudnessMeteringParameters**<br/><br/> minOccurs="0" maxOccurs="1" |Parametry wyniku pomiaru głośności. Aby uzyskać więcej informacji, zobacz LoudnessMeteringResultParameters element. |

## <a name="loudnessmeteringresultparameters-element"></a><a name="LoudnessMeteringResultParameters"></a>LoudnessMeteringResultParametr element
Parametry wyniku pomiaru głośności.  

Przykład XML można znaleźć w [przykładzie XML](#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **DPLMVersionInformation (Informacje o DPLMVersionInformation)** |**xs:ciąg znaków** |**Dolby** profesjonalny sposób pomiaru głośności wersja zestawu do pomiaru. |
| **DialogNormalizacja**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> Wymagany |**xs:int** |DialogNormalization generowane przez DPLM, wymagane, gdy LoudnessMetering jest ustawiony |
| **ZintegrowanaLuść**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> Wymagany |**xs:float** |Zintegrowana głośność |
| **Zintegrowanajspódność**<br/><br/> Wymagany |**xs:ciąg znaków** |Zintegrowana jednostka głośności. |
| **Zintegrowanametoda łępieniagów**<br/><br/> Wymagany |**xs:ciąg znaków** |Identyfikator gating |
| **ZintegrowanaLoudnessSpeechPercentage**<br/><br/> minWłączny ="0" maxInclusive="100" |**xs:float** |Zawartość mowy w programie jako procent. |
| **SamplePeak (właśc.**<br/><br/> Wymagany |**xs:float** |Szczytowa wartość próbki bezwzględnej, od czasu zresetowania lub od czasu ostatniego wyczyszczenie, na kanał.  Jednostki są dBFS. |
| **SamplePeakJednostka**<br/><br/> fixed="dBFS"<br/><br/> Wymagany |**xs:anySimpleType** |Przykładowa jednostka piku. |
| **TruePeak (TruePeak)**<br/><br/> Wymagany |**xs:float** |Maksymalna rzeczywista wartość szczytowa, zgodnie z ITU-R BS.1770-2, od resetu lub od czasu ostatniego wyczyszczenie, na kanał. Jednostki są dBTP. |
| **TruePeakJednostka**<br/><br/> fixed="dBTP"<br/><br/> Wymagany |**xs:anySimpleType** |Prawdziwa jednostka szczytowa. |

## <a name="schema-code"></a>Kod schematu
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               elementFormDefault="qualified">  
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
                  <xs:element name="Sources">  
                    <xs:annotation>  
                      <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:attribute name="Name" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>input source file name</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
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
                            <xs:attribute name="FourCC" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video codec FourCC code</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Profile" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Level" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                            <xs:attribute name="Framerate" use="required">  
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
                            <xs:attribute name="TargetFramerate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:decimal">  
                                  <xs:minInclusive value="0"/>  
                                  <xs:fractionDigits value="3"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="TargetBitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:sequence>  
                              <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                                <xs:annotation>  
                                  <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                                </xs:annotation>  
                                <xs:complexType>  
                                  <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="DialogNormalization" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:int">  
                                        <xs:minInclusive value="-31"/>  
                                        <xs:maxInclusive value="-1"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudness" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation>Integrated loudness</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="-70"/>  
                                        <xs:maxInclusive value="10"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Gating identifier</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                    <xs:annotation>  
                                      <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="0"/>  
                                        <xs:maxInclusive value="100"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                                  </xs:attribute>  
                                </xs:complexType>  
                              </xs:element>  
                            </xs:sequence>  
                            <xs:attribute name="Id" use="required">  
                              <xs:annotation>  
                                <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Codec" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>audio track codec string</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="EncoderVersion" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="BitsPerSample" use="required">  
                              <xs:annotation>  
                                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
                <xs:attribute name="Duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:duration"/>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  



## <a name="xml-example"></a><a name="xml"></a>Przykład XML

Poniższy kod XML jest przykładem pliku metadanych danych wyjściowych.  

    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
                xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
      <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
