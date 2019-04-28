---
title: Schemat metadanych danych wyjściowych w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Temat zawiera omówienie schematu metadanych danych wyjściowych usługi Azure Media Services.
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
ms.openlocfilehash: 42227095c69924cd2922673d020b349aa29f2daa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61129750"
---
# <a name="output-metadata"></a>Metadane wyjściowe
## <a name="overview"></a>Omówienie
Zadania kodowania jest skojarzony z wejściowego elementu (lub zasoby), w której chcesz wykonać kilka zadań kodowania. Na przykład kodowanie pliku MP4 do H.264 MP4 o adaptacyjnej szybkości transmisji bitów zestawów; Tworzenie miniatur; Tworzenie nakładek. Po zakończeniu zadania elementu zawartości wyjściowej jest generowany.  Elementu zawartości wyjściowej zawiera film wideo, audio, miniatury, itp. Elementu zawartości wyjściowej zawiera także plik o metadane dotyczące elementu zawartości wyjściowej. Nazwa pliku XML metadanych ma następujący format: &lt;source_file_name&gt;_manifest.xml (na przykład BigBuckBunny_manifest.xml).  

Usługi Media Services nie skanuje uprzedzając zdarzenie wejściowe zasoby do generowania metadanych. Metadane wejściowe zostanie wygenerowany tylko jako artefakt, gdy dane wejściowe, zasób jest przetwarzana w ramach zadania. Dlatego ten artefakt jest zapisywany w danych wyjściowych zasobów. Różne narzędzia są używane do generowania metadanych dla zasobów danych wejściowych i wyjściowych zasoby. Dlatego metadanych wejściowych ma schemat nieco inne niż metadanych danych wyjściowych.

Jeśli chcesz sprawdzić plik metadanych, możesz utworzyć **sygnatury dostępu Współdzielonego** lokalizatora i pobierania plików na komputerze lokalnym.  

W tym artykule omówiono elementów i typów schematu XML, na którym metada danych wyjściowych (&lt;source_file_name&gt;_manifest.xml) opiera się. Informacje o pliku, który zawiera metadane dotyczące zasobu danych wejściowych na ten temat można znaleźć w danych wejściowych metadanych.  

Można znaleźć schematu kompletny kod i XML — przykład na końcu tego artykułu.  

## <a name="AssetFiles"></a> Element główny AssetFiles
Kolekcja wpisów AssetFile dla zadania kodowania.  

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Opis |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs="0" maxOccurs="1" |Element AssetFile, który jest częścią kolekcji AssetFiles. |

