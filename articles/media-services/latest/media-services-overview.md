---
title: Omówienie usługi Azure Media Services w wersji 3 | Microsoft Docs
description: Ten artykuł zawiera ogólne omówienie usługi Media Services i zapewnia linki do artykułów zawierających więcej szczegółów.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/27/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 33afe5fb24309547a7aacfcbe3b799ed413594ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-media-services-v3"></a>Co to jest usługa Azure Media Services w wersji 3?

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Wersja 2 — ogólnie dostępna](../previous/media-services-overview.md)
> * [Wersja 3 — wersja zapoznawcza](media-services-overview.md)

> [!NOTE]
> Najnowsza wersja usługi Azure Media Services jest w wersji zapoznawczej i może być nazywana wersją 3.

Azure Media Services to oparta na chmurze platforma umożliwiająca tworzenie rozwiązań pozwalających na osiąganie jakości odpowiedniej do emisji w przypadku przesyłania strumieniowego wideo, zwiększanie dostępności i dystrybucji, analizowanie zawartości i wykonywanie wielu innych operacji. Niezależnie od tego, czy jesteś deweloperem aplikacji, biurem obsługi, agencją rządową, czy też firmą rozrywkową, usługa Media Services pomaga tworzyć aplikacje, które zapewniają środowisko multimedialne o najwyższej jakości dużej liczbie odbiorców na obecnie najpopularniejszych urządzeniach przenośnych i przeglądarkach. 

## <a name="what-can-i-do-with-media-services"></a>Co mogę zrobić za pomocą usługi Media Services?

Usługa Media Services umożliwia tworzenie różnych przepływów pracy multimediów w chmurze, a poniżej przedstawiono kilka przykładów tego, co można uzyskać za pomocą usługi Media Services.  

* Dostarczaj wideo w różnych formatach, aby można je było odtworzyć w różnych przeglądarkach i na różnych urządzeniach. Aby umożliwić zarówno dostarczanie na żądanie, jak i transmitowanie strumieniowo na żywo do różnych klientów (urządzeń przenośnych, telewizorów, komputerów itp.), treści wideo i audio muszą być odpowiednio zakodowane oraz zapakowane. Aby poznać sposób dostarczania i strumieniowego przesyłania takiej zawartości, zobacz [Szybki start: kodowanie i przesyłanie strumieniowe plików](stream-files-dotnet-quickstart.md).
* Przesyłaj strumieniowo do wielu odbiorców online imprezy sportowe, takie jak mecze piłki nożnej i baseballa, zawody międzyuczelniane lub międzyszkolne itd. 
* Prowadź emisję publicznych spotkań i wydarzeń, np. odbywających się w urzędach miejskich, albo spotkań rady miejskiej czy komisji.
* Analizuj nagrania wideo lub zawartość audio. Aby na przykład osiągnąć wyższe zadowolenie klientów, organizacje mogą wyodrębnić mowę jako tekst i tworzyć indeksy wyszukiwania oraz pulpity nawigacyjne. Następnie mogą oni wyodrębnić informacje dotyczące typowych skarg, źródeł skarg i inne odpowiednie dane. 
* Utwórz serwis wideo subskrypcji i przesyłaj strumieniowo zawartość chronioną przez technologię DRM, gdy klient (na przykład studio filmowe) chce ograniczyć dostęp i używanie zastrzeżonych prac objętych prawami autorskimi.
* Dostarczaj zawartość offline do odtwarzania w samolotach, pociągach i samochodach. Klient może pobrać zawartość do odtworzenia na swój telefon lub tablet, jeśli przewiduje, że zostanie odłączony od sieci.
* Dodaj napisy i podpisy do filmów, aby spełnić potrzeby szerszego grona odbiorców (na przykład osób z wadami słuchu lub osób chcących jednocześnie czytać w innym języku). 
* Wdróż edukacyjną platformę wideo ze szkoleniami internetowymi za pomocą usługi Azure Media Services i [interfejsów API usług Azure Cognitive Services](https://docs.microsoft.com/en-us/azure/#pivot=products&panel=ai) w celu przekształcania mowy na tekst podpisów, tłumaczenia na wiele języków, itp.
* Włącz usługę Azure CDN, aby osiągnąć wysoką skalowalność w celu zapewnienia lepszej obsługi błyskawicznego zwiększenia obciążenia (na przykład na początku wydarzenia prezentującego nowy produkt). 

## <a name="v3-capabilities"></a>Możliwości wersji 3

Wersja 3 opiera się na ujednoliconym interfejsie API powierzchni, który udostępnia zarówno funkcje zarządzania, jak i operacji oparte na usłudze **Azure Resource Manager**. Ta wersja oferuje następujące możliwości:  

* **Przekształcenia**, które pomagają definiować proste przepływy pracy zadań przetwarzania lub analizy multimediów. Przekształcenie to przepis na przetwarzanie plików audio i wideo. Następnie możesz wielokrotnie je stosować, aby przetworzyć wszystkie pliki w Twojej bibliotece zawartości, przesyłając zadania do przekształcenia.
* **Zadania** przetwarzania (kodowania lub analizowania) Twoich plików wideo. Zawartość wejściową można określić dla zadania przy użyciu adresów URL HTTP, adresów URL SAS lub ścieżek do plików znajdujących się w magazynie obiektów blob platformy Azure. 
* **Powiadomienia**, które monitorują postęp zadania lub stany albo uruchomienie/zatrzymanie kanału na żywo i błędy. Powiadomienia są zintegrowane z systemem powiadomień usługi Azure Event Grid. Możesz łatwo subskrybować zdarzenia dla kilku zasobów w usłudze Azure Media Services. 
* Szablony usługi **Azure Resource Management** mogą służyć do tworzenia i wdrażania przekształceń, punktów końcowych przesyłania strumieniowego, kanałów itd.
* **Kontrola dostępu oparta na rolach** może zostać ustawiona na poziomie zasobów, co umożliwia zablokowanie dostępu do określonych zasobów, takich jak przekształcenia, kanały i inne.
* **Zestawy SDK klientów** w wielu językach: .NET, .NET Core, Python, Go, Java i Node.js.

## <a name="how-can-i-get-started-with-v3"></a>Jak można zacząć korzystać z wersji 3?

Jako deweloper możesz użyć [interfejsu API REST](https://go.microsoft.com/fwlink/p/?linkid=873030) usługi Media Services lub bibliotek klienckich, które umożliwiają interakcję z interfejsem API REST, aby łatwo tworzyć i utrzymywać niestandardowe przepływy pracy multimediów oraz zarządzać nimi. Firma Microsoft generuje i obsługuje następujące biblioteki klientów: 

* [Interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Języki .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)
* .NET Core 
* Java

  Dodaj następujące zależności w swoim projekcie:
  
  ```
  <dependency>
    <groupId>com.microsoft.azure.media-2018-03-30-preview</groupId>
    <artifactId>azure-mgmt- media</artifactId>
    <version>0.0.1-beta</version>
  </dependency> 
  ```
* Node.js 

  Użyj następującego polecenia:
  
  ```
  npm install azure-arm-mediaservices
  ```
  
* [Python](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)
* [Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)

Usługa Media Services udostępnia [pliki Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media), których możesz używać do generowania zestawów SDK dla preferowanego przez Ciebie języka/technologii.  

## <a name="next-steps"></a>Następne kroki

Aby sprawdzić, jak łatwo rozpocząć kodowanie i strumieniowe przesyłanie plików wideo, zobacz [Strumieniowe przesyłanie plików](stream-files-dotnet-quickstart.md). 

