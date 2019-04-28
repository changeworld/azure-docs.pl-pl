---
title: 'Samouczek: Tworzenie aplikacji do łączenia jednostek — C#'
titlesuffix: Azure Cognitive Services
description: Interfejs API Entity Linking służy do analizowania tekstu i łączenia mających nazwę jednostek z odpowiadającymi im pozycjami w bazie wiedzy.
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: tutorial
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: fc1bdd5c6ad4829e22af9922c6749e60f842abaf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816567"
---
# <a name="tutorial-build-an-entity-linking-app-with-c"></a>Samouczek: Tworzenie aplikacji do łączenia jednostek w języku C#

Microsoft Entity Linking to narzędzie do przetwarzania języka naturalnego, które służy do analizowania tekstu i łączenia mających nazwę jednostek z odpowiadającymi im pozycjami bazy wiedzy. 

Ten samouczek prezentuje łączenie jednostek przy użyciu biblioteki klienta Entity Linking w postaci pakietu NuGet. 

### <a name="Prerequisites">Wymagania wstępne</a>

- Visual Studio 2015
- Klucz interfejsu API Microsoft Cognitive Services
- Pobierz bibliotekę klienta i przykład
- Pakiet NuGet z biblioteką Microsoft Entity Linking

Bibliotekę klienta interfejsu API Entity Linking Intelligence Service możesz pobrać za pomocą [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). Pobrany plik zip trzeba rozpakować do dowolnego folderu; wielu użytkowników wybiera folder Visual Studio 2015.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">Krok 1. Zasubskrybowanie usługi Entity Linking Intelligence Service i uzyskanie klucza</a>
Przed rozpoczęciem korzystania z Entity Linking Intelligence Service musisz się zarejestrować, aby uzyskać klucz interfejsu API. Zobacz temat [Subskrypcje](https://www.microsoft.com/cognitive-services/en-us/sign-up). W tym samouczku można używać zarówno klucza podstawowego, jak i pomocniczego.

### <a name="step-2-create-a-new-project-in-visual-studio">Krok 2. Utworzenie nowego projektu w Visual Studio</a>

Na początek utworzymy nowy projekt w Visual Studio. Uruchom Visual Studio 2015 za pomocą menu Start. Następnie utwórz nowy projekt, wybierając jako szablon projektu **Zainstalowane → Szablony → Visual C# → Projekt uniwersalny systemu Windows → Pusta aplikacja**:

 ![Tworzenie uniwersalnej aplikacji](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Krok 3. Dodanie do projektu pakietu NuGet Entity Linking</a>

Funkcja Entity Linking usług Cognitive Services została opublikowana jako pakiet NuGet.org. Przed użyciem musisz go zainstalować.
Aby dodać go do projektu, przejdź na kartę **Eksplorator rozwiązań**, kliknij projekt prawym przyciskiem myszy i wybierz polecenie **Zarządzaj pakietami NuGet**.

Najpierw w prawym górnym rogu okna **Menedżer pakietów NuGet** wybierz NuGet.org jako **Źródło pakietów**. Wybierz polecenie **Przeglądaj** w lewym górnym rogu i wpisz w polu wyszukiwania „ProjectOxford.EntityLinking”. Wybierz pakiet NuGet **Microsoft.ProjectOxford.EntityLinking** i kliknij przycisk **Zainstaluj**.

Następnie wyszukaj i zainstaluj pakiet Newtonsoft.Json. Jeśli pojawi się prośba o przejrzenie zmian, kliknij przycisk **OK**. Jeśli zostanie wyświetlona umowa licencyjna pakietu EntityLinking, kliknij przycisk **Akceptuję**.

Pakiet Entity Linking jest teraz zainstalowany jako część aplikacji. Możesz się o tym upewnić, sprawdzając, czy odwołanie do** Microsoft.ProjectOxford.EntityLinking** jest częścią projektu w Eksploratorze rozwiązań.

 ![Biblioteka NuGet dołączona do projektu](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">Krok 4. Dodanie pola wprowadzania tekstu i bloku tekstu wyjściowego do pliku XAML aplikacji</a>
Znajdź plik **MainPage.xaml** w **Eksploratorze rozwiązań** i kliknij go dwukrotnie. Zostanie on otwarty w nowym oknie. Dla wygody możesz kliknąć dwukrotnie przycisk **XAML** na karcie **Projektant**, aby ukryć **Wizualnego projektanta**, powiększając widok kodu.

 ![Biblioteka NuGet dołączona do projektu](./Images/UWPMainPage.png)
 
Ponieważ używamy usługi przetwarzającej tekst, najlepszym sposobem wizualizacji jej działania jest utworzenie pola wprowadzania tekstu i bloku tekstu wyjściowego. Aby to zrobić, dodaj w obszarze **Siatka** następujący kod XAML. Ten kod dodaje trzy elementy: pole wprowadzania tekstu, blok tekstu wyjściowego i przycisk uruchamiania.
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">Krok 5. Dodanie usługi Entity Linking Intelligence Service</a>
 
Interfejs użytkownika został utworzony. Przed użyciem usługi Entity Linking musimy dodać procedurę obsługi kliknięcia przycisku. Otwórz plik **MainPage.xaml** z poziomu **Eksploratora rozwiązań**. Dodaj procedurę obsługi button_Click na końcu przycisku.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
Procedura obsługi kliknięcia przycisku musi zostać zaimplementowana w kodzie. Otwórz plik **MainPage.xaml.cs** z poziomu **Eksploratora rozwiązań**, aby zaimplementować obsługę kliknięcia przycisku. EntityLinkingServiceClient to otoka do pobierania odpowiedzi usługi Entity Linking. Argumentem konstruktora obiektu EntityLinkingServiceClient jest klucz subskrypcji usług Cognitive Services. Wklej klucz subskrypcji uzyskany w **Kroku 1**, aby wywołać usługę Entity Linking. 

Poniżej znajduje się przykładowy kod, który przy użyciu usługi Entity Linking dodaje do odpowiedzi pole „wikipediaId”. 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
Możesz już uruchomić swoją pierwszą aplikację do przetwarzania języka naturalnego używającą usługi Entity Linking. Naciśnij **klawisz F5**, aby skompilować i uruchomić aplikację. W polu wprowadzania tekstu wklej fragment tekstu. Naciśnij przycisk „Get Result” (Podaj wynik) i zobacz zidentyfikowane jednostki w bloku wyjściowym.
 
 ![Wynik działania przykładowej aplikacji uniwersalnej systemu Windows](./Images/DemoCodeResult.png)
 
### <a name="summary">Podsumowanie</a>
 
W tym samouczku pokazano, jak utworzyć aplikację korzystającą z biblioteki klienta Entity Linking Intelligence Service, pisząc tylko kilka wierszy kodu C# i XAML. 

