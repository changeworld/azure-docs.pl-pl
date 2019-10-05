---
title: Znajdowanie dostępnych pokojów — Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: W tym przewodniku szybki start uruchomisz dwie przykładowe aplikacje .NET Core, aby wysyłać symulowane dane telemetryczne ruchu i ditlenku węgla do przestrzeni na platformie Azure Digital bliźniaczych reprezentacji. Celem jest znalezienie dostępnych pokojów z użyciem świeżego środowiska AIR z interfejsów API zarządzania po przeprowadzeniu przetwarzania w chmurze.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 10/03/2019
ms.openlocfilehash: 3c9a806b936b9f167d1700c95b1e769926abb17b
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958908"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Szybki Start: znajdowanie dostępnych pokojów przy użyciu usługi Azure Digital bliźniaczych reprezentacji

Usługa Digital bliźniaczych reprezentacji systemu Azure umożliwia ponowne utworzenie obrazu cyfrowego środowiska fizycznego. Następnie można otrzymywać powiadomienia o zdarzeniach w środowisku i dostosować do nich odpowiedzi.

Ten przewodnik Szybki Start używa [pary przykładów platformy .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) do cyfrowego tworzenia częściowego budynku biurowego. Pokazuje, jak znaleźć dostępne pokoje w tym budynku. Za pomocą Digital bliźniaczych reprezentacji można skojarzyć wiele czujników ze środowiskiem. Można też dowiedzieć się, czy jakość powietrza dostępnego pokoju jest optymalna dzięki pomocy symulowanego czujnika dla ditlenku węgla. Jedna z przykładowych aplikacji generuje losowe dane czujnika, aby ułatwić wizualizację tego scenariusza.

Poniższe wideo podsumowuje konfigurację szybkiego startu:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz konta platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

1. Dwie aplikacje konsolowe, które są uruchamiane w tym przewodniku szybki C#Start, są zapisywane przy użyciu programu. Zainstaluj [zestaw .NET Core SDK w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na komputerze deweloperskim. Jeśli masz zainstalowaną zestaw .NET Core SDK, sprawdź bieżącą wersję programu C# na komputerze deweloperskim. Uruchom `dotnet --version` w wierszu polecenia.

