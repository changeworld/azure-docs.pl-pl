---
title: Jak wypełniać polecenia od klienta za pomocą SDK mowy
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniamy, jak obsługiwać działania poleceń niestandardowych na kliencie za pomocą SDK mowy.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367753"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Wypełnianie poleceń od klienta za pomocą SDK mowy (wersja zapoznawcza)

Aby wykonać zadania przy użyciu aplikacji Polecenia niestandardowe, można wysyłać ładunki niestandardowe do podłączonego urządzenia klienckiego.

W tym artykule:

- Definiowanie i wysyłanie niestandardowego ładunku JSON z aplikacji Polecenia niestandardowe
- Odbieranie i wizualizowanie niestandardowej zawartości ładunku JSON z aplikacji kliackiej zestawu SDK mowy systemu Windows w języku C#

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Klucz subskrypcji platformy Azure dla usługi mowy
  - [Pobierz go za darmo](get-started.md) lub utwórz go w [witrynie Azure portal](https://portal.azure.com)
- Poprzednio utworzona aplikacja Polecenia niestandardowe
  - [Szybki start: tworzenie niestandardowego polecenia z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)
- Aplikacja kliencka SDK mowy
  - [Szybki start: łączenie się z aplikacją Niestandardowe polecenie za pomocą sdk mowy (wersja zapoznawcza)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Opcjonalnie: szybkie rozpoczęcie pracy

W tym artykule opisano krok po kroku, jak zrobić aplikację kliencką, aby porozmawiać z aplikacją polecenia niestandardowe. Jeśli wolisz nurkować bezpośrednio w, kompletny, gotowy do kompilacji kod źródłowy używany w tym artykule jest dostępny w [przykładach SDK mowy](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Spełnij się dzięki ładowności JSON

1. Otwieranie wcześniej utworzonej aplikacji Polecenia niestandardowe ze [studia mowy](https://speech.microsoft.com/)
1. Sprawdź sekcję **Reguły ukończenia,** aby upewnić się, że masz wcześniej utworzoną regułę, która odpowiada użytkownikowi
1. Aby wysłać ładunek bezpośrednio do klienta, utwórz nową regułę z akcją Wyślij działanie

   > [!div class="mx-imgBorder"]
   > ![Reguła ukończenia wysyłania działania](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Nazwa reguły | AktualizacjaDeviceState | Nazwa opisująca cel reguły |
   | Warunki | Wymagany parametr `OnOff` - i`SubjectDevice` | Warunki określające, kiedy reguła może być uruchamiana |
   | Akcje | `SendActivity`(patrz poniżej) | Działanie, które należy podjąć, gdy warunek reguły jest spełniony |

   > [!div class="mx-imgBorder"]
   > ![Ładunek działania Wyślij](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Tworzenie wizualizacji dla stanu włączania lub wyłączania urządzenia

W [przewodnikach Szybki start: Łączenie się z aplikacją Niestandardowe polecenie za pomocą zestawie SDK mowy (Podgląd)](./quickstart-custom-speech-commands-speech-sdk.md) utworzono aplikację kliencką SDK mowy, która obsługiwała polecenia, takie jak `turn on the tv`, `turn off the fan`. Teraz dodaj kilka wizualizacji, aby zobaczyć wynik tych poleceń.

Dodaj pola oznaczone etykietami z tekstem wskazującym **Włącz** lub **Wyłącz** za pomocą następującego kodu XML dodanego do`MainPage.xaml.cs`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Obsługa konfigurowalnej ładowności

Po utworzeniu ładunku JSON można dodać odwołanie do biblioteki [JSON.NET](https://www.newtonsoft.com/json) w celu obsługi deserializacji.

> [!div class="mx-imgBorder"]
> ![Ładunek działania Wyślij](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

W `InitializeDialogServiceConnector` dodaj następujące `ActivityReceived` do obsługi zdarzeń. Dodatkowy kod wyodrębni ładunek z działania i odpowiednio zmieni stan wizualny telewizora lub wentylatora.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Testowanie

1. Uruchamianie aplikacji
1. Wybierz włącz mikrofon
1. Wybierz przycisk Rozmowa
1. Powiedzieć`turn on the tv`
1. Stan wizualny telewizora powinien zmienić się na "On"

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak: Dodawanie weryfikacji do parametrów polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-validations.md)