## <a name="AssetFile"></a> AssetFile element
Przykład XML można znaleźć [XML — przykład](#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Nazwa**<br/><br/> Wymagane |**xs:String** |Nazwa pliku zasobu multimediów. |
| **Rozmiar**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:Long** |Rozmiar pliku elementu zawartości, w bajtach. |
| **Czas trwania**<br/><br/> Wymagane |**DURATION** |Czas trwania wstecz odtwarzania zawartości. |

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Opis |
| --- | --- |
| **Źródła** |Kolekcja danych wejściowych/źródłowych plików multimedialnych, która została przetworzona w celu utworzenia tego AssetFile. Aby uzyskać więcej informacji zobacz element źródła. |
| **VideoTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Każdy fizyczny AssetFile może zawierać w nim wideo zero lub więcej ścieżek do formatu odpowiedniego kontenera z przeplotem. Aby uzyskać więcej informacji zobacz VideoTracks element. |
| **AudioTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Każdy fizyczny AssetFile może zawierać w nim zero lub więcej ścieżki audio z przeplotem do formatu odpowiedniego kontenera. To jest kolekcja wszystkie te ścieżki audio. Aby uzyskać więcej informacji zobacz AudioTracks element. |

## <a name="Sources"></a> Element źródeł
Kolekcja danych wejściowych/źródłowych plików multimedialnych, która została przetworzona w celu utworzenia tego AssetFile.  

Przykład XML można znaleźć [XML — przykład](#xml).  

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Opis |
| --- | --- |
| **Element źródłowy**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Plik źródła danych wejściowych/używana podczas generowania tego zasobu. Aby uzyskać więcej informacji zobacz element źródła. |

## <a name="Source"></a> element źródłowy
Plik źródła danych wejściowych/używana podczas generowania tego zasobu.  

Przykład XML można znaleźć [XML — przykład](#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Nazwa**<br/><br/> Wymagane |**xs:String** |Nazwa pliku źródła danych wejściowych. |

## <a name="VideoTracks"></a> VideoTracks element
Każdy fizyczny AssetFile może zawierać w nim wideo zero lub więcej ścieżek do formatu odpowiedniego kontenera z przeplotem. **VideoTracks** element reprezentuje kolekcję wideo ścieżki.  

Przykład XML można znaleźć [XML — przykład](#xml).  

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Opis |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Określonej ścieżki wideo w elemencie nadrzędnym AssetFile. Aby uzyskać więcej informacji zobacz VideoTrack element. |

## <a name="VideoTrack"></a> VideoTrack element
Określonej ścieżki wideo w elemencie nadrzędnym AssetFile.  

Przykład XML można znaleźć [XML — przykład](#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Identyfikator**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:int** |Liczony od zera indeks to ścieżka wideo. **Uwaga:**  To **identyfikator** niekoniecznie jest TrackID w przypadku użycia w pliku MP4. |
| **FourCC**<br/><br/> Wymagane |**xs:String** |Kodera-dekodera wideo FourCC kodu. |
| **Profil** |**xs:String** |Profil H264 (dotyczy to tylko H264 koder-dekoder). |
| **Poziom** |**xs:String** |Poziom H264 (dotyczy to tylko H264 koder-dekoder). |
| **Szerokość**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:int** |Kodowany w formacie wideo szerokość w pikselach. |
| **Wysokość**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:int** |Kodowany w formacie wideo wysokość w pikselach. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:double** |Licznik współczynnik proporcji ekranu wideo. |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:double** |Denominator współczynnik proporcji ekranu wideo. |
| **szybkość klatek**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:decimal** |Mierzone szybkość odtwarzania wideo w formacie .3f. |
| **TargetFramerate**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:decimal** |Wstępnie docelowej szybkość odtwarzania wideo, w formacie .3f. |
| **Szybkość transmisji bitów**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:int** |Średnia wideo szybkość transmisji bitów w kilobitów na sekundę obliczoną z AssetFile. Zlicza tylko ładunek podstawowe strumienia, a nie ma konieczności tworzenia pakietów. |
| **TargetBitrate**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:int** |Celem średnia szybkość transmisji bitów dla tej ścieżki wideo, zgodnie z żądaniem przy użyciu kodowania ustawiony w kilobitów na sekundę. |
| **MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |Maksymalna liczba GOP średnia szybkość transmisji bitów dla tej ścieżki wideo w kilobitów na sekundę. |

## <a name="AudioTracks"></a> AudioTracks element
Każdy fizyczny AssetFile może zawierać w nim zero lub więcej ścieżki audio z przeplotem do formatu odpowiedniego kontenera. **AudioTracks** element reprezentuje kolekcję te ścieżki audio.  

Przykład XML można znaleźć [XML — przykład](#xml).  

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Opis |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Określone ścieżki audio w obiekcie nadrzędnym AssetFile. Aby uzyskać więcej informacji zobacz AudioTrack element. |

## <a name="AudioTrack"></a> AudioTrack element
Określone ścieżki audio w obiekcie nadrzędnym AssetFile.  

Przykład XML można znaleźć [XML — przykład](#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Identyfikator**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:int** |Liczony od zera indeks tej ścieżki audio. **Uwaga:**  To nie jest koniecznie TrackID w przypadku użycia w pliku MP4. |
| **Koder-dekoder** |**xs:String** |Parametry kodera-dekodera ścieżkę audio. |
| **EncoderVersion** |**xs:String** |Ciąg wersji kodera opcjonalne, wymagane do EAC3. |
| **Channels**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:int** |Liczba kanałów audio. |
| **SamplingRate**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:int** |Częstotliwość próbkowania audio w prób. / s lub Hz. |
| **Szybkość transmisji bitów**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:int** |Średnia audio szybkość transmisji bitów w bitach na sekundę obliczoną z AssetFile. Zlicza tylko ładunek podstawowe strumienia, a nie ma konieczności tworzenia pakietów. |
| **BitsPerSample**<br/><br/> minInclusive ="0"<br/><br/> Wymagane |**xs:int** |Bity na przykład wFormatTag format typu. |

### <a name="child-elements"></a>Elementy podrzędne
| Name (Nazwa) | Opis |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs="0" maxOccurs="1" |Parametry wynik pomiaru głośności. Aby uzyskać więcej informacji zobacz LoudnessMeteringResultParameters element. |

## <a name="LoudnessMeteringResultParameters"></a> LoudnessMeteringResultParameters element
Parametry wynik pomiaru głośności.  

Przykład XML można znaleźć [XML — przykład](#xml).  

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:String** |**Dolby** profesjonalnych parametrów akustycznych pomiaru development kit w wersji. |
| **DialogNormalization**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> Wymagane |**xs:int** |DialogNormalization wygenerowanych za pośrednictwem DPLM, LoudnessMetering jest ustawiona. |
| **IntegratedLoudness**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> Wymagane |**xs:float** |Zintegrowane głośności |
| **IntegratedLoudnessUnit**<br/><br/> Wymagane |**xs:String** |Jednostka zintegrowane głośności. |
| **IntegratedLoudnessGatingMethod**<br/><br/> Wymagane |**xs:String** |Uzyskania bramowego identyfikator |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs:float** |Mowy zawartości przez program, jako wartość procentowa. |
| **SamplePeak**<br/><br/> Wymagane |**xs:float** |Wartość bezwzględna przykładowa szczytu, od resetowania lub od momentu jego ostatniego wyczyszczone, na kanał.  Jednostki są dBFS. |
| **SamplePeakUnit**<br/><br/> fixed="dBFS"<br/><br/> Wymagane |**xs:anySimpleType** |Przykładowe piku jednostki. |
| **TruePeak**<br/><br/> Wymagane |**xs:float** |Wartość true, maksymalne wartości, zgodnie z BS.1770 ITU-R-2, od resetowania lub od momentu jego ostatniego wyczyszczone na kanał. Jednostki są dBTP. |
| **TruePeakUnit**<br/><br/> fixed="dBTP"<br/><br/> Wymagane |**xs:anySimpleType** |Jednostka szczytowa wartość true. |

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



## <a name="xml"></a> XML — przykład

Następujący kod XML znajduje się przykład danych wyjściowych pliku metadanych.  

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

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
