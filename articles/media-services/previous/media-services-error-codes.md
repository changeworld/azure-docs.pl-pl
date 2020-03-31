---
title: Kody błędów usługi Azure Media Services | Dokumenty firmy Microsoft
description: W zależności od problemów, takich jak tokeny uwierzytelniania wygasające do akcji, które nie są obsługiwane w usłudze Media Services, mogą być otrzymywane kody błędów HTTP. Ten artykuł zawiera omówienie kodów błędów interfejsu API usługi Azure Media Services w wersji 2.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f5a2dd68d86a7a38fc7f2942351c42c84742d104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887072"
---
# <a name="azure-media-services-error-codes"></a>Kody błędów usługi Azure Media Services
Podczas korzystania z usługi Microsoft Azure Media Services, mogą być odbierane kody błędów HTTP z usługi w zależności od problemów, takich jak tokeny uwierzytelniania wygasa do akcji, które nie są obsługiwane w programie Media Services. Poniżej znajduje się lista **kodów błędów HTTP,** które mogą być zwracane przez media services i możliwych przyczyn dla nich.  

## <a name="400-bad-request"></a>400 Zła prośba
Wniosek zawiera nieprawidłowe informacje i jest odrzucany z jednego z następujących powodów:

* Określono nieobsługiconą wersję interfejsu API. Aby zapoznać się z najnowszą wersją, zobacz [Instalator dla programu Media Services REST API Development](media-services-rest-how-to-use.md).
* Nie określono wersji interfejsu API usługi Media Services. Aby uzyskać informacje na temat określania wersji interfejsu API, zobacz [Odwołanie interfejsu API REST operacji usług Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Jeśli używasz .NET lub Java SDK do łączenia się z usługą Media Services, wersja interfejsu API jest określona przy każdej próbie wykonania niektórych akcji przeciwko usługom Media Services.
  > 
  > 
* Określono niezdefiniowana właściwość. Nazwa właściwości znajduje się w komunikacie o błędzie. Można określić tylko te właściwości, które są członkami danej jednostki. Zobacz [odwołanie interfejsu API interfejsu REST usługi Azure Media Services, aby](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) uzyskać listę jednostek i ich właściwości.
* Określono nieprawidłową wartość właściwości. Nazwa właściwości znajduje się w komunikacie o błędzie. Zobacz poprzednie łącze dla prawidłowych typów właściwości i ich wartości.
* Brakuje wartości właściwości i jest wymagana.
* Część określonego adresu URL zawiera złą wartość.
* Podjęto próbę aktualizacji WriteOnce właściwości.
* Podjęto próbę utworzenia zadania, które ma wejściowy zasób z podstawowym AssetFile, który nie został określony lub nie można określić.
* Podjęto próbę zaktualizowania lokalizatora sygnatury dostępu Współdzielonego. Lokalizatory sygnatury dostępu Współdzielonego można tworzyć lub usuwać tylko. Lokalizatory przesyłania strumieniowego mogą być aktualizowane. Aby uzyskać więcej informacji, zobacz [Lokalizatory](https://docs.microsoft.com/rest/api/media/operations/locator).
* Przesłano nieobsługiconą operację lub kwerendę.

## <a name="401-unauthorized"></a>401 Nieautoryzowane
Nie można uwierzytelnić żądania (przed jego autoryzacją) z jednego z następujących powodów:

* Brak nagłówka uwierzytelniania.
* Nieprawidłowa wartość nagłówka uwierzytelniania.
  * Token wygasł. 
  * Token zawiera nieprawidłowy podpis.

## <a name="403-forbidden"></a>403 Zakazane
Wniosek nie jest dozwolony z jednego z następujących powodów:

* Nie można odnaleźć konta usługi Media Services lub zostało ono usunięte.
* Konto usługi Media Services jest wyłączone, a typ żądania nie jest http get. Operacje serwisowe zwróci 403 odpowiedzi, jak również.
* Token uwierzytelniania nie zawiera informacji o poświadczeniach użytkownika: AccountName i/lub SubscriptionId. Te informacje można znaleźć w rozszerzeniu interfejsu użytkownika usługi Media Services dla konta usługi Media Services w portalu azure management portal.
* Nie można uzyskać dostępu do zasobu.
  
  * Podjęto próbę użycia programu MediaProcessor, który nie jest dostępny dla konta usługi Media Services.
  * Podjęto próbę zaktualizowania jobtemplate zdefiniowanego przez usługę Media Services.
  * Podjęto próbę nadpisania lokalizatora niektórych innych kont usługi Media Services.
  * Podjęto próbę nadpisania klucza ContentKey na innym koncie usługi Media Services.
* Nie można utworzyć zasobu z powodu przydziału usługi osiągniętego dla konta usługi Media Services. Aby uzyskać więcej informacji na temat przydziałów usług, zobacz [Przydziały i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 — Nie znaleziono
Żądanie nie jest dozwolone w zasobie z jednego z następujących powodów:

* Podjęto próbę zaktualizowania jednostki, która nie istnieje.
* Podjęto próbę usunięcia jednostki, która nie istnieje.
* Podjęto próbę utworzenia jednostki, która łączy się z encją, która nie istnieje.
* Podjęto próbę GET jednostki, która nie istnieje.
* Podjęto próbę określenia konta magazynu, które nie jest skojarzone z kontem usługi Media Services.  

## <a name="409-conflict"></a>409 Konflikt
Wniosek nie jest dozwolony z jednego z następujących powodów:

* Więcej niż jeden Plik zasobów ma określoną nazwę w zasobie.
* Podjęto próbę utworzenia drugiego podstawowego pliku zasobów w zasobie.
* Podjęto próbę utworzenia contentkey o określonym identyfikatorze już używanym.
* Podjęto próbę utworzenia lokalizatora o określonym identyfikatorze już używanym.
* Więcej niż jeden IngestManifestFile ma określoną nazwę w IngestManifest.
* Podjęto próbę połączenia drugiego szyfrowania magazynu ContentKey z zasobem zaszyfrowanym magazynem.
* Podjęto próbę połączenia tego samego contentkey z zasobem.
* Podjęto próbę utworzenia lokalizatora zasobu, którego kontener magazynu brakuje lub nie jest już skojarzony z zasobem.
* Podjęto próbę utworzenia lokalizatora zasobu, który ma już 5 lokalizatorów w użyciu. (Usługa Azure Storage wymusza limit pięciu zasad dostępu współdzielonego w jednym kontenerze magazynu).
* Łączenie konta magazynu zasobu z zestawem IngestManifestAsset nie jest tym samym, co konto magazynu używane przez nadrzędny program IngestManifest.  

## <a name="500-internal-server-error"></a>500 — wewnętrzny błąd serwera
Podczas przetwarzania żądania usługi Media Services napotyka na jakiś błąd, który uniemożliwia kontynuowanie przetwarzania. Może to być spowodowane jedną z następujących przyczyn:

* Tworzenie zasobu lub zadania kończy się niepowodzeniem, ponieważ informacje o przydziału usługi konta Media Services są tymczasowo niedostępne.
* Tworzenie kontenera magazynu obiektu Blob zasobu lub ingestmanifest kończy się niepowodzeniem, ponieważ informacje o koncie magazynu konta są tymczasowo niedostępne.
* Inny nieoczekiwany błąd.

## <a name="503-service-unavailable"></a>503 — usługa niedostępna
Serwer nie może obecnie odbierać żądań. Ten błąd może być spowodowany przez nadmierne żądania do usługi. Mechanizm ograniczania przepustowości usługi Media Services ogranicza użycie zasobów dla aplikacji, które sprawiają, że nadmierne żądanie do usługi.

> [!NOTE]
> Sprawdź komunikat o błędzie i ciąg kodu błędu, aby uzyskać bardziej szczegółowe informacje o przyczynie, że wystąpił błąd 503. Ten błąd nie zawsze oznacza ograniczanie przepustowości.
> 
> 

Możliwe opisy statusu to:

* "Serwer jest zajęty. Poprzednie przebiegi tego typu żądania {0} trwały dłużej niż sekundy."
* "Serwer jest zajęty. Więcej {0} niż żądania na sekundę mogą być ograniczane."
* "Serwer jest zajęty. Więcej {0} niż {1} żądania w ciągu kilku sekund mogą być ograniczane."

Aby obsłużyć ten błąd, zaleca się przy użyciu wykładniczej logiki ponawiania ponawiania ponawiania. Oznacza to, że przy użyciu stopniowo dłużej czeka między ponownych prób dla kolejnych odpowiedzi błędów.  Aby uzyskać więcej informacji, zobacz [Blok aplikacji obsługi błędów przejściowych](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Jeśli używasz [narzędzia Azure Media Services SDK dla platformy .Net,](https://github.com/Azure/azure-sdk-for-media-services/tree/master)logika ponawiania dla błędu 503 została zaimplementowana przez zestaw SDK.  
> 
> 

## <a name="see-also"></a>Zobacz też
[Kody błędów zarządzania usługami multimedialnymi](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

