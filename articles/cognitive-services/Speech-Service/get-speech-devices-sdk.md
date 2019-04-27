---
title: Pobieranie zestawu Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Usługi mowy współpracować z szerokiej gamy urządzeń i źródła audio. Teraz możesz korzystać z aplikacji mowy na wyższy poziom dzięki dopasowane sprzętu i oprogramowania. W tym artykule dowiesz się, jak uzyskać dostęp do zestawu Speech Devices SDK i zacznij programować.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 3c5874625ee9d1932c401238c1586ad89d5d206d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540124"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Pobieranie usług Cognitive Services Speech Devices SDK

Zestawu Speech Devices SDK jest ograniczone w wersji zapoznawczej i wymaga zarejestrowania się w programie. Obecnie usługa Microsoft preferuje dużych firm jako kandydatów, aby uzyskać dostęp do tego produktu.

## <a name="request-access"></a>Żądaj dostępu

Aby uzyskać dostęp do zestawu Speech Devices SDK:

1. Przejdź do zestawu Microsoft Speech Devices SDK [formularz rejestracji](https://aka.ms/sdsdk-signup).
1. Odczyt [umowę licencyjną](speech-devices-sdk-license.md).
1. Jeśli wyrażasz zgodę na warunki umowy licencyjnej, zaznacz **zgodę**.
1. Odpowiedz na pytania w formularzu.
1. Prześlij formularz.
1. Jeśli Twój adres e-mail nie jest już częścią usługi Azure Active Directory (Azure AD), otrzymasz wiadomość e-mail z zaproszeniem, jak w poniższym przykładzie, po zatwierdzeniu dostępu. Jeśli Twój adres e-mail jest już w usłudze Azure AD, otrzymasz wiadomość e-mail od zespołu Microsoft Speech po zatwierdzeniu dostępu i możesz przejść od razu do [pobierania zestawu Speech Devices SDK](#download-the-speech-devices-sdk).

## <a name="approval-e-mail"></a>Wiadomość e-mail zatwierdzenia

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com>
Subject: You're invited to the Microsoft organization
```

![wiadomości e-mail](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>Zaakceptuj dostępu

Wykonaj poniższe kroki, aby sprzężenia Azure AD za pomocą adresu e-mail, podany podczas rejestracji. Ten proces daje dostęp do zestawu Speech Devices SDK [witryny pobierania plików](https://shares.datatransfer.microsoft.com/).

1. W wiadomości e-mail, otrzymasz, wybierz **wprowadzenie**. Jeśli Twoja organizacja jest już klientów usługi Office 365, zostanie wyświetlony monit o Zaloguj się i możesz przejść od razu do kroku 7.

2. W oknie przeglądarki, zaznacz **dalej**.

    ![okno uwierzytelniania](media/speech-devices-sdk/get-sdk-2.png)

3. Jeśli nie masz jeszcze jeden, Utwórz konto Microsoft. Wprowadź tego samego adresu e-mail, na którym odebrano wiadomość e-mail z zaproszeniem.

    ![Tworzenie konta Microsoft](media/speech-devices-sdk/get-sdk-3.png)

4. Wybierz **dalej** hasła.

5. Po wyświetleniu monitu, aby sprawdzić pocztę e-mail, uzyskiwanie wiadomości e-mail z zaproszeniem, otrzymany kod weryfikacyjny.

7. Wklej lub wpisz kod zabezpieczający z wiadomości e-mail w oknie dialogowym. W tym przykładzie, kod zabezpieczeń to **8406**. Wybierz opcję **Dalej**.

    ![Sprawdź adres e-mail](media/speech-devices-sdk/get-sdk-6.png)

8. Gdy pojawi się aplikacja panelu dostępu w przeglądarce, została potwierdzona, że Twój adres e-mail jest częścią usługi Azure AD. Masz teraz dostęp do witryny pobierania zestawu Speech Devices SDK.

## <a name="download-the-speech-devices-sdk"></a>Pobierz zestaw Speech Devices SDK

Przejdź do [witryny pobierania zestawu Speech Devices SDK](https://shares.datatransfer.microsoft.com/). Zaloguj się przy użyciu konta Microsoft, która została utworzona wcześniej.

![Witryna pobierania zestawu SDK](media/speech-devices-sdk/get-sdk-7.png)

Aby pobrać mowy skojarzonych urządzeń zestawu SDK, przykładowy kod i materiałów referencyjnych:

1. Pobierz i zainstaluj narzędzie połączenia usługi Aspera, po wyświetleniu monitu w przeglądarce.

    ![Pobierz połączyć usługi Aspera](media/speech-devices-sdk/get-sdk-8.png)

1. Wybierz **tak** do przełączania aplikacji do łączenia z usługi Aspera.

    ![Przełącz się do łączenia usługi Aspera](media/speech-devices-sdk/get-sdk-9.png)

1. Wybierz **Zezwalaj** aby upewnić się, pobieranie plików przy użyciu połączenia usługi Aspera.

    ![Pobieranie przy użyciu połączenia usługi Aspera](media/speech-devices-sdk/get-sdk-10.png)

1. Zamknij okno transfery połączyć usługi Aspera, po pobraniu plików.

    ![Okno transfery połączyć usługi Aspera](media/speech-devices-sdk/get-sdk-11.png)

Domyślnie pliki są pobierane do Twojego **pliki do pobrania** folderu. Teraz można Wyloguj się z tej lokacji.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do zestawu Speech Devices SDK](speech-devices-sdk-qsg.md)
