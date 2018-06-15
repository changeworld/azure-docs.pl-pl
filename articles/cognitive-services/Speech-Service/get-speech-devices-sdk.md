---
title: Urządzenia mowy zestawu SDK | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać dostęp do zestawu SDK urządzenia mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 4706ea623dccd2dbb4164bd9cccf22cff121884a
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356291"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Urządzenia mowy kognitywnych usługi SDK

## <a name="requesting-access"></a>Żądania dostępu

SDK urządzenia mowy jest w wersji zapoznawczej ograniczeniami i wymaga rejestracji do programu. Obecnie Microsoft preferuje dużym firmom jako kandydatów do dostępu do tego produktu.

Aby uzyskać dostęp do zestawu SDK urządzenia mowy, wykonaj następujące kroki:

1. Przejdź do zestawu SDK usługi Microsoft mowy urządzeń [wypełnieniu formularza](https://aka.ms/sdsdk-signup).
1. Odczyt [umowę licencyjną](speech-devices-sdk-license.md).
1. Jeśli akceptujesz postanowienia tej umowy licencyjnej, wybierz opcję "Akceptuję".
1. Należy odpowiedzieć na pytania w formularzu.
1. Prześlij formularz. 
1. Jeśli adres e-mail nie jest już częścią usługi Azure Active Directory, otrzymasz wiadomość e-mail z zaproszeniem, tak jak poniżej po zatwierdzeniu. Jeśli adres e-mail jest już w usłudze Azure Active Directory, otrzymasz wiadomość e-mail od zespołu Microsoft Speech po zatwierdzeniu i użytkownik może przejść od razu do [Pobierz zestaw SDK urządzenia mowy](#download-the-speech-devices-sdk).

## <a name="approval-e-mail"></a>Wiadomość e-mail zatwierdzenia

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com> 
Subject: You're invited to the Microsoft organization 
```

![Wiadomości e-mail](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>Zaakceptuj dostępu
Wykonaj poniższe kroki, aby przyłączyć usługi Azure Active Directory z podanych podczas rejestracji adresu e-mail. Ten proces udziela dostępu do zestawu SDK urządzenia mowy [pobrania](https://shares.datatransfer.microsoft.com/).

1. Kliknij przycisk **wprowadzenie** w otrzymanej wiadomości e-mail. Jeśli Twoja organizacja jest już usługi Office 365, będzie monitowany o zalogowanie i może przejść od razu do kroku 8.

2. Kliknij przycisk **dalej** w otwartym oknie przeglądarki.

    ![okno uwierzytelniania](media/speech-devices-sdk/get-sdk-2.png)

3. Utwórz konto Microsoft, jeśli nie został wcześniej. Wprowadź tego samego adresu e-mail, w którym otrzymano zaproszenie w kroku 6 powyżej.

    ![Utwórz konto Microsoft](media/speech-devices-sdk/get-sdk-3.png)

4. Kliknij przycisk **dalej** hasła.

5. Po wyświetleniu monitu, aby sprawdzić wiadomości e-mail, wróć do skrzynki odbiorczej poczty e-mail, aby uzyskać kod weryfikacyjny, który jest wysyłany do użytkownika.
 
7. Wklej lub wpisz kod zabezpieczeń z wiadomości e-mail w oknie dialogowym. W tym przykładzie jest "8406". Następnie kliknij przycisk **Next** (Dalej).

    ![Sprawdź adres e-mail](media/speech-devices-sdk/get-sdk-6.png)
 
8. Po wyświetleniu aplikacji panelu dostępu w oknie przeglądarki została potwierdzona, że Twój adres e-mail (z kroku 6) jest obecnie częścią usługi Azure Active Directory. Masz teraz dostęp do witryny pobierania zestawu SDK urządzenia mowy.

## <a name="download-the-speech-devices-sdk"></a>Pobierz urządzeń mowy zestawu SDK

Przejdź do [witryny pobierania zestawu SDK urządzenia mowy](https://shares.datatransfer.microsoft.com/) i zaloguj się przy użyciu Account Microsoft utworzony wcześniej. Można teraz pobrać mowy urządzeń SDK, skojarzone przykładowy kod i materiały referencyjne, wykonaj następujące czynności.

![Witryna pobierania zestawu SDK](media/speech-devices-sdk/get-sdk-7.png)

1. Pobierz i zainstaluj narzędzia Aspera Connect po wyświetleniu monitu, aby to zrobić przez przeglądarkę.

    ![Pobierz Aspera połączenia](media/speech-devices-sdk/get-sdk-8.png)
 
1. Kliknij przycisk **tak** do przełączania się na Aspera połączenie.

    ![Przełącz się do połączenia Aspera](media/speech-devices-sdk/get-sdk-9.png)
 
1. Kliknij przycisk **Zezwalaj** o potwierdzenie pobierania plików za pomocą Aspera Connect.

    ![Pobierz za pomocą Aspera Connect](media/speech-devices-sdk/get-sdk-10.png)
 
1. Zamknij okno transferów połączyć Aspera, pobrane pliki.

    ![Połącz Aspera okna transferu](media/speech-devices-sdk/get-sdk-11.png)
 
Domyślnie pliki są pobierane do Twojej **pobiera** folderu. Można rejestrować teraz poza tej lokacji. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rozpoczynanie pracy z zestawem SDK urządzenia mowy](speech-devices-sdk-qsg.md)
