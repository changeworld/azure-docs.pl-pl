---
title: Zbiorcze importowanie danych do konta interfejsu API usługi Azure Cosmos DB SQL przy użyciu pliku SDK .Net
description: Dowiedz się, jak importować lub pozyskiwania danych do usługi Azure Cosmos DB, tworząc aplikację konsoli .NET, która optymalizuje aprowizowaną przepływność (RU/s) wymaganą do importowania danych
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 79771e082a4a6ffae15f33f636b0300e93bcdaba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77587570"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Zbiorcze importowanie danych do konta interfejsu API usługi Azure Cosmos DB SQL przy użyciu sdk .NET

W tym samouczku pokazano, jak utworzyć aplikację konsoli platformy .NET, która optymalizuje aprowizowaną przepływność (RU/s) wymaganą do importowania danych do usługi Azure Cosmos DB. W tym artykule odczytasz dane z przykładowego źródła danych i zaimportujesz je do kontenera usługi Azure Cosmos.
W tym samouczku użyto [wersji 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) zestawu Azure Cosmos DB .NET SDK, który może być kierowany do platformy .NET Framework lub .NET Core.

W tym samouczku opisano następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos
> * Konfigurowanie projektu
> * Łączenie się z kontem usługi Azure Cosmos z włączoną obsługą zbiorczą
> * Wykonywanie importu danych za pomocą równoczesnych operacji tworzenia

## <a name="prerequisites"></a>Wymagania wstępne

Przed postępem w tym artykule należy wykonać następujące czynności:

* Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Możesz sprawdzić, która wersja jest dostępna `dotnet --version`w twoim środowisku, uruchamiając program .

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB

[Utwórz konto interfejsu API SQL usługi Azure Cosmos DB](create-cosmosdb-resources-portal.md) z witryny Azure portal lub możesz utworzyć konto przy użyciu [emulatora usługi Azure Cosmos DB.](local-emulator.md)

## <a name="step-2-set-up-your-net-project"></a>Krok 2: Konfigurowanie projektu platformy .NET

Otwórz wiersz polecenia systemu Windows lub okno terminala na komputerze lokalnym. Wszystkie polecenia w następnych sekcjach zostaną uruchomione w wierszu polecenia lub terminalu. Uruchom następujące polecenie dotnet new, aby utworzyć nową aplikację o nazwie *bulk-import-demo*. Parametr `--langVersion` ustawia *właściwość LangVersion* w utworzonym pliku projektu.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

Oczekiwane dane wyjściowe z kompilacji powinny wyglądać mniej więcej tak:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Krok 3: Dodawanie pakietu usługi Azure Cosmos DB

Będąc jeszcze w katalogu aplikacji, zainstaluj bibliotekę klienta usługi Azure Cosmos DB dla platformy .NET Core przy użyciu polecenia dotnet add package.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Krok 4: Uzyskaj poświadczenia konta usługi Azure Cosmos

Przykładowa aplikacja musi uwierzytelnić się na koncie usługi Azure Cosmos. Aby uwierzytelnić, należy przekazać poświadczenia konta usługi Azure Cosmos do aplikacji. Uzyskaj poświadczenia konta usługi Azure Cosmos, wykonując następujące kroki:

1.  Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1.  Przejdź do konta usługi Azure Cosmos.
1.  Otwórz okienko **Klucze** i skopiuj **identyfikator URI** i **klucz podstawowy** konta.

