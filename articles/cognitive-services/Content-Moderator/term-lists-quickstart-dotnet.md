---
title: Umiarkowany z listy terminów niestandardowych w usłudze Azure Content Moderator | Dokumentacja firmy Microsoft
description: Jak średni terminem niestandardowej listy, dla platformy .NET przy użyciu zestawu SDK usługi Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/11/2018
ms.author: sajagtap
ms.openlocfilehash: 2ae080518a9ad78552a8ec173e7f4d70085c7a6b
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022644"
---
# <a name="moderate-with-custom-term-lists-in-net"></a>Średni z listy terminów niestandardowe na platformie .NET

Domyślnej globalnej listy terminy w usłudze Azure Content Moderator jest wystarczająca na potrzeby większości zawartości moderowania. Jednak może być konieczne ekranu warunków, które są specyficzne dla Twojej organizacji. Na przykład możesz chcieć nazwy tagu do dalszego przeglądu. 

Możesz użyć [Content Moderator zestawu SDK dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) do tworzenia niestandardowych list terminów do wykorzystania przy użyciu interfejsu API moderowania tekstu.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **Wyświetla 5 termin** z każdej listy **nie może przekraczać 10 000 warunki**.
>

Ten artykuł zawiera informacje i przykłady kodu, aby pomóc Ci rozpocząć korzystanie z Content Moderator zestawu SDK dla platformy .NET do:
- Utwórz listę.
- Dodawanie warunków do listy.
- Warunki ekranu względem warunki na liście.
- Usuwanie warunków z listy.
- Usuwanie listy.
- Edytuj informacje na liście.
- Odśwież indeks, aby zmiany na liście znajdują się w nowe skanowanie.

W tym artykule założono, że znasz już program Visual Studio i języka C#.

## <a name="sign-up-for-content-moderator-services"></a>Załóż konto usługi Content Moderator

Zanim użyjesz usługi Content Moderator za pośrednictwem interfejsu API REST lub zestawu SDK, potrzebujesz klucza subskrypcji.

Na pulpicie nawigacyjnym Content Moderator możesz znaleźć klucz subskrypcji w **ustawienia** > **poświadczenia** > **API**  >  **Wersji próbnej Ocp-Apim-Subscription-Key**. Aby uzyskać więcej informacji, zobacz [Przegląd](overview.md).

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nową **Aplikacja konsoli (.NET Framework)** projektu do rozwiązania.

1. Nadaj projektowi nazwę **TermLists**. Wybierz ten projekt jako pojedynczy projekt startowy rozwiązania.

1. Dodaj odwołanie do **ModeratorHelper** projektu zestawu, który został utworzony w [pakietu Content Moderator klienta pomocnika Przewodnik Szybki Start](content-moderator-helper-quickstart-dotnet.md).

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet projektu TermLists:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizacja programu za pomocą instrukcji

Modyfikowanie programu za pomocą instrukcji.

    using System;
    using System.Threading;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;

### <a name="add-private-properties"></a>Dodawanie właściwości prywatne

Dodaj następujące właściwości prywatnej do przestrzeni nazw TermLists, klasy programu.

    /// <summary>
    /// The language of the terms in the term lists.
    /// </summary>
    private const string lang = "eng";

    /// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Content Moderator APIs.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of minutes to delay after updating the search index before
    /// performing image match operations against a the list.
    /// </summary>
    private const double latencyDelay = 0.5;

## <a name="create-a-term-list"></a>Tworzenie listy terminów

Utwórz listę termin z **ContentModeratorClient.ListManagementTermLists.Create**. Pierwszy parametr **Utwórz** jest ciąg zawierający typ MIME, który powinien być "application/json". Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). Drugi parametr jest **treści** obiekt, który zawiera nazwę i opis listy nowy termin.

Dodaj następującą definicję metody do przestrzeni nazw TermLists, klasy programu.

> [!NOTE]
> Klucz usługi Content Moderator ma żądań na drugi limit szybkości (jednostek Uzależnionych), a Jeśli przekroczysz limit, zestaw SDK zgłasza wyjątek z kodem błędu 429. 
>
> Klucz w warstwie bezpłatna obowiązuje limit szybkości jeden RPS.

    /// <summary>
    /// Creates a new term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <returns>The term list ID.</returns>
    static string CreateTermList (ContentModeratorClient client)
    {
        Console.WriteLine("Creating term list.");

        Body body = new Body("Term list name", "Term list description");
        TermList list = client.ListManagementTermLists.Create("application/json", body);
        if (false == list.Id.HasValue)
        {
            throw new Exception("TermList.Id value missing.");
        }
        else
        {
            string list_id = list.Id.Value.ToString();
            Console.WriteLine("Term list created. ID: {0}.", list_id);
            Thread.Sleep(throttleRate);
            return list_id;
        }
    }

