---
title: Licencjonowania Microsoft® Smooth Streaming Client Porting Kit
description: Dowiedz się więcej o tym, jak do licencjonowania programu Microsoft® Smooth Streaming klienta przenoszenie SDK.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: xpouyat
ms.openlocfilehash: 505def9cde7cddf2ddcc23408fa3159de886167a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61472728"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licencjonowania Microsoft® Smooth Streaming Client Porting Kit 
## <a name="overview"></a>Omówienie
Microsoft Smooth Streaming klienta przenoszenie Kit (**SSPK** w skrócie) jest Smooth Streaming implementacji klienta, który jest zoptymalizowany pod kątem ułatwiające producenci urządzeń osadzonych, kabel i operatorów komórkowych, dostawców usług zawartości, słuchawki producentów niezależnych dostawców oprogramowania (ISV) i dostawców rozwiązań do tworzenia produktów i usług do przesyłania strumieniowego zawartości przesyłanej w formacie Smooth Streaming. SSPK to urządzenie i niezależne od platformy implementacji klienta Smooth Streaming, które mogą być przenoszone przez licencjobiorcę do dowolnego urządzenia i platformy. 

Przedstawiony poniżej jest Architektura wysokiego poziomu i pole IIS Smooth Streaming przenoszenie Kit jest udostępniana przez firmę Microsoft implementacją Smooth Streaming Client i obejmuje wszystkie podstawowe zasady logiczne do odtwarzania zawartości Smooth Streaming. Ta zawartość następnie są przenoszone przez partnerów dla określonego urządzenia lub platformy z zastosowaniem odpowiednich interfejsów. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Opis
SSPK jest licencjonowane na warunkach, które oferują doskonałą wymierne efekty. Licencja SSPK zapewnia branży ubezpieczeniowej:

* Bezproblemowe źródła przesyłania strumieniowego Porting Kit w języku C++ 
  * implementuje funkcje Smooth Streaming Client
  * dodaje formatu podczas analizowania Algorytm heurystyczny, buforowanie logiki itp.
* Aplikacja odtwarzacza interfejsów API 
  * interfejsy programowania w celu interakcji z aplikacji odtwarzacza multimediów
* Interfejs (PAL) warstwy abstrakcji platformy 
  * interfejsy programowania do interakcji z systemem operacyjnym (wątków, sockets)
* Interfejsu (HAL) warstwy abstrakcji sprzętu 
  * interfejsy do interakcji ze sprzętem A / V dekodery (dekodowanie, renderowanie)