1. Pobierz [przykładowy C# projekt](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Wyodrębnij archiwum Digital-Twins-Samples-CSharp-Master. zip.

## <a name="create-a-digital-twins-instance"></a>Tworzenie wystąpienia bliźniaczych reprezentacji Digital

Utwórz nowe wystąpienie programu Digital bliźniaczych reprezentacji w [portalu](https://portal.azure.com) , wykonując kroki opisane w tej sekcji.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Ustawianie uprawnień dla aplikacji

W tej sekcji zarejestrowano przykładową aplikację do Azure Active Directory (Azure AD), aby mogła ona uzyskać dostęp do Twojego wystąpienia Digital bliźniaczych reprezentacji. Jeśli masz już rejestrację aplikacji usługi Azure AD, użyj jej ponownie dla przykładu. Upewnij się, że jest skonfigurowany zgodnie z opisem w tej sekcji.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Kompilowanie aplikacji

Utwórz aplikację zajętości, wykonując następujące kroki.

1. Otwórz wiersz polecenia. Przejdź do folderu, w którym zostały wyodrębnione pliki `digital-twins-samples-csharp-master.zip`.
1. Uruchom `cd occupancy-quickstart/src`.
1. Uruchom `dotnet restore`.
1. Edytuj plik [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) , aby zaktualizować następujące zmienne:
    - **ClientId**: Wprowadź identyfikator aplikacji rejestracji aplikacji usługi Azure AD zanotowaną w poprzedniej sekcji.
    - **Dzierżawca**: Wprowadź identyfikator katalogu dzierżawy usługi Azure AD, a także zanotowano w poprzedniej sekcji.
    - **Baseurl**: adres URL interfejsu API zarządzania Twojego wystąpienia Digital bliźniaczych reprezentacji ma format `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Zastąp symbole zastępcze w tym adresie URL wartościami dla Twojego wystąpienia z poprzedniej sekcji.

## <a name="provision-graph"></a>Graf udostępniania

W tym kroku zainicjujemy wykres przestrzenny Digital bliźniaczych reprezentacji z:

- Kilka spacji.
- Jedno urządzenie.
- Dwie czujniki.
- Funkcja niestandardowa.
- Jedno przypisanie roli.

Schemat przestrzenny jest inicjowany przy użyciu pliku [provisionSample. YAML](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) .

1. Uruchom `dotnet run ProvisionSample`.

    >[!NOTE]
    >Narzędzie wiersza polecenia platformy Azure do logowania do urządzenia służy do uwierzytelniania użytkownika w usłudze Azure AD. Użytkownik musi wprowadzić dany kod w celu uwierzytelnienia przy użyciu strony [logowania firmy Microsoft](https://microsoft.com/devicelogin) . Po wprowadzeniu kodu postępuj zgodnie z instrukcjami w celu uwierzytelnienia. Użytkownik musi uwierzytelnić się, gdy narzędzie jest uruchomione.

    >[!TIP]
    > Po uruchomieniu tego kroku upewnij się, że zmienne zostały prawidłowo skopiowane, jeśli zostanie wyświetlony następujący komunikat o błędzie: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. Krok aprowizacji może potrwać kilka minut. Inicjuje również IoT Hub w ramach wystąpienia Digital bliźniaczych reprezentacji. Powoduje pętlę, dopóki IoT Hub pokaże stan = `Running`.

    [@no__t — przykład 1Provision](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png)](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png#lightbox)

1. Po zakończeniu wykonywania Skopiuj `ConnectionString` urządzenia do użycia w przykładzie symulatora urządzenia. Skopiuj tylko ciąg opisany w tym obrazie.

    [![Copy parametry połączenia](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png)](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png#lightbox)

    >[!TIP]
    > Możesz wyświetlać i modyfikować wykres przestrzenny przy użyciu narzędzia [Azure Digital bliźniaczych reprezentacji Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="send-sensor-data"></a>Wyślij dane czujnika

Kompiluj i uruchamiaj aplikację symulatora czujnika, wykonując następujące kroki.

1. Otwórz nowy wiersz polecenia. Przejdź do projektu pobranego w folderze Digital-bliźniaczych reprezentacji-Samples-CSharp-Master.
1. Uruchom `cd device-connectivity`.
1. Uruchom `dotnet restore`.
1. Edytuj plik [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) , aby zaktualizować **DeviceConnectionString** z poprzednią `ConnectionString`.
1. Uruchom `dotnet run`, aby rozpocząć wysyłanie danych z czujnika. Zobaczysz ją wysłany do bliźniaczych reprezentacji Digital, jak pokazano na poniższej ilustracji.

     [@no__t — łączność 1Device](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png#lightbox)

1. Pozwól na uruchomienie tego symulatora, tak aby można było wyświetlić wyniki obok akcji następnego kroku. To okno przedstawia dane symulowanego czujnika wysyłane do bliźniaczych reprezentacji cyfrowych. Następne kroki zapytania w czasie rzeczywistym w celu znalezienia dostępnych pokojów z świeżym powietrzem.

    >[!TIP]
    > Po uruchomieniu tego kroku upewnij się, że `DeviceConnectionString` zostało prawidłowo skopiowane, jeśli zostanie wyświetlony następujący komunikat o błędzie: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Znajdź dostępne miejsca z świeżym powietrzem

Przykład czujnika symuluje losowe wartości danych dla dwóch czujników. Są one ruchem i ditlenekem węgla. Dostępne miejsca z świeżym powietrzem są zdefiniowane w próbce bez obecności w pokoju. Są one również zdefiniowane przez poziom dwutlenku węgla na 1 000 ppm. Jeśli warunek nie jest spełniony, obszar nie jest dostępny lub jakość powietrza jest niska.

1. Otwórz wiersz polecenia, który został użyty do uruchomienia poprzedniego kroku aprowizacji.
1. Uruchom `dotnet run GetAvailableAndFreshSpaces`.
1. Sprawdź ten wiersz polecenia i wiersz polecenia danych czujników obok siebie.

    Jeden wiersz polecenia wysyła symulowane ruchy i dane ditlenku węgla do bliźniaczych reprezentacji cyfrowych co pięć sekund. Inne polecenie odczytuje Graf w czasie rzeczywistym, aby znaleźć dostępne pokoje z świeżym powietrzem na podstawie losowo symulowanych danych. Wyświetla jeden z tych warunków niemal w czasie rzeczywistym na podstawie danych czujnika, które zostały wysłane ostatnio:
   - Dostępne pokoje z świeżym powietrzem.
   - Zajmowana lub niska jakość powietrza w pokoju.

     [@no__t 1Get dostępne miejsca z świeżym powietrzem](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png)](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png#lightbox)

Aby zrozumieć, co się stało w tym przewodniku Szybki Start i jakie interfejsy API zostały wywołane, Otwórz [Visual Studio Code](https://code.visualstudio.com/Download) z projektem obszaru roboczego kod znalezionym w Digital-bliźniaczych reprezentacji-Samples-CSharp. Użyj następującego polecenia:

```plaintext
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Samouczki przechodzą do kodu. Naucz się, jak modyfikować dane konfiguracji i jakie interfejsy API są wywoływane. Aby uzyskać więcej informacji na temat interfejsów API zarządzania, przejdź na stronę Digital bliźniaczych reprezentacji Swagger:

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Nazwa | Zamień na |
| --- | --- |
| YOUR_INSTANCE_NAME | Nazwa wystąpienia Digital bliźniaczych reprezentacji |
| YOUR_LOCATION | Region serwera, na którym jest hostowane wystąpienie |

Lub dla wygody przejdź do [Digital bliźniaczych reprezentacji Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W samouczkach przedstawiono szczegółowo następujące informacje:

- Tworzenie aplikacji dla menedżerów infrastruktury w celu zwiększenia produktywności.
- Wydajniej wykonuj kompilację.

Aby przejść do samouczków, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, Usuń wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Usuń folder, który został utworzony podczas pobierania przykładowego repozytorium.
1. Z menu po lewej stronie [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**. Następnie wybierz swój zasób cyfrowy bliźniaczych reprezentacji. W górnej części okienka **wszystkie zasoby** wybierz pozycję **Usuń**.

    > [!TIP]
    > Jeśli wystąpił problem z usunięciem wystąpienia bliźniaczych reprezentacji Digital, aktualizacja usługi została wdrożona przy użyciu poprawki. Spróbuj usunąć wystąpienie.

## <a name="next-steps"></a>Następne kroki

Ten przewodnik Szybki Start używa prostego scenariusza, aby dowiedzieć się, jak znaleźć pokoje z dobrymi warunkami pracy. Aby zapoznać się z szczegółowymi analizami tego scenariusza, zobacz ten samouczek:

>[!div class="nextstepaction"]
>[Samouczek: wdrażanie usługi Azure Digital bliźniaczych reprezentacji i Konfigurowanie wykresu przestrzennego](tutorial-facilities-setup.md)
