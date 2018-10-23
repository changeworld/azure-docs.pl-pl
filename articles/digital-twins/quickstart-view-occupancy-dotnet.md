---
title: Wyszukiwanie dostępnych pomieszczeń za pomocą usługi Azure Digital Twins (C#) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje platformy .NET Core, aby wysłać symulowane dane telemetryczne ruchu i dwutlenku węgla do obszaru w usłudze Azure Digital Twins. Celem jest znalezienie dostępnych pomieszczeń ze świeżym powietrzem z poziomu interfejsów API zarządzania po przetwarzaniu obliczeniowym w chmurze.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/02/2018
ms.author: alinast
ms.openlocfilehash: e0b47b1322a520ad8b09fd2fe2967e628b5e4e03
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322875"
---
# <a name="quickstart-find-available-rooms-using-azure-digital-twins"></a>Szybki start: wyszukiwanie dostępnych pomieszczeń przy użyciu usługi Azure Digital Twins

Usługa Azure Digital Twins umożliwia odtworzenie cyfrowego obrazu środowiska fizycznego. Mając taki obraz, możesz otrzymywać powiadomienia o zdarzeniach w tym środowisku i dostosować sposoby reagowania na nie. 

W tym przewodniku Szybki start użyto [dwóch przykładowych aplikacji platformy .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) w celu utworzenia cyfrowego obrazu wymyślonego biurowca oraz pokazano, jak znaleźć dostępne pomieszczenia w tym budynku. Za pomocą usługi Digital Twins możesz skojarzyć ze swoim środowiskiem wiele czujników. Poza dostępnością pomieszczenia możesz również określić, czy jakość powietrza w tym pomieszczeniu jest optymalna, korzystając z symulowanego czujnika dwutlenku węgla. Jedna z przykładowych aplikacji wygeneruje losowe dane czujnika, aby ułatwić Ci wizualizację tego scenariusza.

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz jeszcze konta platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Dwie aplikacje konsolowe uruchamiane w tym przewodniku Szybki start zostały napisane w języku C#. Musisz zainstalować [zestaw SDK .NET Core w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na komputerze deweloperskim. Jeśli masz zainstalowany zestaw SDK .NET Core, możesz sprawdzić bieżącą wersję języka C# na komputerze deweloperskim, uruchamiając polecenie `dotnet --version` w wierszu polecenia.

1. Pobierz [przykładowy projekt w języku C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip) i wyodrębnij archiwum digital-twins-samples-csharp-master.zip. 


## <a name="create-a-digital-twins-instance"></a>Tworzenie wystąpienia usługi Digital Twins

Utwórz nowe wystąpienie usługi Digital Twins w witrynie [Portal](https://portal.azure.com), wykonując kroki opisane w tej sekcji.

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]

## <a name="set-permissions-for-your-app"></a>Ustawianie uprawnień dla aplikacji

Czynności opisane w tej sekcji pozwalają zarejestrować aplikację przykładową w usłudze Azure Active Directory (AAD), aby mogła ona uzyskiwać dostęp do wystąpienia usługi Digital Twins. Jeśli masz już rejestrację aplikacji usługi AAD, możesz użyć jej ponownie dla tego przykładu, upewniając się, że jest ona skonfigurowana tak, jak opisano w tej sekcji. 

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="build-application"></a>Kompilowanie aplikacji

Skompiluj aplikację zajmowania pomieszczenia przy użyciu następujących kroków:

1. Otwórz wiersz polecenia i przejdź do folderu, do którego zostały wyodrębnione pliki digital-twins-samples-csharp-master.zip.
1. Uruchom polecenie `cd occupancy-quickstart/src`.
1. Uruchom polecenie `dotnet restore`.
1. Edytuj plik *appSettings.json*, aby zaktualizować następujące zmienne:
    - *ClientId*: wprowadź *identyfikator aplikacji* Twojej rejestracji aplikacji w usłudze AAD, który został zanotowany w poprzedniej sekcji.
    - *Tenant*: wprowadź *identyfikator katalogu* Twojej dzierżawy usługi AAD, który również został zanotowany w poprzedniej sekcji.
    - *BaseUrl*: adres URL *interfejsu API zarządzania* wystąpienia usługi Digital Twins, który będzie w następującym formacie: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Zamień symbole zastępcze w tym adresie URL na wartości odpowiadające Twojemu wystąpieniu z poprzedniej sekcji.

## <a name="provision-graph"></a>Aprowizowanie wykresu

W tym kroku wykres przestrzenny usługi Digital Twins jest aprowizowany za pomocą kilku obszarów, jednego urządzenia, dwóch czujników, funkcji niestandardowej i jednego przypisania roli. Wykres przestrzenny zostanie aprowizowany przy użyciu pliku [*provisionSample.yaml*](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

1. Uruchom polecenie `dotnet run ProvisionSample`.
    >[!NOTE]
    >Używamy narzędzia interfejsu wiersza polecenia platformy Azure logowania do urządzenia, aby uwierzytelnić użytkownika w usłudze Azure AD. Użytkownik musi wprowadzić podany kod, aby przeprowadzić uwierzytelnienie przy użyciu strony [logowania firmy Microsoft](https://microsoft.com/devicelogin). Po wprowadzeniu kodu wykonaj kroki w celu uwierzytelnienia. Użytkownik musi się uwierzytelniać za każdym razem, gdy to narzędzie jest uruchomione.
    
    >[!TIP]
    > Jeśli podczas wykonywania tego kroku jest wyświetlany następujący błąd, upewnij się, że zmienne zostały skopiowane poprawnie. 
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`


1. Aprowizowanie może potrwać kilka minut. Obejmuje ono również aprowizowanie usługi IoT Hub w ramach wystąpienia usługi Digital Twins, które będzie działać w pętli do czasu, aż stan usługi IoT Hub będzie mieć wartość `Running`.

    ![Przykład aprowizacji][4]

1. Na koniec wykonywania skopiuj wartość parametru `ConnectionString` urządzenia — będzie ona potrzebna w przykładzie symulatora urządzenia. Skopiuj tylko ciąg wyróżniony na poniższym rysunku:

    ![Przykład aprowizacji][1]

## <a name="send-sensor-data"></a>Wysyłanie danych z czujników

Aplikację symulatora czujników można skompilować i uruchomić, używając poniższych kroków:

1. Otwórz nowy wiersz polecenia i przejdź do pobranego projektu w folderze digital-twins-samples-csharp-master.
1. Uruchom polecenie `cd device-connectivity`.
1. Uruchom polecenie `dotnet restore`.
1. Edytuj plik *appsettings.json*, aby zaktualizować wartość *DeviceConnectionString* za pomocą wartości `ConnectionString` skopiowanej powyżej.
1. Uruchom polecenie `dotnet run`, aby rozpocząć wysyłanie danych z czujników. Ich wysyłanie do usługi Digital Twins powinno być widoczne, jak na poniższym rysunku:

     ![Łączność urządzeń][2]

1. Pozostaw ten symulator uruchomiony, aby można było wyświetlić jego wyniki obok wyników uzyskanych po wykonaniu kolejnego kroku. To okno pokaże symulowane dane czujników wysyłane do usługi Digital Twins, natomiast następnym krokiem będzie wysyłanie zapytań w czasie rzeczywistym w celu znalezienia dostępnego pomieszczenia ze świeżym powietrzem.

    >[!TIP]
    > Jeśli podczas wykonywania tego kroku jest wyświetlany następujący błąd, upewnij się, że wartość `DeviceConnectionString` została skopiowana poprawnie.  
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Znajdowanie dostępnych obszarów ze świeżym powietrzem

Przykładowy czujnik symuluje losowe wartości danych dla dwóch czujników, ruchu i dwutlenku węgla. Dostępne obszary ze świeżym powietrzem są zdefiniowane w naszym przykładzie przez brak obecności w pomieszczeniu i poziom dwutlenku węgla poniżej 1000 ppm. Jeśli ten warunek nie jest spełniony, to pomieszczenie nie jest dostępne lub jakość powietrza jest niska.

1. Otwórz wiersz polecenia używany do wykonania kroku aprowizacji powyżej.
1. Uruchom polecenie `dotnet run GetAvailableAndFreshSpaces`.
1. Przyjrzyj się temu wierszowi polecenia oraz wierszowi polecenia danych czujnika, umieszczając je obok siebie, jak pokazano poniżej. 
1. Jeden wiersz polecenia wysyła symulowane dane ruchu i dwutlenku węgla do usługi Digital Twins co 5 sekund. Drugie polecenie odczytuje wykres w czasie rzeczywistym, aby znaleźć dostępne pomieszczenia ze świeżym powietrzem na podstawie symulowanych danych losowych. Wyświetli on jeden z tych warunków w czasie niemal rzeczywistym na podstawie wysłanych ostatnio danych czujników:
    - Dostępne pomieszczenia ze świeżym powietrzem.
    - Zajęte pomieszczenie lub niska jakość powietrza w pomieszczeniu.

     ![Uzyskiwanie dostępnych obszarów ze świeżym powietrzem][3]

Aby zrozumieć, co wydarzyło się w tym przewodniku Szybki start i jakie interfejsy API zostały wywołane, otwórz program [Visual Studio Code](https://code.visualstudio.com/Download) z projektem kodu obszaru roboczego znajdującym się w pliku digital-twins-samples-csharp (zobacz poniższe polecenie). Samouczki szczegółowo omawiają kod i uczą, jak zmodyfikować dane konfiguracji oraz jakie interfejsy API zostały wywołane. Aby lepiej poznać interfejsy API zarządzania, przejdź na stronę usługi Digital Twins programu Swagger `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net//management/swagger` lub dla wygody przejrzyj witrynę [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger). 

```
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Samouczki szczegółowo omawiają sposób tworzenia aplikacji dla menedżerów budynków, umożliwiającej zwiększenie wydajności zajmowania pomieszczeń i efektywniejsze zarządzanie budynkiem.

Jeśli planujesz kontynuować pracę z samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start:

1. Usuń folder, który został utworzony podczas pobierania przykładowego repozytorium.
1. W witrynie [Azure Portal](http://portal.azure.com) w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swój zasób usługi Digital Twins. U góry okienka **Wszystkie zasoby** kliknij polecenie **Usuń**.

## <a name="next-steps"></a>Następne kroki

Ten przewodnik Szybki start przedstawił omówienie prostego scenariusza znajdowania pomieszczeń z dobrymi warunkami do pracy. Aby uzyskać bardziej szczegółową analizę tego scenariusza, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
> [Samouczek: wdrażanie usługi Azure Digital Twins i konfigurowanie wykresu przestrzennego](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
