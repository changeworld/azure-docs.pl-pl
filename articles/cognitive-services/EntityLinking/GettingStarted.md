---
title: Rozpoczynanie pracy z interfejsem API łączenie jednostki | Dokumentacja firmy Microsoft
description: Analizowanie tekstu i łącza o nazwie jednostki do odpowiednich wpisów w bazie wiedzy przy użyciu interfejsu API łączenie jednostki w usługach percepcji.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: entity-linking-intelligence
ms.topic: article
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: 54c4a3bbb3637c248bd7705ed291633368b542c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347132"
---
# <a name="get-started-with-entity-linking-api-in-c35"></a>Rozpoczynanie pracy z jednostką łączenie interfejsu API w języku C&#35;

Łączenie jednostki firmy Microsoft jest narzędziem przetwarzania języka naturalnego analizowanie tekstu, a następnie połącz jednostek o nazwie odpowiednie wpisy w bazie wiedzy. 

W tym samouczku Eksploruje jednostki łączenie za pomocą biblioteki klienta łączenie jednostki jako pakietu NuGet. 

### <a name="Prerequisites">Wymagania wstępne</a>

- Visual Studio 2015
- Klucz interfejsu API usługi Microsoft kognitywnych
- Pobierz klienta biblioteki i przykładowe
- Pakiet NuGet łączenie jednostki firmy Microsoft

Możesz pobrać jednostki łączenie analizy usługi interfejsu API biblioteki klienta za pośrednictwem [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). Plik zip pobranego ma zostać wyodrębniony do wybranego folderu, w przypadku wielu użytkowników wybierz folder, w programie Visual Studio 2015.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">Krok 1: Zasubskrybować usługę analizy łączenie jednostki i Pobierz klucz</a>
Przed rozpoczęciem korzystania z usługi analizy łączenie jednostki, musisz zapisać się klucz interfejsu API. Zobacz [subskrypcje](https://www.microsoft.com/cognitive-services/en-us/sign-up). W tym samouczku można zarówno klucza podstawowego i pomocniczego.

### <a name="step-2-create-a-new-project-in-visual-studio"> Krok 2: Utwórz nowy projekt w programie Visual Studio</a>

Zacznijmy od utworzenia nowego projektu w programie Visual Studio. Najpierw uruchom program Visual Studio 2015 z Start Menu. Następnie utwórz nowy projekt, wybierając **zainstalowane szablony → → Visual C# → pusta aplikacja systemu Windows Universal →** szablonu projektu:

 ![Tworzy uniwersalnej aplikacji](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Krok 3: Dodaj pakiet NuGet łączenie jednostki do projektu</a>

Jednostki łączenie z kognitywnych usługi zostanie opublikowana pakietu NuGet.org i musi być zainstalowany przed jego użyciem.
Aby dodać go do projektu, przejdź do **Eksploratora rozwiązań** karcie, kliknij prawym przyciskiem myszy projekt i wybierz **Zarządzaj pakietami Nuget**.

Pierwszy w **Menedżera pakietów NuGet** okna, wybierz opcję NuGet.org jako sieci **źródła pakietu** w prawym górnym rogu. Wybierz **Przeglądaj** w lewym górnym rogu i w polu wyszukiwania wpisz "ProjectOxford.EntityLinking". Wybierz **Microsoft.ProjectOxford.EntityLinking** NuGet pakiet, a następnie kliknij przycisk **zainstalować**.

Następnie wyszukaj Newtonsoft.Json i instalację. Jeśli zostanie wyświetlony monit o przegląd zmian dokonanych, kliknij przycisk **OK**. Jeśli prezentowany EntityLinking postanowień licencyjnych, kliknij przycisk **akceptuję**.

Łączenie jednostki został zainstalowany w ramach aplikacji. Można to potwierdzić, sprawdzając który ** Microsoft.ProjectOxford.EntityLinking** odwołanie jest obecny w ramach projektu w Eksploratorze rozwiązań.

 ![Biblioteki dołączony nuget w projekcie](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">Krok 4: Dodawanie danych wejściowych i wyjściowych bloku tekstu do aplikacji XAML</a>
Przejdź do ** MainPage.xaml ** w **Eksploratora rozwiązań**, kliknij dwukrotnie plik, który zostanie otwarty w nowym oknie. Dla wygody, możesz dwukrotnie kliknąć **XAML** przycisk **projektanta** kartę, to spowoduje ukrycie **projektanta wizualnego** i zarezerwować wszystkie miejsca do widoku kodu.

 ![Biblioteki dołączony nuget w projekcie](./Images/UWPMainPage.png)
 
Usługa tekstu najlepszy sposób, aby wizualizować funkcji jest utworzenie danych wejściowych i wyjściowych bloku tekstu. W tym celu należy dodać następujące XAML w **siatki**. Ten kod dodaje trzech składników, pola tekstu wejściowego bloku tekstu wyjściowego i przycisk start.
 
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
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">Krok 5: Przystąpić do dodawania jednostki łączenie usługi analizy</a>
 
Interfejs użytkownika został utworzony. Przed rozpoczęciem korzystania z usługi łączenie jednostki, musimy dodać obsługi kliknięcia przycisku. Otwórz **MainPage.xaml** z **Eksploratora rozwiązań**. Dodawanie obsługi button_Click na końcu przycisku.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
Obsługi kliknięcia przycisku musi zostać wdrożone w kodzie. Otwórz **MainPage.xaml.cs** z **Eksploratora rozwiązań** do zaimplementowania kliknięcia przycisku. EntityLinkingServiceClient jest otoką elementu można pobrać połączeń jednostek odpowiedzi. Argument konstruktora EntityLinkingServiceClient jest klucz subskrypcji usługi kognitywnych. Wklej w kluczu subskrypcji uzyskano w **krok 1** do wywołania tej usługi łączenie jednostki. 

Poniżej przedstawiono przykładowy kod, który dodaje "wikipediaId" do odpowiedzi przy użyciu usługi łączenie jednostki. 
 
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
 
Teraz można przystąpić do uruchomienia z pierwszego języka naturalnego przetwarzania jednostki łączenie aplikacji. Naciśnij klawisz **klawisz F5** Aby skompilować i uruchomić aplikację. Wklej fragmentów tekstu lub akapitów w odpowiednim polu. Naciśnij przycisk "Pobierz wynik" i obserwować zidentyfikowanych jednostek w bloku danych wyjściowych.
 
 ![Wynik próbki platformy uniwersalnej systemu Windows](./Images/DemoCodeResult.png)
 
### <a name="summary">Podsumowanie</a>
 
W tym samouczku zostały przedstawiono sposób tworzenia aplikacji wykorzystać jednostki połączeń, analizy i biblioteki klienta usługi zawierającym tylko kilka wierszy C# i kod XAML. 