Jeśli używasz emulatora usługi Azure Cosmos DB, uzyskaj [poświadczenia emulatora z tego artykułu](local-emulator.md#authenticating-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Krok 5: Inicjowanie obiektu CosmosClient za pomocą obsługi wykonywania zbiorczego

Otwórz wygenerowany `Program.cs` plik w edytorze kodu. Utworzysz nowe wystąpienie CosmosClient z włączonym wykonywaniem zbiorczym i użyjesz go do wykonywania operacji przeciwko usłudze Azure Cosmos DB. 

Zacznijmy od nadpisania metody `Main` domyślnej i zdefiniowania zmiennych globalnych. Te zmienne globalne będą zawierać klucze punktu końcowego i autoryzacji, nazwę bazy danych, kontener, który zostanie utworzony, oraz liczbę elementów, które będą wstawiane zbiorczo. Pamiętaj, aby zastąpić wartości klucza endpointURL i autoryzacji zgodnie ze środowiskiem. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

Wewnątrz `Main` metody dodaj następujący kod, aby zainicjować CosmosClient obiektu:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Po włączeniu wykonywania zbiorczego CosmosClient wewnętrznie grupuje równoczesnych operacji w wywołania pojedynczej usługi. W ten sposób optymalizuje wykorzystanie przepływności przez dystrybucję wywołań usługi między partycjami i ostatecznie przypisanie poszczególnych wyników do oryginalnych wywołań.

Następnie można utworzyć kontener do przechowywania wszystkich naszych przedmiotów.  Zdefiniuj `/pk` jako klucz partycji, 50000 RU/s jako aprowizowaną przepływność i niestandardową zasadę indeksowania, która wykluczy wszystkie pola w celu optymalizacji przepływności zapisu. Dodaj następujący kod po instrukcji inicjowania CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Krok 6: Wypełnianie listy równoczesnych zadań

Aby skorzystać z obsługi wykonywania zbiorczego, należy utworzyć listę zadań asynchronicznych na podstawie źródła danych `Task.WhenAll` i operacji, które chcesz wykonać, i użyć do ich wykonania jednocześnie.
Zacznijmy od wygenerowania listy elementów z naszego modelu danych przy użyciu danych "Bogus". W rzeczywistych aplikacjach elementy będą pochodzić z żądanego źródła danych.

Najpierw dodaj pakiet Bogus do rozwiązania za pomocą polecenia dotnet add package.

   ```bash
   dotnet add package Bogus
   ```

Zdefiniuj definicję elementów, które chcesz zapisać. Należy zdefiniować `Item` klasę w `Program.cs` pliku:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Następnie utwórz funkcję pomocnika `Program` wewnątrz klasy. Ta funkcja pomocnika otrzyma liczbę elementów zdefiniowanych do wstawienia i generuje losowe dane:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Przeczytaj elementy i serializować je do `System.Text.Json` wystąpień strumienia przy użyciu klasy. Ze względu na charakter danych z autogenerowanymi są serializacji danych jako strumieni. Można również użyć wystąpienia elementu bezpośrednio, ale konwertując je do strumieni, można wykorzystać wydajność interfejsów API strumienia w CosmosClient. Zazwyczaj można użyć danych bezpośrednio tak długo, jak znasz klucz partycji. 


Aby przekonwertować dane na `Main` wystąpienia strumienia, w ramach metody dodaj następujący kod zaraz po utworzeniu kontenera:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Następnie użyj strumieni danych, aby utworzyć równoczesne zadania i wypełnić listę zadań, aby wstawić elementy do kontenera. Aby wykonać tę operację, dodaj `Program` następujący kod do klasy:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Wszystkie te równoczesne operacje punktowe będą wykonywane razem (co jest zbiorczo), zgodnie z opisem w sekcji wprowadzenia.

## <a name="step-7-run-the-sample"></a>Krok 7: Uruchom próbkę

Aby uruchomić próbkę, można to zrobić `dotnet` po prostu za pomocą polecenia:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Pobierz pełną próbkę

Jeśli nie masz czasu na ukończenie tego samouczka lub po prostu chcesz pobrać przykłady kodu, możesz uzyskać je w serwisie [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

Po sklonowaniu projektu należy zaktualizować żądane poświadczenia wewnątrz [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25).

Przykład można uruchomić, zmieniając katalog repozytorium i `dotnet`używając:

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos
> * Konfigurowanie projektu
> * Łączenie się z kontem usługi Azure Cosmos z włączoną obsługą zbiorczą
> * Wykonywanie importu danych za pomocą równoczesnych operacji tworzenia

Teraz możesz przejść do następnego samouczka:

> [!div class="nextstepaction"]
>[wykonywanie zapytań w usłudze Azure Cosmos DB przy użyciu interfejsu API SQL](tutorial-query-sql-api.md)