* Digital Rights Management (DRM) interfejsu 
  * interfejsy programowania obsługę DRM za pośrednictwem warstwy abstrakcji DRM (DAL)
  * Microsoft PlayReady Porting Kit jest dostarczany oddzielnie, ale integruje się za pośrednictwem tego interfejsu. Aby uzyskać szczegółowe informacje na temat licencjonowania Device PlayReady firmy Microsoft, kliknij [tutaj](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Przykłady implementacji 
  * przykład implementacji PAL dla systemu Linux
  * przykład implementacji HAL dla GStreamer

## <a name="licensing-options"></a>Opcje licencjonowania
Microsoft Smooth Streaming klienta przenoszenie Kit są udostępniane Licencjobiorcy w dwóch odrębnych licencjami: jeden do tworzenia Smooth produkty przejściowej klienckie przesyłania strumieniowego i inny wpis dla dystrybucji Smooth Streaming klienta końcowego produktów dla użytkowników końcowych.

* Dla producentów mikroukładu integratorzy systemów i niezależni dostawcy oprogramowania dostawcy (ISV), którzy muszą mieć źródło kodu przenoszenia kit do tworzenia produktów przejściowej, Microsoft Smooth Streaming klienta przenoszenie zestaw **licencję na produkt przejściowej** powinna zostać wykonana.
* Producenci urządzeń lub niezależnych dostawców oprogramowania, którzy potrzebują prawami dystrybucji Smooth Streaming klienta końcowego produktów do użytkowników końcowych, Microsoft Smooth Streaming klienta przenoszenie Kit **licencji produktu końcowego** mają zostać wykonane.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming Client Porting Kit przejściowej licencji produktu
Postanowieniami niniejszej licencji, firma Microsoft oferuje Smooth Streaming klienta przenoszenie zestaw i niezbędne własności intelektualnej można opracować i rozpowszechnić Smooth produkty przejściowej klienckie przesyłania strumieniowego innym licencjobiorcom urządzenia Smooth Streaming klienta przenoszenie Kit, Dystrybuuj Smooth Streaming końcowego produkty klienckie.

#### <a name="fee-structure"></a>Struktura opłata
Opłata jednorazowe licencji 50 000 zł USA udostępnia Smooth Streaming klienta Porting Kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming Client Porting Kit końcowa licencji produktu
W ramach niniejszej licencji firma Microsoft oferuje wszelkie prawa własności intelektualnej niezbędne do odbierania Smooth produkty przejściowej klienckie przesyłania strumieniowego z innym licencjobiorcom Smooth Streaming klienta przenoszenie Kit i rozpowszechniać oznaczony marką firmy Smooth Streaming klienta końcowego Produkty do użytkowników końcowych.

#### <a name="fee-structure"></a>Struktura opłata
Smooth Streaming klienta finalnym produktem, jest oferowana w ramach modelu licencjonowanych w postaci w obszarze:

* 0,10 USD na implementację urządzenia wysłane
* Uzyskiwana jest ograniczone do 50 000 zł każdego roku
* Nie licencjonowanych pierwszych 10 000 urządzeń implementacji każdego roku 

## <a name="licensing-procedure-and-sspk-access"></a>Procedurę licencjonowania i SSPK dostępu
Adres e-mail [ sspkinfo@microsoft.com ](mailto:sspkinfo@microsoft.com) dla wszystkich licencji zapytania.

[Portal dystrybucji SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) jest dostępny dla zarejestrowanego licencjobiorców przejściowej.

Przejściowe i końcowe SSPK licencjobiorców mogą przesyłać pytania techniczne na [ smoothpk@microsoft.com ](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming licencjobiorców umowy pośrednie produktu klienta
* Adroit Business Solutions, Inc
* Zaawansowane SA emisji cyfrowej
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* EchoStar Corporation zakupu
* Enseo, Inc.
* Fluendo S.A.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* iWEDIA S.A. 
* Liberty Global Services BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Szafran cyfrowy Limited
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung technologii Inc.
* TCL Technology Electronics (Huizhou) Co., Ltd.
* TOP zwycięstwa inwestycje, Ltd.
* Ve Vestel Elektronik Sanayi Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Smooth Streaming licencjobiorców umowy produktu końcowego klienta
* Zaawansowane SA emisji cyfrowej
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* British Sky Broadcasting Limited
* Technologia CastPal, Inc., Shenzhen
* Compal Electronics, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar Corporation zakupu
* Enseo, Inc.
* FilmFlex Movies Limited
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Gibson innowacje Limited
* Haier informacji Applicantion S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Nintendo Co., Ltd.
* Pomarańczowy SA
* Szafran cyfrowy Limited
* Połączenie szerokopasmowe Sagemcom sygnatury dostępu Współdzielonego
* Shenzhen Coship Electronics CO., Ltd.
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin przemysłowej Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* Ograniczone TCL Zbiorowość Marketing (urządzeniach oddalonych od brzegu komercyjne SRA Makau)
* Technicolor dostarczanie technologii, sygnatury dostępu Współdzielonego
* Tongfang Global Ltd.
* TOP zwycięstwa inwestycje, Ltd.
* Toshiba Lifestyle Products & Services Corporation
* Universal Media Corporation /Slovakia/ s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