## <a name="update-term-list-name-and-description"></a>Aktualizacja termin nazwę i opis listy

Zaktualizuj informacje o liście termin z **ContentModeratorClient.ListManagementTermLists.Update**. Pierwszy parametr **aktualizacji** jest określenie listy. Drugi parametr jest typ MIME, który powinien być "application/json". Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). Trzeci parametr jest **treści** obiekt, który zawiera nową nazwę i opis.

Dodaj następującą definicję metody do przestrzeni nazw TermLists, klasy programu.

    /// <summary>
    /// Update the information for the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to update.</param>
    /// <param name="name">The new name for the term list.</param>
    /// <param name="description">The new description for the term list.</param>
    static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
    {
        Console.WriteLine("Updating information for term list with ID {0}.", list_id);
        Body body = new Body(name, description);
        client.ListManagementTermLists.Update(list_id, "application/json", body);
        Thread.Sleep(throttleRate);
    }

## <a name="add-a-term-to-a-term-list"></a>Dodaj warunek do listy terminów

Dodaj następującą definicję metody do przestrzeni nazw TermLists, klasy programu.

    /// <summary>
    /// Add a term to the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to update.</param>
    /// <param name="term">The term to add to the term list.</param>
    static void AddTerm (ContentModeratorClient client, string list_id, string term)
    {
        Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
        client.ListManagementTerm.AddTerm(list_id, term, lang);
        Thread.Sleep(throttleRate);
    }

## <a name="get-all-terms-in-a-term-list"></a>Pobierz wszystkie terminy w listy terminów

Dodaj następującą definicję metody do przestrzeni nazw TermLists, klasy programu.

    /// <summary>
    /// Get all terms in the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to get all terms.</param>
    static void GetAllTerms(ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
        Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
        TermsData data = terms.Data;
        foreach (TermsInList term in data.Terms)
        {
            Console.WriteLine(term.Term);
        }
        Thread.Sleep(throttleRate);
    }

## <a name="add-code-to-refresh-the-search-index"></a>Dodaj kod, aby odświeżyć indeks wyszukiwania

Po wprowadzeniu zmian do listy terminów możesz odświeżyć jej indeks wyszukiwania dla zmian do uwzględnienia następnym razem, użyj listy terminów, aby tekst na ekranie. Jest to podobne do jak wyszukiwarki na pulpicie systemu Windows (jeśli jest włączone) lub aparatu wyszukiwania w sieci web nieustannie odświeża jej indeks w celu uwzględnienia nowych plików lub stron.

Odśwież indeksu wyszukiwania listy terminów z **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod**.

Dodaj następującą definicję metody do przestrzeni nazw TermLists, klasy programu.

    /// <summary>
    /// Refresh the search index for the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to refresh.</param>
    static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
        client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
        Thread.Sleep((int)(latencyDelay * 60 * 1000));
    }

## <a name="screen-text-using-a-term-list"></a>Tekst na ekranie przy użyciu listy terminów

Ekran tekstu przy użyciu listy terminów, za pomocą **ContentModeratorClient.TextModeration.ScreenText**, który przyjmuje następujące parametry.

- Język wyrażeń w listy terminów.
- Typ MIME, który może być "text/html", "text/xml", "text/języka markdown" lub "text/plain".
- Tekst do ekranu.
- Wartość logiczna. Ustaw to pole **true** Autokorekty tekstu przed specjalistycznego go.
- Wartość logiczna. Ustaw to pole **true** wykrywania osobistych identyfikowalne dane osobowe w tekście.
- Identyfikator listy terminów

Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** zwraca **ekranu** obiektu, który ma **warunki** właściwość, która zawiera dowolne warunki tego pakietu Content Moderator wykryte w kontroli. Należy pamiętać, że jeśli Content Moderator nie wykrył żadnych warunków podczas osłaniania, **warunki** właściwość ma wartość **null**.

