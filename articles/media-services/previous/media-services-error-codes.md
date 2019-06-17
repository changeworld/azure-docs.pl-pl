---
title: Kody błędów usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Temat zawiera omówienie kody błędów usługi Azure Media Services.
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
ms.openlocfilehash: f3c362730e7908e88b363659b7fa580b6f2cddf1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217237"
---
# <a name="azure-media-services-error-codes"></a>Kody błędów usługi Azure Media Services
Korzystając z usługi Microsoft Azure Media Services, usługi, w zależności od problemów, takich jak tokeny uwierzytelniania wygasa do akcji, które nie są obsługiwane w usłudze Media Services może zostać wyświetlony kody błędów HTTP. Poniżej przedstawiono listę **kody błędów HTTP** , mogą być zwrócone przez usługi Media Services i możliwych przyczyn ich.  

## <a name="400-bad-request"></a>400 Niewłaściwe żądanie
Żądanie zawiera nieprawidłowe informacje i zostanie odrzucony, ze względu na jedną z następujących powodów:

* Określono nieobsługiwaną wersję interfejsu API. Aby uzyskać najbardziej aktualną wersję, zobacz [Instalatora w celu tworzenia interfejsu API REST usługi Media](media-services-rest-how-to-use.md).
* Nie określono wersji interfejsu API usługi Media Services. Aby uzyskać informacje na temat sposobu określenia wersji interfejsu API, zobacz [dokumentacja interfejsu API REST operacje usług Media](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Jeśli używasz platformy .NET lub Java SDK do łączenia z usługą Media Services, wersja interfejsu API jest określona dla możesz zawsze wtedy, gdy spróbujesz teraz wykonanie akcji względem usługi Media Services.
  > 
  > 
* Podano niezdefiniowane właściwości. Nazwa właściwości jest komunikat o błędzie. Można określić tylko te właściwości, które są członkami danej jednostki. Zobacz [dokumentacja interfejsu API REST usługi multimediów Azure](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) listę jednostek i ich właściwości.
* Określono nieprawidłową wartość właściwości. Nazwa właściwości jest komunikat o błędzie. Zobacz poprzedni link dla typów prawidłowej właściwości i ich wartości.
* Wartość właściwości nie istnieje i jest wymagany.
* Część określony adres URL zawiera nieprawidłowe wartości.
* Nastąpiła próba można zaktualizować właściwości WriteOnce.
* Próbowano utworzyć zadanie, które ma danych wejściowych zasobu ze wszystkimi AssetFile podstawowego, który nie został określony lub nie można określić.
* Nastąpiła próba aktualizacji lokalizatora sygnatury dostępu Współdzielonego. Lokalizatory sygnatury dostępu Współdzielonego tylko można utworzyć ani usunąć. Lokalizatory przesyłania strumieniowego może zostać zaktualizowana. Aby uzyskać więcej informacji, zobacz [Lokalizatory](https://docs.microsoft.com/rest/api/media/operations/locator).
* Nieobsługiwana operacja lub zapytanie zostało przesłane.

## <a name="401-unauthorized"></a>401 Brak autoryzacji
Nie można uwierzytelnić żądania, (zanim może być autoryzowane) z jednego z następujących powodów:

* Brak nagłówka uwierzytelniania.
* Wartość nagłówka nieprawidłowe uwierzytelnianie.
  * Token wygasł. 
  * Token ma nieprawidłowy podpis.

## <a name="403-forbidden"></a>403 Zabroniony
Żądanie jest niedozwolona z powodu jednej z następujących powodów:

* Konto usługi Media Services nie można odnaleźć lub została usunięta.
* Konto usługi Media Services jest wyłączona, a żądania nie jest to typ HTTP GET. Operacje usługi zwróci odpowiedź 403 także.
* Token uwierzytelniania nie zawiera informacji o poświadczeniach użytkownika: Nazwa konta i/lub identyfikator subskrypcji. Te informacje można znaleźć w rozszerzeniu usługi Media Services w interfejsie użytkownika dla konta usługi Media Services w portalu zarządzania Azure.
* Nie można uzyskać dostępu do zasobu.
  
  * Próbowano użyć MediaProcessor, który nie jest dostępny dla Twojego konta usługi Media Services.
  * Aby zaktualizować obiekt JobTemplate zdefiniowane przez usługę Media Services została podjęta próba.
  * Próbowano zastąpić niektóre inne konto usługi Media Services firmy lokalizatora.
  * Nastąpiła próba zastąpienia ContentKey niektóre inne konto usługi Media Services firmy.
* Nie można utworzyć zasobu z powodu przydział usługi, który został osiągnięty dla konta usługi Media Services. Aby uzyskać więcej informacji na temat przydziałów usługi, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 — Nie odnaleziono
Żądanie nie jest dozwolona w zasobie z jednego z następujących powodów:

* Nastąpiła próba do zaktualizowania jednostki, która nie istnieje.
* Próbowano usunąć jednostki, która nie istnieje.
* Próbowano utworzyć obiekt, który stanowi łącze do jednostki, która nie istnieje.
* Nastąpiła próba pobrania jednostki, który nie istnieje.
* Nastąpiła próba, aby określić konto magazynu, który nie jest skojarzony z kontem usługi Media Services.  

## <a name="409-conflict"></a>409 Konflikt
Żądanie jest niedozwolona z powodu jednej z następujących powodów:

* Więcej niż jeden AssetFile o określonej nazwie w elemencie zawartości.
* Próbowano utworzyć drugi AssetFile podstawowy, w ramach zasobu.
* Próbowano utworzyć ContentKey o określonym identyfikatorze już używana.
* Próbowano utworzyć Lokalizator o określonym identyfikatorze już używana.
* Więcej niż jeden IngestManifestFile o określonej nazwie w ramach IngestManifest.
* Nastąpiła próba połączyć drugiego szyfrowania magazynu ContentKey szyfrowane magazynu trwałego.
* Nastąpiła próba połączyć ContentKey tego samego zasobu.
* Próbowano utworzyć lokalizatora do elementu zawartości kontenera magazynu, których brakuje lub nie jest już skojarzony z elementem zawartości.
* Próbowano utworzenie lokalizatora do elementu zawartości, która ma już lokalizatorów 5 w użyciu. (Usługa azure Storage wymusza limit pięciu zasady dostępu współdzielonego na jeden kontener magazynu).
* Łączenie konta magazynu trwałego IngestManifestAsset nie jest taka sama jak konto magazynu używane przez nadrzędne IngestManifest.  

## <a name="500-internal-server-error"></a>500 Wewnętrzny błąd serwera
Podczas przetwarzania żądania usługi Media Services napotka błąd uniemożliwiający przetwarzania za kontynuację. Może to być spowodowane jedną z następujących przyczyn:

* Tworzenie zasobu lub zadanie nie powiedzie się, ponieważ informacje o limitach przydziału usługi konta usługi Media Services jest tymczasowo niedostępna.
* Tworzenie zasobu lub IngestManifest kontenera magazynu obiektów blob nie powiedzie się, ponieważ informacje o koncie magazynu dla konta jest tymczasowo niedostępna.
* Inne wystąpił nieoczekiwany błąd.

## <a name="503-service-unavailable"></a>503 — usługa niedostępna
Serwer nie może obecnie odbierać żądań. Ten błąd może być spowodowane zbyt żądania do usługi. Usługa Media Services, mechanizm ograniczania ogranicza użycie zasobów dla aplikacji, które wysyłać nadmierne żądania do usługi.

> [!NOTE]
> Sprawdź komunikat o błędzie i ciągu kodu błędu, aby uzyskać bardziej szczegółowe informacje dotyczące przyczyny, że stało się błąd 503. Ten błąd nie zawsze oznacza ograniczania przepustowości.
> 
> 

Opisy możliwych stanów są:

* "Serwer jest zajęty. Poprzednie uruchomienia tego typu żądania zajęło więcej niż {0} sekund. "
* "Serwer jest zajęty. Więcej niż {0} żądań na sekundę mogą być ograniczone. "
* "Serwer jest zajęty. Więcej niż {0} żądań w ciągu {1} sekund, które mogą być ograniczone. "

Aby obsłużyć tego błędu, zaleca się przy użyciu logiki ponawiania wykładniczego wycofywania. Oznacza to, za pomocą stopniowo dłużej oczekiwania między ponownymi próbami dla kolejnych odpowiedzi na błędy.  Aby uzyskać więcej informacji, zobacz [blok aplikacji obsługi błędów przejściowych](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Jeśli używasz [Azure Media Services SDK dla platformy .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), Logika ponawiania dla błąd 503 została zaimplementowana przez zestaw SDK.  
> 
> 

## <a name="see-also"></a>Zobacz też
[Kody błędów zarządzania usługi Media Services](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

