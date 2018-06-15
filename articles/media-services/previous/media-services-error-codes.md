---
title: Kody błędów w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Temat zawiera omówienie usługi Azure Media Services kody błędów.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: 8a374393a6a5b3d563a441654b7b5df8a510f304
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788767"
---
# <a name="azure-media-services-error-codes"></a>Kody błędów w usłudze Azure Media Services
Korzystając z usługi Microsoft Azure Media Services, usługi, w zależności od problemów, takich jak tokeny uwierzytelniania wygasa do akcji, które nie są obsługiwane w usłudze Media Services może zostać wyświetlony kody błędów HTTP. Poniżej przedstawiono listę **kody błędów HTTP** które mogą być zwrócone przez usługi Media Services i możliwe przyczyny dla nich.  

## <a name="400-bad-request"></a>400 Niewłaściwe żądanie
Żądanie zawiera nieprawidłowe informacje i zostało odrzucone z jednego z następujących powodów:

* Określono nieobsługiwaną wersję interfejsu API. Aktualna wersja dla [ustawień dla rozwoju interfejsu API REST usługi Media](media-services-rest-how-to-use.md).
* Nie określono wersji interfejsu API usługi Media Services. Aby uzyskać informacje dotyczące sposobu określania wersji interfejsu API, zobacz [dokumentacja interfejsu API REST Media Services operacji](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Jeśli połączyć z usługą Media Services przy użyciu platformy .NET lub zestawów SDK Java, wersja interfejsu API jest określony dla Ciebie za każdym razem spróbuj i wykonanie akcji dla usługi Media Services.
  > 
  > 
* Niezdefiniowane właściwości został określony. Nazwa właściwości jest w komunikacie o błędzie. Można określić tylko właściwości, które są członkami danej jednostki. Zobacz [dokumentacja interfejsu API REST usługi Azure Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) listę obiektów i ich właściwości.
* Określono nieprawidłową wartość właściwości. Nazwa właściwości jest w komunikacie o błędzie. Zobacz poprzedniej konsolidacji dla typów prawidłowej właściwości i ich wartości.
* Wartość właściwości nie istnieje i jest wymagany.
* Część określony adres URL zawiera nieprawidłowe wartości.
* Nastąpiła próba zaktualizować właściwości WriteOnce.
* Próbowano utworzyć zadanie, które ma wejściowych zasobu z AssetFile głównej, która nie została określona lub nie można określić.
* Nastąpiła próba aktualizacji lokalizatora SAS. Lokalizatory sygnatury dostępu Współdzielonego tylko można utworzyć ani usunąć. Przesyłanie strumieniowe lokalizatorów można aktualizować. Aby uzyskać więcej informacji, zobacz [Lokalizatory](https://docs.microsoft.com/rest/api/media/operations/locator).
* Nieobsługiwana operacja lub zapytanie zostało przesłane.

## <a name="401-unauthorized"></a>401 nieautoryzowane
Nie można uwierzytelnić żądania (przed może być upoważnionych) z jednego z następujących powodów:

* Brak nagłówka uwierzytelniania.
* Wartość nagłówka uwierzytelnienia zła.
  * Token utracił ważność. 
  * Token ma nieprawidłowy podpis.

## <a name="403-forbidden"></a>403 Zabroniony
Żądanie nie jest dozwolone z jednego z następujących powodów:

* Konto usługi Media Services nie można odnaleźć lub została usunięta.
* Konto usługi Media Services jest wyłączona, a typ żądania nie jest HTTP GET. Operacje usługi zwróci również 403 odpowiedzi.
* Token uwierzytelniania nie zawiera informacji o poświadczeniach użytkownika: Nazwa konta i/lub identyfikator subskrypcji. Informacje te można znaleźć w rozszerzeniu interfejsu użytkownika usługi multimediów dla konta usługi Media Services w portalu zarządzania Azure.
* Nie można uzyskać dostępu do zasobu.
  
  * Próbowano użyć MediaProcessor, który nie jest dostępny dla konta usługi Media Services.
  * Nastąpiła próba aktualizacji obiekt JobTemplate zdefiniowany przez usługę Media Services.
  * Próbowano zastąpić lokalizatora niektóre inne konta usługi Media Services firmy.
  * Próbowano zastąpić ContentKey niektóre inne konta usługi Media Services firmy.
* Nie można utworzyć zasobu z powodu przydziału usługi, który został osiągnięty dla konta usługi Media Services. Aby uzyskać więcej informacji na przydziały usługi, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 — Nie odnaleziono
Żądanie nie jest dozwolona w zasobie z jednego z następujących powodów:

* Nastąpiła próba zaktualizować jednostki, która nie istnieje.
* Nastąpiła próba można usunąć jednostki, która nie istnieje.
* Próbowano utworzyć jednostki prowadzący do jednostki, która nie istnieje.
* Została podjęta próba pobrania jednostki, która nie istnieje.
* Aby określić konto magazynu, który nie jest skojarzony z kontem usługi Media Services została podjęta próba.  

## <a name="409-conflict"></a>409 Konflikt
Żądanie nie jest dozwolone z jednego z następujących powodów:

* Więcej niż jeden AssetFile ma określoną nazwę, w ramach zasobu.
* Próbowano utworzyć drugi AssetFile głównej w ramach zasobu.
* Próbowano utworzyć ContentKey o określonym identyfikatorze już używana.
* Próbowano utworzyć Lokalizator o określonym identyfikatorze już używana.
* Więcej niż jeden IngestManifestFile ma określoną nazwę w IngestManifest.
* Nastąpiła próba połączyć drugi szyfrowanie magazynu ContentKey szyfrowane magazynu trwałego.
* Nastąpiła próba połączyć ContentKey tego samego zasobu.
* Próbowano utworzyć trwały, którego kontenera magazynu nie istnieje lub nie jest już skojarzona z trwałym.
* Próbowano utworzyć do elementu zawartości, który ma już lokalizatorów 5 w użyciu. (Usługa azure Storage wymusza limit pięciu zasady dostępu współużytkowanego na jeden kontener magazynu).
* Łączenie z kontem magazynu trwałego do IngestManifestAsset nie jest taka sama, co konto magazynu używane przez element nadrzędny IngestManifest.  

## <a name="500-internal-server-error"></a>500 — wewnętrzny błąd serwera
Podczas przetwarzania żądania usługi Media Services napotka błąd uniemożliwiający przetwarzania przed kontynuowaniem. Może to być spowodowane jedną z następujących przyczyn:

* Tworzenie zasobu lub zadanie nie powiedzie się, ponieważ informacje o limicie przydziału usługi konta usługi Media Services jest tymczasowo niedostępna.
* Tworzenie zasobów lub IngestManifest kontenera magazynu obiektów blob nie powiedzie się, ponieważ informacje o koncie magazynu konta jest tymczasowo niedostępna.
* Inne wystąpił nieoczekiwany błąd.

## <a name="503-service-unavailable"></a>503 — usługa niedostępna
Serwer nie może obecnie odbierać żądań. Przyczyną tego błędu może być nadmiernego żądań do usługi. Usługa Media Services mechanizm ograniczania ogranicza użycie zasobów dla aplikacji, które należy nadmiernego żądania do usługi.

> [!NOTE]
> Sprawdź komunikat o błędzie i ciąg kodu błędu, aby uzyskać bardziej szczegółowe informacje o przyczynie się, że masz błąd 503. Ten błąd nie zawsze oznacza ograniczania.
> 
> 

Opisy stanu możliwe są:

* "Serwer jest zajęty. Poprzednie uruchamia tego typu żądania zajęło więcej niż {0} sekund. "
* "Serwer jest zajęty. Więcej niż {0} żądań na sekundę mogą ograniczany. "
* "Serwer jest zajęty. Więcej niż {0} żądań w ramach {1} sekund można ograniczany. "

Do obsługi tego błędu, firma Microsoft zaleca używanie Logika ponawiania wykładniczego wycofywania. Oznacza to, za pomocą oczekiwania stopniowo dłużej między kolejnymi próbami kolejnych odpowiedzi.  Aby uzyskać więcej informacji, zobacz [bloku aplikacji obsługi błędów przejściowych](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Jeśli używasz [Azure Media Services SDK dla platformy .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), logikę ponawiania błąd 503 zostało zaimplementowane przez zestaw SDK.  
> 
> 

## <a name="see-also"></a>Zobacz też
[Kody błędów zarządzania usługi multimediów](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