Dodaj następującą definicję metody do przestrzeni nazw TermLists, klasy programu.

    /// <summary>
    /// Screen the indicated text for terms in the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to use to screen the text.</param>
    /// <param name="text">The text to screen.</param>
    static void ScreenText (ContentModeratorClient client, string list_id, string text)
    {
        Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
        Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
        if (null == screen.Terms)
        {
            Console.WriteLine("No terms from the term list were detected in the text.");
        }
        else
        {
            foreach (DetectedTerms term in screen.Terms)
            {
                Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
            }
        }
        read.Sleep(throttleRate);
    }

## <a name="delete-terms-and-lists"></a>Usuń warunki i list

Usuwanie termin lub listy jest bardzo proste. Zestaw SDK umożliwia wykonywanie następujących zadań:

- Usuń termin. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- Usuń wszystkie warunki na liście, bez usuwania listy. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- Usuń listę i całą jego zawartość. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>Usuń termin

Dodaj następującą definicję metody do przestrzeni nazw TermLists, klasy programu.

    /// <summary>
    /// Delete a term from the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to delete the term.</param>
    /// <param name="term">The term to delete.</param>
    static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
    {
        Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
        client.ListManagementTerm.DeleteTerm(list_id, term, lang);
        Thread.Sleep(throttleRate);
    }

### <a name="delete-all-terms-in-a-term-list"></a>Usuń wszystkie warunki na liście termin

Dodaj następującą definicję metody do przestrzeni nazw TermLists, klasy programu.

    /// <summary>
    /// Delete all terms from the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to delete all terms.</param>
    static void DeleteAllTerms (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
        client.ListManagementTerm.DeleteAllTerms(list_id, lang);
        Thread.Sleep(throttleRate);
    }

### <a name="delete-a-term-list"></a>Usuwanie listy terminów

Dodaj następującą definicję metody do przestrzeni nazw TermLists, klasy programu.

    /// <summary>
    /// Delete the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to delete.</param>
    static void DeleteTermList (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Deleting term list with ID {0}.", list_id);
        client.ListManagementTermLists.Delete(list_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>Łączenie wszystkiego razem

Dodaj **Main** definicję metody do przestrzeni nazw TermLists, klasy programu. Na koniec można zamknąć, klasę Program i TermLists przestrzeni nazw.

    static void Main(string[] args)
    {
        using (var client = Clients.NewClient())
        {
            string list_id = CreateTermList(client);

            UpdateTermList(client, list_id, "name", "description");
            AddTerm(client, list_id, "term1");
            AddTerm(client, list_id, "term2");

            GetAllTerms(client, list_id);

            // Always remember to refresh the search index of your list
            RefreshSearchIndex(client, list_id);

            string text = "This text contains the terms \"term1\" and \"term2\".";
            ScreenText(client, list_id, text);

            DeleteTerm(client, list_id, "term1");

            // Always remember to refresh the search index of your list
            RefreshSearchIndex(client, list_id);

            text = "This text contains the terms \"term1\" and \"term2\".";
            ScreenText(client, list_id, text);

            DeleteAllTerms(client, list_id);
            DeleteTermList(client, list_id);

            Console.WriteLine("Press ENTER to close the application.");
            Console.ReadLine();
        }
    }

## <a name="run-the-application-to-see-the-output"></a>Uruchom aplikację, aby wyświetlić dane wyjściowe

Dane wyjściowe będą znajdować się na następujące wiersze, ale dane mogą się różnić.

    Creating term list.
    Term list created. ID: 252.
    Updating information for term list with ID 252.
    
    Adding term "term1" to term list with ID 252.
    Adding term "term2" to term list with ID 252.
    
    Getting terms in term list with ID 252.
    term1
    term2
    
    Refreshing search index for term list with ID 252.
    
    Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
    Found term: "term1" from list ID 252 at index 32.
    Found term: "term2" from list ID 252 at index 46.
    
    Removed term "term1" from term list with ID 252.
    
    Refreshing search index for term list with ID 252.
    
    Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
    Found term: "term2" from list ID 252 at index 46.
    
    Removing all terms from term list with ID 252.
    Deleting term list with ID 252.
    Press ENTER to close the application.
    
## <a name="next-steps"></a>Kolejne kroki

Pobierz [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego programu oraz inne Przewodniki Szybki Start pakietu Content Moderator dla platformy .NET i Rozpocznij pracę nad integracją.
